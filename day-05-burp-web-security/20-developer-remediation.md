# 20. Geliştirici Gözüyle Çözüm

## 🎯 Amaç

Bu çalışmada önceki görevlerde tespit ettiğim güvenlik problemlerine yalnızca saldırgan perspektifinden değil, **uygulamanın geliştiricisi perspektifinden** baktım.

Her bulgu için:

> **"Ben bu uygulamanın geliştiricisi olsaydım bu problemi nasıl düzeltirdim?"**

sorusuna cevap vermeye çalıştım.

Bir güvenlik testinin yalnızca zafiyeti bulmakla bitmediğini, asıl amacın problemin kaynağını anlayıp güvenli bir çözüm üretmek olduğunu öğrendim.

---

# 1. Broken Access Control / IDOR

## 🔴 Problem

Basket endpoint'inde kullanılan ID değeri değiştirilerek başka bir kullanıcıya ait basket verisine erişilebildiğini gözlemledim.

Örneğin:

```http
GET /rest/basket/1
```

isteğindeki ID değerinin değiştirilmesiyle başka bir basket kaynağının döndüğünü gördüm.

Buradaki temel problem, sunucunun yalnızca:

> "Bu basket mevcut mu?"

kontrolünü yapması yerine:

> "Bu basket gerçekten bu kullanıcıya mı ait?"

kontrolünü de yapması gerektiğidir.

---

## ❌ Güvensiz yaklaşım

Backend yalnızca gelen ID'yi kullanarak veriyi getirirse:

```text
GET /rest/basket/2
        ↓
Basket ID = 2
        ↓
Veriyi getir
        ↓
200 OK
```

Burada isteği yapan kullanıcının kaynak üzerinde yetkisi kontrol edilmemektedir.

---

## ✅ Güvenli yaklaşım

Sunucu tarafında ownership/authorization kontrolü yapılmalıdır:

```text
Request
   ↓
Kullanıcı kim?
   ↓
İstenen basket kimin?
   ↓
Aynı kullanıcı mı?
   │
 ┌─┴───────────────┐
 │                 │
EVET              HAYIR
 │                 │
 ↓                 ↓
200              403
```

Örneğin backend mantığı kavramsal olarak:

```text
currentUser = authenticatedUser

basket = getBasket(requestedBasketId)

if basket.userId != currentUser.id:
    return 403 Forbidden

return basket
```

Burada önemli olan kontrolün **server-side** yapılmasıdır.

---

## 🔐 Ek Güvenlik Önlemleri

* Her kaynak erişiminde ownership kontrolü yapılmalı.
* Authorization middleware kullanılmalı.
* Frontend'deki ID gizleme veya buton gizleme güvenlik kontrolü olarak kullanılmamalı.
* Kullanıcıların başka kullanıcıların ID'lerini tahmin edebilmesi güvenlik açısından dikkate alınmalı.
* Access control için otomatik testler yazılmalı.

### Geliştirici olarak çıkardığım sonuç:

> **Authentication kullanıcının kim olduğunu belirler, authorization ise o kullanıcının hangi kaynağa erişebileceğini belirler.**

---

# 2. SQL Injection

## 🔴 Problem

Login endpoint'indeki `email` parametresini kontrollü şekilde test ettiğimde SQL/database seviyesinde hata oluştuğunu gözlemledim.

Temel problem:

> Kullanıcıdan gelen verinin SQL sorgusunun yapısını etkileyebilme ihtimalidir.

---

## ❌ Güvensiz yaklaşım

Kullanıcı girdisini doğrudan SQL sorgusuna eklemek:

```text
query = "SELECT * FROM users WHERE email = '" + email + "'"
```

gibi bir yaklaşım güvenli değildir.

Burada `email` değişkeni kullanıcı tarafından kontrol edilmektedir.

---

## ✅ Güvenli yaklaşım — Parameterized Query

Kullanıcı girdisi SQL kodundan ayrılmalıdır.

Kavramsal olarak:

```text
SQL:
SELECT * FROM users WHERE email = ?

Parameter:
email
```

şeklinde kullanılmalıdır.

Böylece kullanıcı tarafından gönderilen veri SQL komutunun bir parçası değil, **veri** olarak değerlendirilir.

---

## ✅ Prepared Statement

Prepared Statement kullanımı da aynı güvenlik mantığını destekler.

Örneğin kavramsal yapı:

```text
Prepared SQL
      ↓
SELECT * FROM users WHERE email = ?
      ↓
Parameter
      ↓
User Input
```

şeklindedir.

Buradaki amaç:

> **SQL komutu ile kullanıcı verisini birbirinden ayırmaktır.**

---

## 🔐 Ek Güvenlik Önlemleri

Parameterized Query tek başına bırakılmamalıdır.

Ayrıca:

* Prepared Statement kullanılmalı.
* ORM/database kütüphaneleri güvenli şekilde kullanılmalı.
* Database kullanıcısına minimum yetki verilmeli.
* Kullanıcı girdileri uygun şekilde doğrulanmalı.
* SQL/database hata mesajları kullanıcıya gösterilmemeli.
* Database sorguları güvenli şekilde loglanmalı.
* Production ortamında ayrıntılı debug bilgileri kapatılmalı.

### Geliştirici olarak çıkardığım sonuç:

> **Input validation bir güvenlik katmanıdır ancak SQL Injection'a karşı temel savunma parameterized query / prepared statement kullanmaktır.**

---

# 3. Ayrıntılı Hata Mesajları / Information Disclosure

## 🔴 Problem

Yaptığım testlerde uygulamanın bazı hatalarda kullanıcıya:

* Backend dosya yolları
* Framework bilgileri
* Stack trace
* Database bilgileri
* Ayrıntılı hata mesajları

gösterdiğini gözlemledim.

Örneğin hatalı HTTP methodunda `500 Internal Server Error` response'u içerisinde backend path ve stack trace bilgileri görünüyordu.

SQL Injection testinde ise database/framework hakkında teknik bilgiler açığa çıkmıştı.

---

## ❌ Güvensiz yaklaşım

Development sırasında faydalı olan ayrıntılı hata mesajlarını production ortamında da kullanıcıya göndermek:

```text
500 Internal Server Error

Error:
...
backend/path/file.js
...
stack trace
...
database error
...
```

şeklinde response üretmek güvenli değildir.

---

## ✅ Güvenli yaklaşım

Kullanıcıya yalnızca gerekli bilgiyi vermeliyim:

```http
HTTP/1.1 500 Internal Server Error
```

ve örneğin:

```json
{
  "error": "Beklenmeyen bir hata oluştu."
}
```

Teknik detaylar ise server-side loglarda tutulmalıdır.

```text
Kullanıcı
   ↓
Genel hata mesajı

Backend
   ↓
Detaylı hata
   ↓
Güvenli server log
```

---

## 🔐 Ek Güvenlik Önlemleri

### 1. Production'da debug kapatılmalı

Development ortamındaki ayrıntılı hata ekranları production'a taşınmamalıdır.

### 2. Stack trace response'tan kaldırılmalı

Stack trace yalnızca yetkili geliştiricilerin erişebildiği loglarda tutulmalıdır.

### 3. Backend path bilgileri gizlenmeli

Örneğin:

```text
/juice-shop/build/routes/...
```

gibi bilgiler kullanıcıya gönderilmemelidir.

### 4. Database bilgileri gizlenmeli

SQLite, PostgreSQL, MySQL, Sequelize vb. teknik bilgiler gereksiz yere response içerisinde gösterilmemelidir.

### 5. Merkezi logging kullanılmalı

Hata detaylarını kullanıcıya göstermek yerine:

```text
Application
     ↓
Logging System
     ↓
Developer / Security Team
```

şeklinde güvenli bir loglama sistemi kullanılmalıdır.

### Geliştirici olarak çıkardığım sonuç:

> **Kullanıcıya faydalı hata mesajı vermek ile geliştiriciye faydalı hata logu vermek aynı şey değildir.**

---

# 4. Authentication Kontrolü

## 🔴 Problem

Repeater üzerinde Authorization header'ını kaldırdığımda:

```http
401 Unauthorized
```

cevabı aldım.

Bu aslında authentication kontrolünün çalıştığını gösterdi.

Ancak geliştirici açısından burada iki konuya dikkat etmek gerekir:

1. Authentication gerçekten server-side yapılmalı.
2. Authentication hatasında gereksiz teknik bilgiler response'a eklenmemeli.

---

## ❌ Güvensiz yaklaşım

```text
Authorization header yok
       ↓
Detaylı Express hatası
       ↓
Stack trace
       ↓
Backend bilgileri
       ↓
Client
```

---

## ✅ Güvenli yaklaşım

```text
Authorization header yok
       ↓
Authentication middleware
       ↓
401 Unauthorized
       ↓
Genel hata mesajı
```

Örneğin:

```json
{
  "error": "Authentication required."
}
```

gibi genel bir response yeterlidir.

---

## 🔐 Geliştirici açısından

Authentication mekanizmasında:

* Token doğrulanmalı.
* Token geçerliliği kontrol edilmeli.
* Expiration kontrol edilmeli.
* Token imzası doğrulanmalı.
* Authentication bilgileri güvenli şekilde işlenmeli.
* Hata response'ları gereksiz teknik bilgi içermemeli.

### Geliştirici olarak çıkardığım sonuç:

> **Authentication başarısız olduğunda yalnızca erişimi reddetmek değil, bunu güvenli bir hata response'u ile yapmak da önemlidir.**

---

# 5. HTTP Method Kontrolü

## 🔴 Problem

Burp Repeater kullanarak:

```http
GET /rest/basket/1
```

isteğini:

```http
POST /rest/basket/1
```

şeklinde değiştirdim.

Uygulama `500 Internal Server Error` döndürdü ve response içerisinde backend hata bilgileri görüldü.

Burada iki ayrı konuyu değerlendirdim:

1. Endpoint'in hangi HTTP methodlarını kabul ettiği
2. Geçersiz method gönderildiğinde oluşan hata response'u

---

## ❌ Güvensiz yaklaşım

Geçersiz method geldiğinde uygulamanın beklenmeyen exception üretmesi ve ayrıntılı backend bilgisini client'a göndermesi.

---

## ✅ Güvenli yaklaşım

Endpoint yalnızca desteklediği HTTP methodlarını kabul etmelidir.

Desteklenmeyen bir method geldiğinde uygun bir HTTP response dönülmelidir.

Örneğin:

```text
GET       → izin ver
POST      → desteklenmiyor
             ↓
        uygun hata response'u
```

HTTP methodunun desteklenmediği durumlarda uygulamanın durumuna göre uygun bir `405 Method Not Allowed` response'u kullanılabilir.

Ayrıca hata response'unda:

* Stack trace,
* Backend path,
* Framework bilgisi

bulunmamalıdır.

---

# 6. XSS Açısından Geliştirici Yaklaşımı

Bu çalışmada yaptığımız testte belirli bir endpoint üzerinde XSS'i doğrulamadım.

Ancak hocanın verdiği görev kapsamında XSS'e karşı geliştirici olarak nasıl önlem alınması gerektiğini de araştırdım.

## ❌ Güvensiz yaklaşım

Kullanıcı girdisini doğrudan HTML içerisine yazmak:

```text
userInput
    ↓
innerHTML
    ↓
HTML
```

şeklinde kullanılmamalıdır.

Özellikle kullanıcı kontrollü verinin güvenli olmayan şekilde DOM'a eklenmesi DOM XSS riskine neden olabilir.

---

## ✅ Güvenli yaklaşım

### Output Encoding

Kullanıcı girdisi HTML olarak yorumlanmak yerine güvenli metin olarak işlenmelidir.

Örneğin DOM tarafında mümkün olduğunca:

```text
textContent
```

gibi güvenli yöntemler tercih edilmelidir.

### Input Validation

Beklenen veri formatı belirlenmeli ve gereksiz inputlar reddedilmelidir.

Ancak yalnızca input validation'a güvenilmemelidir.

### Content Security Policy

Uygun bir **Content Security Policy (CSP)** yapılandırması ek bir savunma katmanı oluşturabilir.

### Güvenli DOM Kullanımı

Kullanıcı kontrollü veriler:

```text
User Input
    ↓
Validation
    ↓
Safe Processing
    ↓
Output Encoding
    ↓
HTML / DOM
```

şeklinde işlenmelidir.

### Geliştirici olarak çıkardığım sonuç:

> **XSS'e karşı temel yaklaşım kullanıcı girdisini güvenli şekilde encode etmek ve DOM'a güvenli yöntemlerle eklemektir. CSP ise ek bir savunma katmanıdır.**

---

# 📊 Genel Çözüm Tablosu

| Bulgu                      | Güvensiz yaklaşım                                     | Güvenli yaklaşım                               |
| -------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| **Broken Access Control**  | ID'yi alıp doğrudan kaynağı döndürmek                 | Server-side ownership + authorization          |
| **SQL Injection**          | Inputu SQL sorgusuna doğrudan eklemek                 | Parameterized Query + Prepared Statement       |
| **Information Disclosure** | Stack trace'i client'a göndermek                      | Genel hata + server-side logging               |
| **Authentication**         | Hatalı authentication'da ayrıntılı hata döndürmek     | Güvenli authentication middleware + genel hata |
| **HTTP Method**            | Desteklenmeyen methodda ayrıntılı exception döndürmek | Method kontrolü + uygun HTTP response          |
| **XSS**                    | Kullanıcı girdisini doğrudan HTML/DOM'a yazmak        | Output Encoding + güvenli DOM kullanımı + CSP  |

---

# 🧠 Saldırgan ve Geliştirici Perspektifleri

Bu çalışmadan önce güvenlik testini daha çok:

```text
İstek gönder
     ↓
Açık bul
     ↓
Sonucu incele
```

şeklinde düşünüyordum.

Bu çalışmada ise aynı problemi iki taraftan değerlendirmeye başladım:

```text
                 WEB UYGULAMASI
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
     SALDIRGAN                  GELİŞTİRİCİ
          │                         │
     Nasıl kötüye                 Neden oluştu?
     kullanılabilir?              │
          │                       │
          ▼                       ▼
        TEST                    ÇÖZÜM
          │                       │
          └───────────┬───────────┘
                      ▼
              GÜVENLİ UYGULAMA
```

## 🎯 Sonuç

Bu çalışmada güvenlik açıklarının yalnızca saldırgan açısından incelenmemesi gerektiğini öğrendim.

Bir güvenlik uzmanı olarak:

> **"Bu açık nasıl çalışıyor?"**

sorusunun yanında,

> **"Bu açık kod ve sistem mimarisi seviyesinde neden oluştu ve nasıl kalıcı olarak engellenebilir?"**

sorusunu da cevaplayabilmem gerektiğini gördüm.

Benim için temel yaklaşım şu hale geldi:

```text
Zafiyeti bul
    ↓
Kök nedeni anla
    ↓
Güvensiz yaklaşımı belirle
    ↓
Güvenli yaklaşımı tasarla
    ↓
Düzeltmeyi uygula
    ↓
Tekrar test et
```

Böylece penetration testing çalışmasının yalnızca saldırı simülasyonu değil, aynı zamanda **geliştiriciye uygulanabilir güvenlik çözümü üretme süreci** olduğunu öğrendim.
