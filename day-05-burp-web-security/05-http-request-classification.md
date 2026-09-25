# 05 - HTTP İsteklerini Sınıflandırma

## Amaç

Bu bölümde bir önceki çalışmada Burp Suite ile yakaladığım HTTP isteklerini kullanım amaçlarına göre sınıflandırdım.

İstekleri;

* Authentication
* Authorization
* Data Retrieval
* Data Modification
* Search
* API

kategorilerine ayırarak hangi isteğin ne amaçla kullanıldığını anlamaya çalıştım.

> **Not:** Çalışma yalnızca lokal OWASP Juice Shop laboratuvar ortamında gerçekleştirilmiştir. Authorization token ve cookie gibi hassas değerler notlarda paylaşılmamıştır.

---

## 1. Authentication

Authentication, kullanıcının **kim olduğunu doğrulama** işlemidir.

Login, logout ve kullanıcı oturumunun başlatılması gibi işlemler bu kategoriye girer.

İncelediğim 7 farklı HTTP isteği içerisinde doğrudan bir **login veya logout isteği yakalamadım**.

Bazı isteklerde:

```http
Authorization: Bearer ...
```

header'ının bulunduğunu gördüm.

Bu header'ın kimlik doğrulama amacıyla kullanılan token bilgisini taşıyabildiğini öğrendim. Ancak bu isteklerin kendisi login isteği değildir.

**Sonuç:** Bu çalışma kapsamında doğrudan Authentication örneği yakalanmadı.

---

## 2. Authorization

Authorization, kimliği doğrulanmış bir kullanıcının **hangi kaynaklara ve işlemlere erişebileceğini** belirleyen mekanizmadır.

Örnek olarak:

```http
GET /rest/basket/0
Authorization: Bearer ...
```

isteğini inceleyebilirim.

Bu istekte kullanıcıya ait bir kaynak isteniyor ve Authorization header içerisinde Bearer token gönderiliyor.

Burada önemli olarak şunu öğrendim:

> Bir istekte Authorization token bulunması, tek başına bir yetkilendirme zafiyeti olduğu anlamına gelmez.

Yetkilendirme kontrolünün doğru yapılıp yapılmadığını anlamak için daha sonraki bölümlerde farklı kullanıcılarla ve farklı kaynaklarla kontrollü testler yapılması gerekir.

---

## 3. Data Retrieval

Data Retrieval, uygulamanın sunucudan **veri alması** anlamına gelir.

Bu kategoride en açık örneklerden biri:

```http
GET /rest/products/2/reviews
```

isteğidir.

Bu endpoint üzerinden ID'si `2` olan ürünün yorumları istenmektedir.

Bir diğer örnek:

```http
GET /rest/basket/0
```

isteğidir.

Bu istek ile sepet bilgisi alınmaktadır.

Bu örneklerde `GET` metodunun veri almak için kullanıldığını pratik olarak gözlemledim.

---

## 4. Data Modification

Data Modification, sunucudaki verinin **oluşturulması veya değiştirilmesi** ile ilgili işlemlerdir.

Örnek:

```http
PUT /rest/products/1/reviews
```

Request Body:

```json
{
  "message": "very nice",
  "author": "Anonymous"
}
```

Bu istekte istemci tarafından sunucuya JSON formatında veri gönderildiğini gördüm.

Sunucu:

```http
201 Created
```

cevabını döndürdü.

Bir başka örnek:

```http
POST /rest/products/reviews
```

isteğidir.

Bu istekte de Request Body içerisinde JSON formatında veri gönderildi.

Bu örnekler sayesinde HTTP request içerisindeki **Request Body'nin kullanıcı veya uygulama tarafından sunucuya veri aktarmak için kullanılabildiğini** gördüm.

---

## 5. Search

Search kategorisi, kullanıcının uygulama içerisinde bir veri veya içerik araması yaptığı HTTP isteklerini ifade eder.

İncelediğim istekler arasında doğrudan bir **ürün arama isteğini kesin olarak tespit edemedim**.

Örneğin:

```http
GET /api/Challenges/?name=Score%20Board
```

isteğinde:

```text
name=Score%20Board
```

şeklinde bir query parametresi bulunmaktadır.

Ancak bunun doğrudan kullanıcı tarafından gerçekleştirilen bir arama işlemi olduğunu kesin olarak söylemek için yeterli bilgi bulunmadığından bu isteği Search kategorisine kesin örnek olarak değerlendirmedim.

---

## 6. API

API kategorisinde uygulamanın API endpointleri üzerinden gerçekleştirdiği istekleri değerlendirdim.

Örneğin:

```http
GET /api/Quantitys/
```

ve:

```http
GET /api/Challenges/?name=Score%20Board
```

istekleri API endpointlerine yapılan HTTP istekleridir.

Ayrıca:

```http
GET /rest/products/2/reviews
```

ve:

```http
GET /rest/basket/0
```

gibi `/rest/` altında bulunan endpointlerin de uygulamanın REST tabanlı API iletişiminde kullanıldığını gözlemledim.

---

## Genel Sınıflandırma

| Kategori              | Örnek                          | Açıklama                                       |
| --------------------- | ------------------------------ | ---------------------------------------------- |
| **Authentication**    | Doğrudan örnek yakalanmadı     | Login/logout isteği görülmedi                  |
| **Authorization**     | `GET /rest/basket/0`           | Authorization token ile korunan kaynağa erişim |
| **Data Retrieval**    | `GET /rest/products/2/reviews` | Ürün yorumlarını getiriyor                     |
| **Data Modification** | `PUT /rest/products/1/reviews` | Yorum verisi gönderiyor                        |
| **Search**            | Doğrudan örnek yakalanmadı     | Kesin bir arama isteği tespit edilmedi         |
| **API**               | `GET /api/Quantitys/`          | API endpointine yapılan istek                  |

---

## Bir İstek Birden Fazla Kategoriye Girebilir

Bu kategorilerin birbirini tamamen dışlamadığını fark ettim.

Örneğin:

```http
GET /rest/basket/0
Authorization: Bearer ...
```

isteği aynı anda:

* **Data Retrieval** → Sepet verisini getiriyor.
* **Authorization** → Authorization bilgisiyle erişim sağlanıyor.
* **API** → REST API endpointine istek gönderiliyor.

şeklinde değerlendirilebilir.

Bu nedenle HTTP isteklerini sınıflandırırken yalnızca HTTP metoduna bakmak yerine isteğin **amacını, endpointini, gönderdiği veriyi ve authentication/authorization bilgilerini birlikte değerlendirmek gerektiğini öğrendim.**

---

## Bu Bölümde Öğrendiklerim

Bu çalışmada farklı HTTP isteklerinin uygulama içerisindeki amaçlarının birbirinden farklı olduğunu gördüm.

Özellikle:

* Authentication ile Authorization arasındaki farkı,
* Veri alma ve veri değiştirme isteklerini,
* API endpointlerinin nasıl göründüğünü,
* Request Body'nin veri gönderimindeki rolünü,
* Authorization header'ının isteklerde nasıl kullanılabildiğini,
* Bir HTTP isteğinin birden fazla kategoriyle ilişkilendirilebileceğini

pratik olarak gözlemledim.

Ayrıca elimde olmayan bir isteği varsayarak sınıflandırmak yerine, **yalnızca Burp Suite üzerinde gerçekten gözlemlediğim trafiği kullanmanın** daha doğru bir analiz yöntemi olduğunu öğrendim.
