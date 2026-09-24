# 🔍 HTTP Request ve Response Analizi

Web güvenliği çalışmalarında HTTP request ve response yapılarını anlayabilmek temel konulardan biridir.

Bir kullanıcı web uygulamasında bir işlem yaptığında tarayıcı ile sunucu arasında request ve response alışverişi gerçekleşir.

```text
Browser
   │
   │ HTTP Request
   ↓
Web Server / Application
   │
   │ HTTP Response
   ↓
Browser
```

Bu nedenle bir web uygulamasını analiz ederken öncelikle:

* Kullanıcı sunucuya ne gönderiyor?
* Sunucu hangi bilgileri döndürüyor?
* Kullanıcının kimliği nasıl takip ediliyor?
* Hangi parametreler gönderiliyor?
* Hangi endpoint'e erişiliyor?

gibi soruların cevaplarını anlamak gerekir.

---

# 1. HTTP Request Nedir?

HTTP request, istemcinin sunucuya gönderdiği istektir.

Örneğin:

```http
GET /profile HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: session=abc123
```

Bu request'i parçalara ayırabiliriz.

---

# 2. Request Line

İlk satır:

```http
GET /profile HTTP/1.1
```

üç temel bölümden oluşur:

```text
GET        → HTTP Method
/profile   → Path
HTTP/1.1   → HTTP Version
```

### GET

İstemcinin bir kaynağı almak istediğini belirtir.

### `/profile`

Erişilmek istenen endpoint/path bilgisidir.

### `HTTP/1.1`

Kullanılan HTTP sürümünü belirtir.

Dolayısıyla:

```text
GET /profile HTTP/1.1
```

ifadesi:

> HTTP/1.1 kullanarak `/profile` kaynağını GET yöntemiyle istiyorum.

şeklinde yorumlanabilir.

---

# 3. Host Header

```http
Host: example.com
```

İsteğin hangi host'a gönderildiğini belirtir.

Örneğin farklı servislerde:

```text
example.com
api.example.com
admin.example.com
```

gibi farklı host'lar bulunabilir.

Host bilgisi, request'in hangi web hizmetine yönlendirildiğini anlamak açısından önemlidir.

---

# 4. User-Agent

```http
User-Agent: Mozilla/5.0
```

Request'i gönderen istemci hakkında bilgi verir.

Örneğin tarayıcı ve işletim sistemi hakkında bazı bilgiler içerebilir.

Gerçek bir örnekte:

```http
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
```

gibi daha ayrıntılı bir değer görülebilir.

Ancak User-Agent güvenilir bir kimlik doğrulama yöntemi değildir. İstemci tarafından gönderildiği için değiştirilebilir.

---

# 5. Accept Header

```http
Accept: text/html
```

İstemcinin kabul edebileceği response içerik türlerini belirtir.

Örneğin:

```http
Accept: text/html
```

HTML içerik beklediğini gösterebilir.

Bir API isteğinde ise:

```http
Accept: application/json
```

gibi bir değer kullanılabilir.

---

# 6. Cookie Header

```http
Cookie: session=abc123
```

Tarayıcının sunucuya cookie bilgisi gönderdiğini gösterir.

Cookie içerisinde session bilgisi bulunabilir.

Örneğin:

```text
session=abc123
```

değeri, uygulamanın kullanıcının oturumunu tanımasına yardımcı olabilir.

Basitleştirilmiş olarak:

```text
Kullanıcı giriş yapar
        ↓
Sunucu oturum oluşturur
        ↓
Session bilgisi oluşturulur
        ↓
Tarayıcı cookie'yi saklar
        ↓
Sonraki request'lerde cookie gönderilir
```

Session ve cookie konusu ilerleyen bölümde ayrıntılı olarak incelenecektir.

---

# 7. Query Parameter

Bazı veriler URL içerisinde query parameter olarak gönderilebilir.

Örneğin:

```http
GET /search?q=phone HTTP/1.1
Host: example.com
```

Burada:

```text
/search
```

path,

```text
q=phone
```

ise query parameter'dır.

Birden fazla parametre de bulunabilir:

```text
/search?q=phone&page=2
```

Burada:

```text
q=phone
page=2
```

iki farklı query parameter'dır.

Web güvenliği açısından kullanıcı tarafından kontrol edilebilen bu tür parametrelerin nasıl işlendiği önemlidir.

---

# 8. Request Body

Bazı HTTP methodlarında veri request body içerisinde gönderilebilir.

Örneğin:

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "username": "zeynep",
  "password": "123456"
}
```

Burada:

```text
POST /login
```

login endpoint'ine istek gönderildiğini gösterir.

Body içerisinde ise:

```json
{
  "username": "zeynep",
  "password": "123456"
}
```

verisi bulunmaktadır.

Bu nedenle request analiz ederken sadece URL'ye değil, **request body'ye de bakmak gerekir.**

---

# 9. Request Analizi İçin Temel Sorular

Bir HTTP request gördüğümde şu soruları sorabilirim:

### 1. Nereye gidiyor?

```text
Host + Path
```

### 2. Hangi işlem isteniyor?

```text
HTTP Method
```

### 3. Hangi parametreler gönderiliyor?

```text
Query Parameters
```

veya:

```text
Request Body
```

### 4. Kullanıcı oturumu nasıl belirleniyor?

```text
Cookie
Authorization Header
```

gibi bilgiler kontrol edilebilir.

### 5. İstemci hakkında hangi bilgiler gönderiliyor?

```text
User-Agent
```

gibi header'lar incelenebilir.

### 6. İstemci nasıl bir response bekliyor?

```text
Accept
```

header'ı incelenebilir.

---

# 10. HTTP Response Nedir?

HTTP response, sunucunun request'e verdiği cevaptır.

Örneğin:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5230
```

Burada:

```text
HTTP/1.1
```

HTTP sürümünü,

```text
200 OK
```

işlemin başarılı olduğunu,

```text
Content-Type: text/html
```

response içeriğinin HTML olduğunu,

```text
Content-Length: 5230
```

ise response body'sinin uzunluğunu belirtir.

---

# 11. Request ve Response Birlikte

Bir kullanıcı profil sayfasını açtığında basitleştirilmiş iletişim:

### Request

```http
GET /profile HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: session=abc123
```

### Response

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5230
```

şeklinde olabilir.

Bu iletişim sonucunda tarayıcı response body içerisinde gelen HTML'i kullanarak profil sayfasını oluşturabilir.

---

# 12. GET ve POST Arasındaki Temel Fark

GET örneği:

```http
GET /search?q=phone HTTP/1.1
```

Burada veri URL içerisindeki query parameter ile gönderiliyor.

POST örneği:

```http
POST /login HTTP/1.1
Content-Type: application/json

{
  "username": "zeynep",
  "password": "123456"
}
```

Burada veri request body içerisinde gönderiliyor.

Ancak:

> POST kullanmak tek başına veriyi güvenli hale getirmez.

İletişimin ağ üzerinde korunması için HTTPS/TLS gibi mekanizmalar gerekir.

Ayrıca sunucunun aldığı veriyi güvenli şekilde işlemesi gerekir.

---

# 🔐 Siber Güvenlik Açısından Önemi

HTTP request ve response analizi, web güvenliğinin temelini oluşturur.

Örneğin ilerleyen bölümlerde:

* Cookie ve Session
* Authentication
* Authorization
* SQL Injection
* XSS
* IDOR
* CSRF
* File Upload
* Burp Suite

konularını incelerken request ve response yapılarını sürekli kullanacağım.

Örneğin:

```http
GET /profile?id=100 HTTP/1.1
Host: lab.local
Cookie: session=abc123
```

gibi bir request gördüğümde artık:

* Methodun GET olduğunu
* `/profile` endpoint'ine gidildiğini
* `id=100` parametresinin gönderildiğini
* Bir session cookie'sinin bulunduğunu

anlayabilirim.

Bu bilgiler daha sonra web uygulamasının güvenlik kontrollerini anlamak için kullanılabilir.

---

# 🧠 Kısa Özet

HTTP request:

```text
Request
│
├── Method
├── Path
├── Headers
├── Query Parameters
└── Body
```

HTTP response:

```text
Response
│
├── Status Code
├── Headers
└── Body
```

Bir request analiz ederken temel olarak:

```text
Nereye gidiyor?
Ne yapmak istiyor?
Hangi verileri gönderiyor?
Kullanıcı oturumu nasıl belirleniyor?
```

sorularına cevap ararım.

Web güvenliği açısından önemli olan nokta, HTTP trafiğini sadece okumak değil, **uygulamanın kullanıcıdan aldığı ve kullanıcıya döndürdüğü bilgilerin güvenlik açısından ne anlama geldiğini anlayabilmektir.**
