# 15. Burp Repeater

## 🎯 Amaç

Bu çalışmada Burp Suite içerisindeki **Repeater** özelliğini kullanmayı öğrendim.

Repeater, daha önce yakalanmış bir HTTP isteğini tekrar göndermeme ve request içerisindeki belirli bölümleri değiştirerek uygulamanın davranışını karşılaştırmama olanak sağlıyor.

Bu çalışmada aynı türden bir HTTP isteği üzerinde farklı değişiklikler yaparak:

* Parameter
* Header
* HTTP Method

değişikliklerinin uygulamanın response'u üzerindeki etkisini inceledim.

Buradaki temel mantığım:

> **İsteği tekrar gönder → tek bir bölümü değiştir → response'u karşılaştır → davranışı yorumla.**

---

# 1. Test — Parameter Değiştirme

### Orijinal istek

```http
GET /rest/basket/[ID]
```

Bu isteği Burp Suite Repeater'a gönderdim ve önce herhangi bir değişiklik yapmadan çalıştırdım.

İlk response:

```http
HTTP/1.1 304 Not Modified
```

olarak geldi.

Daha sonra yalnızca basket ID değerini değiştirdim.

### Değiştirilen bölüm

**Basket ID**

### Değişiklik

Basket ID değerini farklı bir ID ile değiştirdim.

### Response farkı

Değişiklik sonrasında uygulama:

```http
HTTP/1.1 200 OK
```

response'u döndürdü.

Response içerisinde farklı bir basket kaydına ait bilgiler bulundu.

Örneğin response içerisinde basket ID, UserId ve Products gibi alanlar bulunuyordu.

### Sonuç

Yalnızca URL içerisindeki ID değerinin değiştirilmesinin response'u değiştirdiğini gözlemledim.

Bu test, URL parametrelerinin uygulamanın hangi kaynağı döndüreceğini etkileyebildiğini gösterdi.

Daha önce yaptığım **Broken Access Control / IDOR** çalışmasıyla da bağlantılı olarak, kaynak ID'lerinin güvenlik testlerinde önemli olduğunu gördüm.

---

# 2. Test — Authorization Header Değiştirme

İkinci testte aynı basket isteğini kullandım.

### Orijinal istek

```http
GET /rest/basket/[ID]
Authorization: Bearer <token>
```

Gerçek token değerini rapora eklemedim.

### Değiştirilen bölüm

**Authorization header**

### Değişiklik

Authorization header'ını tamamen kaldırdım.

Böylece uygulamanın kimlik doğrulama bilgisi olmadan nasıl davranacağını kontrol ettim.

### Response farkı

Authorization header kaldırıldıktan sonra:

```http
HTTP/1.1 401 Unauthorized
```

response'u döndü.

Response içerisinde:

```text
No Authorization header was found
```

şeklinde bir hata mesajı da gösterildi.

### Sonuç

Authorization header'ı olmadan endpoint'e erişimin engellendiğini gözlemledim.

Bu test sonucunda endpoint'in authentication bilgisine ihtiyaç duyduğunu gördüm.

Ayrıca hata response'unda Express framework bilgisi ve ayrıntılı hata mesajı gösterilmesi dikkatimi çekti. Bu durum daha önce yaptığım **Information Disclosure** çalışmasıyla da bağlantılıdır.

---

# 3. Test — HTTP Method Değiştirme

Üçüncü testte aynı isteğin HTTP methodunu değiştirdim.

### Orijinal istek

```http
GET /rest/basket/[ID]
```

### Değiştirilen bölüm

**HTTP Method**

### Değişiklik

`GET` methodunu:

```http
POST
```

olarak değiştirdim.

Yeni request:

```http
POST /rest/basket/[ID]
```

şeklinde oldu.

### Response farkı

İstek gönderildiğinde:

```http
HTTP/1.1 500 Internal Server Error
```

response'u döndü.

Response içerisinde:

```text
Unexpected path
```

şeklinde bir hata mesajı ve backend tarafına ait teknik bilgiler bulundu.

### Sonuç

HTTP methodunun değiştirilmesi uygulamanın davranışını değiştirdi.

GET isteğinin normal şekilde işlediği endpoint'e POST gönderildiğinde uygulamanın beklenmeyen path hatası verdiğini gözlemledim.

Bu test bana HTTP methodunun yalnızca istek türünü belirtmediğini, aynı zamanda uygulamanın hangi routing ve işlem mantığını kullanacağını da etkilediğini gösterdi.

Buradaki sonucu doğrudan bir güvenlik açığı olarak değerlendirmedim. Kontrollü test sonucunda uygulamanın farklı HTTP methodlarına verdiği davranışı gözlemledim.

---

# 📊 Genel Karşılaştırma

| Test | Değiştirilen bölüm   | Response                    | Gözlem                                                    |
| ---- | -------------------- | --------------------------- | --------------------------------------------------------- |
| 1    | Basket ID            | `200 OK`                    | Farklı kaynak verisi döndü                                |
| 2    | Authorization Header | `401 Unauthorized`          | Authentication olmadan erişim engellendi                  |
| 3    | HTTP Method          | `500 Internal Server Error` | Beklenmeyen method farklı bir hata davranışına neden oldu |

---

# 🧠 Bu Çalışmadan Ne Öğrendim?

Burp Repeater'ın temel amacının sadece bir request'i tekrar göndermek olmadığını öğrendim.

Asıl önemli olan, **tek bir değişiklik yaparak uygulamanın davranışını gözlemlemek ve response'lar arasındaki farkı analiz etmek.**

Örneğin:

```text
ID değiştir
      ↓
Response değişti mi?

Authorization kaldır
      ↓
Erişim devam ediyor mu?

Method değiştir
      ↓
Uygulama nasıl davranıyor?
```

şeklinde kontrollü testler yapılabilir.

Ayrıca bir test sırasında yalnızca status code'a bakmanın yeterli olmadığını gördüm. Response body ve header'lar da incelendiğinde uygulamanın authentication mekanizması veya backend yapısı hakkında ek bilgiler ortaya çıkabiliyor.

**Özet olarak:**

> Burp Repeater, bir HTTP isteğini kontrollü şekilde değiştirerek uygulamanın farklı girdilere nasıl tepki verdiğini incelememi sağlayan önemli bir web güvenlik test aracıdır.
