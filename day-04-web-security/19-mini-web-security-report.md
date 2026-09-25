# Mini Web Güvenlik Raporu

Bu çalışmada, web güvenliği kapsamında daha önce incelediğim konuları bir laboratuvar uygulaması üzerinde bir araya getirerek genel bir güvenlik değerlendirmesi yaptım.

Laboratuvar ortamında OWASP Juice Shop uygulamasını Kali Linux üzerinde Docker kullanarak çalıştırdım ve HTTP trafiğini Burp Suite üzerinden inceledim.

Bu raporda özellikle HTTP request/response yapısı, authentication, authorization, cookie/session yönetimi ve OWASP Top 10 kapsamında değerlendirilebilecek güvenlik problemleri üzerinde durdum.

> **Not:** Bu çalışma yalnızca eğitim amaçlı, yerel laboratuvar ortamında gerçekleştirilmiştir. Gerçek sistemler üzerinde yetkisiz test yapılmamıştır.

---

## 1. Uygulama

### 1.1 Uygulama Adı

**OWASP Juice Shop**

OWASP Juice Shop, web uygulama güvenliği konularını öğrenmek ve güvenlik testleri gerçekleştirmek amacıyla hazırlanmış eğitim amaçlı bir web uygulamasıdır.

### 1.2 Kullanılan Ortam

Çalışmada aşağıdaki ortam ve araçları kullandım:

* Kali Linux
* Docker
* OWASP Juice Shop
* Burp Suite
* Web Browser
* HTTP/HTTPS trafiği

Juice Shop'u Docker üzerinde yerel olarak çalıştırdım ve uygulamaya:

```text
http://localhost:3000
```

adresinden eriştim.

Burp Suite'i ise tarayıcı ile web uygulaması arasındaki HTTP trafiğini gözlemlemek için kullandım.

Temel çalışma akışım:

```text
Browser
   ↓
Burp Suite Proxy
   ↓
OWASP Juice Shop
   ↓
Application
   ↓
Database
   ↓
Response
   ↓
Burp Suite
   ↓
Browser
```

### 1.3 Test Kapsamı

Bu çalışmada aşağıdaki konular incelendi:

* HTTP request/response yapısı
* HTTP metodları
* HTTP header bilgileri
* Cookie ve session yapısı
* Authentication
* Authorization
* Kullanıcı rolleri
* OWASP Top 10
* SQL Injection
* XSS
* IDOR / Broken Access Control
* Directory Traversal
* File Upload Security
* CSRF
* Security Misconfiguration
* Web güvenlik kontrol listesi

Çalışmanın temel amacı, bir web uygulamasına yalnızca "çalışıyor mu?" açısından değil, **güvenlik açısından nasıl bakılması gerektiğini** öğrenmekti.

---

# 2. HTTP Analizi

Burp Suite kullanarak Juice Shop ile tarayıcı arasındaki HTTP trafiğini gözlemledim.

HTTP analizinde özellikle şu bilgileri incelemeye çalıştım:

* HTTP method
* URL/path
* Query parameter
* Request headers
* Request body
* Cookie
* Response status code
* Response headers
* Response body

## 2.1 Request 1 — Ana Sayfa

Ana sayfaya erişirken tarayıcı tarafından bir `GET` isteği gönderildi.

Örnek:

```http
GET / HTTP/1.1
Host: localhost:3000
User-Agent: Mozilla/5.0 ...
Accept: ...
```

### İstek

* **Method:** GET
* **Path:** `/`
* **Host:** `localhost:3000`
* **Body:** Yok
* **Amaç:** Uygulamanın ana sayfasını istemek

### Response

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
```

`200 OK` cevabı, isteğin başarılı şekilde işlendiğini gösteriyor.

### Gözlem

Bu istekte kullanıcı tarafından doğrudan gönderilen temel bilgiler arasında tarayıcı bilgisi ve kabul edilen içerik türleri bulunuyor.

---

## 2.2 Request 2 — Ürün Arama

Uygulamadaki ürün arama işlemlerini incelerken ürün arama API'sine yönelik bir HTTP isteği gözlemledim.

Örnek yapı:

```http
GET /rest/products/search?q=<search_value> HTTP/1.1
Host: localhost:3000
Accept: application/json, text/plain, */*
User-Agent: Mozilla/5.0 ...
```

### İstek

* **Method:** GET
* **Path:** `/rest/products/search`
* **Query parameter:** `q`
* **Body:** Yok
* **Response formatı:** JSON

Burada kullanıcı tarafından girilen arama değerinin URL içerisindeki `q` parametresine gönderildiğini gözlemledim.

### Gözlem

Bu nokta web güvenliği açısından önemlidir çünkü kullanıcı tarafından gönderilen veriler backend tarafından işlenmektedir.

Kullanıcı girdilerinin güvenli şekilde işlenmemesi durumunda SQL Injection veya XSS gibi farklı güvenlik problemleri ortaya çıkabilir.

Ancak yalnızca bir parametrenin kullanıcı tarafından kontrol edilebilir olması tek başına bir zafiyet olduğunu göstermez. Backend tarafındaki veri işleme yönteminin ayrıca değerlendirilmesi gerekir.

---

## 2.3 Request 3 — Login

Kullanıcı girişi sırasında `POST` metodunun kullanıldığı bir login isteği gözlemledim.

Örnek yapı:

```http
POST /rest/user/login HTTP/1.1
Host: localhost:3000
Content-Type: application/json
```

Request body içerisinde login bilgileri JSON formatında gönderildi.

Örnek yapı:

```json
{
  "email": "<email>",
  "password": "<password>"
}
```

### Gözlem

Login işleminin `POST` üzerinden gerçekleştirildiğini ve kullanıcı bilgilerinin request body içerisinde gönderildiğini gördüm.

Login sonrasında uygulamanın authentication durumunu devam ettirmek için token tabanlı bir yapı kullandığını gözlemledim.

> **Güvenlik notu:** Gerçek token, JWT veya cookie değerleri GitHub'a kesinlikle eklenmemelidir. Bu raporda bu değerleri `<JWT>` veya `<COOKIE>` şeklinde maskeledim.

---

## 2.4 Request 4 — Product API

Ürün verilerinin alınması sırasında uygulamanın API endpoint'lerinden birine istek gönderdiğini gözlemledim.

Örnek response yapısı:

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: ...
```

Response body içerisinde ürün bilgilerinin JSON formatında döndüğünü gördüm.

Örnek:

```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "name": "Apple Juice (1000ml)"
    }
  ]
}
```

### Gözlem

Bu yapı bana web uygulamalarında frontend ile backend arasındaki iletişimin yalnızca HTML üzerinden gerçekleşmediğini, API'lerin de uygulamanın önemli bir parçası olduğunu gösterdi.

---

## 2.5 Request 5 — API / Arama İsteği

Bir başka API isteğinde aşağıdaki gibi header bilgileri gözlemledim:

```http
GET /rest/products/search?q= HTTP/1.1
Host: localhost:3000
Accept: application/json, text/plain, */*
User-Agent: Mozilla/5.0 ...
Referer: http://localhost:3000/
Cookie: <COOKIE>
```

### Gözlem

Bu istekte:

* HTTP methodu `GET`
* Kullanılan endpoint `/rest/products/search`
* `q` query parametresi
* `Accept` header'ı
* `User-Agent`
* `Referer`
* Cookie bilgileri

bulunuyor.

Bu istek üzerinden özellikle kullanıcı girdisinin `q` parametresi ile backend'e aktarılabildiğini ve authentication/session ile ilişkili bilgilerin request içerisinde taşınabildiğini gözlemledim.

---

# 3. Authentication

Authentication, kullanıcının kimliğinin doğrulanması işlemidir.

Temel olarak:

> **Authentication = "Sen kimsin?"**

Juice Shop içerisinde kullanıcıların login olabildiği bir authentication mekanizması bulunmaktadır.

## 3.1 Login Sistemi

Login sırasında kullanıcı tarafından email ve password bilgileri gönderilir.

Örnek:

```http
POST /rest/user/login
```

Backend bu bilgileri değerlendirerek authentication sonucunu oluşturur.

Başarılı login sonrasında uygulama tarafından authentication durumunun devam ettirilmesi için token tabanlı bir yapı kullanıldığını gözlemledim.

## 3.2 Session Yönetimi

Web uygulamalarında kullanıcının her request'te tekrar login olması gerekmez.

Bunun yerine uygulama kullanıcıyı tanımaya yarayan bir session/token mekanizması kullanabilir.

Juice Shop üzerinde request'lerde authentication ile ilişkili token bilgileri gözlemledim.

Örneğin:

```http
Authorization: Bearer <JWT>
```

ve bazı request'lerde cookie içerisinde token benzeri bilgiler bulunabiliyor.

Bu nedenle token/session bilgilerinin korunması önemlidir.

Bir saldırgan geçerli bir authentication bilgisini ele geçirirse, uygulamanın tasarımına bağlı olarak kullanıcının oturumunu kötüye kullanmaya çalışabilir.

## 3.3 Tespit Ettiğim Güvenlik Kontrolleri

İnceleme sırasında şu kontrolleri gözlemledim:

* Login endpoint'i bulunması
* Authentication token kullanılması
* HTTP request'lerinde authentication bilgisinin taşınması
* Cookie mekanizmasının kullanılması
* API endpoint'lerinin bulunması

Burada önemli nokta, authentication mekanizmasının bulunmasının tek başına uygulamanın güvenli olduğu anlamına gelmemesidir.

Authentication başarılı olsa bile authorization kontrollerinin ayrıca yapılması gerekir.

---

# 4. Authorization

Authorization, authenticated kullanıcının hangi kaynaklara erişebileceğini belirleyen kontroldür.

Temel olarak:

> **Authorization = "Neye erişmeye yetkin var?"**

Authentication ile authorization birbirinden farklıdır.

Örneğin bir kullanıcı login olmuş olabilir ancak bu kullanıcının admin paneline erişme yetkisi olmayabilir.

## 4.1 Kullanıcı Rolleri

Web uygulamalarında farklı kullanıcı rolleri bulunabilir.

Örneğin:

```text
Normal User
Admin
```

Normal bir kullanıcı ile admin kullanıcının erişebileceği kaynaklar aynı olmak zorunda değildir.

## 4.2 Erişim Kontrolleri

Özellikle aşağıdaki kaynakların server tarafında authorization kontrolüne tabi olması gerekir:

```text
/profile
/admin
/admin/users
/users
```

Bir kullanıcı login olmuş olsa bile yalnızca sahip olduğu veya rolü gereği erişebileceği kaynaklara ulaşabilmelidir.

## 4.3 Authentication ve Authorization İlişkisi

Örneğin:

```text
Kullanıcı login oldu
        ↓
Authentication başarılı
        ↓
Kullanıcı kim?
        ↓
Hangi role sahip?
        ↓
Bu kaynağa erişebilir mi?
        ↓
Authorization kontrolü
```

Bu kontrollerden authorization kısmı eksik olursa Broken Access Control veya IDOR gibi güvenlik problemleri ortaya çıkabilir.

---

# 5. OWASP Analizi

OWASP Top 10, web uygulamalarında önemli güvenlik risklerini anlamak için kullanılan temel kaynaklardan biridir.

Bu laboratuvarda daha önce incelediğim OWASP Top 10:2021 kategorileri üzerinden Juice Shop'u değerlendirdim.

İncelenebilecek başlıca kategoriler:

| Kategori                                       | Uygulama açısından değerlendirme                                        |
| ---------------------------------------------- | ----------------------------------------------------------------------- |
| A01 Broken Access Control                      | Authorization ve IDOR açısından incelenebilir                           |
| A02 Cryptographic Failures                     | Hassas verilerin korunması açısından değerlendirilebilir                |
| A03 Injection                                  | Kullanıcı girdilerinin backend tarafından işlenmesi incelenebilir       |
| A04 Insecure Design                            | Güvenlik kontrollerinin tasarım seviyesinde değerlendirilmesi gerekir   |
| A05 Security Misconfiguration                  | Header, debug, servis ve yapılandırmalar incelenebilir                  |
| A06 Vulnerable and Outdated Components         | Kullanılan bağımlılıkların güncelliği incelenebilir                     |
| A07 Identification and Authentication Failures | Login ve session mekanizmaları incelenebilir                            |
| A08 Software and Data Integrity Failures       | Uygulama ve veri bütünlüğü açısından değerlendirilebilir                |
| A09 Security Logging and Monitoring Failures   | Güvenlik olaylarının loglanması açısından incelenebilir                 |
| A10 SSRF                                       | Sunucunun dış kaynaklara yaptığı istekler açısından değerlendirilebilir |

Bu kategorilerin bir uygulamada bulunması, her kategorinin kesin olarak zafiyet içerdiği anlamına gelmez. Her bulgunun ayrıca test edilmesi ve doğrulanması gerekir.

---

# 6. Potansiyel Zafiyetler

Bu bölümde gerçek sistemlerde doğrulanmış zafiyet iddiasında bulunmak yerine, laboratuvar uygulamasında incelenmesi gereken potansiyel güvenlik problemlerini belirledim.

## 6.1 Bulgu 1 — Broken Access Control / IDOR

**Bulgu:** Kullanıcı tarafından kontrol edilen object ID değerlerinin authorization kontrolü yapılmadan kullanılması durumunda başka kullanıcılara ait kaynaklara erişim mümkün olabilir.

**Kategori:** A01 — Broken Access Control

**Açıklama:** Örneğin:

```text
/profile?id=100
/profile?id=101
/profile?id=102
```

gibi bir yapıda kullanıcının ID değerini değiştirmesi tek başına güvenlik açığı değildir. Ancak uygulama, kullanıcının başka bir kullanıcıya ait kaynağa erişip erişemeyeceğini kontrol etmiyorsa Broken Access Control ortaya çıkabilir.

**Etki:** Yetkisiz kullanıcı bilgilerine veya başka kaynaklara erişim mümkün olabilir.

**Risk:** Yüksek olabilir. Gerçek risk, erişilebilen verinin türüne ve authorization mekanizmasına bağlıdır.

**Önerilen çözüm:** Her kaynak için server tarafında object-level authorization kontrolü yapılmalıdır. Kullanıcının yalnızca kendi kaynaklarına veya rolü gereği erişebileceği kaynaklara erişmesine izin verilmelidir.

---

## 6.2 Bulgu 2 — Injection

**Bulgu:** Kullanıcı tarafından gönderilen verilerin backend tarafından güvenli şekilde işlenmemesi durumunda injection problemleri oluşabilir.

**Kategori:** A03 — Injection

**Açıklama:** Özellikle arama gibi kullanıcı girdisi alan endpoint'ler dikkatle değerlendirilmelidir.

Örneğin:

```text
/rest/products/search?q=<user_input>
```

Buradaki `q` değeri kullanıcı tarafından kontrol edilmektedir.

Bu değer backend tarafında güvenli olmayan şekilde SQL sorgusuna eklenirse SQL Injection oluşabilir.

**Etki:** Uygulamanın yapısına ve veritabanı yetkilerine bağlı olarak yetkisiz veri erişimi veya veri manipülasyonu gibi sonuçlar ortaya çıkabilir.

**Risk:** Orta veya yüksek olabilir; gerçek risk backend uygulamasının nasıl çalıştığına bağlıdır.

**Önerilen çözüm:**

* Prepared Statement
* Parameterized Query
* Güvenli ORM kullanımı
* Least privilege
* Güvenli hata yönetimi

kullanılmalıdır.

---

## 6.3 Bulgu 3 — XSS

**Bulgu:** Kullanıcı tarafından sağlanan verilerin HTML veya DOM içerisinde güvenli şekilde işlenmemesi durumunda XSS oluşabilir.

**Kategori:** A03 — Injection

**Açıklama:** Arama alanları, yorumlar, profil bilgileri ve diğer kullanıcı girdileri güvenilmeyen veri kaynaklarıdır.

Bu veriler uygun şekilde encode edilmeden sayfaya aktarılırsa tarayıcı tarafından kod olarak yorumlanabilecek bir durum oluşabilir.

**Etki:**

* Sayfa içeriğinin manipüle edilmesi
* Kullanıcıya sahte içerik gösterilmesi
* Kullanıcı adına bazı işlemlerin gerçekleştirilmesi
* Hassas kullanıcı bilgilerinin hedeflenmesi

gibi etkiler oluşabilir.

**Risk:** Uygulamanın etkilenen alanına ve saldırının çalıştırılabildiği bağlama göre değişir.

**Önerilen çözüm:**

* Context-aware output encoding
* Uygun input validation
* Güvenli DOM kullanımı
* Content Security Policy (CSP)

uygulanmalıdır.

---

## 6.4 Bulgu 4 — Security Misconfiguration

**Bulgu:** Web uygulamalarında gereksiz servisler, debug ayarları, gereksiz bilgi veren header'lar veya uygun olmayan yapılandırmalar güvenlik riski oluşturabilir.

**Kategori:** A05 — Security Misconfiguration

**Açıklama:** Bir uygulamanın güvenliği yalnızca koddan oluşmaz. Sunucu ve uygulama yapılandırmaları da saldırı yüzeyinin bir parçasıdır.

Örneğin:

* Debug mode'un açık olması
* Gereksiz servislerin çalışması
* Directory listing
* Gereksiz bilgi veren header'lar
* Açık admin paneli
* Varsayılan parolalar
* Gereğinden fazla izin

gibi durumlar incelenmelidir.

**Etki:** Saldırganın uygulama hakkında daha fazla bilgi edinmesi veya mevcut bir açığın etkisini artırması mümkün olabilir.

**Risk:** Yapılandırmanın türüne ve maruz kalan bileşene bağlıdır.

**Önerilen çözüm:** Production ortamlarında güvenli yapılandırma uygulanmalı, gereksiz servisler kapatılmalı, debug mode kapatılmalı, varsayılan bilgiler değiştirilmeli ve least privilege uygulanmalıdır.

---

## 6.5 Bulgu 5 — Authentication / Session Güvenliği

**Bulgu:** Authentication ve session mekanizmalarının güvenli şekilde tasarlanmaması durumunda kullanıcı hesapları ve oturumlar risk altında kalabilir.

**Kategori:** A07 — Identification and Authentication Failures

**Açıklama:** Login mekanizması tek başına yeterli değildir. Session/token yaşam döngüsü de güvenli şekilde yönetilmelidir.

Özellikle:

* Session/token süresi
* Logout sonrası geçersizleştirme
* Brute-force koruması
* Cookie güvenlik özellikleri
* Token gizliliği
* Yeniden authentication gerektiren işlemler

değerlendirilmelidir.

**Etki:** Hesap ele geçirme veya mevcut authentication oturumunun kötüye kullanılması gibi sonuçlar ortaya çıkabilir.

**Risk:** Kullanılan authentication mekanizmasına ve uygulamadaki kontrollerin seviyesine bağlıdır.

**Önerilen çözüm:**

* Güvenli session/token yönetimi
* Uygun session expiration
* Brute-force ve rate limiting kontrolleri
* Secure / HttpOnly / SameSite cookie özellikleri
* Kritik işlemlerde yeniden authentication
* Güvenli logout mekanizması

uygulanmalıdır.

---

# 7. Sonuç

Bu çalışma sırasında web uygulaması güvenliğinin yalnızca login ekranını korumaktan ibaret olmadığını gördüm.

Bir kullanıcının login olabilmesi yalnızca **authentication** mekanizmasının çalıştığını gösterir. Bundan sonra uygulamanın:

* Kullanıcının hangi kaynaklara erişebileceğini kontrol etmesi,
* Kullanıcı girdilerini güvenli şekilde işlemesi,
* Session ve cookie bilgilerini koruması,
* API endpoint'lerini güvenli şekilde tasarlaması,
* Sunucu ve uygulama yapılandırmalarını güvenli tutması,
* Güvenlik olaylarını loglaması,
* Güncel bileşenler kullanması

gerekir.

Örneğin kullanıcı adı ve parolanın güvenli olması, uygulamada IDOR bulunmasını engellemez. HTTPS kullanılması SQL Injection veya XSS problemlerini otomatik olarak çözmez. Login kontrolünün bulunması da kullanıcının başka kullanıcıların verilerine erişmesini engellemek için tek başına yeterli değildir.

Bu nedenle bir web uygulamasının güvenliği birden fazla katmanın birlikte değerlendirilmesini gerektirir.

Benim için bu çalışmanın en önemli noktası:

> **Web güvenliği yalnızca login ekranını korumak değil, uygulamanın kullanıcıdan aldığı veriden yetkilendirmeye, session yönetiminden sunucu yapılandırmasına kadar bütün güvenlik yüzeyini değerlendirmektir.**

Bu yaklaşım, daha sonraki web güvenliği çalışmalarında bir uygulamayı sistematik olarak incelemem için temel bir değerlendirme yöntemi oluşturdu.

---

## Genel Değerlendirme

Bu laboratuvarda özellikle şu güvenlik sorularını sormayı öğrendim:

```text
Kullanıcı kim?
        ↓
Authentication doğru yapılıyor mu?
        ↓
Kullanıcı neye erişebilir?
        ↓
Authorization doğru yapılıyor mu?
        ↓
Kullanıcıdan hangi veriler geliyor?
        ↓
Input güvenli işleniyor mu?
        ↓
Session nasıl korunuyor?
        ↓
Uygulama nasıl yapılandırılmış?
        ↓
Güvenlik olayları loglanıyor mu?
```

Bu soruların tamamı birlikte değerlendirildiğinde daha kapsamlı bir web güvenlik analizi yapılabilir.

> **Not:** Bu rapordaki "potansiyel zafiyet" ifadeleri, doğrulanmış gerçek sistem bulguları olarak değerlendirilmemelidir. Gerçek bir zafiyet tespiti için ilgili davranışın yetkili ve kontrollü bir laboratuvar ortamında test edilmesi ve bulgunun doğrulanması gerekir.
