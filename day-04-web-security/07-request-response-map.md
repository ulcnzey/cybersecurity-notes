# Request ve Response Haritası

Bu çalışmada Juice Shop üzerinde Burp Suite kullanarak farklı HTTP isteklerini incelemeye başladım. Amacım, bir web uygulamasında kullanıcı ile sunucu arasındaki iletişimin nasıl gerçekleştiğini ve bir isteğin hangi bilgileri taşıdığını daha iyi anlamaktı.

Burada özellikle bir isteğin sadece `GET` veya `POST` olmasından ibaret olmadığını; URL, header, cookie, body ve response bilgilerinin birlikte değerlendirilmesi gerektiğini gördüm.

## 1. Ana Sayfa İsteği

Uygulamayı ilk açtığımda oluşan isteği Burp Suite HTTP History üzerinden inceledim.

### Request

```http
GET / HTTP/1.1
Host: localhost:3000
```

### Method

`GET`

### Amaç

Tarayıcı Juice Shop'un ana sayfasını istedi.

### Response

```http
HTTP/1.1 200 OK
```

Sunucu isteği başarıyla işledi ve ana sayfanın içeriğini döndürdü.

Bu istek sayesinde bir web sayfasını açmanın arka planda bir HTTP request/response iletişimi oluşturduğunu gözlemledim.

---

## 2. Arama İsteği

Juice Shop üzerinde arama yaptığımda yeni bir HTTP isteğinin oluştuğunu gözlemledim.

Arama sırasında kullanıcı tarafından girilen değer URL içerisinde query parameter olarak gönderilebiliyor.

Örneğin:

```text
/rest/products/search?q=apple
```

Buradaki `q` parametresi arama değerini temsil ediyor.

### Method

```http
GET
```

### Kullanıcı tarafından gönderilen bilgi

Arama kutusuna yazılan değer.

### Öğrendiğim Nokta

Kullanıcı tarafından girilen bir bilginin URL içerisinde gönderilebildiğini ve uygulamanın bu değeri backend tarafındaki bir API'ye iletebildiğini gördüm.

---

## 3. Login İsteği

Juice Shop üzerinde giriş yaptığımda Burp Suite üzerinden login isteğini inceledim.

İstek:

```http
POST /rest/user/login HTTP/1.1
```

Login bilgilerinin request body içerisinde gönderildiğini gördüm.

Örneğin genel yapı:

```json
{
  "email": "...",
  "password": "..."
}
```

Sunucu başarılı giriş sonrasında kullanıcıya authentication bilgisi içeren bir response döndürdü.

Bu response içerisinde JWT (JSON Web Token) bulunduğunu gördüm.

JWT'nin daha sonraki API isteklerinde kullanıcının kimliğinin doğrulanması için kullanıldığını öğrendim.

> Not: Gerçek token değerlerini GitHub'a veya başka bir yere paylaşmamak gerekir.

---

## 4. Ürün Arama API İsteği

Bu çalışmada incelediğim önemli isteklerden biri ürün arama API'sine yapılan istekti.

### Request

```http
GET /rest/products/search?q= HTTP/1.1
Host: localhost:3000
Authorization: Bearer <JWT>
Accept: application/json, text/plain, */*
User-Agent: Mozilla/5.0 ...
Referer: http://localhost:3000/
```

### URL

```text
http://localhost:3000/rest/products/search?q=
```

### Method

```text
GET
```

### Request Body

Bu istekte body bulunmuyordu.

Arama değeri URL içerisindeki query parameter üzerinden gönderiliyordu:

```text
?q=
```

Örneğin bir arama yapılmış olsaydı:

```text
?q=apple
```

şeklinde görülebilirdi.

### Authorization

Request içerisinde:

```http
Authorization: Bearer <JWT>
```

header'ını gördüm.

Buradaki JWT, kullanıcının oturum/kimlik bilgisinin API isteğiyle birlikte taşınmasında kullanılıyor.

### Cookie

Request içerisinde uygulamaya ait cookie değerleri de bulunuyordu. Bunların arasında token ile ilişkili bir cookie de vardı.

Bu durum, web uygulamalarında oturum bilgilerinin request'lerle nasıl taşınabildiğini görmem açısından önemliydi.

### Response

Sunucunun cevabı:

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

şeklindeydi.

Response body içerisinde ürünlerin JSON formatında döndüğünü gördüm.

Örneğin:

```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "name": "Apple Juice (1000ml)",
      "price": 1.99
    }
  ]
}
```

Burada sunucunun ürün bilgilerini JSON formatında browser'a gönderdiğini gözlemledim.

---

## Request ve Response Arasındaki Genel Akış

Bu çalışmadan sonra bir web uygulamasındaki iletişimi şu şekilde düşünmeye başladım:

```text
Browser
   ↓
HTTP Request
   ↓
Web/Application Server
   ↓
Database
   ↓
HTTP Response
   ↓
Browser
```

Örneğin ürün arama işleminde:

```text
Kullanıcı arama yapar
        ↓
Browser GET request gönderir
        ↓
/rest/products/search?q=...
        ↓
Application API isteği işler
        ↓
Ürün verileri alınır
        ↓
JSON Response oluşturulur
        ↓
Browser'a gönderilir
```

## Bu Çalışmada Öğrendiklerim

* HTTP request ve response arasındaki ilişkiyi daha iyi anladım.
* GET isteğinde bilgilerin URL/query parameter üzerinden gönderilebildiğini gördüm.
* POST isteğinde verilerin request body içerisinde gönderilebildiğini gördüm.
* HTTP header'larının istemci ve sunucu arasında ek bilgiler taşıdığını öğrendim.
* Cookie'lerin web uygulamalarında oturum ve kullanıcı tercihleri gibi bilgilerin taşınmasında kullanılabildiğini gördüm.
* JWT'nin authentication sürecinde nasıl kullanılabildiğini gözlemledim.
* API endpoint'lerinin web uygulamasının frontend ve backend arasındaki iletişiminde önemli bir rol oynadığını gördüm.
* Burp Suite HTTP History'nin bu trafiği incelemek için ne kadar kullanışlı olduğunu deneyimledim.

## Güvenlik Açısından Çıkardığım Sonuç

Bir HTTP isteğini incelerken sadece URL'ye bakmanın yeterli olmadığını fark ettim.

Request içerisinde;

* Kullanıcı tarafından gönderilen veriler,
* Authentication bilgileri,
* Cookie'ler,
* Header'lar,
* Query parameter'lar

gibi birçok farklı bilgi bulunabiliyor.

Bu nedenle web güvenliği açısından request ve response trafiğini okuyabilmek, daha sonraki aşamalarda yapılacak güvenlik analizlerinin temelini oluşturuyor.

Bu çalışmada herhangi bir saldırı veya istismar gerçekleştirmeden, yalnızca kendi lokal Juice Shop laboratuvarım üzerinde HTTP trafiğini gözlemledim.
