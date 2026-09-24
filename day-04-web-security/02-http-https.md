# 🌐 HTTP ve HTTPS

Web uygulamalarında tarayıcı ile sunucu arasındaki iletişimin temelinde **HTTP (HyperText Transfer Protocol)** bulunur.

Bir web sitesine girdiğimde aslında tarayıcı ile sunucu arasında sürekli olarak **request (istek)** ve **response (cevap)** alışverişi gerçekleşir.

Temel yapı:

```text
Client → HTTP Request → Server
Client ← HTTP Response ← Server
```

---

## 1. HTTP Nedir?

HTTP, istemci ve sunucu arasındaki iletişimin nasıl gerçekleşeceğini belirleyen bir protokoldür.

Örneğin tarayıcıdan:

```text
https://example.com/profile
```

adresine girdiğimde tarayıcı sunucuya bir HTTP isteği gönderir.

Sunucu da bu isteği işleyerek bir HTTP response döndürür.

HTTP'nin temel amacı:

* İstemcinin ne istediğini belirtmek
* İsteğin nasıl gönderileceğini belirlemek
* Sunucunun cevabını standartlaştırmak
* İstemci ve sunucu arasındaki iletişimi düzenlemek

---

# 2. HTTP Request

**Request**, istemcinin sunucuya gönderdiği istektir.

Örneğin kullanıcı profil sayfasını açmak istediğinde:

```http
GET /profile HTTP/1.1
Host: example.com
```

gibi bir istek gönderilebilir.

Bir HTTP request içerisinde temel olarak:

* Method
* URL / Path
* Headers
* Body

bulunabilir.

---

# 3. HTTP Response

**Response**, sunucunun istemciye verdiği cevaptır.

Örneğin:

```http
HTTP/1.1 200 OK
Content-Type: text/html
```

Buradaki `200 OK`, isteğin başarılı olduğunu belirtir.

Response içerisinde de:

* Status Code
* Headers
* Body

bulunabilir.

---

# 4. HTTP Methodları

HTTP methodları, sunucuya hangi işlemin yapılmak istendiğini belirtir.

## GET

Bir kaynağı almak için kullanılır.

Örneğin:

```http
GET /profile
```

Kullanıcının profil bilgilerinin istenmesi gibi düşünülebilir.

---

## POST

Sunucuya yeni veri göndermek veya yeni bir kaynak oluşturmak için kullanılabilir.

Örneğin:

```http
POST /login
```

Login bilgilerinin sunucuya gönderilmesi.

Başka bir örnek:

```http
POST /users
```

Yeni kullanıcı oluşturulması.

---

## PUT

Bir kaynağın tamamını güncellemek için kullanılabilir.

Örneğin:

```http
PUT /users/15
```

---

## PATCH

Bir kaynağın belirli bir bölümünü güncellemek için kullanılır.

Örneğin kullanıcının sadece e-posta adresini değiştirmek:

```http
PATCH /users/15
```

---

## DELETE

Bir kaynağı silmek için kullanılır.

```http
DELETE /users/15
```

---

## OPTIONS

Sunucunun desteklediği HTTP yöntemleri veya iletişim seçenekleri hakkında bilgi almak için kullanılır.

---

## HEAD

GET'e benzer şekilde kullanılır ancak response body gönderilmez.

Genellikle kaynağın varlığı veya header bilgileri hakkında kontrol yapmak için kullanılabilir.

---

# 5. URL Nedir?

URL, bir web kaynağının adresini belirtir.

Örneğin:

```text
https://example.com/profile?id=15
```

Bu URL'yi parçalara ayırabiliriz:

```text
https://
example.com
/profile
?id=15
```

### Scheme

```text
https://
```

İletişimde kullanılan protokolü belirtir.

### Host / Domain

```text
example.com
```

İstek gönderilen sunucunun alan adıdır.

### Path

```text
/profile
```

Sunucuda hangi kaynağa erişilmek istendiğini belirtir.

### Query Parameter

```text
?id=15
```

Sunucuya gönderilen ek parametredir.

---

# 6. HTTP Headers

Headers, request veya response hakkında ek bilgiler taşır.

Örneğin bir request içerisinde:

```http
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: session=abc123
```

gibi header'lar bulunabilir.

Bazı önemli header'lar:

### Host

İsteğin hangi host'a gönderildiğini belirtir.

```http
Host: example.com
```

### User-Agent

İsteği gönderen istemci hakkında bilgi verir.

```http
User-Agent: Mozilla/5.0
```

### Accept

İstemcinin hangi tür içerikleri kabul edebileceğini belirtir.

```http
Accept: text/html
```

### Cookie

İstemci tarafından sunucuya cookie bilgisi gönderilmesini sağlar.

```http
Cookie: session=abc123
```

Cookie ve session konusu ilerleyen bölümlerde ayrıca incelenecektir.

---

# 7. HTTP Request Body

Bazı HTTP isteklerinde sunucuya veri gönderilir.

Bu veriler request body içerisinde bulunabilir.

Örneğin:

```http
POST /login HTTP/1.1
Content-Type: application/json

{
  "username": "zeynep",
  "password": "123456"
}
```

Burada kullanıcı adı ve parola request body içerisinde gönderilmektedir.

Bu nedenle özellikle POST, PUT ve PATCH gibi methodlarda request body'nin incelenmesi web güvenliği açısından önemlidir.

---

# 8. HTTP Status Code

Sunucunun request sonucunu belirtmek için status code kullanılır.

Status code'lar genel olarak beş gruba ayrılır:

| Kod Grubu | Anlamı                |
| --------- | --------------------- |
| 1xx       | Bilgilendirme         |
| 2xx       | Başarılı              |
| 3xx       | Yönlendirme           |
| 4xx       | İstemci kaynaklı hata |
| 5xx       | Sunucu kaynaklı hata  |

Sık karşılaşılan bazı kodlar:

### 200 OK

İstek başarıyla tamamlanmıştır.

### 201 Created

Yeni bir kaynak başarıyla oluşturulmuştur.

### 301 Moved Permanently

Kaynağın kalıcı olarak başka bir adrese taşındığını belirtir.

### 302 Found

Geçici yönlendirme için kullanılır.

### 400 Bad Request

Gönderilen request'in geçersiz veya hatalı olduğunu belirtir.

### 401 Unauthorized

Kimlik doğrulama gerektiğini veya mevcut kimlik doğrulamanın geçerli olmadığını belirtir.

### 403 Forbidden

Sunucu isteği anlıyor ancak kullanıcının bu kaynağa erişmesine izin vermiyor.

Bu nedenle:

```text
401 → Kimlik doğrulama problemi
403 → Yetki problemi
```

ayrımını bilmek önemlidir.

### 404 Not Found

İstenen kaynak bulunamamıştır.

### 405 Method Not Allowed

Kullanılan HTTP methodu ilgili kaynak için desteklenmiyordur.

### 429 Too Many Requests

Kısa sürede çok fazla istek gönderildiğini belirtir.

### 500 Internal Server Error

Sunucu tarafında beklenmeyen bir hata oluşmuştur.

### 502 Bad Gateway

Bir gateway veya proxy'nin arka plandaki sunucudan geçerli bir cevap alamadığını belirtir.

---

# 9. HTTP ve HTTPS Arasındaki Fark

HTTP iletişimi tek başına şifreli değildir.

HTTPS ise:

```text
HTTP + TLS
```

mantığıyla çalışır.

TLS, istemci ile sunucu arasındaki iletişimin güvenli hale getirilmesini sağlar.

HTTPS sayesinde temel olarak:

* Gizlilik
* Veri bütünlüğü
* Sunucunun kimliğinin doğrulanması

sağlanmaya çalışılır.
<img width="1024" height="961" alt="image" src="https://github.com/user-attachments/assets/ab70a484-973e-4322-a7ae-cf46ce4b3f44" />

---

# 10. TLS Nedir?

**TLS (Transport Layer Security)**, ağ üzerinden yapılan iletişimi güvenli hale getiren bir güvenlik protokolüdür.

Örneğin HTTP ile:

```text
password=123456
```

gibi bir veri gönderildiğinde iletişim şifrelenmemiş olabilir.

HTTPS/TLS kullanıldığında ise ağ üzerinden taşınan veri şifrelenir.

Basitleştirilmiş şekilde:

```text
HTTP:

Browser
   ↓
Açık HTTP verisi
   ↓
Server
```

HTTPS:

```text
Browser
   ↓
TLS ile korunan iletişim
   ↓
Server
```

Bu nedenle özellikle login, ödeme ve kişisel veri gibi hassas işlemlerde HTTPS kritik öneme sahiptir.

---

# 11. SSL Nedir?

SSL, TLS'den önce kullanılan eski bir güvenlik protokolüdür.

Günümüzde modern sistemlerde kullanılan teknoloji **TLS**'dir.

Bu yüzden günlük kullanımda insanlar bazen "SSL sertifikası" dese de teknik olarak modern HTTPS bağlantılarında TLS kullanılır.

---

# 12. HTTPS Her Web Açığını Çözer mi?

Hayır.

Bu önemli bir noktadır.

HTTPS, iletişim kanalını korur fakat web uygulamasının kendi içerisindeki güvenlik açıklarını otomatik olarak ortadan kaldırmaz.

Örneğin uygulamada:

* SQL Injection
* XSS
* IDOR
* Broken Access Control
* CSRF
* Security Misconfiguration

gibi açıklar varsa HTTPS kullanılması bu açıkları tek başına çözmez.

Örneğin:

```text
Browser
   ↓ HTTPS
Web Application
   ↓
SQL Injection
```

Burada browser ile sunucu arasındaki iletişim şifreli olsa bile uygulamanın SQL sorgularını güvensiz oluşturması nedeniyle SQL Injection problemi devam edebilir.

Bu nedenle:

> HTTPS güvenli web uygulamasının önemli bir parçasıdır ancak tek başına uygulama güvenliği sağlamaz.

---

# 13. HTTP Request → Response Örneği

Bir kullanıcı profil sayfasını açtığında basitleştirilmiş olarak:

### Request

```http
GET /profile HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: session=abc123
```

Sunucu bu isteği işler.

### Response

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5230
```

Ardından response body içerisinde sayfanın içeriği gönderilebilir.

Bu işlem kullanıcı açısından sadece:

```text
Profile sayfasını açtım.
```

gibi görünür.

Fakat arka planda HTTP request ve response alışverişi gerçekleşmektedir.

---

# 🔐 Siber Güvenlik Açısından Neden Önemli?

Web güvenliği çalışırken yalnızca "açık var mı?" diye bakmak yeterli değildir.

Öncelikle web uygulamasının nasıl iletişim kurduğunu anlamak gerekir.

HTTP request ve response bilgilerini anlayabilmek;

* Burp Suite kullanırken
* Cookie ve session incelerken
* Authentication kontrol ederken
* Authorization kontrol ederken
* SQL Injection araştırırken
* XSS analiz ederken
* IDOR araştırırken
* HTTP status code'larını yorumlarken

temel oluşturur.

Özellikle Burp Suite kullanırken sürekli olarak şu yapıyla karşılaşacağım:

```text
Request
   ↓
Web Application
   ↓
Response
```

Bu yüzden HTTP bilgisi web güvenliği çalışmalarının temel taşlarından biridir.

---

# 🧠 Kısa Özet

Bu bölümde şunları öğrendim:

* HTTP'nin istemci ve sunucu arasındaki iletişim protokolü olduğunu
* Request'in istemciden sunucuya gönderildiğini
* Response'un sunucudan istemciye döndüğünü
* GET, POST, PUT, PATCH, DELETE, OPTIONS ve HEAD methodlarını
* URL'nin temel parçalarını
* HTTP headers ve body yapısını
* Status code'ların ne anlama geldiğini
* 401 ile 403 arasındaki farkı
* HTTPS'in HTTP + TLS mantığıyla çalıştığını
* TLS'nin iletişimi koruduğunu
* SSL'nin eski bir teknoloji olduğunu
* HTTPS'in SQL Injection, XSS veya IDOR gibi uygulama açıklarını tek başına çözmediğini

En önemli mantık:

```text
HTTP = Web iletişiminin kuralları

Request = Ben sunucudan ne istiyorum?
Response = Sunucu bana ne cevap verdi?

HTTPS = HTTP + TLS ile korunan iletişim
```
