# 13. Security Misconfiguration

## 🎯 Amaç

Bu çalışmada OWASP Juice Shop üzerinde güvenlik yapılandırmalarını ve uygulamanın HTTP response'larında kullanıcıya gereğinden fazla teknik bilgi verip vermediğini incelemeyi amaçladım.

Özellikle;

* Gereksiz HTTP header'ları
* Güvenlik header'larını
* CORS yapılandırmasını
* Sunucu tarafından verilen teknik bilgileri
* Hata mesajlarını
* Gereksiz bilgi ifşasını

inceledim.

---

## 🔎 Security Misconfiguration Nedir?

Security Misconfiguration, bir uygulamanın veya sunucunun güvenli olmayan ya da gereksiz şekilde bilgi veren yapılandırmalarla çalışmasıdır.

Örneğin;

* Gereksiz servislerin açık bırakılması
* Debug modunun açık olması
* Ayrıntılı hata mesajlarının kullanıcıya gösterilmesi
* Sunucu ve teknoloji bilgilerinin gereksiz şekilde paylaşılması
* Çok geniş CORS yapılandırması
* Gereksiz HTTP header'larının kullanılması

gibi durumlar Security Misconfiguration kapsamında değerlendirilebilir.

Buradaki önemli nokta, gördüğüm her farklı header'ın güvenlik açığı olmadığıdır. Bir header'ın gerçekten risk oluşturup oluşturmadığını uygulamanın davranışı ve ortaya çıkabilecek etkisi üzerinden değerlendirmek gerekir.

---

# 1. HTTP Response Header Analizi

Burp Suite üzerinden Juice Shop'a ait bir HTTP response'u inceledim.

Response header'larında şu bilgiler bulunuyordu:

```http
HTTP/1.1 304 Not Modified
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: ...
ETag: ...
```

### `Access-Control-Allow-Origin: *`

Bu header CORS yapılandırmasıyla ilgilidir.

`*` kullanılması, kaynağın farklı originlerden gelen isteklere geniş şekilde erişilebilir olmasını ifade eder.

Ancak yalnızca bu header'ı görerek doğrudan güvenlik açığı olduğunu söylememek gerekir. Gerçek risk; endpoint'in hangi verileri döndürdüğü, kimlik doğrulama mekanizması ve credential kullanımına bağlıdır.

Bu nedenle bunu:

> Geniş CORS yapılandırması açısından incelenmesi gereken bir nokta

olarak değerlendirdim.

---

### `X-Content-Type-Options: nosniff`

Bu header bir güvenlik önlemidir.

Tarayıcının response içeriğinin Content-Type bilgisini tahmin etmeye çalışmasını engellemeye yardımcı olur.

Bu nedenle Security Misconfiguration bulgusu olarak değerlendirmedim.

---

### `X-Frame-Options: SAMEORIGIN`

Bu header sayfanın farklı originlerden iframe içerisinde çalıştırılmasını sınırlandırmaya yardımcı olur.

`SAMEORIGIN` değeri yalnızca aynı origin içerisinden frame kullanımına izin verir.

Bu nedenle bunu da olumlu bir güvenlik yapılandırması olarak değerlendirdim.

---

### `Feature-Policy: payment 'self'`

Response içerisinde `Feature-Policy` header'ının bulunduğunu gördüm.

Bu mekanizma web özelliklerinin hangi originler tarafından kullanılabileceğini kontrol etmek için kullanılmıştır. Modern web uygulamalarında bunun yerine Permissions Policy yaklaşımı kullanılmaktadır.

Bu nedenle eski bir yapılandırma kullanımı açısından dikkat edilmesi gereken bir nokta olduğunu düşündüm.

---

### `X-Recruiting: /#/jobs`

Response içerisinde:

```http
X-Recruiting: /#/jobs
```

şeklinde bir header bulunduğunu gördüm.

Bu header uygulamanın işe alım sayfasının yolunu dışarıya bildiriyor.

Tek başına ciddi bir güvenlik açığı oluşturmuyor. Ancak uygulamanın çalışması için gerekli olmayan bilgilerin HTTP response içerisinde verilmesi nedeniyle **gereksiz bilgi ifşasına** örnek olarak değerlendirilebilir.

---

### `ETag`, `Last-Modified` ve `Cache-Control`

Bu header'ların HTTP önbellekleme mekanizmasıyla ilgili olduğunu gördüm.

Bunlar normal HTTP mekanizmaları olduğu için tek başlarına güvenlik problemi olarak değerlendirilmemelidir.

---

# 2. Hata Mesajlarında Teknik Bilgi İfşası

Security Misconfiguration açısından daha önemli bir gözlemi SQL Injection çalışması sırasında yaptım.

Kontrollü test sırasında uygulama `500 Internal Server Error` döndürdü.

Response içerisinde yalnızca genel bir hata mesajı yerine backend teknolojisi ve veritabanıyla ilgili teknik bilgiler de görüldü.

Örneğin:

* SQLite ile ilgili hata bilgisi
* Sequelize hata türü
* SQL sorgusuyla ilgili bilgiler
* Backend stack trace bilgileri
* Sunucu tarafındaki dosya/yol bilgileri

gibi ayrıntılar response içerisinde görünüyordu.

Bu durum daha önemli bir **information disclosure** örneğidir.

Çünkü normal bir kullanıcıya gösterilmesi gerekmeyen backend teknolojileri ve hata ayrıntıları saldırganın uygulamanın iç yapısını anlamasını kolaylaştırabilir.

Örneğin uygulamanın:

* Hangi veritabanını kullandığı
* Hangi ORM/framework ile çalıştığı
* Hatanın backend'in hangi bölümünde oluştuğu

gibi bilgiler öğrenilebilir.

Bu bilgiler tek başına sisteme erişim sağlamaz. Ancak başka zafiyetlerin araştırılması sırasında saldırgan için yardımcı bilgi sağlayabilir.

---

# 3. Gereksiz Teknik Bilgi Neden Risklidir?

Bir web uygulamasının kullanıcıya gereğinden fazla teknik bilgi vermesi saldırı yüzeyinin daha iyi anlaşılmasına neden olabilir.

Örneğin bir hata mesajı:

```text
Bir hata oluştu.
```

demek yerine backend teknolojisini, veritabanını ve stack trace bilgisini gösterirse uygulamanın iç yapısı hakkında daha fazla bilgi edinilebilir.

Bu nedenle production ortamlarında:

* Ayrıntılı stack trace gösterilmemeli
* SQL hata detayları kullanıcıya gönderilmemeli
* Backend dosya yolları gizlenmeli
* Gereksiz teknoloji bilgileri response'larda paylaşılmamalı
* Kullanıcıya genel ve güvenli hata mesajları gösterilmeli
* Ayrıntılı hata kayıtları yalnızca sunucu tarafındaki loglarda tutulmalıdır

---

# 4. Bulgular

| Gözlem                                        | Değerlendirme                                     |
| --------------------------------------------- | ------------------------------------------------- |
| `Access-Control-Allow-Origin: *`              | Geniş CORS yapılandırması açısından incelenebilir |
| `X-Recruiting`                                | Gereksiz bilgi ifşası örneği                      |
| `Feature-Policy`                              | Eski yapılandırma yaklaşımı                       |
| `X-Content-Type-Options`                      | Güvenlik önlemi                                   |
| `X-Frame-Options`                             | Güvenlik önlemi                                   |
| `ETag / Last-Modified`                        | Normal HTTP mekanizması                           |
| Ayrıntılı SQL/SQLite/Sequelize hata bilgileri | Bilgi ifşası açısından daha önemli bulgu          |

---

# 5. Çözüm Önerileri

Security Misconfiguration risklerini azaltmak için:

1. Gereksiz HTTP header'ları kaldırılmalıdır.
2. CORS yalnızca gerekli originlerle sınırlandırılmalıdır.
3. Production ortamında ayrıntılı hata mesajları gösterilmemelidir.
4. Stack trace bilgileri kullanıcıya gönderilmemelidir.
5. SQL ve veritabanı hata detayları gizlenmelidir.
6. Backend dosya yolları response içerisinde paylaşılmamalıdır.
7. Güncel güvenlik politikaları kullanılmalıdır.
8. Debug modu production ortamında kapalı tutulmalıdır.
9. Hata detayları güvenli şekilde sunucu loglarına yazılmalıdır.
10. HTTP response'ları gereksiz teknik bilgi açısından düzenli olarak kontrol edilmelidir.

---

# 🧠 Bu Çalışmadan Ne Öğrendim?

Bu çalışmada Security Misconfiguration'ın sadece "yanlış ayarlanmış bir ayar" anlamına gelmediğini gördüm.

HTTP response'larını incelerken her header'ın güvenlik açığı olmadığını, bazı header'ların doğrudan güvenlik önlemi olduğunu öğrendim.

Özellikle hata mesajlarının önemli olduğunu fark ettim. Bir uygulamanın kullanıcıya sadece işlemin başarısız olduğunu söylemesi ile backend'in kullandığı veritabanını, framework'ünü ve stack trace bilgisini göstermesi arasında ciddi bir bilgi farkı bulunuyor.

Bu nedenle güvenlik testlerinde yalnızca doğrudan saldırı yapılabilecek açıkları değil, uygulamanın dışarıya verdiği gereksiz bilgileri de değerlendirmek gerektiğini öğrendim.

**Özet olarak:**

> Güvenli bir uygulama yalnızca saldırıya karşı korunmamalı, aynı zamanda kendi iç yapısı hakkında gereksiz bilgi de vermemelidir.
