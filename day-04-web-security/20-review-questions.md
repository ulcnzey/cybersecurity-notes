# 20. Değerlendirme Soruları

Bu bölümde web güvenliği çalışması boyunca öğrendiğim konuları kendi cümlelerimle tekrar ettim.

---

## 1. HTTP request ve response nedir?

HTTP request, istemcinin (örneğin tarayıcının) sunucuya gönderdiği istektir.

HTTP response ise sunucunun bu isteğe verdiği cevaptır.

Örneğin tarayıcı bir web sayfasını istediğinde:

```text
Browser → HTTP Request → Server
Browser ← HTTP Response ← Server
```

şeklinde bir iletişim gerçekleşir.

Request içerisinde method, URL, header ve gerektiğinde body bulunabilir. Response içerisinde ise status code, header ve response body bulunabilir.

---

## 2. GET ve POST arasındaki temel fark nedir?

GET genellikle sunucudan veri istemek için kullanılır.

POST ise genellikle sunucuya veri göndermek veya bir işlem başlatmak için kullanılır.

Örneğin:

```text
GET /products
```

ürünleri istemek için kullanılabilir.

```text
POST /login
```

ise login bilgilerini sunucuya göndermek için kullanılabilir.

GET ile gönderilen veriler çoğunlukla URL içerisindeki query parameter'larda görülebilirken, POST verileri genellikle request body içerisinde taşınır.

Ancak POST kullanmak tek başına bir isteği güvenli hale getirmez. Güvenlik ayrıca authentication, authorization, input validation ve diğer kontrollerle sağlanmalıdır.

---

## 3. Cookie ile session arasındaki fark nedir?

Cookie, tarayıcı tarafında saklanan ve uygun durumlarda sunucuya tekrar gönderilen küçük veri parçalarıdır.

Session ise kullanıcının oturum durumunu takip etmek için kullanılan mekanizmadır. Session bilgileri genellikle server tarafında tutulur ve kullanıcıyı tanımak için bir session ID kullanılabilir.

Basit olarak:

```text
Cookie → Tarayıcı tarafındaki veri
Session → Kullanıcının oturum durumunu takip eden yapı
```

Cookie içerisinde session ID bulunabilir.

Örneğin:

```http
Set-Cookie: session=<SESSION_ID>
```

Daha sonraki request'te:

```http
Cookie: session=<SESSION_ID>
```

şeklinde gönderilebilir.

Burada cookie ile session aynı şey değildir ancak birlikte çalışabilirler.

---

## 4. Authentication ve authorization arasındaki fark nedir?

Authentication, kullanıcının kim olduğunu doğrulama işlemidir.

> **Authentication = Sen kimsin?**

Authorization ise doğrulanmış kullanıcının hangi kaynaklara veya işlemlere erişebileceğini belirler.

> **Authorization = Neye erişmeye yetkin var?**

Örneğin kullanıcı login olduğunda authentication gerçekleşir.

Ancak bu kullanıcının `/admin` sayfasına erişip erişemeyeceğine karar verilmesi authorization konusudur.

---

## 5. 401 ile 403 arasındaki fark nedir?

**401 Unauthorized**, isteğin geçerli bir authentication bilgisi olmadan yapılması veya authentication probleminin bulunması durumunda kullanılan status code'dur.

**403 Forbidden** ise sunucunun isteği yapan kullanıcıyı tanıdığı veya authentication'ın mevcut olduğu ancak kullanıcının istenen kaynağa erişme yetkisinin olmadığı durumlarda kullanılır.

Kısaca:

```text
401 → Kim olduğunu doğrulayamadım / authentication problemi var.

403 → Seni tanıyorum ama buna erişme yetkin yok.
```

---

## 6. SQL Injection neden oluşur?

SQL Injection, kullanıcı tarafından kontrol edilen verilerin güvenli olmayan şekilde SQL sorgusuna dahil edilmesi sonucunda oluşabilir.

Örneğin uygulamanın kullanıcı girdisini doğrudan SQL sorgusunun bir parçası haline getirmesi problem oluşturabilir.

Temel problem:

```text
Kullanıcı verisi + SQL kodu
```

birbirinden güvenli şekilde ayrılmadığında ortaya çıkar.

Bunu önlemek için özellikle:

* Prepared Statement
* Parameterized Query
* Güvenli ORM kullanımı
* Least privilege

gibi yöntemler kullanılmalıdır.

---

## 7. XSS ile SQL Injection arasındaki temel fark nedir?

İki zafiyetin temel farkı etkilenen katmandır.

**SQL Injection**, kullanıcı girdisinin backend tarafında SQL sorgusunu etkilemesiyle ilgilidir.

**XSS** ise kullanıcı tarafından kontrol edilen verinin başka bir kullanıcının tarayıcısında güvenli olmayan şekilde işlenmesiyle ilgilidir.

Basit olarak:

```text
SQL Injection → Backend / Database

XSS → Browser / Frontend / DOM
```

İkisinde de temel problemlerden biri güvenilmeyen kullanıcı girdisinin güvenli şekilde işlenmemesidir.

---

## 8. IDOR hangi güvenlik kategorisiyle ilişkilidir?

IDOR, **Broken Access Control** ile ilişkilidir.

OWASP Top 10:2021 içerisinde bu konu:

> **A01 — Broken Access Control**

kategorisi altında değerlendirilir.

Örneğin:

```text
/profile?id=100
/profile?id=101
```

şeklinde ID değerinin değiştirilmesi tek başına zafiyet değildir.

Asıl problem, uygulamanın kullanıcının gerçekten o kaynağa erişme yetkisi olup olmadığını kontrol etmemesidir.

---

## 9. CSRF neden kullanıcı session'ıyla ilişkilidir?

CSRF, kullanıcının zaten giriş yapmış olduğu bir uygulamadaki mevcut authentication/session durumunun kötüye kullanılmasına dayanır.

Tarayıcı bazı durumlarda hedef uygulamaya ait authentication cookie'sini otomatik olarak gönderebilir.

Eğer uygulamada gerekli CSRF kontrolleri yoksa, başka bir web sayfasından başlatılan istek kullanıcının mevcut oturumuyla ilişkilendirilebilir.

Bu nedenle CSRF'de session ve özellikle authentication cookie'lerinin davranışı önemlidir.

---

## 10. `HttpOnly` cookie ne işe yarar?

`HttpOnly` özelliği, cookie'nin JavaScript tarafından okunmasını sınırlar.

Örneğin:

```http
Set-Cookie: session=<SESSION_ID>; HttpOnly
```

şeklindeki bir cookie'ye normal JavaScript kodunun erişmesi engellenir.

Bu özellik özellikle XSS durumunda session cookie'sinin JavaScript tarafından okunabilmesi riskini azaltmaya yardımcı olur.

Ancak:

> **HttpOnly, XSS'i ortadan kaldırmaz.**

Sadece cookie'nin JavaScript tarafından okunmasına karşı ek bir koruma sağlar.

---

## 11. `Secure` cookie ne işe yarar?

`Secure` özelliği, cookie'nin yalnızca HTTPS üzerinden gönderilmesini sağlar.

Örneğin:

```http
Set-Cookie: session=<SESSION_ID>; Secure
```

şeklindeki bir cookie HTTP üzerinden gönderilmemelidir.

Bu nedenle authentication/session cookie'lerinde HTTPS ile birlikte `Secure` kullanılması önemlidir.

Ancak `Secure` özelliği tek başına session güvenliğini tamamen sağlamaz.

---

## 12. SameSite nedir?

`SameSite`, cookie'nin cross-site isteklerde hangi durumlarda gönderileceğini kontrol eden cookie özelliğidir.

Başlıca değerleri:

```text
Strict
Lax
None
```

şeklindedir.

Özellikle cross-site request'lerde cookie davranışını sınırladığı için CSRF riskini azaltmaya yardımcı olabilir.

Örneğin:

```http
Set-Cookie: session=<SESSION_ID>; SameSite=Lax
```

şeklinde bir kullanım mümkündür.

Ancak SameSite da tek başına bütün CSRF problemlerini çözmez. Uygulamanın diğer CSRF kontrolleri de doğru şekilde tasarlanmalıdır.

---

## 13. Burp Suite neden web güvenliği çalışmalarında kullanılır?

Burp Suite, web uygulaması ile tarayıcı arasındaki HTTP/HTTPS trafiğini incelemek için kullanılan bir güvenlik test aracıdır.

Ben laboratuvar çalışmamda özellikle **Proxy** ve **HTTP History** özelliklerini kullandım.

Örneğin:

```text
Browser
   ↓
Burp Proxy
   ↓
Web Application
```

şeklindeki iletişimi gözlemleyebildim.

Burp Suite sayesinde:

* Request'leri görmek
* Response'ları incelemek
* Header'ları analiz etmek
* Cookie bilgilerini görmek
* Query parameter'ları incelemek
* Request'leri Repeater'a göndermek

mümkün oluyor.

Bu nedenle web uygulamasının dışarıdan nasıl göründüğünü anlamak için önemli bir araç olduğunu öğrendim.

---

## 14. OWASP Top 10 nedir?

OWASP Top 10, web uygulamalarında önemli güvenlik risklerini anlamak ve güvenlik çalışmalarına temel oluşturmak için kullanılan OWASP tarafından hazırlanan bir listedir.

Bu liste web uygulamalarındaki yaygın ve önemli güvenlik risklerini kategoriler halinde ele alır.

Ben bu çalışmada özellikle **OWASP Top 10:2021** kategorilerini inceledim.

Örneğin:

* Broken Access Control
* Cryptographic Failures
* Injection
* Insecure Design
* Security Misconfiguration
* Vulnerable and Outdated Components
* Identification and Authentication Failures
* Software and Data Integrity Failures
* Security Logging and Monitoring Failures
* SSRF

gibi kategoriler bulunur.

OWASP Top 10'u benim için bir web uygulamasını değerlendirirken kullanabileceğim temel bir güvenlik kontrol listesi olarak görüyorum.

---

## 15. Bir web uygulamasının HTTPS kullanması tüm güvenlik problemlerini çözer mi?

Hayır.

HTTPS, HTTP iletişimini TLS kullanarak korur ve özellikle:

* Veri gizliliği
* Veri bütünlüğü
* Sunucunun doğrulanması

konularında önemli bir koruma sağlar.

Ancak HTTPS uygulamanın içerisindeki bütün güvenlik açıklarını otomatik olarak çözmez.

Örneğin uygulamada:

```text
SQL Injection
XSS
IDOR
Broken Access Control
CSRF
Security Misconfiguration
```

gibi problemler bulunabilir.

Bu nedenle:

> **HTTPS kullanmak web uygulamasını güvenli hale getiren önemli bir katmandır ancak bütün güvenlik problemlerini çözmez.**

---

## 16. Bir kullanıcı giriş yapabiliyor diye uygulamadaki bütün sayfalara erişebilmesi gerekir mi?

Hayır.

Login olmak authentication'ın başarılı olduğunu gösterir. Ancak kullanıcının hangi kaynaklara erişebileceği authorization ile belirlenir.

Örneğin normal bir kullanıcı:

```text
/profile
```

sayfasına erişebilirken:

```text
/admin
/admin/users
```

gibi yönetici kaynaklarına erişememelidir.

Bu nedenle login olmuş olmak:

> **"Uygulamadaki her şeye erişebilirim."**

anlamına gelmez.

Her korunan kaynak için uygun authorization kontrolü yapılmalıdır.

---

## 17. Bir web uygulamasında kullanıcıdan alınan veriler neden güvenilmemesi gereken veri olarak değerlendirilir?

Çünkü uygulama kullanıcının gönderdiği verinin gerçekten güvenli olduğunu varsaymamalıdır.

Kullanıcıdan gelen veri:

* Form
* URL parametresi
* JSON
* Cookie
* Header
* Dosya adı
* Arama alanı
* Dosya upload

gibi birçok farklı kaynaktan gelebilir.

Kullanıcı bu verileri değiştirebildiği için backend tarafında güvenilmeyen veri olarak değerlendirilmelidir.

Bu nedenle uygulama:

```text
Kullanıcı verisi
      ↓
Validation
      ↓
Güvenli işleme
      ↓
Authorization / uygun kontrol
```

mantığıyla çalışmalıdır.

Özellikle SQL Injection, XSS, IDOR ve diğer birçok web güvenlik probleminde kullanıcı girdisinin nasıl işlendiği önemli bir noktadır.

---

## 18. Bir güvenlik açığını tespit etmek ile onu istismar etmek arasındaki fark nedir?

Bir güvenlik açığını **tespit etmek**, uygulamanın güvenlik açısından problem oluşturabilecek bir davranışını belirlemek ve doğrulamaktır.

**İstismar etmek** ise bu güvenlik açığını kullanarak sistem üzerinde saldırı gerçekleştirmeye çalışmaktır.

Örneğin:

```text
Zafiyet tespiti:
"Bu endpoint'te authorization kontrolü eksik olabilir."

İstismar:
"Bu eksikliği kullanarak başka bir kullanıcının verisine erişmeye çalışmak."
```

Güvenlik çalışmalarında tespit ve doğrulama ile yetkisiz saldırı gerçekleştirmek birbirinden farklıdır.

Benim yaptığım laboratuvar çalışmasında amaç, kontrollü ve yetkili ortamda güvenlik problemlerini anlamak ve değerlendirmektir.

---

## 19. Security Misconfiguration nedir?

Security Misconfiguration, uygulama, sunucu veya güvenlik bileşenlerinin güvenli olmayan şekilde yapılandırılmasıdır.

Örneğin:

* Default password
* Debug mode
* Gereksiz servisler
* Directory listing
* Açık admin paneli
* Gereksiz bilgi veren HTTP header'ları
* Eski yazılım versiyonları
* Gereğinden fazla izin

security misconfiguration kapsamında değerlendirilebilir.

Burada önemli nokta, güvenliğin yalnızca uygulama kodundan oluşmamasıdır.

Sunucu, framework, servisler, izinler ve production ayarları da güvenlik açısından değerlendirilmelidir.

---

## 20. Bir web uygulamasında loglama neden önemlidir?

Loglama, uygulamada gerçekleşen olayların kaydedilmesini sağlar.

Güvenlik açısından özellikle:

* Başarılı loginler
* Başarısız login denemeleri
* Yetkisiz erişim denemeleri
* Şüpheli request'ler
* Kritik işlemler
* Sistem hataları

gibi olayların izlenmesi önemlidir.

Loglama yalnızca saldırıyı önlemek için değil, saldırı veya güvenlik olayından **sonra ne olduğunu anlamak** için de gereklidir.

Örneğin bir kullanıcı hesabında şüpheli hareket görülürse loglar:

```text
Ne oldu?
Ne zaman oldu?
Hangi kullanıcı?
Hangi endpoint?
Hangi işlem?
```

gibi soruların cevaplanmasına yardımcı olabilir.

Bu nedenle loglama, güvenlikte **tespit, inceleme ve olay müdahalesi** açısından önemli bir katmandır.

---

# Genel Değerlendirme

Bu 20 soruyu cevapladıktan sonra web güvenliğinde birbirinden ayrı görünen konuların aslında birbirleriyle bağlantılı olduğunu daha net görebiliyorum.

Örneğin:

```text
Authentication
      ↓
Kullanıcı kim?
      ↓
Authorization
      ↓
Neye erişebilir?
      ↓
Input
      ↓
Kullanıcıdan ne geliyor?
      ↓
Session
      ↓
Oturum nasıl korunuyor?
      ↓
Configuration
      ↓
Uygulama nasıl yapılandırılmış?
      ↓
Logging
      ↓
Şüpheli olayları nasıl fark edeceğiz?
```

Benim için Day 4'ün en önemli çıkarımı, web güvenliğinin tek bir özellikten oluşmadığı oldu.

Bir uygulamada login ekranının güvenli olması tek başına yeterli değildir. Uygulamanın **authentication, authorization, input validation, session yönetimi, güvenli yapılandırma ve logging** gibi farklı katmanlarının birlikte değerlendirilmesi gerekir.

> **Sonuç:** Güvenli bir web uygulaması için yalnızca "kullanıcı giriş yapabiliyor mu?" sorusuna değil, uygulamanın kullanıcıdan aldığı veriden yetkilendirme kontrollerine ve güvenlik olaylarının izlenmesine kadar bütün sürece bakmak gerekir.
