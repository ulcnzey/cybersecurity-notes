# 16. HTTP Status Code Analizi

## 🎯 Amaç

Bu çalışmada Burp Suite ve OWASP Juice Shop üzerinde yaptığım testlerde karşılaştığım HTTP status code'larını inceleyerek, sunucunun farklı durumlarda nasıl cevap verdiğini anlamaya çalıştım.

Özellikle **401 Authentication** ve **403 Authorization** arasındaki farkı anlamaya odaklandım.

---

## 🌐 HTTP Status Code Nedir?

HTTP status code, istemcinin gönderdiği HTTP isteğine karşılık sunucunun verdiği durum bilgisidir.

Bu kodlar sayesinde isteğin başarılı olup olmadığını, kimlik doğrulama gerekip gerekmediğini, yetki problemi olup olmadığını veya sunucu tarafında bir hata oluşup oluşmadığını anlayabiliriz.

---

# 📌 İncelenen Status Code'lar

| Status Code                 | Anlamı                     | Bu çalışmada |
| --------------------------- | -------------------------- | ------------ |
| `200 OK`                    | İstek başarılı             | Gözlemlendi  |
| `201 Created`               | Yeni kaynak oluşturuldu    | Gözlemlendi  |
| `400 Bad Request`           | İstek hatalı/geçersiz      | Araştırıldı  |
| `401 Unauthorized`          | Authentication problemi    | Gözlemlendi  |
| `403 Forbidden`             | Kullanıcının yetkisi yok   | Araştırıldı  |
| `404 Not Found`             | Kaynak/endpoint bulunamadı | Araştırıldı  |
| `500 Internal Server Error` | Sunucu tarafında hata      | Gözlemlendi  |

---

## 1. `200 OK`

`200 OK`, HTTP isteğinin başarıyla işlendiğini gösterir.

Juice Shop üzerinde basket ve ürünlerle ilgili bazı GET isteklerinde `200 OK` cevabı aldım.

Örneğin bir basket isteğini değiştirdiğimde sunucu başarılı şekilde JSON formatında basket verisi döndürdü.

### Öğrendiğim:

> `200`, sunucunun isteği başarıyla işlediğini gösterir.

Ancak `200` alınması tek başına uygulamanın güvenli olduğu anlamına gelmez. Örneğin daha önce yaptığım Broken Access Control testinde başka bir basket ID'sine erişirken de `200 OK` cevabı almıştım.

---

# 2. `201 Created`

`201 Created`, isteğin başarıyla işlendiğini ve yeni bir kaynağın oluşturulduğunu ifade eder.

Daha önce ürün yorumu ile ilgili yaptığım istekte:

```http
PUT /rest/products/1/reviews
```

isteğine karşılık:

```http
201 Created
```

cevabını aldım.

### Öğrendiğim:

> `201`, özellikle veri oluşturma işlemlerinde kullanılan başarılı bir HTTP status code'udur.

---

# 3. `400 Bad Request`

`400 Bad Request`, sunucunun gönderilen isteği geçerli bir istek olarak işleyemediğini gösterir.

Örneğin bozuk JSON, geçersiz parametre veya beklenmeyen request formatı gibi durumlarda kullanılabilir.

Bu çalışmada `400` kodunu doğrudan gözlemlemedim; HTTP status code davranışını araştırırken inceledim.

### Öğrendiğim:

> `400`, problemin istemciden gönderilen isteğin yapısı veya içeriğiyle ilgili olduğunu gösterir.

---

# 4. `401 Unauthorized`

Bu status code'u Burp Repeater çalışmasında doğrudan gözlemledim.

Normal istekte:

```http
Authorization: Bearer <token>
```

header'ı bulunuyordu.

Repeater üzerinde bu header'ı kaldırıp isteği tekrar gönderdiğimde:

```http
401 Unauthorized
```

cevabını aldım.

Sunucu ayrıca Authorization header'ının bulunmadığını belirten bir hata mesajı döndürdü.

### Authentication ile ilişkisi

`401`, kullanıcının kimliğinin doğrulanamadığı veya gerekli authentication bilgilerinin bulunmadığı durumlarla ilişkilidir.

Benim testimde token bilgisini kaldırdığım için sunucu isteği kimlik doğrulaması yapılmadan kabul etmedi.

### Öğrendiğim:

> **401 → Authentication**

Yani basitçe:

**"Sen kimsin?"**

sorusuyla ilişkilendirebilirim.

---

# 5. `403 Forbidden`

`403 Forbidden`, sunucunun isteği yapan kullanıcının kimliğini biliyor olmasına rağmen, kullanıcının istenen kaynağa veya işleme erişme yetkisinin olmadığını ifade eder.

Bu çalışmada `403` kodunu doğrudan gözlemlemedim ancak authentication ve authorization arasındaki farkı anlamak için inceledim.

### Authorization ile ilişkisi

Örneğin:

```text
Kullanıcı giriş yaptı
        ↓
Authentication başarılı
        ↓
Sunucu kullanıcının kim olduğunu biliyor
        ↓
İstenen işlem için yetkisi yok
        ↓
403 Forbidden
```

### Öğrendiğim:

> **403 → Authorization**

Bunu şu şekilde aklımda tutuyorum:

```text
401 → Kimsin?
403 → Seni tanıyorum ama buna yetkin yok.
```

Bu ayrım özellikle **Broken Access Control** analizlerinde önemlidir.

---

# 6. `404 Not Found`

`404 Not Found`, istenen kaynağın veya endpoint'in bulunamadığını ifade eder.

Örneğin uygulamada bulunmayan bir endpoint'e istek gönderildiğinde sunucu `404` döndürebilir.

Bu çalışmada `404` kodunu doğrudan gözlemlemedim; anlamını ve kullanım durumunu araştırdım.

### Öğrendiğim:

> `404`, istemcinin istediği kaynağın sunucuda bulunamadığını gösterir.

---

# 7. `500 Internal Server Error`

`500 Internal Server Error`, sunucu tarafında beklenmeyen bir hata meydana geldiğini gösterir.

Juice Shop üzerinde bunu iki farklı çalışmada gözlemledim.

### SQL Injection testinde

Login endpoint'inde `email` parametresine kontrollü olarak özel karakter içeren bir değer gönderdiğimde sunucu:

```http
500 Internal Server Error
```

döndürdü.

Response içerisinde SQLite, Sequelize ve backend hata bilgileri de açığa çıktı.

Bu nedenle burada yalnızca `500` kodunu değil, **hata mesajının bilgi sızdırmasını** da önemli bir bulgu olarak değerlendirdim.

### Repeater testinde

Basket isteğinde HTTP methodunu:

```http
GET
```

yerine:

```http
POST
```

olarak değiştirdiğimde de:

```http
500 Internal Server Error
```

cevabını aldım.

Response içerisinde backend dosya yolları ve Express ile ilgili hata bilgileri görünüyordu.

### Öğrendiğim:

> `500`, sunucu tarafında beklenmeyen bir hata olduğunu gösterir.

Ancak `500` görmek tek başına bir güvenlik açığı olduğunu kanıtlamaz. Hatanın oluşturduğu response içeriği ayrıca incelenmelidir.

---

# 🔐 401 ve 403 Arasındaki Temel Fark

Bu çalışmada benim için en önemli ayrımlardan biri `401` ve `403` oldu.

| Kod   | Konu           | Anlam                                          |
| ----- | -------------- | ---------------------------------------------- |
| `401` | Authentication | Kullanıcının kimliği doğrulanmamış/geçersiz    |
| `403` | Authorization  | Kullanıcı doğrulanmış ancak erişim yetkisi yok |

Bunu şu şekilde düşünebilirim:

```text
401
↓
"Sen kimsin?"
↓
Authentication problemi


403
↓
"Seni tanıyorum."
↓
"Ama bunu yapmaya yetkin yok."
↓
Authorization problemi
```

---

# 🧠 Önceki Çalışmalarla Bağlantısı

Bu status code'ları önceki çalışmalarla ilişkilendirdiğimde konu daha anlaşılır hale geldi:

```text
Authentication
      ↓
   401

Authorization
      ↓
   403

Başarılı istek
      ↓
   200

Yeni kaynak oluşturma
      ↓
   201

Hatalı request
      ↓
   400

Kaynak bulunamadı
      ↓
   404

Sunucu tarafında hata
      ↓
   500
```

Ayrıca daha önce yaptığım Broken Access Control testlerinde farklı bir basket ID'sine erişirken `200 OK` almam, HTTP status code'un tek başına güvenlik değerlendirmesi için yeterli olmadığını gösterdi.

Yani:

> **200 = güvenli**

veya

> **500 = güvenlik açığı**

şeklinde düşünmemek gerekiyor.

Status code'u, response body'sini, header'ları ve uygulamanın beklenen davranışını birlikte değerlendirmek gerekiyor.

---

# 📌 Bu Çalışmadan Öğrendiklerim

* HTTP status code'ların sunucunun isteğe verdiği durum bilgisini gösterdiğini öğrendim.
* `200` ve `201` gibi başarılı response'ların farklı anlamlara geldiğini gördüm.
* `401` kodunun authentication ile ilişkili olduğunu gerçek bir Burp testiyle gözlemledim.
* `403` kodunun authentication'dan farklı olarak authorization ile ilişkili olduğunu öğrendim.
* `400` ve `404` kodlarının hangi durumlarda kullanılabileceğini araştırdım.
* `500` kodunu hem SQL Injection testinde hem de Repeater çalışmasında gözlemledim.
* Status code'un tek başına güvenlik açığını kanıtlamadığını öğrendim.
* Response body'sinin ve header'ların da güvenlik analizi açısından önemli olduğunu gördüm.

## 🎯 Sonuç

Bu çalışmadan sonra HTTP response'larına yalnızca "başarılı" veya "hatalı" şeklinde bakmak yerine, **sunucunun neden bu status code'u döndürdüğünü ve bunun uygulamanın güvenlik davranışı hakkında ne söylediğini** değerlendirmeye başladım.

Özellikle:

```text
401 → Authentication
403 → Authorization
```

ayrımının web güvenliği açısından önemli olduğunu öğrendim.
