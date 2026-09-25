# 14. Information Disclosure

## 🎯 Amaç

Bu çalışmada OWASP Juice Shop üzerinde uygulamanın dışarıya gereğinden fazla bilgi verip vermediğini araştırdım.

Özellikle;

* Hata mesajları
* API response'ları
* JavaScript dosyaları
* HTML source
* Endpoint bilgileri
* Metadata
* Kullanıcı bilgileri

üzerinden bilgi sızıntısı oluşturabilecek noktaları incelemeyi amaçladım.

Buradaki amacım doğrudan bir sisteme zarar vermek değil, uygulamanın normal kullanıcıya veya yetkisiz bir kişiye hangi bilgileri gösterdiğini anlamaktı.

---

# 🔎 Information Disclosure Nedir?

Information Disclosure, bir uygulamanın normalde dışarıya vermemesi gereken bilgileri kullanıcıya veya yetkisiz kişilere göstermesidir.

Bu bilgiler doğrudan parola gibi gizli bilgiler olmak zorunda değildir.

Örneğin:

* Veritabanı türü
* Kullanılan framework veya kütüphane
* Backend dosya yolları
* Stack trace
* API endpoint'leri
* Kullanıcı bilgileri
* Sistem hakkında metadata
* Ayrıntılı hata mesajları

gibi bilgiler de bilgi ifşası kapsamında değerlendirilebilir.

Önemli olan bilginin uygulamanın iç yapısı veya kullanıcıları hakkında gereğinden fazla bilgi verip vermediğidir.

---

# 1. Hata Mesajlarının İncelenmesi

Daha önce SQL Injection çalışması sırasında kontrollü bir test gerçekleştirmiştim.

Test sırasında uygulama:

```text
500 Internal Server Error
```

cevabı verdi.

Response içerisinde yalnızca genel bir hata mesajı bulunmak yerine backend hakkında teknik bilgiler de görüldü.

Response içerisinde;

* SQLite ile ilgili hata bilgileri
* Sequelize hata türü
* SQL sorgusuyla ilgili bilgiler
* Backend stack trace
* Sunucu tarafındaki dosya/yol bilgileri

gibi ayrıntılar bulunuyordu.

Bu durum bilgi ifşasına örnek olarak değerlendirilebilir.

---

# 2. Bu Bilgi Neden Değerlidir?

Bir hata mesajının saldırgana kullanılan veritabanını veya framework'ü söylemesi doğrudan sisteme erişim sağlamaz.

Ancak uygulamanın iç yapısı hakkında bilgi verir.

Örneğin hata mesajından:

```text
SQLite
Sequelize
```

gibi teknolojilerin kullanıldığı anlaşılırsa uygulamanın teknik yapısı hakkında bilgi edinilmiş olur.

Aynı şekilde stack trace içerisinde dosya yollarının veya kullanılan modüllerin görünmesi backend'in nasıl organize edildiği hakkında fikir verebilir.

Bu bilgiler daha sonra yapılabilecek güvenlik araştırmalarında kullanılabilecek **yardımcı bilgiler** haline gelebilir.

Bu nedenle production ortamında kullanıcının ihtiyacı olmayan teknik hata ayrıntılarının gösterilmemesi gerekir.

---

# 3. API Response'larının İncelenmesi

Burp Suite üzerinden Juice Shop'un API isteklerini daha önce incelemiştim.

Örneğin:

```http
GET /rest/products/2/reviews
```

isteğinde API response'u ürün yorumlarıyla ilgili veri döndürüyordu.

Benzer şekilde:

```http
GET /rest/basket/1
```

gibi endpoint'lerde kullanıcı sepetiyle ilgili bilgiler response içerisinde bulunabiliyordu.

API response'larını incelerken yalnızca endpoint'in çalışıp çalışmadığına değil, response içerisinde **gereğinden fazla veri bulunup bulunmadığına** da bakılması gerektiğini öğrendim.

Özellikle kullanıcıya ait;

* E-posta
* Kullanıcı ID
* Rol
* Sipariş/sepet bilgileri
* Dahili ID'ler

gibi bilgilerin gereksiz şekilde döndürülmesi bilgi ifşası açısından incelenmelidir.

Ancak bir verinin response içerisinde bulunması tek başına zafiyet anlamına gelmez. Verinin o kullanıcıya gösterilmesinin gerekli olup olmadığı ve yetkilendirme kontrolünün doğru uygulanıp uygulanmadığı ayrıca değerlendirilmelidir.

---

# 4. Endpoint Bilgileri

Burp Suite HTTP History üzerinde Juice Shop'un çeşitli endpoint'lerini gözlemledim.

Örneğin:

```text
/rest/user/login
/rest/products/2/reviews
/rest/basket/1
/rest/products/1/reviews
/api/Challenges/
/api/Quantitys/
```

gibi endpoint'ler uygulamanın backend API yapısı hakkında bilgi vermektedir.

Endpoint'lerin istemci tarafından kullanılabilir olması tek başına bilgi ifşası değildir.

Modern web uygulamalarında frontend'in API endpoint'lerini kullanması normaldir.

Burada önemli olan endpoint'in hassas bir işlem veya veri içerip içermediği ve gerekli authorization kontrollerinin uygulanıp uygulanmadığıdır.

---

# 5. JavaScript Dosyaları

Modern web uygulamalarında JavaScript dosyaları frontend uygulamasının çalışması için kullanılır.

Bu dosyalar incelendiğinde;

* API endpoint'leri
* Route bilgileri
* Kullanılan kütüphaneler
* Uygulama fonksiyonları
* Frontend mantığı

gibi bilgiler görülebilir.

Bu bilgilerin görülmesi tek başına güvenlik açığı değildir.

Ancak JavaScript içerisine;

* API secret
* Private key
* Gerçek parola
* Veritabanı bilgileri
* Gizli API anahtarları

gibi hassas bilgilerin yanlışlıkla eklenmesi ciddi bir bilgi ifşası oluşturabilir.

Bu nedenle frontend dosyalarında gizli bilgilerin tutulmaması gerekir.

---

# 6. HTML Source

HTML source incelenirken uygulamanın frontend tarafında kullanıcıya gösterilmeyen ancak kaynak kod içerisinde bulunan bilgiler araştırılabilir.

Örneğin;

* HTML yorumları
* Gizli input alanları
* Dahili endpoint bilgileri
* Debug bilgileri
* Gereksiz metadata

gibi içerikler bulunabilir.

Ancak HTML içerisinde bir bilginin bulunması otomatik olarak güvenlik açığı anlamına gelmez.

Asıl değerlendirme, bu bilginin gizli olması gerekip gerekmediği üzerinden yapılmalıdır.

---

# 7. Metadata

HTTP response'larında çeşitli metadata bilgileri bulunabilir.

Örneğin daha önce incelediğim response içerisinde:

```http
ETag: ...
Last-Modified: ...
Date: ...
```

gibi bilgiler bulunuyordu.

Bu bilgilerin çoğu HTTP'nin normal çalışma mekanizmalarının parçasıdır ve tek başına güvenlik açığı değildir.

Bu nedenle Information Disclosure analizinde gördüğüm her metadata bilgisini otomatik olarak bulgu olarak değerlendirmedim.

---

# 8. Kullanıcı Bilgileri

API response'larında kullanıcılarla ilgili bilgilerin gereğinden fazla paylaşılması ayrıca incelenmelidir.

Örneğin bir endpoint yalnızca kullanıcının adını döndürmesi gerekirken;

* e-posta
* rol
* kullanıcı ID
* profil bilgileri
* dahili sistem bilgileri

gibi gereksiz alanları da döndürüyorsa bu durum bilgi ifşası açısından değerlendirilebilir.

Bu nedenle API tasarımında yalnızca gerekli verilerin response içerisinde gönderilmesi önemlidir.

---

# 📋 İnceleme Sonuçları

| İnceleme alanı      | Gözlem                                                           | Değerlendirme                                                         |
| ------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------- |
| Hata mesajları      | SQLite, Sequelize ve stack trace bilgileri görüldü               | ⚠️ Bilgi ifşası                                                       |
| API response        | Uygulama verileri ve endpoint yapısı gözlemlendi                 | 🔎 İçerik bazlı değerlendirme gerekli                                 |
| Endpoint bilgileri  | API endpoint'leri Burp üzerinden görülebiliyor                   | ℹ️ Tek başına zafiyet değil                                           |
| HTTP metadata       | ETag, Last-Modified, Date vb. görüldü                            | ℹ️ Normal HTTP mekanizması                                            |
| JavaScript          | Frontend uygulamasının çalışması için kullanılan dosyalar mevcut | 🔎 Hassas bilgi bulunup bulunmadığı kontrol edilmeli                  |
| HTML source         | Frontend yapısının incelenebileceği görüldü                      | 🔎 Gizli bilgi olup olmadığı kontrol edilmeli                         |
| Kullanıcı bilgileri | API response'larında kullanıcıya ait veriler bulunabiliyor       | ⚠️ Yetkilendirme ve veri minimizasyonu ile birlikte değerlendirilmeli |

---

# ❓ Bir Hata Mesajı Neden Değerlidir?

Bir hata mesajının kullanılan veritabanını veya framework'ü göstermesi, saldırgana uygulamanın teknik yapısı hakkında bilgi sağlar.

Örneğin:

```text
SQLite
Sequelize
```

gibi bilgiler görüldüğünde saldırgan artık uygulamanın hangi teknolojilerle çalıştığı hakkında daha fazla bilgiye sahip olur.

Bu bilgi tek başına sistemi ele geçirmek anlamına gelmez.

Ancak saldırganın sonraki araştırmalarında kullanabileceği teknik bir ipucu oluşturabilir.

Bu nedenle uygulamanın kullanıcıya:

```text
Bir hata oluştu.
```

gibi genel bir mesaj göstermesi, backend'e ait ayrıntılı hata ve stack trace bilgilerinin ise yalnızca sunucu tarafındaki loglarda tutulması daha güvenli bir yaklaşımdır.

---

# 🛡️ Korunma Yöntemleri

Information Disclosure riskini azaltmak için:

1. Kullanıcıya ayrıntılı stack trace gönderilmemelidir.
2. SQL ve veritabanı hata mesajları gizlenmelidir.
3. Backend dosya yolları response içerisinde paylaşılmamalıdır.
4. JavaScript dosyalarında secret ve private key tutulmamalıdır.
5. API response'larında yalnızca gerekli veriler gönderilmelidir.
6. Kullanıcıya ait hassas bilgiler gereksiz endpoint'lerde döndürülmemelidir.
7. Production ortamında debug mesajları kapatılmalıdır.
8. Hata ayrıntıları sunucu tarafındaki güvenli log sistemlerinde tutulmalıdır.
9. API endpoint'lerinde uygun authentication ve authorization kontrolleri uygulanmalıdır.
10. HTML, JavaScript ve API response'ları düzenli olarak bilgi ifşası açısından kontrol edilmelidir.

---

# 🧠 Bu Çalışmadan Ne Öğrendim?

Bu çalışmada Information Disclosure'ın yalnızca parola veya gizli anahtar sızdırılması anlamına gelmediğini öğrendim.

Bir uygulamanın;

* hangi veritabanını kullandığını,
* hangi framework ile çalıştığını,
* backend dosya yollarını,
* API yapısını,
* kullanıcı bilgilerini

gereğinden fazla göstermesi de güvenlik açısından değerlendirilebilir.

Özellikle hata mesajlarının önemli olduğunu gördüm.

Bir hata mesajı saldırgana doğrudan erişim vermese bile uygulamanın teknolojik yapısını anlamasına yardımcı olabilir.

Bu nedenle güvenlik testlerinde yalnızca "gizli veri sızmış mı?" sorusuna değil:

> **"Uygulama hakkında normalde bilinmesi gerekmeyen hangi bilgileri dışarıya veriyor?"**

sorusuna da bakmak gerektiğini öğrendim.

**Özet olarak:**

> Information Disclosure, uygulamanın saldırgana doğrudan erişim vermese bile sonraki güvenlik araştırmalarını kolaylaştırabilecek gereksiz bilgiler sağlamasıdır.
