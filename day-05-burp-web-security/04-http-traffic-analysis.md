# 04 - HTTP Trafik Analizi

## Amaç

Bu çalışmada OWASP Juice Shop üzerinde gerçekleştirilen işlemler sırasında oluşan HTTP trafiğini Burp Suite üzerinden inceledim.

Amacım, bir web uygulamasında tarayıcı ile sunucu arasındaki iletişimin nasıl gerçekleştiğini anlamak ve HTTP request/response yapısını pratik olarak gözlemlemekti.

İncelediğim isteklerde özellikle;

* HTTP metodunu
* Endpoint bilgisini
* Request Header'larını
* Request Body'yi
* Cookie bilgisini
* Authorization bilgisini
* Response Header'larını
* Response Body'yi
* HTTP Status Code'u

kontrol ettim.

> **Not:** Çalışma yalnızca lokal OWASP Juice Shop laboratuvar ortamında gerçekleştirilmiştir. Authorization token ve cookie gibi hassas değerler notlarda paylaşılmamıştır.

---

## HTTP Request ve Response Mantığı

Bir web uygulamasında tarayıcı bir işlem gerçekleştirdiğinde sunucuya bir HTTP request gönderir. Sunucu bu isteği işledikten sonra HTTP response ile cevap verir.

Genel akış:

```text
Browser
   │
   │ HTTP Request
   ↓
Burp Suite Proxy
   │
   ↓
Web Server / Application
   │
   │ HTTP Response
   ↓
Browser
```

Burp Suite sayesinde bu iletişimin iki tarafını da inceleyebildim.

---

## İncelenen HTTP İstekleri

Çalışma sırasında Juice Shop üzerinde toplam **7 farklı HTTP isteğini** inceledim.

| # | Method | Endpoint                              | Request Body | Cookie | Authorization | Status |
| - | ------ | ------------------------------------- | ------------ | ------ | ------------- | ------ |
| 1 | GET    | `/api/Challenges/?name=Score%20Board` | Yok          | Var    | Var           | 304    |
| 2 | POST   | `/socket.io/`                         | `2`          | Var    | Yok           | 200    |
| 3 | GET    | `/api/Quantitys/`                     | Yok          | Var    | Var           | 304    |
| 4 | GET    | `/rest/products/2/reviews`            | Yok          | Var    | Var           | 304    |
| 5 | GET    | `/rest/basket/0`                      | Yok          | Var    | Var           | 200    |
| 6 | PUT    | `/rest/products/1/reviews`            | JSON         | Var    | Var           | 201    |
| 7 | POST   | `/rest/products/reviews`              | JSON         | Var    | Var           | 200    |

---

## 1. GET - Challenges API

```http
GET /api/Challenges/?name=Score%20Board
```

Bu istekte uygulama `GET` metodunu kullanarak Challenges API'sinden veri istemektedir.

* Request Body: Yok
* Cookie: Var
* Authorization: Bearer token mevcut
* Status Code: `304 Not Modified`
* Response Body: Görünür bir body bulunmadı.

Burada `304` kodunun bir hata olmadığını öğrendim. Kaynağın değişmediğini ve istemcinin mevcut cache bilgisini kullanabileceğini ifade ediyor.

---

## 2. POST - Socket.IO

```http
POST /socket.io/?EIO=4&transport=polling&...
```

Bu isteğin normal bir REST API isteğinden farklı olarak Socket.IO iletişimiyle ilgili olduğunu gördüm.

* Method: `POST`
* Request Body: `2`
* Cookie: Var
* Authorization: Yok
* Status Code: `200 OK`
* Response Body: `ok`

Bu istek sayesinde web uygulamalarında yalnızca klasik REST API isteklerinin bulunmadığını, gerçek zamanlı iletişim için farklı HTTP tabanlı yapıların da kullanılabildiğini gördüm.

---

## 3. GET - Quantitys API

```http
GET /api/Quantitys/
```

Bu istekte uygulama bir API endpoint'ine `GET` isteği gönderiyor.

* Request Body: Yok
* Cookie: Var
* Authorization: Var
* Status Code: `304 Not Modified`
* Response Body: Görünür değil.

Bu istek API endpoint'lerinin uygulamadaki farklı veri veya işlevlere erişim sağlayabildiğini anlamama yardımcı oldu.

---

## 4. GET - Product Reviews

```http
GET /rest/products/2/reviews
```

Bu endpoint'in URL içerisinde ürün ID'si taşıdığını gördüm.

Buradaki:

```text
/products/2/reviews
```

kısmında `2` değerinin ürün kimliği olarak kullanıldığını gözlemledim.

* Method: `GET`
* Request Body: Yok
* Cookie: Var
* Authorization: Var
* Status Code: `304 Not Modified`

Bu tarz ID içeren endpointlerin ileride Authorization ve IDOR gibi konularda neden önemli olduğunu daha iyi anladım.

---

## 5. GET - Basket

```http
GET /rest/basket/0
```

Bu istek sepet bilgilerini almak için kullanılıyor.

Response:

```json
{
  "status": "success",
  "data": null
}
```

* Method: `GET`
* Request Body: Yok
* Cookie: Var
* Authorization: Var
* Status Code: `200 OK`
* Response Content-Type: `application/json`

Burada ilk defa response body içerisinde JSON formatında veri gördüm.

`200 OK` kodunun isteğin başarıyla işlendiğini ifade ettiğini tekrar gözlemledim.

---

## 6. PUT - Product Review

```http
PUT /rest/products/1/reviews
```

Bu istekte diğer GET isteklerinden farklı olarak sunucuya veri gönderildiğini gördüm.

Request Body:

```json
{
  "message": "very nice\n",
  "author": "Anonymous"
}
```

* Method: `PUT`
* Content-Type: `application/json`
* Request Body: JSON
* Cookie: Var
* Authorization: Var
* Status Code: `201 Created`

Bu istek sayesinde Request Body'nin kullanıcı tarafından gönderilen verileri taşıyabildiğini pratik olarak gördüm.

Ayrıca `Content-Type: application/json` header'ının gönderilen verinin JSON formatında olduğunu belirttiğini öğrendim.

---

## 7. POST - Product Reviews

```http
POST /rest/products/reviews
```

Bu istekte de sunucuya JSON formatında veri gönderildi.

Request Body:

```json
{
  "id": "..."
}
```

* Method: `POST`
* Content-Type: `application/json`
* Cookie: Var
* Authorization: Var
* Status Code: `200 OK`
* Response Body: JSON formatında.

Bu istek ile aynı uygulamada farklı endpointlerin farklı HTTP metodları ve farklı request body yapıları kullanabildiğini gördüm.

---

## HTTP Status Code'lar

Çalışma sırasında özellikle üç farklı status code ile karşılaştım:

### 200 OK

İstek başarıyla işlendi.

```text
200 OK
```

### 201 Created

İşlem sonucunda yeni bir kaynağın oluşturulduğunu ifade eder.

```text
201 Created
```

### 304 Not Modified

Kaynağın değişmediğini ve cache mekanizmasının kullanılabileceğini ifade eder.

```text
304 Not Modified
```

Bu çalışmada `304` kodunun hata kodu olmadığını özellikle öğrendim.

---

## GET, POST ve PUT Arasındaki Fark

Çalışma sırasında bu HTTP metodlarının kullanımını pratik olarak karşılaştırabildim.

### GET

Genellikle sunucudan veri almak için kullanılır.

```text
GET /rest/basket/0
```

### POST

Sunucuya veri göndermek veya yeni bir işlem başlatmak için kullanılabilir.

```text
POST /rest/products/reviews
```

### PUT

Bir kaynağın oluşturulması veya güncellenmesi gibi işlemlerde kullanılabilir.

```text
PUT /rest/products/1/reviews
```

Bu farkları yalnızca teorik olarak değil, Burp Suite üzerinde gerçek HTTP trafiği üzerinden görmüş oldum.

---

## Request Header ve Response Header

Request Header'larda istemcinin gönderdiği bilgiler bulunur.

Çalışmada örnek olarak:

```text
Authorization
Accept
Content-Type
User-Agent
Cookie
Origin
Referer
```

gibi header'ları gördüm.

Response Header'larda ise sunucunun cevabı ile ilgili bilgiler bulunur.

Örneğin:

```text
Content-Type
Content-Length
ETag
X-Content-Type-Options
X-Frame-Options
```

gibi header'ları gözlemledim.

---

## Authorization ve Cookie

Bazı isteklerde:

```http
Authorization: Bearer ...
```

şeklinde bir Authorization header'ı bulunduğunu gördüm.

Bu alanın kimlik doğrulama amacıyla kullanılan token bilgisini taşıyabildiğini öğrendim.

Ayrıca isteklerde Cookie header'ının da gönderildiğini gözlemledim.

JWT ve token yapısını daha detaylı olarak sonraki bölümlerde inceleyeceğim.

---

## Bu Çalışmada Öğrendiklerim

Bu çalışmadan önce HTTP request ve response yapısını teorik olarak biliyordum. Burp Suite ile trafik inceleyince bu yapıların gerçek bir web uygulamasında nasıl göründüğünü daha net anladım.

Özellikle;

* GET ile veri alma işlemini,
* POST ve PUT ile veri gönderimini,
* Request Header ve Response Header farkını,
* Request Body'nin kullanımını,
* JSON formatındaki verileri,
* Cookie ve Authorization header'larını,
* HTTP status code'larını,
* API ve REST endpointlerini,
* URL içerisindeki kaynak ID'lerini

pratik olarak incelemiş oldum.

Bu çalışma ayrıca ileride yapacağım **authentication, authorization, IDOR, SQL Injection ve XSS** analizlerinde HTTP trafiğini okuyabilmenin temel bir beceri olduğunu gösterdi.
