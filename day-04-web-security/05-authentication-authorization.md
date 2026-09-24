# 🔐 Authentication ve Authorization

Web uygulamalarında kullanıcı girişinin güvenli olması sadece kullanıcı adı ve şifre kontrolünden ibaret değildir. Kullanıcının **kim olduğunun doğrulanması** ve **hangi kaynaklara erişebileceğinin kontrol edilmesi** birbirinden farklı iki güvenlik konusudur.

Bu iki kavram genellikle birlikte kullanılsa da aynı şeyi ifade etmez.

---

## 1. Authentication Nedir?

**Authentication (Kimlik Doğrulama)**, kullanıcının gerçekten iddia ettiği kişi olup olmadığının kontrol edilmesidir.

Kısaca:

> **"Sen kimsin?"**

sorusuna cevap verir.

Örneğin bir kullanıcı:

```text
Kullanıcı adı: zeynep
Şifre: ********
```

bilgileriyle `/login` adresine istek gönderdiğinde uygulama bu bilgileri kontrol eder.

Bilgiler doğruysa kullanıcı doğrulanır ve uygulama genellikle kullanıcıya ait bir **session** oluşturur.

Örneğin:

```http
POST /login HTTP/1.1
Content-Type: application/json
```

```json
{
  "username": "zeynep",
  "password": "********"
}
```

Başarılı bir girişten sonra uygulama:

```http
Set-Cookie: session=abc123
```

gibi bir session cookie oluşturabilir.

Bundan sonraki isteklerde uygulama bu session üzerinden kullanıcının kim olduğunu anlayabilir.

---

## 2. Authorization Nedir?

**Authorization (Yetkilendirme)**, doğrulanmış kullanıcının hangi kaynaklara ve işlemlere erişebileceğinin kontrol edilmesidir.

Kısaca:

> **"Neye erişmeye yetkin var?"**

sorusuna cevap verir.

Örneğin sistemde iki kullanıcı olduğunu düşünelim:

```text
Zeynep → normal kullanıcı
Admin → yönetici
```

Zeynep sisteme başarıyla giriş yapmış olabilir. Ancak bu onun yönetici işlemlerini yapabileceği anlamına gelmez.

Örneğin:

```text
/profile
```

adresine erişmesine izin verilebilirken:

```text
/admin
```

adresine erişmesi engellenmelidir.

---

## 3. Authentication ve Authorization Arasındaki Fark

En önemli ayrım:

| Konu           | Sorduğu soru              |
| -------------- | ------------------------- |
| Authentication | Sen kimsin?               |
| Authorization  | Neye erişmeye yetkin var? |

Örneğin:

```text
1. Kullanıcı giriş yapar.
        ↓
2. Sistem kullanıcıyı doğrular.
        ↓
3. Kullanıcının kimliği belirlenir.
        ↓
4. Kullanıcı /profile sayfasına erişmek ister.
        ↓
5. Sistem bu kullanıcının bu kaynağa erişme yetkisini kontrol eder.
```

Burada 1-3. adımlar **Authentication**, 5. adım ise **Authorization** ile ilgilidir.

---

## 4. HTTP Durum Kodları ile İlişkisi

Authentication ve Authorization konularında özellikle iki HTTP durum kodunu ayırt etmek önemlidir.

### 401 Unauthorized

Kullanıcının kimliği doğrulanmamışsa veya geçerli bir authentication bilgisi yoksa kullanılabilir.

Örneğin:

```http
GET /profile HTTP/1.1
```

isteğinde geçerli bir session bulunmuyorsa:

```http
HTTP/1.1 401 Unauthorized
```

cevabı dönebilir.

Basitçe:

> **"Önce kim olduğunu doğrula."**

---

### 403 Forbidden

Kullanıcı tanınıyor veya doğrulanmış durumda olabilir ancak istediği kaynağa erişme yetkisi yoktur.

Örneğin normal bir kullanıcı:

```http
GET /admin HTTP/1.1
```

isteğini gönderirse:

```http
HTTP/1.1 403 Forbidden
```

cevabı dönebilir.

Basitçe:

> **"Seni tanıyorum ama buna erişme yetkin yok."**

Bu yüzden:

```text
401 → Authentication problemi
403 → Authorization / erişim yetkisi problemi
```

şeklinde düşünmek faydalıdır.

---

## 5. Web Uygulamalarında Korunması Gereken Alanlar

Bir web uygulamasında sadece `/login` adresinin korunması yeterli değildir.

Örneğin:

```text
/login
/profile
/admin
/users
/settings
/orders
```

gibi farklı kaynaklar bulunabilir.

Her kaynak için uygun erişim kontrolünün yapılması gerekir.

Örneğin:

```text
/login
   ↓
Kimlik doğrulama

/profile
   ↓
Giriş yapmış kullanıcı gerekli

/admin
   ↓
Giriş + admin yetkisi gerekli

/users
   ↓
Giriş + uygun rol/yetki gerekli
```

Burada önemli olan nokta, uygulamanın her hassas istekte kullanıcının **erişim yetkisini gerçekten kontrol etmesidir.**

---

## 6. Broken Access Control ile İlişkisi

Authentication başarılı olduğu halde Authorization düzgün uygulanmıyorsa **Broken Access Control** gibi güvenlik problemleri ortaya çıkabilir.

Örneğin uygulama:

```text
"Kullanıcı giriş yapmış mı?"
```

kontrolünü yapıyor fakat:

```text
"Bu kullanıcı bu kaynağa erişebilir mi?"
```

kontrolünü yapmıyorsa problem oluşabilir.

Örneğin normal bir kullanıcı:

```text
GET /admin
```

isteğini gönderdiğinde uygulama sadece session'ın geçerli olup olmadığını kontrol ederse, normal kullanıcıya yönetici kaynakları açılabilir.

Bu nedenle:

> **Authentication tek başına yeterli değildir.**

Kimliği doğrulanan kullanıcının erişebileceği kaynaklar ayrıca kontrol edilmelidir.

---

## 7. Rol Tabanlı Yetkilendirme

Uygulamalar kullanıcıları farklı roller altında gruplayabilir.

Örneğin:

```text
User
Admin
Moderator
Manager
```

gibi roller olabilir.

Basit bir örnek:

```text
User
 ├── /profile
 └── /orders

Admin
 ├── /profile
 ├── /orders
 ├── /users
 └── /admin
```

Burada Admin rolündeki kullanıcının erişebildiği bazı kaynaklara normal User erişememelidir.

Ancak güvenli bir sistemde sadece kullanıcı arayüzünde butonu gizlemek yeterli değildir.

Örneğin:

```text
Admin panel butonunu kullanıcıdan gizlemek
```

tek başına bir güvenlik önlemi değildir.

Sunucu tarafında da gerçekten:

```text
Bu kullanıcı bu işlemi yapabilir mi?
```

kontrolünün yapılması gerekir.

---

## 8. Authentication ve Session İlişkisi

Authentication sonrasında uygulama kullanıcının oturumunu takip etmek için session kullanabilir.

Örneğin:

```http
Set-Cookie: session=abc123
```

Sonraki istekte:

```http
Cookie: session=abc123
```

gönderildiğinde sunucu bu session ID üzerinden kullanıcının kimliğini belirleyebilir.

Ancak burada yalnızca kullanıcının kimliğinin belirlenmesi yeterli değildir.

Uygulamanın ayrıca kullanıcının:

```text
hangi kaynağa,
hangi işleme,
hangi role sahip olarak
```

erişebileceğini kontrol etmesi gerekir.

---

## 9. Güvenli Bir Erişim Kontrolünde Temel Mantık

Güvenli bir web uygulamasında genel akış şu şekilde düşünülebilir:

```text
Kullanıcı isteği
       ↓
Authentication kontrolü
       ↓
Kullanıcı tanındı mı?
       ↓
Authorization kontrolü
       ↓
Bu kullanıcı bu kaynağa erişebilir mi?
       ↓
       ├── Evet → İstek işlenir
       │
       └── Hayır → Erişim engellenir
```

Bu kontroller özellikle:

* yönetici panellerinde
* kullanıcı profillerinde
* başka kullanıcılara ait verilerde
* dosyalarda
* API endpoint'lerinde
* ödeme ve sipariş işlemlerinde

önemlidir.

---

## 10. Siber Güvenlik Açısından Neden Önemli?

Bir web uygulamasında kullanıcı giriş sisteminin bulunması uygulamanın otomatik olarak güvenli olduğu anlamına gelmez.

Örneğin:

```text
Authentication var
        ↓
Kullanıcı giriş yapabiliyor
        ↓
Ancak Authorization kontrolü eksik
        ↓
Kullanıcı yetkisi olmayan kaynağa erişebiliyor
```

Bu durumda ciddi bir erişim kontrolü problemi ortaya çıkabilir.

Bu nedenle web güvenliğinde şu iki sorunun ayrı ayrı sorulması gerekir:

```text
1. Kullanıcı kim?
2. Bu kullanıcının ne yapmaya yetkisi var?
```

Bu ayrım özellikle **Broken Access Control** konusunu anlamak için temel bir noktadır.

---

## Kısa Özet

```text
Authentication
→ Kimlik doğrulama
→ "Sen kimsin?"

Authorization
→ Yetkilendirme
→ "Neye erişebilirsin?"

401
→ Authentication problemi

403
→ Yetki / erişim problemi

Session
→ Kullanıcının oturumunu takip etmek için kullanılabilir

Broken Access Control
→ Kullanıcının yetkisi olmayan kaynaklara veya işlemlere erişebilmesi gibi erişim kontrolü problemleri
```

### 🎯 Bu bölümden çıkardığım temel fikir

Bir kullanıcının sisteme giriş yapabilmesi ile sistemde istediği her şeye erişebilmesi aynı şey değildir.

**Authentication kimliği doğrular, Authorization erişim hakkını kontrol eder.**
