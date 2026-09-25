# 06 - API Endpoint'lerini Keşfet

## Amaç

Bu bölümde OWASP Juice Shop'un Burp Suite üzerinden yakalanan HTTP trafiğini inceleyerek kullanılan API endpoint'lerini belirledim.

Endpoint keşfi sayesinde uygulamanın hangi backend kaynaklarıyla iletişim kurduğunu, hangi HTTP methodlarının kullanıldığını, hangi parametrelerin gönderildiğini ve sunucunun nasıl cevap verdiğini incelemeyi amaçladım.

---

## API Endpoint Envanteri

### 1. `/api/Challenges/?name=Score%20Board`

* **HTTP Method:** GET
* **Amaç:** Challenge / Score Board ile ilişkili bir veri isteği olduğu gözlemlendi.
* **Authentication:** İstekte Bearer token ve Cookie bilgisi gözlendi.
* **Gönderilen Parametreler:** `name=Score Board`
* **Response:** `304 Not Modified`

---

### 2. `/socket.io/`

* **HTTP Method:** POST
* **Amaç:** Socket.IO üzerinden uygulama ile iletişim sağlamak.
* **Authentication:** Bu istekte Bearer token gözlenmedi, Cookie bilgisi gözlendi.
* **Gönderilen Parametreler:** `EIO`, `transport`, `t`, `sid`
* **Response:** `200 OK`
* **Not:** Bu endpoint klasik REST API isteğinden farklı olarak Socket.IO iletişimi için kullanılıyor.

---

### 3. `/api/Quantitys/`

* **HTTP Method:** GET
* **Amaç:** Quantitys API kaynağına yönelik veri isteği.
* **Authentication:** İstekte Bearer token ve Cookie bilgisi gözlendi.
* **Gönderilen Parametreler:** Yok.
* **Response:** `304 Not Modified`

---

### 4. `/rest/products/2/reviews`

* **HTTP Method:** GET
* **Amaç:** ID'si `2` olan ürünün yorumlarını almak.
* **Authentication:** İstekte Bearer token ve Cookie bilgisi gözlendi.
* **Gönderilen Parametreler:** Path üzerinde ürün ID'si: `2`
* **Response:** `304 Not Modified`

Bu endpoint'te kaynak ID'sinin URL içerisinde kullanıldığı görüldü. Bu yapı daha sonra authorization kontrollerinin incelenmesi açısından önemlidir.

---

### 5. `/rest/basket/0`

* **HTTP Method:** GET
* **Amaç:** ID'si `0` olan sepet kaynağına erişmek.
* **Authentication:** İstekte Bearer token ve Cookie bilgisi gözlendi.
* **Gönderilen Parametreler:** Path üzerinde kaynak ID'si: `0`
* **Response:** `200 OK`

Response içerisinde `status: success` ve `data: null` bilgisi gözlendi.

---

### 6. `/rest/products/1/reviews`

* **HTTP Method:** PUT
* **Amaç:** ID'si `1` olan ürünle ilgili yorum verisi göndermek.
* **Authentication:** İstekte Bearer token ve Cookie bilgisi gözlendi.
* **Gönderilen Parametreler:**

  * Path ID: `1`
  * JSON body:

    * `message`
    * `author`
* **Response:** `201 Created`

Bu istekte veri sunucuya gönderildiği için GET isteklerinden farklı olarak bir veri değiştirme/gönderme işlemi gözlemlendi.

---

### 7. `/rest/products/reviews`

* **HTTP Method:** POST
* **Amaç:** Ürün yorumlarıyla ilişkili bir işlem gerçekleştirmek.
* **Authentication:** İstekte Bearer token ve Cookie bilgisi gözlendi.
* **Gönderilen Parametreler:** JSON body içerisinde `id` alanı.
* **Response:** `200 OK`

Bu endpoint'in kesin işlevini yalnızca yakalanan istekten çıkarmak yerine, gözlemlenen trafik kapsamında ürün yorumlarıyla ilişkili olduğu şeklinde değerlendirdim.

---

## Endpoint Keşfi Neden Önemlidir?

API endpoint'lerinin keşfedilmesi uygulamanın saldırı yüzeyinin anlaşılması açısından önemlidir.

Bir endpoint'in keşfedilmesi sayesinde:

* Uygulamanın hangi API kaynaklarını kullandığı,
* Hangi HTTP methodlarının desteklendiği,
* Hangi parametrelerin kabul edildiği,
* Authentication bilgilerinin kullanılıp kullanılmadığı,
* ID gibi kaynak belirleyicilerinin URL'de kullanılıp kullanılmadığı,
* Hangi endpoint'lerin veri aldığı veya veri değiştirdiği

anlaşılabilir.

Bu bilgiler daha sonraki güvenlik testlerinde hangi alanların incelenmesi gerektiğini belirlemeye yardımcı olur.

Örneğin:

`/rest/products/2/reviews`

endpoint'inde ürün ID'sinin URL içerisinde kullanılması, ilerleyen aşamalarda authorization kontrollerinin incelenebileceği bir alan olduğunu gösterir.

Ancak yalnızca bir endpoint'in keşfedilmesi **tek başına güvenlik açığı anlamına gelmez**. Güvenlik problemi; endpoint'in uygun authentication/authorization kontrollerine sahip olmaması, hatalı input validation yapması veya hassas bilgileri gereksiz şekilde açığa çıkarması gibi durumlarda ortaya çıkabilir.

## Bu Bölümde Öğrendiklerim

Bu çalışmada Burp Suite üzerinden yakaladığım HTTP trafiğini kullanarak Juice Shop içerisindeki farklı API endpoint'lerini belirledim.

Endpoint, HTTP methodu, parametreler, authentication bilgileri ve response durumlarını birlikte incelemenin uygulamanın backend yapısını anlamak açısından önemli olduğunu gördüm.

Ayrıca API endpoint keşfinin tek başına bir zafiyet olmadığını, ancak sonraki güvenlik testleri için uygulamanın saldırı yüzeyini anlamaya yardımcı olduğunu öğrendim.
