# Authentication ve Authorization

Web uygulamalarında kullanıcıların kimliklerinin doğrulanması ve hangi kaynaklara erişebileceğinin kontrol edilmesi iki farklı güvenlik konusudur.

Bu çalışmada Authentication ve Authorization kavramlarını tekrar inceledim ve web uygulamalarında `/login`, `/profile`, `/admin` ve `/users` gibi alanların nasıl korunması gerektiğini araştırdım.

---

## Authentication Nedir?

**Authentication (kimlik doğrulama)**, bir kullanıcının gerçekten kim olduğunu doğrulama işlemidir.

Temel olarak şu soruya cevap verir:

> "Sen kimsin?"

Örneğin bir kullanıcı:

```text
Email + Password
       ↓
     /login
       ↓
Sunucu bilgileri kontrol eder
       ↓
Kimlik doğrulanır
```

Başarılı bir authentication işleminden sonra uygulama kullanıcı için bir session veya token oluşturabilir.

Örneğin:

```text
Kullanıcı login olur
       ↓
Authentication başarılı
       ↓
Session / JWT oluşturulur
       ↓
Sonraki requestlerde kullanıcı tanınabilir
```

Authentication için kullanıcı adı/şifre, MFA, passkey veya başka kimlik doğrulama yöntemleri kullanılabilir.

---

## Authorization Nedir?

**Authorization (yetkilendirme)**, kimliği doğrulanmış bir kullanıcının hangi kaynaklara ve işlemlere erişebileceğini belirleme işlemidir.

Temel olarak şu soruya cevap verir:

> "Bu kullanıcının neye erişmeye yetkisi var?"

Örneğin normal bir kullanıcı login olmuş olabilir:

```text
Authentication → Başarılı
```

Fakat bu kullanıcının admin paneline erişme yetkisi olmayabilir:

```text
Authorization → Başarısız
```

Bu nedenle authentication ile authorization aynı şey değildir.

---

# Authentication ve Authorization Farkı

Kısaca:

```text
Authentication
      ↓
"Sen kimsin?"
      ↓
Authorization
      ↓
"Neye erişebilirsin?"
```

Örneğin bir kullanıcının sisteme başarılı şekilde giriş yapması, otomatik olarak admin yetkisine sahip olduğu anlamına gelmez.

---

# `/login` Güvenliği

`/login` authentication sürecinin başlangıç noktalarından biridir.

Örneğin:

```http
POST /login
```

isteğinde kullanıcı email ve password gibi bilgilerini gönderebilir.

Sunucu:

1. Kullanıcının mevcut olup olmadığını kontrol eder.
2. Kimlik doğrulama bilgilerini kontrol eder.
3. Başarılı girişte session veya token oluşturur.
4. Başarısız girişte uygun bir hata response'u döndürür.

Login işlemi sırasında HTTPS kullanılması ve şifrelerin güvenli şekilde saklanması önemlidir.

Ayrıca başarısız giriş denemelerine karşı rate limiting gibi mekanizmalar kullanılabilir.

---

# `/profile` Güvenliği

`/profile` gibi kullanıcıya özel alanlarda öncelikle kullanıcının authentication durumunun kontrol edilmesi gerekir.

Örneğin:

```text
GET /profile
      ↓
Session / Token kontrolü
      ↓
Geçerli kullanıcı mı?
      ↓
Evet
      ↓
Kullanıcının kendi profilini göster
```

Kullanıcı giriş yapmamışsa uygulama uygun bir authentication hatası döndürmelidir.

Örneğin:

```http
401 Unauthorized
```

---

# `/admin` Güvenliği

`/admin` gibi yönetici alanlarında sadece kullanıcının giriş yapmış olması yeterli değildir.

Uygulamanın aynı zamanda kullanıcının **admin yetkisine sahip olup olmadığını** kontrol etmesi gerekir.

Örneğin:

```text
GET /admin
      ↓
Authentication kontrolü
      ↓
Kullanıcı giriş yapmış mı?
      ↓
Authorization kontrolü
      ↓
Admin yetkisi var mı?
```

Admin yetkisi yoksa:

```http
403 Forbidden
```

gibi bir response döndürülebilir.

Burada `401` ve `403` arasındaki farkı tekrar görmek mümkündür:

* `401` → Kullanıcının kimliği doğrulanmamış / geçerli authentication yok.
* `403` → Kullanıcı tanınıyor ancak bu kaynağa erişme yetkisi yok.

---

# `/users` Güvenliği

`/users` gibi kullanıcıların yönetildiği veya hassas bilgilerin bulunduğu endpoint'lerde de hem authentication hem authorization kontrolü yapılmalıdır.

Örneğin:

```text
GET /users
      ↓
Authentication
      ↓
Kullanıcı giriş yapmış mı?
      ↓
Authorization
      ↓
Bu kullanıcı /users kaynağına erişebilir mi?
```

Normal kullanıcı bu kaynağa erişme yetkisine sahip değilse erişimi engellenmelidir.

---

# Senaryo: Normal Kullanıcının Admin Alanına Erişmesi

Senaryoda normal kullanıcı:

```text
/user/profile
```

sayfasına erişebiliyor.

Bu normal bir davranış olabilir.

Fakat aynı kullanıcı:

```text
/admin/users
```

sayfasına da erişebiliyorsa uygulamada **Broken Access Control** problemi bulunabilir.

Çünkü uygulama kullanıcının giriş yapmış olduğunu kontrol ediyor olabilir fakat kullanıcının admin yetkisine sahip olup olmadığını doğru şekilde kontrol etmiyor olabilir.

Yanlış bir uygulama mantığı:

```text
Kullanıcı login olmuş mu?
       ↓
      Evet
       ↓
Admin sayfasını göster
```

Doğru yaklaşım ise:

```text
Kullanıcı login olmuş mu?
       ↓
      Evet
       ↓
Bu kullanıcının admin yetkisi var mı?
       ↓
    ┌───┴───┐
    ↓       ↓
   Evet    Hayır
    ↓       ↓
   İzin    403
   ver
```

---

# Broken Access Control

Broken Access Control, kullanıcının sahip olmaması gereken kaynaklara veya işlemlere erişebilmesine neden olan erişim kontrolü problemlerini kapsar.

Örneğin:

```text
Normal User
     ↓
Authentication ✅
     ↓
/user/profile  → ✅
/admin/users   → ❌ olması gerekir
```

Eğer normal kullanıcı `/admin/users` alanına erişebiliyorsa authorization mekanizması doğru uygulanmamış olabilir.

Benzer problemler sadece admin sayfalarında ortaya çıkmaz.

Örneğin:

```text
/profile?id=100
/profile?id=101
/orders/1001
/users/123
```

gibi kaynaklarda da kullanıcının yalnızca yetkili olduğu verilere erişip erişemediği kontrol edilmelidir.

Başka bir kullanıcının kaynak ID'sinin değiştirilmesiyle o kullanıcıya ait verilere erişilebilmesi, Broken Access Control ve IDOR gibi güvenlik problemleri açısından incelenebilir.

---

# Authentication, Session ve Authorization İlişkisi

Bu konuları birlikte düşündüğümde web uygulamasındaki akışı şu şekilde anlayabiliyorum:

```text
              LOGIN
                ↓
         Authentication
          "Sen kimsin?"
                ↓
           Session/JWT
                ↓
         Authorization
       "Neye erişebilirsin?"
                ↓
      ┌─────────┼─────────┐
      ↓         ↓         ↓
  /profile   /admin    /users
      ↓         ↓         ↓
     ✅        ❌         ❌
```

Burada önemli nokta, session veya JWT kullanılmasının tek başına authorization sağlamamasıdır.

Sunucu, korunan kaynaklara gelen requestlerde kullanıcının kim olduğunu ve o kaynağa erişmeye yetkili olup olmadığını kontrol etmelidir.

---

# Öğrendiğim Temel Noktalar

* Authentication kullanıcının kim olduğunu doğrular.
* Authorization kullanıcının neye erişebileceğini belirler.
* Login işlemi authentication sürecinin bir parçasıdır.
* Kullanıcının login olmuş olması her kaynağa erişebileceği anlamına gelmez.
* `/profile` gibi alanlarda authentication kontrolü gerekir.
* `/admin` ve `/users` gibi alanlarda authentication'ın yanında authorization kontrolü de gerekir.
* `401 Unauthorized` ile `403 Forbidden` farklı durumları ifade eder.
* Normal bir kullanıcının admin kaynaklarına erişebilmesi Broken Access Control problemi olabilir.
* Session veya JWT kullanılması tek başına yetkilendirme kontrolü anlamına gelmez.
* Erişim kontrollerinin server tarafında uygulanması gerekir.

## Sonuç

Bu çalışmada Authentication ve Authorization arasındaki farkı daha net anladım.

Authentication ile kullanıcının kimliği doğrulanırken, Authorization ile bu kullanıcının hangi kaynaklara erişebileceği belirleniyor.

Özellikle bir kullanıcının login olmuş olmasının tek başına yeterli olmadığını; `/admin` veya `/users` gibi hassas alanlarda ayrıca yetki kontrolü yapılması gerektiğini öğrendim.

Bir web uygulamasının güvenli olması için sadece login mekanizmasının bulunması yeterli değil. Kullanıcının her kaynağa erişiminde sahip olduğu yetkinin de doğru şekilde kontrol edilmesi gerekiyor.
