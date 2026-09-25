# 21. Web Güvenlik Test Metodolojisi

Bu çalışmada OWASP Juice Shop uygulaması üzerinde yaptığım web güvenlik testlerini belirli bir metodoloji doğrultusunda gerçekleştirdim.

Benim için metodoloji, sadece hangi araçları kullandığımı değil; **neyi, neden, hangi sırayla ve hangi amaçla test ettiğimi** açıklayan bir yol haritasıdır.

Test sürecini aşağıdaki aşamalara ayırdım:

```text
1. Scope
   ↓
2. Reconnaissance
   ↓
3. Application Mapping
   ↓
4. Endpoint Discovery
   ↓
5. Authentication Testing
   ↓
6. Authorization Testing
   ↓
7. Input Validation Testing
   ↓
8. Vulnerability Identification
   ↓
9. Validation
   ↓
10. Risk Assessment
   ↓
11. Reporting
   ↓
12. Remediation
```

---

## 1. Scope

İlk aşamada testin sınırlarını belirledim.

Bu çalışmada yalnızca:

* Kali Linux
* Burp Suite
* OWASP Juice Shop
* `localhost:3000`

üzerinde çalıştım.

Gerçek sistemlere, şirket uygulamalarına veya iznim olmayan herhangi bir sisteme test uygulamadım.

### Neden önemli?

Scope belirlenmeden yapılan bir güvenlik testi hem teknik hem de etik açıdan problem oluşturabilir.

Bu nedenle ilk sorum:

> **Neyi test etmeye yetkim var?**

olmalıdır.

---

## 2. Reconnaissance

Bu aşamada uygulama hakkında genel bilgi toplamaya başladım.

Juice Shop'un bir web uygulaması olduğunu, içerisinde farklı kullanıcı işlemleri, API'ler, ürünler, sepetler, kimlik doğrulama mekanizmaları ve farklı challenge'lar bulunduğunu inceledim.

Burada henüz doğrudan bir zafiyet bulmaya çalışmaktan çok uygulamanın genel yapısını anlamaya odaklandım.

### Amacım:

> **Test edeceğim uygulamanın nasıl çalıştığını anlamak.**

---

## 3. Application Mapping

Bu aşamada uygulamanın kullanıcı açısından ve teknik açıdan hangi bölümlerden oluştuğunu anlamaya çalıştım.

Örneğin:

* Login
* Ürünler
* Ürün yorumları
* Sepet
* API istekleri
* Socket.IO bağlantıları
* Challenge/Score Board gibi bölümler

üzerinden uygulamanın farklı işlevlerini gözlemledim.

Burp Suite'in HTTP History bölümünü kullanarak tarayıcının uygulama ile yaptığı iletişimi incelemek bu aşamada önemliydi.

### Amacım:

> **Uygulamanın saldırı yüzeyini ve temel işlevlerini haritalamak.**

---

## 4. Endpoint Discovery

Uygulamanın kullandığı API endpointlerini belirledim.

Örneğin:

```text
GET  /rest/basket/1
GET  /rest/products/2/reviews
POST /rest/user/login
PUT  /rest/products/1/reviews
GET  /api/Quantitys/
```

Her endpoint için mümkün olduğunca:

* HTTP metodunu
* Endpoint yolunu
* Parametreleri
* Authentication durumunu
* Dönen HTTP durum kodunu
* Response yapısını

inceledim.

### Neden önemli?

Bir web uygulamasının güvenlik açısından değerlendirilmesinde yalnızca ana sayfaya bakmak yeterli değildir.

Asıl önemli işlemler çoğu zaman API endpointleri üzerinden gerçekleşir.

Bu nedenle:

> **Hangi endpointler var ve bunlar ne yapıyor?**

sorusunun cevabını bulmak gerekir.

---

## 5. Authentication Testing

Authentication, kullanıcının kimliğini doğrulama sürecidir.

Bu aşamada login işlemini ve sonrasında kullanılan authentication mekanizmasını inceledim.

Örneğin:

```text
POST /rest/user/login
```

isteğinde kullanıcı bilgilerinin JSON body içerisinde gönderildiğini gözlemledim.

Login sonrasında uygulamanın JWT tabanlı bir token kullandığını ve sonraki isteklerde:

```http
Authorization: Bearer <token>
```

şeklinde gönderildiğini inceledim.

Ayrıca JWT'nin:

```text
Header.Payload.Signature
```

yapısını ve token'ın şifreleme ile aynı şey olmadığını öğrendim.

### Amacım:

> **Uygulamanın kullanıcı kimliğini nasıl doğruladığını ve oturum bilgisini nasıl yönettiğini anlamak.**

---

## 6. Authorization Testing

Authentication ile authorization birbirinden farklıdır.

Authentication:

> **Sen kimsin?**

Authorization:

> **Bunu yapmaya yetkin var mı?**

sorusuna cevap verir.

Bu aşamada farklı kullanıcıların farklı kaynaklara erişip erişememesi gerektiğini değerlendirdim.

Özellikle kaynak kimliklerinin değiştirilmesiyle başka bir kullanıcının verisine erişilip erişilemediğini kontrol ettim.

Örneğin:

```text
GET /rest/basket/1
```

isteğindeki ID değerini:

```text
GET /rest/basket/2
```

olarak değiştirdiğimde farklı bir kullanıcıya ait sepet verisinin döndüğünü gözlemledim.

Bu durum Broken Access Control / IDOR açısından önemli bir bulgu olarak değerlendirildi.

### Amacım:

> **Kullanıcının yalnızca yetkili olduğu kaynaklara erişebildiğini doğrulamak.**

---

## 7. Input Validation Testing

Bu aşamada uygulamanın kullanıcıdan aldığı verileri nasıl işlediğini inceledim.

Özellikle:

* Login parametreleri
* URL parametreleri
* JSON body değerleri
* Arama parametreleri
* Ürün ve yorum verileri

üzerinde kontrollü testler gerçekleştirdim.

Örneğin login endpointindeki `email` parametresine kontrollü olarak SQL sözdizimini etkileyebilecek bir karakter eklediğimde uygulamanın `500 Internal Server Error` döndürdüğünü ve SQL/Sequelize/SQLite ile ilgili teknik hata bilgileri açığa çıkardığını gözlemledim.

Bu sonuç SQL Injection açısından güçlü bir gösterge olarak değerlendirildi ancak tek başına tam bir veri çıkarma veya sistem ele geçirme kanıtı olarak kabul edilmedi.

XSS tarafında ise test edilen arama endpointinde gönderdiğim değerin doğrudan response içerisinde dönmediğini gözlemledim. Bu nedenle bu test sonucunu doğrulanmış bir XSS bulgusu olarak raporlamadım.

### Amacım:

> **Kullanıcı girdilerinin güvenli şekilde doğrulanıp işlenip işlenmediğini kontrol etmek.**

---

## 8. Vulnerability Identification

Bu aşamada önceki aşamalarda topladığım verileri değerlendirerek olası güvenlik problemlerini belirledim.

Çalışmam sırasında özellikle:

* Broken Access Control / IDOR
* SQL Injection göstergesi
* Information Disclosure
* Security Misconfiguration

gibi konular üzerinde durdum.

Ancak her anormal davranışı otomatik olarak zafiyet kabul etmedim.

Örneğin:

```text
500 Internal Server Error
```

görülmesi tek başına zafiyet anlamına gelmez.

Önemli olan:

> **Bu davranış güvenlik açısından kontrol edilebilir bir etki oluşturuyor mu?**

sorusudur.

---

## 9. Validation

Bir davranışın gerçekten güvenlik bulgusu olup olmadığını doğrulamaya çalıştım.

Bunun için Burp Suite Repeater kullanarak istekleri tekrar gönderdim ve kontrollü şekilde değiştirdim.

Örneğin:

```text
GET /rest/basket/1
```

isteğini:

```text
GET /rest/basket/2
```

şeklinde değiştirerek response'ları karşılaştırdım.

Benzer şekilde:

* Authorization header'ını kaldırdım.
* Parametreleri değiştirdim.
* HTTP metodunu değiştirdim.
* Request body üzerinde değişiklik yaptım.
* Önceki ve sonraki response'ları karşılaştırdım.

Buradaki temel mantığım:

```text
Normal Request
      ↓
Değişiklik
      ↓
Tekrar Gönder
      ↓
Response'u Karşılaştır
      ↓
Davranışı Yorumla
```

şeklindeydi.

### Neden önemli?

Bir güvenlik testinde ilk gördüğüm anormal davranışa hemen “zafiyet” demek yerine, davranışı tekrar üretmeye ve kanıtlamaya çalışmak gerekir.

---

## 10. Risk Assessment

Bir bulguyu keşfetmek tek başına yeterli değildir.

Bulgunun uygulama açısından ne kadar önemli olduğunu da değerlendirmek gerekir.

Bu aşamada:

* Etkilenen varlığı
* Gizlilik etkisini (Confidentiality)
* Bütünlük etkisini (Integrity)
* Kullanılabilirlik etkisini (Availability)
* Saldırının gerçekleşme olasılığını
* Olası iş etkisini

değerlendirdim.

Örneğin başka bir kullanıcının sepet verisine erişilebilmesi öncelikle **gizlilik** açısından önem taşır.

Basit şekilde:

```text
Zafiyet
   ↓
Etkilenen Varlık
   ↓
CIA Etkisi
   ↓
Olasılık
   ↓
İş Etkisi
   ↓
Risk
```

şeklinde düşündüm.

Buradaki risk seviyelerinin laboratuvar ortamındaki değerlendirmeler olduğunu; gerçek bir kurumda varlık değeri, tehdit modeli, mevcut güvenlik kontrolleri ve iş süreçlerinin de dikkate alınması gerektiğini öğrendim.

---

## 11. Reporting

Test sonucunda elde ettiğim bulguları düzenli bir güvenlik raporuna dönüştürdüm.

Bir bulgunun raporunda yalnızca:

> “SQL Injection var.”

demek yeterli değildir.

Bulgunun:

* Başlığı
* Açıklaması
* Etkilenen endpoint
* Teknik detayları
* PoC / kanıtı
* Etkisi
* Risk değerlendirmesi
* Çözüm önerisi

açıklanmalıdır.

Örneğin:

```text
Finding:
Broken Access Control / IDOR

Affected Endpoint:
GET /rest/basket/{id}

Observation:
Basket ID değiştirildiğinde başka kullanıcıya ait
sepet verisi döndü.

Impact:
Unauthorized access to another user's basket data.

Remediation:
Server-side ownership and authorization check.
```

Bu şekilde teknik ekip bulgunun ne olduğunu ve nasıl düzeltileceğini anlayabilir.

---

## 12. Remediation

Son aşamada bulgunun nasıl giderileceğini geliştirici açısından değerlendirdim.

Örneğin Broken Access Control için:

```text
Request
   ↓
User Authentication
   ↓
Resource Ownership Check
   ↓
Authorization Check
   ↓
Allowed → Resource
Denied → 403 Forbidden
```

SQL Injection için:

```text
User Input
    ↓
Parameterized Query
    ↓
Database
```

kullanılmalıdır.

Kullanıcı girdisinin SQL sorgusuna doğrudan eklenmesi yerine parameterized query / prepared statement kullanılmalıdır.

Information Disclosure için ise:

```text
User
 ↓
Generic Error
```

ve

```text
Server
 ↓
Detailed Secure Log
```

mantığı kullanılmalıdır.

Yani kullanıcıya stack trace, veritabanı bilgisi veya backend dosya yolları gösterilmemeli; gerekli teknik bilgiler yalnızca güvenli sunucu loglarında tutulmalıdır.

---

# Metodolojinin Genel Mantığı

Bu çalışmadan sonra web güvenlik testini yalnızca:

> “Burp Suite açtım ve bazı istekleri değiştirdim.”

şeklinde düşünmüyorum.

Benim için süreç artık:

```text
Kapsamı Belirle
      ↓
Uygulamayı Tanı
      ↓
Saldırı Yüzeyini Haritala
      ↓
Endpointleri Keşfet
      ↓
Authentication'ı Test Et
      ↓
Authorization'ı Test Et
      ↓
Inputları Test Et
      ↓
Olası Zafiyetleri Belirle
      ↓
Bulguları Doğrula
      ↓
Riski Değerlendir
      ↓
Raporla
      ↓
Çözüm Öner
```

şeklindedir.

Bu yaklaşım sayesinde test rastgele denemelerden oluşmak yerine **tekrarlanabilir ve sistematik bir güvenlik değerlendirmesine** dönüşür.

---

# Araştırma Sorusu

## Neden profesyonel bir güvenlik testinin sonunda sadece "açık var/yok" denmez?

Çünkü bir güvenlik testinde yalnızca bir zafiyetin bulunması yeterli değildir.

Aynı zafiyet farklı sistemlerde tamamen farklı sonuçlara neden olabilir.

Örneğin bir güvenlik açığı için şu soruların cevaplanması gerekir:

### 1. Nerede?

Hangi endpoint, parametre, özellik veya bileşen etkileniyor?

### 2. Nasıl oluşuyor?

Zafiyetin teknik olarak oluşmasına neden olan hata nedir?

### 3. Gerçekten doğrulanabiliyor mu?

Bulgu tekrar üretilebiliyor mu?

### 4. Etkisi ne?

Saldırgan:

* Veri okuyabilir mi?
* Veri değiştirebilir mi?
* Yetkisini aşabilir mi?
* Sistemin çalışmasını etkileyebilir mi?

### 5. Hangi varlık etkileniyor?

Örneğin:

* Kullanıcı hesabı
* Kişisel veri
* Veritabanı
* Uygulama sunucusu
* İş süreçleri

### 6. Risk ne kadar?

Olası etki ve gerçekleşme ihtimali birlikte değerlendirilmelidir.

### 7. Nasıl düzeltilir?

Geliştiricinin uygulayabileceği teknik çözüm açıkça belirtilmelidir.

Bu nedenle profesyonel bir güvenlik raporunun amacı sadece:

```text
Açık var.
```

demek değildir.

Asıl amaç:

```text
Zafiyeti bul
     ↓
Doğrula
     ↓
Teknik nedenini açıkla
     ↓
Etkisini belirle
     ↓
Riski değerlendir
     ↓
Çözüm öner
     ↓
Düzeltmenin doğrulanmasını sağla
```

şeklinde **güvenlik problemini anlaşılabilir ve düzeltilebilir bir sonuca dönüştürmektir.**

Bu çalışmada benim öğrendiğim temel yaklaşım:

> **“Bir açığı bulmak testin sonu değil, raporlanabilir ve düzeltilebilir bir güvenlik bulgusuna dönüştürmenin başlangıcıdır.”**

---

## Kısa Özet

| Aşama                        | Temel Soru                                |
| ---------------------------- | ----------------------------------------- |
| Scope                        | Neyi test etmeme izin var?                |
| Reconnaissance               | Uygulama hakkında ne biliyorum?           |
| Application Mapping          | Uygulama nasıl çalışıyor?                 |
| Endpoint Discovery           | Hangi endpointler var?                    |
| Authentication               | Kullanıcı kimliği nasıl doğrulanıyor?     |
| Authorization                | Kullanıcının buna erişim yetkisi var mı?  |
| Input Validation             | Kullanıcı girdileri güvenli işleniyor mu? |
| Vulnerability Identification | Hangi güvenlik problemleri olabilir?      |
| Validation                   | Bulgu gerçekten doğrulanabiliyor mu?      |
| Risk Assessment              | Etkisi ve riski ne?                       |
| Reporting                    | Bulguyu nasıl açıklarım?                  |
| Remediation                  | Nasıl düzeltilir?                         |
