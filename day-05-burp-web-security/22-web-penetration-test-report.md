# WEB APPLICATION SECURITY ASSESSMENT

**Test Environment:** OWASP Juice Shop
**Test Type:** Web Application Security Assessment
**Testing Environment:** Local / Controlled Laboratory
**Target:** `localhost:3000`
**Tools:** Kali Linux, Burp Suite, Browser
**Assessment Focus:** HTTP traffic, API endpoints, authentication, authorization, input validation and common web application vulnerabilities

---

# 1. Executive Summary

Bu çalışmada, güvenlik eğitimi amacıyla geliştirilmiş ve kasıtlı olarak güvenlik açıkları içeren **OWASP Juice Shop** uygulaması üzerinde kontrollü bir web güvenlik değerlendirmesi gerçekleştirdim.

OWASP Juice Shop, eğitim, CTF ve güvenlik araçlarının test edilmesi amacıyla kullanılan, gerçek web uygulamalarında görülebilecek birçok güvenlik problemini barındıran bir uygulamadır.

Test sürecinde öncelikle uygulamanın HTTP trafiğini Burp Suite üzerinden gözlemledim. Daha sonra endpoint keşfi, authentication, authorization, kullanıcı girdilerinin işlenmesi, hata mesajları ve HTTP response davranışları üzerinde kontrollü testler gerçekleştirdim.

Çalışma sonucunda özellikle aşağıdaki güvenlik problemleri gözlemlendi:

1. **Broken Access Control / IDOR**
2. **SQL Injection göstergesi**
3. **Detailed Error Messages / Information Disclosure**

Bunlara ek olarak authentication mekanizması, JWT kullanımı, HTTP status kodları ve çeşitli API endpointleri incelendi.

Test sırasında her anormal davranışı doğrudan güvenlik açığı olarak kabul etmedim. Bulguları mümkün olduğunca tekrar ederek doğrulamaya ve teknik etkilerini değerlendirmeye çalıştım.

Örneğin XSS konusunda bir challenge ve ilgili arama endpointi incelendi ancak yapılan test sonucunda girdinin response içerisinde doğrudan yansımadığı görüldü. Bu nedenle bu çalışma kapsamında **doğrulanmış bir XSS bulgusu raporlamadım**.

---

# 2. Scope

## 2.1 Test Edilen Sistem

Test yalnızca aşağıdaki kontrollü laboratuvar ortamında gerçekleştirilmiştir:

```text
Application: OWASP Juice Shop
Host: localhost
Port: 3000
Target: http://localhost:3000
```

Test kapsamında:

* OWASP Juice Shop
* Kali Linux
* Burp Suite
* Web Browser

kullanılmıştır.

## 2.2 Scope Dışı

Aşağıdaki sistemler test kapsamına dahil edilmemiştir:

* Gerçek şirket sistemleri
* Kamuya açık web siteleri
* Üçüncü taraf uygulamalar
* Yetkisiz sistemler
* Juice Shop dışındaki sistemler

Tüm güvenlik testleri kontrollü laboratuvar ortamında gerçekleştirilmiştir.

---

# 3. Methodology

Test sürecinde aşağıdaki metodolojiyi kullandım:

```text
Scope
  ↓
Reconnaissance
  ↓
Application Mapping
  ↓
Endpoint Discovery
  ↓
Authentication Testing
  ↓
Authorization Testing
  ↓
Input Validation Testing
  ↓
Vulnerability Identification
  ↓
Validation
  ↓
Risk Assessment
  ↓
Reporting
  ↓
Remediation
```

## 3.1 Scope

Öncelikle test edilecek sistem ve test sınırlarını belirledim.

## 3.2 Reconnaissance

Uygulamanın temel özelliklerini ve çalışma mantığını inceledim.

## 3.3 Application Mapping

Uygulamadaki temel işlevleri ve tarayıcı ile sunucu arasındaki iletişimi gözlemledim.

## 3.4 Endpoint Discovery

Burp Suite HTTP History üzerinden kullanılan API ve REST endpointlerini belirledim.

## 3.5 Authentication Testing

Login işlemini, session/token kullanımını ve JWT yapısını inceledim.

## 3.6 Authorization Testing

Kullanıcıların erişebileceği kaynaklar üzerinde yetki kontrollerini değerlendirdim.

## 3.7 Input Validation Testing

Login, URL parametreleri ve JSON body gibi kullanıcı girdilerinin nasıl işlendiğini test ettim.

## 3.8 Vulnerability Identification

Elde edilen davranışları güvenlik açısından değerlendirerek olası zafiyetleri belirledim.

## 3.9 Validation

Burp Suite Repeater kullanarak istekleri değiştirdim, tekrar gönderdim ve response'ları karşılaştırdım.

## 3.10 Risk Assessment

Bulguları gizlilik, bütünlük, kullanılabilirlik, olasılık ve iş etkisi açısından değerlendirdim.

## 3.11 Reporting

Doğrulanmış veya güçlü göstergesi bulunan bulguları teknik detaylarıyla raporladım.

## 3.12 Remediation

Her bulgu için geliştirici açısından uygulanabilecek çözüm önerilerini belirledim.

---

# 4. Tools

## 4.1 Kali Linux

Güvenlik testlerinin gerçekleştirildiği çalışma ortamı olarak kullanıldı.

## 4.2 Burp Suite

HTTP/HTTPS trafiğini yakalamak, incelemek ve değiştirmek için kullanıldı.

Kullanılan temel Burp Suite bileşenleri:

* Proxy
* HTTP History
* Repeater
* Intruder
* Decoder
* Comparer

Özellikle **Proxy + HTTP History** trafik analizi için, **Repeater** ise kontrollü request değişiklikleri ve doğrulama için kullanıldı.

## 4.3 Web Browser

OWASP Juice Shop ile normal kullanıcı etkileşimini gerçekleştirmek ve tarayıcı tarafından oluşturulan HTTP isteklerini gözlemlemek için kullanıldı.

---

# 5. Application Mapping

Test sırasında Burp Suite üzerinden çeşitli endpointler gözlemlendi.

| Method | Endpoint                              | Amaç / Gözlem                        |
| ------ | ------------------------------------- | ------------------------------------ |
| POST   | `/rest/user/login`                    | Kullanıcı login işlemi               |
| GET    | `/rest/basket/1`                      | Sepet verisi                         |
| GET    | `/rest/products/2/reviews`            | Ürün yorumları                       |
| PUT    | `/rest/products/1/reviews`            | Ürün yorumuyla ilgili işlem          |
| POST   | `/rest/products/reviews`              | Ürün yorumlarıyla ilgili işlem       |
| GET    | `/api/Quantitys/`                     | API kaynağı                          |
| GET    | `/api/Challenges/?name=Score%20Board` | Challenge/Score Board ile ilgili API |
| POST   | `/socket.io/`                         | Socket.IO polling iletişimi          |

## 5.1 Parametreler

Test sırasında farklı parametre türleri gözlemlendi:

### Path Parameter

```text
/rest/basket/1
/rest/products/2/reviews
```

Buradaki sayısal değerler kaynak kimliği olarak kullanılıyor.

### Query Parameter

```text
/api/Challenges/?name=Score%20Board
```

ve:

```text
/rest/products/search?q=test123
```

örneklerinde query parametreleri gözlemlendi.

### JSON Body

Login ve review gibi işlemlerde JSON body kullanıldığı görüldü.

Örneğin login isteğinde:

```json
{
  "email": "<redacted>",
  "password": "<redacted>"
}
```

şeklinde kullanıcı bilgileri gönderildi.

Gerçek test sırasında kullanılan parola, token ve cookie değerleri güvenlik nedeniyle bu raporda paylaşılmamıştır.

---

# 6. Authentication Analysis

Authentication testinde öncelikle login endpointini inceledim.

```text
POST /rest/user/login
```

Login isteğinde kullanıcı bilgilerinin JSON body içerisinde gönderildiğini gözlemledim.

Başarılı login sonrasında uygulamanın token tabanlı authentication kullandığını ve sonraki isteklerde:

```http
Authorization: Bearer <token>
```

formatının kullanıldığını gözlemledim.

## 6.1 JWT Analysis

Elde edilen token'ın JWT formatında olduğu görüldü:

```text
Header.Payload.Signature
```

JWT header bölümünde:

```json
{
  "typ": "JWT",
  "alg": "RS256"
}
```

bilgileri gözlemlendi.

JWT payload bölümünde kullanıcıya ilişkin çeşitli claim'ler bulunuyordu.

Önemli olarak JWT'nin Base64URL encoding kullandığı ve payload bölümünün **şifrelenmiş veri olmadığı** değerlendirildi.

JWT'nin bütünlüğü signature mekanizması ile korunmaktadır.

## 6.2 Authorization Header Testi

Burp Suite Repeater kullanılarak `Authorization` header'ı kaldırıldığında:

```text
401 Unauthorized
```

response'u alındı.

Bu sonuç, ilgili endpointin authentication kontrolü uyguladığını gösterdi.

Ancak response içerisinde teknik hata bilgilerinin bulunması ayrıca **Information Disclosure / Error Handling** açısından değerlendirildi.

---

# 7. Authorization Analysis

Authorization testlerinde authentication ile authorization arasındaki farkı özellikle değerlendirdim.

Authentication:

> Kullanıcının kim olduğunu doğrulamak.

Authorization:

> Kullanıcının belirli bir kaynağa veya işleve erişme yetkisinin olup olmadığını kontrol etmek.

## 7.1 Basket ID Manipulation

Normal request:

```http
GET /rest/basket/1
```

şeklindeydi.

Burp Suite Repeater üzerinden yalnızca basket ID değerini değiştirerek:

```http
GET /rest/basket/2
```

isteğini gönderdim.

Response sonucunda farklı bir basket kaynağının ve farklı bir `UserId` değerinin döndüğünü gözlemledim.

Bu durum, kaynak ID'sinin değiştirilmesiyle başka bir kullanıcıya ait kaynağın okunabildiğini gösterdi.

OWASP Top 10:2021 içerisinde bu tür erişim kontrol problemleri **A01: Broken Access Control** kapsamında değerlendirilmektedir. OWASP, kullanıcıların kendi yetkileri dışındaki kaynaklara benzersiz ID'leri değiştirerek erişebilmesini bu kategoriyle ilişkilendirmektedir.

---

# 8. Vulnerability Findings

Bu değerlendirmede aşağıdaki temel bulgular belirlenmiştir:

| ID   | Finding                                          | OWASP Category | Risk   |
| ---- | ------------------------------------------------ | -------------- | ------ |
| F-01 | Broken Access Control / IDOR                     | A01            | High   |
| F-02 | SQL Injection Indicator                          | A03            | High*  |
| F-03 | Detailed Error Messages / Information Disclosure | A05            | Medium |
| F-04 | Authentication Error Information Disclosure      | A05            | Medium |
| F-05 | Backend Path / Stack Trace Disclosure            | A05            | Medium |

* F-02 için **tam SQL Injection sömürüsü veya veri çıkarma gerçekleştirilmemiştir**. Risk değerlendirmesi, gözlenen SQL hata davranışı ve potansiyel etkisi üzerinden yapılmıştır.

A05 kapsamında özellikle kullanıcıya stack trace veya aşırı teknik hata mesajlarının gösterilmesi OWASP tarafından Security Misconfiguration örnekleri arasında belirtilmektedir.

F-03, F-04 ve F-05 teknik olarak farklı response'larda gözlemlense de aynı temel problem olan **aşırı detaylı hata yönetimi / information disclosure** altında birlikte ele alınabilir.

---

# 9. Detailed Findings

## F-01 — Broken Access Control / IDOR

### Description

Uygulamanın basket endpointinde kullanılan kaynak ID'si değiştirildiğinde başka bir kullanıcıya ait basket verisinin döndüğü gözlemlendi.

### Affected Endpoint

```text
GET /rest/basket/{id}
```

### Test

Normal:

```text
GET /rest/basket/1
```

Değiştirilmiş:

```text
GET /rest/basket/2
```

### Observation

ID değiştirildiğinde response içerisinde farklı basket ve kullanıcı bilgileri döndü.

### Impact

Bu durum, uygun server-side ownership kontrolü bulunmadığında bir kullanıcının başka kullanıcıların kaynaklarına erişmesine neden olabilir.

Özellikle kullanıcıya ait sepet verilerinin gizliliği açısından risk oluşturur.

OWASP A01, erişim kontrolünün server-side uygulanmasını ve kaynak sahipliğinin doğrulanmasını önermektedir.

### Risk

**High**

### Remediation

Sunucu tarafında:

```text
Authenticated User
        ↓
Requested Resource
        ↓
Ownership Check
        ↓
Authorized?
   ↙          ↘
 Yes           No
 ↓             ↓
Allow         403
```

şeklinde bir kontrol uygulanmalıdır.

Kullanıcı yalnızca kendisine ait basket kaynaklarına erişebilmelidir.

Frontend tarafında ID gizlemek güvenlik kontrolü olarak kabul edilmemelidir.

---

## F-02 — SQL Injection Indicator

### Description

Login endpointindeki `email` parametresine kontrollü SQL karakteri gönderildiğinde uygulamanın `500 Internal Server Error` verdiği ve response içerisinde SQLite, Sequelize ve SQL sorgusuyla ilişkili teknik hata bilgilerinin bulunduğu gözlemlendi.

### Affected Endpoint

```text
POST /rest/user/login
```

### Affected Parameter

```text
email
```

### Test

Normal kullanıcı girdisi yerine kontrollü olarak SQL sözdizimini etkileyebilecek bir karakter kullanıldı.

### Observation

Response:

```text
500 Internal Server Error
```

oldu.

Response içerisinde:

* SQLite
* Sequelize
* SQL hata bilgileri
* Backend stack trace

gibi teknik bilgiler gözlemlendi.

### Impact

Bu davranış kullanıcı girdisinin SQL katmanını etkileyebildiğine dair güçlü bir gösterge oluşturur.

Ayrıca hata mesajı veritabanı ve backend teknolojileri hakkında saldırgana teknik bilgi sağlayabilir.

Ancak bu çalışma kapsamında:

* Tam veri çıkarma
* Authentication bypass'ın kesin olarak gösterilmesi
* Veritabanı üzerinde değişiklik
* Sunucu ele geçirme

gerçekleştirilmedi.

Bu nedenle bulgu **SQL Injection göstergesi** olarak raporlanmıştır.

### Risk

**High**

### Remediation

Kullanıcı girdisi SQL sorgusuna doğrudan birleştirilmemelidir.

Güvenli yaklaşım:

```text
User Input
    ↓
Parameterized Query
    ↓
Database
```

veya:

```text
Prepared Statement
```

kullanılmasıdır.

Ek olarak database hesabına minimum gerekli yetkiler verilmelidir.

---

## F-03 — Detailed Error Messages / Information Disclosure

### Description

Bazı hatalı veya değiştirilmiş isteklerde uygulamanın kullanıcıya ayrıntılı teknik hata bilgileri döndürdüğü gözlemlendi.

Örneğin:

* Express bilgileri
* Juice Shop backend bilgileri
* Sequelize / SQLite bilgileri
* Backend dosya yolları
* Stack trace

response içerisinde görülebildi.

### Örnek Durumlar

Authorization header kaldırıldığında:

```text
401 Unauthorized
```

ile birlikte teknik hata bilgileri döndü.

HTTP metodunun değiştirilmesi sonucunda:

```text
500 Internal Server Error
```

ve backend path/stack bilgileri içeren response alındı.

### Impact

Saldırgan uygulamanın:

* kullandığı frameworkleri,
* veritabanı teknolojisini,
* backend yapısını,
* dosya yollarını

öğrenebilir.

Bu bilgiler tek başına sistem ele geçirme anlamına gelmez ancak sonraki saldırılar için reconnaissance bilgisini artırabilir.

OWASP A05, stack trace veya aşırı detaylı hata mesajlarının kullanıcıya gösterilmesini güvenlik yapılandırma problemi olarak örneklemektedir.

### Risk

**Medium**

### Remediation

Production ortamında kullanıcıya:

```text
Internal Server Error
```

gibi genel hata mesajları gösterilmelidir.

Detaylı hata bilgileri ise yalnızca güvenli server-side loglarda tutulmalıdır.

Ayrıca:

* Debug mode kapatılmalı
* Stack trace response'tan kaldırılmalı
* Backend path bilgileri gizlenmeli
* Database/framework bilgileri response'a verilmemeli

---

# 10. Risk Assessment

Risk değerlendirmesinde yalnızca “açık var mı?” sorusuna bakmadım.

Her bulgu için:

* Etkilenen varlık
* Confidentiality
* Integrity
* Availability
* Olasılık
* Etki
* İş etkisi

değerlendirildi.

| Finding                     | Confidentiality | Integrity      | Availability     | Likelihood  | Risk   |
| --------------------------- | --------------- | -------------- | ---------------- | ----------- | ------ |
| F-01 IDOR                   | High            | Low/Unknown    | Low              | High        | High   |
| F-02 SQLi Indicator         | High potential  | High potential | Medium potential | Medium-High | High*  |
| F-03 Information Disclosure | Medium          | Low            | Low              | High        | Medium |

* SQL Injection için tam exploit doğrulanmadığından risk değerlendirmesi kontrollü bir laboratuvar değerlendirmesidir.

## 10.1 Confidentiality

Yetkisiz kişilerin bilgiye erişebilmesi durumudur.

F-01 için temel etki gizlilik üzerindedir çünkü farklı kullanıcıya ait basket verisi okunabilmiştir.

## 10.2 Integrity

Verilerin yetkisiz şekilde değiştirilmesi veya manipüle edilmesidir.

SQL Injection'ın tam olarak doğrulanması halinde veri bütünlüğü açısından daha geniş etkiler oluşabilir. Ancak bu çalışmada bu aşama doğrulanmamıştır.

## 10.3 Availability

Sistemin kullanılabilirliğinin etkilenmesidir.

Bu çalışmada doğrudan kalıcı bir servis kesintisi oluşturulmamıştır.

## 10.4 Business Impact

Gerçek bir uygulamada bu bulguların etkisi yalnızca teknik açıdan değil, iş açısından da değerlendirilmelidir.

Örneğin:

* Kullanıcı verilerinin gizliliği
* Müşteri güveni
* Veri koruma yükümlülükleri
* Operasyonel süreçler
* İtibar
* Olası finansal kayıplar

dikkate alınmalıdır.

---

# 11. Remediation

## 11.1 Broken Access Control / IDOR

Her kaynak için server-side authorization ve ownership kontrolü uygulanmalıdır.

```text
Request
 ↓
Authentication
 ↓
Authorization
 ↓
Ownership Check
 ↓
Resource
```

Kullanıcı yetkili değilse:

```text
403 Forbidden
```

döndürülmelidir.

OWASP da erişim kontrolünün güvenilir server-side kod üzerinde uygulanmasını ve kaynak sahipliğinin doğrulanmasını önermektedir.

---

## 11.2 SQL Injection

Kullanıcı girdileri SQL sorgularına doğrudan eklenmemelidir.

Bunun yerine:

* Parameterized queries
* Prepared statements
* ORM güvenli kullanım prensipleri
* Database least privilege

uygulanmalıdır.

Ayrıca SQL hata mesajları doğrudan kullanıcıya gönderilmemelidir.

---

## 11.3 Information Disclosure

Hata yönetimi production ortamına uygun hale getirilmelidir.

Güvenli yaklaşım:

```text
User
 ↓
Generic Error
```

Detaylı teknik bilgi:

```text
Application
 ↓
Secure Server Logs
```

şeklinde ayrıştırılmalıdır.

---

## 11.4 Authentication

Authentication tokenlarının:

* Güvenli şekilde doğrulanması
* Sürelerinin yönetilmesi
* Gerektiğinde geçersizleştirilmesi
* Uygun güvenlik kontrollerinin uygulanması

sağlanmalıdır.

JWT'nin payload bölümündeki verilerin gizli bilgi saklama mekanizması olarak kullanılmaması gerekir.

---

## 11.5 Error Handling

Hatalı HTTP metodları veya beklenmeyen inputlar uygulamanın ayrıntılı stack trace döndürmesine neden olmamalıdır.

Uygun HTTP status kodları kullanılmalı ve teknik hata detayları istemciye açılmamalıdır.

---

# 12. Conclusion

Bu çalışma sonucunda bir web uygulamasının güvenlik değerlendirmesinin yalnızca birkaç saldırı payload'ı denemekten ibaret olmadığını gördüm.

Test sürecinde önce uygulamayı tanıdım, HTTP trafiğini inceledim, endpointleri belirledim, authentication ve authorization mekanizmalarını analiz ettim, kullanıcı girdilerini kontrollü olarak test ettim ve gözlemlediğim davranışları Burp Suite Repeater ile doğrulamaya çalıştım.

Çalışmanın en önemli bulguları:

```text
1. Broken Access Control / IDOR
2. SQL Injection Indicator
3. Detailed Error Messages / Information Disclosure
```

oldu.

Özellikle basket ID'sinin değiştirilmesiyle farklı bir kullanıcıya ait basket verisinin alınabilmesi, erişim kontrolünün yalnızca frontend veya kullanıcı tarafından gönderilen ID'ye bırakılmaması gerektiğini gösterdi.

SQL Injection testinde ise kullanıcı girdisinin SQL katmanını etkileyebildiğine dair güçlü bir hata davranışı gözlemlendi. Ancak tam exploit gerçekleştirilmediği için sonuç kontrollü bir **SQL Injection göstergesi** olarak raporlandı.

Ayrıca ayrıntılı hata mesajlarının ve stack trace bilgilerinin istemciye gönderilmesinin saldırgana uygulamanın teknolojik yapısı hakkında bilgi sağlayabileceğini gözlemledim.

Bu çalışmadan çıkardığım temel sonuç:

> **Profesyonel bir güvenlik testinin amacı yalnızca açık bulmak değil; bulguyu doğrulamak, etkisini anlamak, riskini değerlendirmek ve geliştiricinin uygulayabileceği somut bir çözüm sunmaktır.**

OWASP Top 10, web uygulaması güvenliği için yaygın risk kategorilerini tanımlayan bir farkındalık ve değerlendirme kaynağıdır; bu çalışmadaki bulgular özellikle A01 Broken Access Control, A03 Injection ve A05 Security Misconfiguration kategorileriyle ilişkilendirilmiştir.

---

# 13. References

1. **OWASP Top 10:2021**
   Web uygulaması güvenlik riskleri ve kategorileri. [OWASP Top 10:2021](https://top10.owasp.org/2021/?utm_source=chatgpt.com)

2. **OWASP A01:2021 – Broken Access Control**
   Erişim kontrolü, IDOR ve yetkilendirme problemleri. [OWASP A01:2021 – Broken Access Control](https://top10.owasp.org/2021/A01_2021-Broken_Access_Control/?utm_source=chatgpt.com)

3. **OWASP A05:2021 – Security Misconfiguration**
   Hata yönetimi, stack trace ve güvenlik yapılandırması. [OWASP A05:2021 – Security Misconfiguration](https://top10.owasp.org/2021/A05_2021-Security_Misconfiguration/?utm_source=chatgpt.com)

4. **OWASP Juice Shop**
   Güvenlik eğitimi ve kontrollü web güvenlik testleri için kasıtlı olarak zafiyetli uygulama. [OWASP Juice Shop](https://owasp.org/projects/juice-shop?tab=overview&utm_source=chatgpt.com)

5. **OWASP Developer Guide – Juice Shop**
   Juice Shop'un kullanım amacı ve güvenlik eğitimi bağlamı. [OWASP Developer Guide – Juice Shop](https://devguide.owasp.org/en/07-training-education/01-vulnerable-apps/01-juice-shop/?utm_source=chatgpt.com)

---

# Assessment Limitations

Bu rapordaki sonuçlar yalnızca kontrollü laboratuvar ortamında gerçekleştirilen testlere dayanmaktadır.

Test sırasında tüm Juice Shop özellikleri veya tüm olası saldırı senaryoları kapsamlı şekilde değerlendirilmemiştir.

Özellikle:

* Tam SQL Injection exploitation
* Tam XSS doğrulaması
* Tüm authorization senaryoları
* Tüm API endpointleri
* Source-code seviyesinde inceleme
* Production ortamı güvenlik kontrolleri

bu çalışmanın kapsamı dışında kalmıştır.

Bu nedenle rapordaki sonuçlar **laboratuvar ortamındaki gözlemler ve kontrollü testlerle sınırlıdır.**
