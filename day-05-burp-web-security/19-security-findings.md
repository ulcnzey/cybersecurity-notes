# 19. Profesyonel Güvenlik Bulguları

## 🎯 Amaç

Bu çalışmada OWASP Juice Shop üzerinde gerçekleştirdiğim güvenlik testlerinden elde ettiğim en önemli üç bulguyu, profesyonel penetration testing raporlarında kullanılan bir formatta hazırladım.

Her bulguyu:

* Başlık
* Açıklama
* Etkilenen Alan
* Teknik Detay
* Proof of Concept
* Etki
* Risk
* Çözüm Önerisi
* Referans

başlıkları altında değerlendirdim.

Testler yalnızca **yerel OWASP Juice Shop laboratuvar ortamında** gerçekleştirilmiştir.

---

# Finding 01 — IDOR / Broken Access Control ile Başka Kullanıcıya Ait Basket Verisine Erişim

## Başlık

**Basket ID Manipülasyonu ile Yetkisiz Kullanıcı Verisine Erişim**

## Açıklama

Uygulamanın basket endpoint'inde kullanılan ID değerinin değiştirilmesiyle farklı bir kullanıcıya ait basket kaynağına erişilebildiğini gözlemledim.

Kullanıcının kendi basket kaynağı yerine başka bir ID ile istek göndermesi durumunda sunucu isteği engellemek yerine ilgili basket verisini döndürdü.

Bu durum, sunucu tarafında kaynak sahipliği ve authorization kontrolünün yeterince uygulanmadığını göstermektedir.

OWASP, URL veya parametrelerdeki ID değerlerinin değiştirilmesiyle başka kullanıcıların kaynaklarına erişilmesini Broken Access Control kapsamında değerlendirmektedir.

## Etkilenen Alan

**Endpoint:**

```http
GET /rest/basket/{id}
```

**Kontrol edilen parametre:**

```text
basket ID
```

## Teknik Detay

Uygulama, istemciden gelen basket ID değerini kullanarak ilgili kaynağı belirlemektedir.

Ancak sunucu tarafında:

```text
İstek yapan kullanıcı
        ↓
İstenen basket
        ↓
Basket gerçekten bu kullanıcıya mı ait?
```

şeklinde bir sahiplik kontrolünün yeterli şekilde uygulanmadığı gözlemlendi.

Bu nedenle kullanıcı, URL içerisindeki ID değerini değiştirerek başka bir basket kaynağına erişebildi.

Bu durum **Broken Access Control** ve özellikle kullanıcı tarafından kontrol edilen bir ID üzerinden authorization kontrolünün atlanması ile ilişkilidir.

## Proof of Concept

İlk olarak mevcut basket isteğini Burp Suite üzerinde yakaladım:

```http
GET /rest/basket/1
```

Daha sonra Burp Repeater içerisinde yalnızca basket ID değerini değiştirdim:

```http
GET /rest/basket/2
```

Sunucu isteği reddetmek yerine:

```text
200 OK
```

cevabı verdi ve farklı bir basket kaynağına ait bilgiler döndürdü.

Response içerisinde basket ID'sinin yanında farklı bir `UserId` bilgisi de görüldü.

Test sırasında gerçek bir kullanıcı verisini değiştirmedim veya silmedim; yalnızca erişim kontrolünü görüntüleme seviyesinde değerlendirdim.

## Etki

Bu zafiyet gerçek bir uygulamada:

* Başka kullanıcıların basket bilgilerinin görüntülenmesine,
* Kullanıcıya ait ürün bilgilerinin açığa çıkmasına,
* Yetkisiz veri erişimine

neden olabilir.

Test sırasında yalnızca veri görüntüleme davranışını doğruladım. Başka bir kullanıcının verisini değiştirme veya silme gerçekleştirmedim.

## Risk

**Risk: Yüksek**

Bulgunun temel etkisi **Confidentiality (Gizlilik)** üzerindedir.

Bir kullanıcının yalnızca kendi kaynağına erişmesi gerekirken başka kullanıcıların kaynaklarına erişebilmesi, uygulamanın authorization modelinde önemli bir kontrol problemi olduğunu gösterir.

OWASP Broken Access Control kategorisi; başka kullanıcıların kaynaklarına erişme ve ID değerlerini manipüle ederek erişim kontrollerini atlama gibi durumları kapsamaktadır.

## Çözüm Önerisi

Authorization kontrolü yalnızca frontend tarafında yapılmamalıdır.

Sunucu tarafında her kaynak erişiminde sahiplik kontrolü yapılmalıdır:

```text
Authenticated User
       ↓
Requested Resource
       ↓
Ownership Check
       ↓
 ┌───────────────┐
 │               │
Kendi kaynağı   Başkasının kaynağı
 │               │
 ↓               ↓
200             403
```

Ayrıca:

* Access control kontrolleri server-side uygulanmalı,
* Kullanıcı yalnızca yetkili olduğu kaynaklara erişebilmeli,
* ID değerlerinin tahmin edilemez olması tek başına güvenlik kontrolü olarak kullanılmamalı,
* Access control için otomatik testler oluşturulmalıdır.

OWASP da record ownership kontrollerinin server-side uygulanmasını önermektedir.

## Referans

* **OWASP Top 10:2021 – A01: Broken Access Control**
* **CWE-639 – Authorization Bypass Through User-Controlled Key**
* **CWE-862 – Missing Authorization**

---

# Finding 02 — SQL Injection Göstergesi

## Başlık

**Login Endpoint'inde SQL Injection Göstergesi**

## Açıklama

Juice Shop login endpoint'inde bulunan `email` parametresini Burp Suite Repeater kullanarak kontrollü şekilde test ettim.

Özel karakter içeren bir input gönderdiğimde uygulamanın SQL/database katmanında hata oluştuğunu ve `500 Internal Server Error` döndürdüğünü gözlemledim.

Response içerisinde SQLite ve Sequelize ile ilgili teknik hata bilgileri de açığa çıktı.

Bu davranış, kullanıcı tarafından kontrol edilen inputun SQL işleme sürecini etkileyebildiğine dair güçlü bir SQL Injection göstergesidir.

OWASP Top 10:2021 içerisinde SQL Injection, **A03:2021 – Injection** kategorisi altında yer almaktadır.

## Etkilenen Alan

**Endpoint:**

```http
POST /rest/user/login
```

**Parameter:**

```text
email
```

## Teknik Detay

Login isteği JSON formatında kullanıcı bilgileri içermektedir:

```json
{
  "email": "<user-input>",
  "password": "<password>"
}
```

Kontrollü test sırasında `email` parametresine özel karakter içeren bir input gönderildiğinde uygulama SQL/database seviyesinde hata oluşturdu.

Bu davranış, inputun güvenli şekilde ayrıştırılmadığı veya SQL sorgusunun kullanıcı kontrollü veriden etkilenebildiği ihtimalini ortaya çıkardı.

OWASP, kullanıcıdan gelen verinin uygun şekilde validate/filter edilmemesi veya parameterized query kullanılmadan dinamik sorgulara dahil edilmesini Injection riskiyle ilişkilendirmektedir.

## Proof of Concept

Burp Suite üzerinde login isteğini Repeater'a gönderdim.

Orijinal endpoint:

```http
POST /rest/user/login
```

Daha sonra yalnızca `email` parametresini kontrollü şekilde değiştirdim.

Test inputu:

```text
test'
```

Password alanındaki gerçek değer rapora dahil edilmemiştir.

Sunucunun cevabı:

```http
500 Internal Server Error
```

oldu.

Response içerisinde:

* SQLite ile ilgili hata,
* Sequelize database hatası,
* SQL işlemine ilişkin teknik bilgiler

görüldü.

Bu çalışma sırasında veri çıkarma, kullanıcı hesabı ele geçirme veya veritabanı üzerinde değişiklik yapma gerçekleştirmedim.

## Etki

SQL Injection'ın doğrulanması ve uygun şekilde sömürülebilmesi durumunda saldırganın:

* Veritabanındaki yetkisiz verilere erişmesi,
* Verileri değiştirmesi,
* Uygulamanın authentication mekanizmasını etkilemesi,
* Bazı senaryolarda veritabanının kullanılabilirliğini etkilemesi

mümkün olabilir.

Ancak bu laboratuvar çalışmasında yalnızca kontrollü bir hata davranışı doğrulanmıştır. Bu nedenle veri çıkarma veya hesap ele geçirme gerçekleşmiş gibi değerlendirme yapmıyorum.

## Risk

**Risk: Yüksek**

Login gibi kritik bir endpoint üzerinde SQL işleme sürecini etkileyen input davranışı gözlemlenmesi nedeniyle önemlidir.

Bununla birlikte kesin sömürülebilirlik derecesi için kaynak kod incelemesi veya kontrollü ileri doğrulama gibi ek çalışmalar gerekebilir.

## Çözüm Önerisi

SQL sorgularında kullanıcı girdisi doğrudan sorgu içerisine eklenmemelidir.

Bunun yerine:

### Parameterized Query

```text
SQL Query
   +
Parameter
   ↓
Database
```

yaklaşımı kullanılmalıdır.

Ayrıca:

* Prepared Statement kullanılmalı,
* ORM/database sorguları güvenli şekilde yapılandırılmalı,
* Input validation defense-in-depth olarak uygulanmalı,
* Database hesabına minimum yetki verilmeli,
* Kullanıcıya SQL/database stack trace gösterilmemeli,
* Hatalar güvenli şekilde server-side loglanmalıdır.

OWASP Injection için parameterized sorgular ve güvenli input işleme yaklaşımlarını önermektedir.

## Referans

* **OWASP Top 10:2021 – A03: Injection**
* **CWE-89 – Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')**
* **OWASP SQL Injection Prevention Cheat Sheet**

---

# Finding 03 — Ayrıntılı Hata Mesajları ve Stack Trace Bilgi Sızıntısı

## Başlık

**Ayrıntılı Hata Mesajları ile Backend Bilgilerinin Açığa Çıkması**

## Açıklama

Juice Shop üzerinde gerçekleştirdiğim farklı testlerde hata oluştuğunda uygulamanın kullanıcıya gereğinden fazla teknik bilgi döndürdüğünü gözlemledim.

Özellikle:

* Framework bilgileri,
* Backend dosya yolları,
* Stack trace,
* Database/framework hata bilgileri,
* Endpoint'e ilişkin teknik hata mesajları

response içerisinde görülebiliyordu.

OWASP A05:2021 Security Misconfiguration kapsamında stack trace ve aşırı bilgilendirici hata mesajlarının kullanıcıya gösterilmesini güvenlik problemi olarak ele almaktadır.

## Etkilenen Alan

Farklı HTTP request'lerinde oluşan error response'ları.

Örnek olarak:

```http
GET /rest/basket/{id}
```

isteğinde Authorization header'ının kaldırılması ve HTTP methodunun değiştirilmesi sırasında oluşan hata response'ları incelendi.

Ayrıca SQL Injection testinde oluşan database error response'u da aynı problemi göstermektedir.

## Teknik Detay

Uygulama, beklenmeyen veya hatalı bir request sonucunda kullanıcıya yalnızca genel bir hata mesajı vermek yerine backend hakkında ayrıntılı bilgiler döndürmektedir.

Örneğin Repeater testinde Authorization header'ını kaldırdığımda:

```http
401 Unauthorized
```

cevabının yanında ayrıntılı hata bilgileri görüldü.

HTTP methodunu değiştirdiğim başka bir testte ise:

```http
500 Internal Server Error
```

response'u içerisinde backend dosya yolları ve stack trace bilgileri görüldü.

Bu bilgiler uygulamanın iç mimarisinin anlaşılmasını kolaylaştırabilir.

## Proof of Concept

### Test 1 — Authorization Header

Normal istekte:

```http
Authorization: Bearer <token>
```

header'ı bulunuyordu.

Header'ı kaldırıp isteği tekrar gönderdiğimde:

```http
401 Unauthorized
```

aldım.

Ancak response içerisinde ayrıntılı framework/hata bilgileri de yer aldı.

### Test 2 — HTTP Method

Normal:

```http
GET /rest/basket/1
```

isteğini:

```http
POST /rest/basket/1
```

şeklinde değiştirdim.

Sonuç:

```http
500 Internal Server Error
```

Response içerisinde backend dosya yolları ve stack trace bilgileri görüldü.

### Test 3 — SQL Error

Kontrollü SQL Injection testinde de database/framework hata bilgileri response içerisinde açığa çıktı.

Bu üç testteki ortak problem:

> **Uygulamanın hata response'larında kullanıcıya gereğinden fazla teknik bilgi vermesi.**

## Etki

Açığa çıkan bilgiler doğrudan sisteme erişim sağlamaz.

Ancak saldırganın:

* Kullanılan framework'ü,
* Backend mimarisini,
* Database teknolojisini,
* Dosya yapısını,
* Hata oluşan endpointleri

anlamasını kolaylaştırabilir.

Bu bilgiler başka güvenlik açıklarının araştırılması sırasında yardımcı olabilir.

## Risk

**Risk: Orta**

Bu bulgunun etkisi tek başına sınırlı olabilir.

Ancak bilgi sızıntısı başka zafiyetlerle birleştirildiğinde saldırganın uygulamayı daha iyi tanımasına ve sonraki saldırılarını daha hedefli gerçekleştirmesine yardımcı olabilir.

OWASP, ayrıntılı stack trace ve hata mesajlarının altyapı hakkında hassas teknik bilgiler açığa çıkarabileceğini belirtmektedir.

## Çözüm Önerisi

Production ortamında ayrıntılı hata bilgileri istemciye gönderilmemelidir.

Bunun yerine:

```text
Kullanıcı
   ↓
Genel hata mesajı
   ↓
"Bir hata oluştu."
```

şeklinde güvenli bir response verilmelidir.

Teknik hata detayları ise:

```text
Application
     ↓
Server-side Log
     ↓
Security/Development Team
```

şeklinde yalnızca yetkili kişilerin erişebileceği log sistemlerinde tutulmalıdır.

Ayrıca:

* Production ortamında debug modu kapatılmalı,
* Stack trace response'a eklenmemeli,
* Backend dosya yolları kullanıcıya gösterilmemeli,
* Database/framework hata detayları gizlenmeli,
* Merkezi ve güvenli logging kullanılmalıdır.

OWASP A05:2021, güvenli configuration ve error handling süreçlerinin uygulanmasını önermektedir.

## Referans

* **OWASP Top 10:2021 – A05: Security Misconfiguration**
* **CWE-209 – Generation of Error Message Containing Sensitive Information**
* **CWE-497 – Exposure of System Data to an Unauthorized Control Sphere**

---

# 📋 Genel Değerlendirme

Bu üç bulguyu raporlarken her birinin farklı bir güvenlik problemini temsil etmesine dikkat ettim:

| Finding | Kategori                        | Temel Problem                                    |
| ------- | ------------------------------- | ------------------------------------------------ |
| **01**  | A01 – Broken Access Control     | Kullanıcı başka kaynağa erişebiliyor             |
| **02**  | A03 – Injection                 | Input SQL işleme sürecini etkiliyor              |
| **03**  | A05 – Security Misconfiguration | Hata response'larında teknik bilgi açığa çıkıyor |

Bu üç bulgu farklı güvenlik katmanlarını göstermektedir:

```text
                    WEB APPLICATION
                          │
          ┌───────────────┼───────────────┐
          │               │               │
          ▼               ▼               ▼
   Access Control     Input Handling    Error Handling
          │               │               │
          ▼               ▼               ▼
        A01             A03             A05
          │               │               │
       IDOR/           SQLi          Information
       BAC                            Disclosure
```

## 🧠 Bu Çalışmadan Öğrendiklerim

Profesyonel bir penetration testing raporunda sadece:

> "Şurada açık var."

demek yeterli değildir.

Bir bulguyu anlaşılır şekilde anlatabilmek için:

```text
Ne buldum?
     ↓
Nerede?
     ↓
Teknik olarak neden oluşuyor?
     ↓
Nasıl doğruladım?
     ↓
Saldırgan ne yapabilir?
     ↓
Neden önemli?
     ↓
Nasıl düzeltilir?
```

sorularının tamamına cevap vermek gerekir.

Bu nedenle bu çalışmada daha önce yaptığım laboratuvar testlerini, geliştirici veya sistem yöneticisinin okuyup problemi anlayabileceği profesyonel bir rapor formatına dönüştürdüm.

## 🎯 Sonuç

Bu çalışma sonunda OWASP Juice Shop üzerinde yaptığım testlerden üç önemli güvenlik bulgusunu profesyonel penetration testing raporu formatında belgeledim.

En önemli kazanımım, teknik bir test sonucunu yalnızca teknik olarak açıklamak yerine, **bulgu → kanıt → etki → risk → çözüm** zinciri içerisinde raporlayabilmek oldu.

🔗 Kullandığımız resmi referanslar
OWASP A01 – Broken Access Control
OWASP A03 – Injection
OWASP A05 – Security Misconfiguration
OWASP Top 10:2021
