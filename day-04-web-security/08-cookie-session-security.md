# Cookie ve Session Güvenliği

Web uygulamalarında kullanıcı giriş yaptıktan sonra uygulamanın kullanıcının kim olduğunu ve oturumunun devam ettiğini bilmesi gerekir. HTTP stateless bir protokol olduğu için her request'in önceki requestlerden bağımsız olduğu düşünülebilir. Bu nedenle web uygulamalarında cookie ve session gibi mekanizmalar kullanılır.

Bu çalışmada cookie, session, session ID ve session güvenliğiyle ilgili temel kavramları inceledim.

---

## Cookie Nedir?

Cookie, web uygulamasının browser üzerinde saklanmasını istediği küçük veri parçalarıdır.

Sunucu browser'a örneğin:

```http
Set-Cookie: session=abc123
```

şeklinde bir response gönderebilir.

Browser da daha sonraki uygun requestlerde bu cookie'yi:

```http
Cookie: session=abc123
```

şeklinde gönderebilir.

Cookie'ler sadece oturum bilgisi için kullanılmaz. Dil tercihi, kullanıcı tercihleri, bazı uygulama ayarları ve çeşitli takip mekanizmaları için de kullanılabilir.

---

## Session Nedir?

Session, uygulamanın belirli bir kullanıcıya ait oturum durumunu takip etmesini sağlayan mekanizmadır.

Örneğin kullanıcı giriş yaptığında sunucu bir session oluşturabilir ve bu session içerisinde kullanıcının kimliği veya yetkileriyle ilgili bilgiler tutulabilir.

Basit bir mantıkla:

```text
Kullanıcı login olur
        ↓
Sunucu session oluşturur
        ↓
Session ID oluşturulur
        ↓
Browser bu ID'yi taşır
        ↓
Sonraki requestlerde gönderilir
        ↓
Sunucu kullanıcının oturumunu tanır
```

Burada önemli olan nokta cookie ile session'ın aynı şey olmamasıdır.

* Cookie, browser tarafında saklanan ve requestlerde gönderilebilen veridir.
* Session ise uygulamanın kullanıcı oturumunu takip etmesini sağlayan mekanizmadır.

---

## Session ID Nedir?

Session ID, bir kullanıcı oturumunu diğer oturumlardan ayırmak için kullanılan benzersiz tanımlayıcıdır.

Örneğin:

```text
session=abc123
```

ifadesinde `abc123` session ID olabilir.

Gerçek uygulamalarda session ID'nin tahmin edilmesi zor, yeterince uzun ve rastgele olması gerekir.

Çünkü session ID'nin ele geçirilmesi, saldırganın kullanıcının aktif oturumundan yararlanabilmesine neden olabilir.

---

## Cookie Neden Kullanılır?

HTTP stateless olduğu için sunucunun farklı requestlerin aynı kullanıcıya ait olduğunu anlayabilmesi gerekir.

Örneğin kullanıcı login olduktan sonra:

```text
GET /profile
GET /orders
GET /settings
```

gibi farklı requestler gönderebilir.

Cookie içerisinde bulunan session bilgisi sayesinde bu requestler kullanıcının aktif oturumuyla ilişkilendirilebilir.

Özetle:

```text
Browser
   ↓
Cookie: session=abc123
   ↓
Server
   ↓
abc123 → ilgili kullanıcı oturumu
```

şeklinde bir ilişki kurulabilir.

---

# Cookie Güvenlik Özellikleri

## HttpOnly

`HttpOnly`, cookie'nin JavaScript tarafından okunmasını sınırlar.

Örneğin:

```http
Set-Cookie: session=abc123; HttpOnly
```

şeklinde ayarlanan bir cookie, normal JavaScript koduyla `document.cookie` üzerinden okunamaz.

Bu özellik özellikle XSS gibi saldırılarda session cookie'sinin JavaScript tarafından okunması riskini azaltmaya yardımcı olur.

Ancak `HttpOnly` XSS açığını ortadan kaldırmaz. Sadece cookie'ye JavaScript üzerinden erişimi sınırlar.

---

## Secure

`Secure` özelliği, cookie'nin yalnızca HTTPS bağlantıları üzerinden gönderilmesini sağlar.

Örneğin:

```http
Set-Cookie: session=abc123; Secure
```

şeklinde bir cookie tanımlanabilir.

Amaç, session gibi hassas bilgilerin güvensiz HTTP bağlantıları üzerinden gönderilmesi riskini azaltmaktır.

Kısaca:

```text
Secure → Cookie HTTPS üzerinden gönderilir.
```

---

## SameSite

`SameSite`, cookie'nin cross-site isteklerde gönderilme davranışını kontrol etmeye yardımcı olur.

Temel olarak üç farklı değeri vardır:

* `Strict`
* `Lax`
* `None`

Örneğin:

```http
Set-Cookie: session=abc123; SameSite=Lax
```

şeklinde kullanılabilir.

`SameSite`, özellikle CSRF saldırılarına karşı korunmada önemli bir güvenlik mekanizmasıdır.

`SameSite=None` kullanıldığında cookie'nin cross-site gönderimine izin verilir ve modern browserlarda bunun `Secure` ile birlikte kullanılması gerekir.

---

# Session Hijacking Nedir?

Session hijacking, saldırganın başka bir kullanıcının geçerli session bilgisini ele geçirerek o kullanıcının oturumundan yararlanmaya çalışmasıdır.

Örneğin kullanıcının:

```text
session=abc123
```

şeklinde bir session cookie'si olduğunu düşünelim.

Normal kullanıcı:

```text
Browser
   ↓
Cookie: session=abc123
   ↓
Server
```

şeklinde request gönderiyor.

Eğer saldırgan `abc123` session ID'sini ele geçirirse kendi requestinde de bu session bilgisini kullanmaya çalışabilir:

```text
Saldırgan
   ↓
Cookie: session=abc123
   ↓
Server
```

Uygulama session ID'yi geçerli kabul ederse saldırgan kullanıcının oturumuna erişmeye çalışabilir.

Bu nedenle session ID hassas bir bilgidir ve korunması gerekir.

---

# Session Güvenliği İçin Temel Önlemler

Session güvenliğinde tek bir önleme güvenmek yerine birden fazla güvenlik mekanizması birlikte kullanılmalıdır.

Temel önlemler:

* Session ID tahmin edilemez ve yeterince rastgele olmalıdır.
* HTTPS kullanılmalıdır.
* Hassas session cookie'lerinde `HttpOnly` kullanılmalıdır.
* Cookie'lerde uygun şekilde `Secure` kullanılmalıdır.
* `SameSite` değeri uygulamanın ihtiyacına göre ayarlanmalıdır.
* Session'ların geçerlilik süresi kontrol edilmelidir.
* Logout sonrasında session geçersiz hale getirilmelidir.
* Kritik işlemlerde gerektiğinde yeniden doğrulama uygulanmalıdır.

---

# Öğrendiğim Temel Bağlantı

Bu çalışmada kavramların birbirleriyle bağlantısını şu şekilde oturttum:

```text
Cookie
   ↓
Session ID taşıyabilir
   ↓
Session ID
   ↓
Sunucudaki kullanıcı oturumunu temsil eder
   ↓
Session ID ele geçirilirse
   ↓
Session Hijacking riski oluşabilir
```

Cookie güvenliği açısından ise:

```text
HttpOnly → JavaScript erişimini sınırlar
Secure   → HTTPS üzerinden gönderilmesini sağlar
SameSite → Cross-site cookie gönderimini kontrol eder
```

Bu özelliklerin birbirinin yerine geçmediğini ve farklı güvenlik risklerini azaltmaya yardımcı olduğunu öğrendim.

## Sonuç

Cookie ve session kavramlarını incelerken aslında web uygulamalarında kullanıcı oturumunun nasıl sürdürüldüğünü anlamış oldum.

Özellikle session ID'nin bir kullanıcının aktif oturumunu temsil edebilmesi nedeniyle korunmasının önemli olduğunu gördüm. `HttpOnly`, `Secure` ve `SameSite` gibi cookie özelliklerinin ise session güvenliğini artırmak için kullanılan önemli mekanizmalar olduğunu öğrendim.

Bu bölümde herhangi bir saldırı veya istismar gerçekleştirmeden, cookie ve session güvenliğinin temel mantığını araştırdım.
