# 17. Güvenlik Bulgularını OWASP Kategorilerine Göre Sınıflandırma

## 🎯 Amaç

Bu çalışmada OWASP Juice Shop üzerinde önceki görevlerde yaptığım testlerden elde ettiğim güvenlik bulgularını **OWASP Top 10:2021** kategorilerine göre sınıflandırdım.

Amacım sadece bir problemin varlığını belirtmek değil, bu problemin hangi güvenlik kategorisiyle ilişkili olduğunu ve uygulama açısından ne gibi bir etkisi olabileceğini anlamaktı.

OWASP Top 10:2021 içerisinde erişim kontrolü **A01**, Injection **A03** ve Security Misconfiguration **A05** kategorileri altında yer almaktadır.

---

# 📊 Güvenlik Bulguları

| # | Bulgu                                                               | OWASP Kategorisi                         | Etki                                                |
| - | ------------------------------------------------------------------- | ---------------------------------------- | --------------------------------------------------- |
| 1 | Basket ID değiştirerek başka kullanıcıya ait basket verisine erişim | **A01:2021 – Broken Access Control**     | Yetkisiz kullanıcı verisine erişim                  |
| 2 | Login `email` parametresinde SQL Injection göstergesi               | **A03:2021 – Injection**                 | SQL sorgusunun etkilenebilmesi                      |
| 3 | SQL hatasında veritabanı ve framework bilgilerinin açığa çıkması    | **A05:2021 – Security Misconfiguration** | Uygulamanın teknik yapısı hakkında bilgi edinilmesi |
| 4 | Yetkisiz istekte ayrıntılı hata mesajı ve stack trace gösterilmesi  | **A05:2021 – Security Misconfiguration** | Backend/framework bilgilerinin açığa çıkması        |
| 5 | Hatalı HTTP methodunda backend dosya yollarının açığa çıkması       | **A05:2021 – Security Misconfiguration** | Sunucu yapısı hakkında teknik bilgi sızıntısı       |

---

# 1. Başka Kullanıcıya Ait Basket Verisine Erişim

### Bulgu

Basket endpoint'inde kullanılan ID değerini değiştirerek farklı bir basket kaynağına erişmeyi test ettim.

Örneğin:

```http
GET /rest/basket/1
```

isteğindeki ID değerini değiştirdiğimde başka bir basket kaynağının döndüğünü gözlemledim.

Daha önce yaptığım testte dönen response içerisinde farklı bir `Basket ID` ve `UserId` bilgisi bulunuyordu.

### OWASP Kategorisi

**A01:2021 – Broken Access Control**

OWASP, URL veya parametrelerdeki ID değerlerinin değiştirilmesiyle başka kullanıcıların kaynaklarına erişilmesini Broken Access Control kapsamında ele almaktadır.

### Etki

Normal bir kullanıcının yalnızca kendisine ait olması gereken bir kaynağa farklı bir ID kullanarak erişebilmesi, kullanıcı verilerinin yetkisiz şekilde görüntülenmesine neden olabilir.

### Değerlendirmem

Bu test benim için özellikle önemliydi çünkü authentication ile authorization arasındaki farkı daha iyi anlamamı sağladı.

Kullanıcının login olmuş olması, uygulamadaki bütün kaynaklara erişebileceği anlamına gelmemelidir.

---

# 2. Login Endpoint'inde SQL Injection Göstergesi

### Bulgu

Juice Shop login endpoint'ini Burp Repeater üzerinden test ettim:

```http
POST /rest/user/login
```

JSON içerisindeki `email` parametresine kontrollü olarak özel karakter içeren bir değer gönderdim.

Test sonucunda uygulama:

```text
500 Internal Server Error
```

döndürdü.

Response içerisinde SQLite ve Sequelize ile ilgili database hata bilgileri ve SQL işleme sürecine ait detaylar görüldü.

### OWASP Kategorisi

**A03:2021 – Injection**

OWASP, kullanıcıdan gelen verinin güvenli şekilde ayrıştırılmaması ve dinamik/parametresiz sorgularda kullanılması gibi durumları Injection kategorisi altında değerlendirir. SQL Injection da bu kategorinin önemli örneklerinden biridir.

### Etki

Kontrollü testte gönderdiğim inputun SQL işleme sürecini etkilediğine dair güçlü bir belirti elde ettim.

Ancak bu çalışmada veri çıkarma veya veritabanını ele geçirme gibi ileri bir işlem gerçekleştirmedim.

### Değerlendirmem

Bu nedenle bulguyu:

> **SQL Injection açısından güçlü zafiyet göstergesi**

olarak değerlendiriyorum.

---

# 3. SQL Hatasında Teknik Bilgilerin Açığa Çıkması

### Bulgu

SQL Injection testinde oluşan `500 Internal Server Error` response'unda uygulamanın kullandığı bazı teknik bileşenler hakkında bilgi açığa çıktı.

Örneğin:

* SQLite
* Sequelize
* SQL ile ilgili hata bilgileri
* Backend çalışma yapısına ilişkin bilgiler

görülebiliyordu.

### OWASP Kategorisi

**A05:2021 – Security Misconfiguration**

OWASP, kullanıcıya stack trace veya gereğinden fazla ayrıntılı hata mesajlarının gösterilmesini Security Misconfiguration kapsamında örneklemektedir.

### Etki

Bu bilgiler doğrudan uygulamaya erişim sağlamasa da uygulamanın kullandığı teknolojiler hakkında bilgi verir.

Bir saldırgan açısından bu tür bilgiler sonraki araştırmalar için yol gösterici olabilir.

### Öğrendiğim

> Hata mesajının kullanıcıya gösterilmesi sadece kullanıcı deneyimi açısından değil, güvenlik açısından da değerlendirilmelidir.

---

# 4. Authorization Olmadan Ayrıntılı Hata Mesajı

### Bulgu

Burp Repeater üzerinde basket isteğindeki:

```http
Authorization: Bearer <token>
```

header'ını kaldırarak isteği tekrar gönderdim.

Sunucu:

```http
401 Unauthorized
```

cevabını verdi.

Authentication kontrolünün çalıştığını görmemin yanında response içerisinde ayrıntılı hata mesajı ve Express/Juice Shop ile ilgili teknik bilgiler de görüldü.

### OWASP Kategorisi

**A05:2021 – Security Misconfiguration**

OWASP, gereğinden fazla ayrıntılı hata mesajlarının ve stack trace'lerin kullanıcıya gösterilmesini Security Misconfiguration kapsamında değerlendirmektedir.

### Etki

Authentication başarısız olsa bile response içerisinde backend/framework hakkında gereksiz bilgiler açığa çıkabiliyor.

Bu bilgiler uygulamanın teknik yapısının anlaşılmasını kolaylaştırabilir.

### Değerlendirmem

Burada önemli olan iki farklı şeyi birbirinden ayırdım:

```text
401
↓
Authentication kontrolü çalışıyor

Ayrıntılı hata mesajı
↓
Information Disclosure / Security Misconfiguration riski
```

Yani `401` response'unun kendisini güvenlik açığı olarak değerlendirmiyorum. Sorun, response içerisinde gereğinden fazla teknik bilgi bulunmasıdır.

---

# 5. Hatalı HTTP Methodunda Backend Bilgilerinin Açığa Çıkması

### Bulgu

Normalde:

```http
GET /rest/basket/1
```

şeklinde olan isteğin HTTP methodunu Burp Repeater üzerinde:

```http
POST /rest/basket/1
```

olarak değiştirdim.

Sunucu:

```http
500 Internal Server Error
```

cevabını verdi.

Response içerisinde `Unexpected path` mesajının yanında backend dosya yolları ve Express stack trace bilgileri de görünüyordu.

### OWASP Kategorisi

**A05:2021 – Security Misconfiguration**

Ayrıntılı stack trace ve backend hata bilgilerinin kullanıcıya gösterilmesi OWASP tarafından Security Misconfiguration kapsamında ele alınmaktadır.

### Etki

Backend dosya yapısı ve kullanılan framework hakkında gereksiz teknik bilgiler açığa çıkabilir.

Bu bilgiler tek başına sisteme erişim sağlamaz ancak uygulamanın iç yapısının anlaşılmasına yardımcı olabilir.

### Değerlendirmem

HTTP methodunu değiştirmenin kendisini güvenlik açığı olarak değerlendirmiyorum.

Buradaki güvenlik açısından önemli nokta, hatalı isteğe verilen response'un gereğinden fazla backend bilgisi içermesidir.

---

# 🧩 Bulguların Genel Sınıflandırması

Yaptığım çalışmalar sonucunda bulgularımı üç temel OWASP kategorisinde topladım:

```text
A01: Broken Access Control
        │
        └── Başka kullanıcıya ait basket verisine erişim


A03: Injection
        │
        └── Login email parametresinde SQL Injection göstergesi


A05: Security Misconfiguration
        │
        ├── SQL hata bilgilerinin açığa çıkması
        ├── Authorization hatasında ayrıntılı hata bilgisi
        └── Hatalı method isteğinde stack trace açığa çıkması
```

---

# 📌 Önemli Bir Gözlem

Bu çalışma sırasında her `500`, `401` veya `200` response'unu otomatik olarak güvenlik açığı olarak değerlendirmemem gerektiğini öğrendim.

Örneğin:

```text
200 → Her zaman güvenli değildir.
500 → Her zaman güvenlik açığı değildir.
401 → Authentication kontrolünün çalıştığını gösterebilir.
403 → Authorization kontrolünün reddettiğini gösterebilir.
```

Bir response'u güvenlik bulgusu olarak değerlendirmek için:

* İsteğin ne olduğunu,
* Uygulamanın normalde ne yapması gerektiğini,
* Gerçekte ne yaptığını,
* Response body'sini,
* Header'ları,
* Kullanıcı yetkisini,
* Ve ortaya çıkan güvenlik etkisini

birlikte değerlendirmek gerekir.

---

# 🧠 Bu Çalışmadan Öğrendiklerim

Bu çalışmada daha önce yaptığım testlerin aslında birbirinden bağımsız olmadığını fark ettim.

Örneğin:

```text
ID değiştirme
      ↓
Access Control
      ↓
A01 Broken Access Control
```

ve:

```text
Özel karakter içeren input
      ↓
SQL hatası
      ↓
Injection
      ↓
A03 Injection
```

gibi bir bağlantı kurabiliyorum.

Ayrıca hata mesajlarında ortaya çıkan teknik bilgilerin ayrı bir güvenlik problemi oluşturabileceğini ve bunun **A05 Security Misconfiguration** kapsamında değerlendirilebileceğini öğrendim.

## 🎯 Sonuç

Bu çalışmanın sonunda Juice Shop üzerinde yaptığım testlerden **en az 5 güvenlik bulgusunu** belirleyip OWASP Top 10:2021 kategorileriyle eşleştirdim.

Benim için en önemli öğrenme, bir güvenlik testinin yalnızca:

> "Bir hata buldum."

demekten ibaret olmadığı oldu.

Asıl önemli olan:

> **"Ne buldum → neden güvenlik problemi → hangi OWASP kategorisi → etkisi ne → nasıl düzeltilebilir?"**

şeklinde düşünebilmek oldu.
