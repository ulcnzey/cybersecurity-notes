# 18. Risk Değerlendirmesi

## 🎯 Amaç

Bu çalışmada OWASP Juice Shop üzerinde önceki görevlerde belirlediğim güvenlik bulgularını risk açısından değerlendirdim.

Bir güvenlik açığının sadece var olup olmadığına bakmak yerine, bu açığın:

* Hangi varlığı etkilediğini,
* Hangi zafiyetten kaynaklandığını,
* Nasıl kötüye kullanılabileceğini,
* Gizlilik, bütünlük ve erişilebilirlik açısından etkisini,
* İşletme açısından oluşturabileceği etkiyi,
* Gerçekleşme olasılığını

değerlendirmeye çalıştım.

---

# 🧠 Risk Değerlendirmesinde Kullandığım Kavramlar

## Confidentiality – Gizlilik

Bilginin yalnızca yetkili kişiler tarafından erişilebilir olmasıdır.

Örneğin bir kullanıcının başka bir kullanıcıya ait basket bilgilerinin görüntülenmesi **Confidentiality** açısından problemdir.

Kısaca:

> **Bilgi başkasının eline geçiyor mu?**

---

## Integrity – Bütünlük

Verinin yetkisiz kişiler tarafından değiştirilmemesi veya bozulmamasıdır.

Örneğin bir saldırgan başka bir kullanıcının verisini değiştirebiliyorsa bütünlük açısından problem oluşabilir.

Kısaca:

> **Veri izinsiz değiştirilebiliyor mu?**

---

## Availability – Erişilebilirlik

Sistemin veya hizmetin ihtiyaç duyulduğunda kullanılabilir durumda olmasıdır.

Örneğin bir saldırı sonucunda uygulama çalışamaz hale gelirse Availability etkilenir.

Kısaca:

> **Sistem ihtiyaç duyulduğunda çalışıyor mu?**

---

## Business Impact – İş Etkisi

Bir güvenlik probleminin teknik sistemin dışında kuruma nasıl zarar verebileceğini ifade eder.

Örneğin:

* Müşteri güveninin azalması
* Veri gizliliğinin ihlal edilmesi
* Operasyonların aksaması
* Finansal kayıp
* Yasal/regülasyon kaynaklı sonuçlar
* İtibar kaybı

gibi etkiler business impact kapsamında düşünülebilir.

Kısaca:

> **Bu güvenlik problemi kuruma ne kaybettirebilir?**

---

# 📊 Risk Değerlendirme Tablosu

| # | Bulgu                                                        | Varlık                    | CIA Etkisi                      | Olasılık    | Risk   |
| - | ------------------------------------------------------------ | ------------------------- | ------------------------------- | ----------- | ------ |
| 1 | Başka kullanıcıya ait basket verisine erişim                 | Kullanıcı basket/verileri | Gizlilik                        | Yüksek      | Yüksek |
| 2 | Login endpoint'inde SQL Injection göstergesi                 | Veritabanı / uygulama     | Gizlilik + Bütünlük potansiyeli | Orta-Yüksek | Yüksek |
| 3 | SQL hatasında teknik bilgilerin açığa çıkması                | Uygulama altyapısı        | Gizlilik                        | Yüksek      | Orta   |
| 4 | Authorization hatasında ayrıntılı hata bilgisi               | Backend altyapısı         | Gizlilik                        | Yüksek      | Orta   |
| 5 | Hatalı method response'unda backend yollarının açığa çıkması | Backend altyapısı         | Gizlilik                        | Yüksek      | Orta   |

> **Not:** Buradaki risk değerlendirmesi Juice Shop laboratuvarındaki gözlemlere dayalı nitel bir değerlendirmedir. Gerçek bir kurumda risk seviyesi; varlığın değeri, tehdit modeli, mevcut kontroller ve iş süreçleri gibi ek bilgilerle belirlenmelidir.

---

# 1. Başka Kullanıcıya Ait Basket Verisine Erişim

### Bulgu:

Basket ID değiştirilerek başka bir kullanıcıya ait basket verisine erişilebildiğini gözlemledim.

### Varlık:

* Kullanıcı basket bilgileri
* Kullanıcıya ait ürün bilgileri
* Kullanıcı ID bilgileri

### Zafiyet:

Sunucu tarafında kaynak sahipliği kontrolünün yeterince uygulanmaması.

Kullanıcı giriş yapmış olsa bile istediği basket ID'sine erişebiliyorsa authentication yapılması tek başına yeterli değildir.

### Saldırı senaryosu:

Bir saldırgan geçerli bir kullanıcı hesabıyla uygulamaya giriş yapar.

Daha sonra kendi basket isteğindeki ID değerini değiştirerek başka basket ID'lerini dener.

Eğer sunucu kaynak sahibini kontrol etmezse başka kullanıcılara ait basket verileri görüntülenebilir.

### Etki:

**Confidentiality – Gizlilik:** Yüksek

Başka kullanıcıların basket bilgilerinin yetkisiz şekilde görüntülenmesi söz konusu olabilir.

**Integrity – Bütünlük:** Bu testte başka kullanıcının verisini değiştirmedim. Bu nedenle bütünlük etkisinin gerçekleştiğini söylemiyorum.

**Availability – Erişilebilirlik:** Bu testte doğrudan bir erişilebilirlik etkisi gözlemlemedim.

### Business Impact:

Gerçek bir e-ticaret uygulamasında kullanıcı verilerinin başka kullanıcılar tarafından görülmesi:

* müşteri güvenini,
* veri gizliliğini,
* kurumun itibarını

olumsuz etkileyebilir.

### Olasılık:

**Yüksek**

Çünkü test sırasında yalnızca kaynak ID'sini değiştirerek farklı bir kaynağa erişilebildiğini gözlemledim.

### Risk:

**Yüksek**

Özellikle kullanıcı verilerinin yetkisiz şekilde görüntülenebilmesi nedeniyle gizlilik açısından önemli bir risk oluşturabilir.

### Çözüm:

Sunucu tarafında her istekte:

```text
İstek yapan kullanıcı
        ↓
İstenen kaynak
        ↓
Kaynak bu kullanıcıya ait mi?
        ↓
Evet → erişime izin ver
Hayır → 403 Forbidden
```

şeklinde sahiplik kontrolü yapılmalıdır.

Sadece frontend tarafındaki kontroller güvenlik için yeterli olmamalıdır.

---

# 2. Login Endpoint'inde SQL Injection Göstergesi

### Bulgu:

Login endpoint'indeki `email` parametresine kontrollü özel karakter içeren input gönderdiğimde uygulamanın SQL/database hatası verdiğini gözlemledim.

### Varlık:

* Login sistemi
* Veritabanı
* Kullanıcı hesap bilgileri

### Zafiyet:

Kullanıcıdan gelen inputun SQL sorgusu üzerinde beklenmeyen şekilde etkili olabilmesi.

Test sırasında SQL işleme sürecine ait hata ortaya çıktı.

### Saldırı senaryosu:

Saldırgan login endpoint'indeki kullanıcı kontrollü parametreleri manipüle ederek uygulamanın SQL sorgusunu etkileyebilecek girdiler göndermeye çalışabilir.

Bu çalışmada yalnızca kontrollü bir syntax-error doğrulaması yaptım.

Veri çıkarma veya hesap ele geçirme gerçekleştirmedim.

### Etki:

**Confidentiality – Gizlilik:** Potansiyel olarak yüksek

SQL Injection'ın gerçek anlamda sömürülebilmesi durumunda veritabanındaki yetkisiz verilere erişim riski oluşabilir.

**Integrity – Bütünlük:** Potansiyel olarak yüksek

Yetkisiz SQL işlemleri mümkün olursa verilerin değiştirilmesi riski ortaya çıkabilir.

**Availability – Erişilebilirlik:** Potansiyel

Kötüye kullanımın niteliğine bağlı olarak veritabanı veya uygulama hizmetinin etkilenmesi mümkün olabilir.

### Business Impact:

Gerçek bir uygulamada kullanıcı verilerinin açığa çıkması veya değiştirilmesi:

* veri ihlali,
* kullanıcı güveninin kaybedilmesi,
* operasyonel sorunlar,
* finansal ve hukuki sonuçlar

doğurabilir.

### Olasılık:

**Orta-Yüksek**

Kontrollü test sırasında inputun SQL işleme sürecini etkilediğine dair güçlü bir belirti elde ettim.

Ancak bu çalışmada tam bir SQL Injection sömürüsü gerçekleştirmediğim için kesin bir istismar sonucu çıkarmıyorum.

### Risk:

**Yüksek**

Zafiyetin gerçekten SQL Injection olarak doğrulanması durumunda etki alanı veritabanı seviyesine kadar ulaşabilir.

### Çözüm:

* Parameterized Query kullanılmalı.
* Prepared Statement kullanılmalı.
* Kullanıcı girdileri güvenli şekilde işlenmeli.
* Veritabanı kullanıcısına minimum yetki verilmeli.
* Kullanıcıya ayrıntılı SQL/database hataları gösterilmemeli.
* Hatalar güvenli şekilde loglanmalıdır.

---

# 3. SQL Hatasında Teknik Bilgilerin Açığa Çıkması

### Bulgu:

SQL testinde oluşan hata response'unda SQLite, Sequelize ve backend hata bilgileri gibi teknik detayların açığa çıktığını gözlemledim.

### Varlık:

* Uygulama altyapısı
* Veritabanı teknolojisi
* Backend framework/kütüphaneleri

### Zafiyet:

Production benzeri bir uygulamada ayrıntılı teknik hata bilgilerinin kullanıcıya gösterilmesi.

### Saldırı senaryosu:

Saldırgan uygulamaya hatalı veya beklenmeyen inputlar göndererek hata response'larını inceler.

Response içerisindeki teknoloji ve framework bilgilerini kullanarak uygulamanın altyapısı hakkında daha fazla bilgi toplamaya çalışabilir.

### Etki:

**Confidentiality – Gizlilik:** Orta

Teknik bilgiler doğrudan kullanıcı verisi olmasa da uygulamanın iç yapısı hakkında bilgi açığa çıkar.

**Integrity:** Doğrudan bir bütünlük etkisi gözlemlemedim.

**Availability:** Doğrudan bir erişilebilirlik etkisi gözlemlemedim.

### Business Impact:

Teknik bilgi sızıntısı tek başına büyük bir iş kaybı oluşturmayabilir ancak başka güvenlik açıklarıyla birleştiğinde saldırı araştırmasını kolaylaştırabilir.

### Olasılık:

**Yüksek**

Test sırasında ayrıntılı hata bilgilerinin doğrudan response içerisinde görüldüğünü gözlemledim.

### Risk:

**Orta**

Tek başına etkisi sınırlı olabilir ancak diğer zafiyetlerle birlikte daha değerli hale gelebilir.

### Çözüm:

* Kullanıcıya genel hata mesajı gösterilmeli.
* Stack trace response'a gönderilmemeli.
* Debug modu production ortamında kapatılmalı.
* Teknik detaylar yalnızca güvenli server-side loglarda tutulmalı.

---

# 4. Authorization Hatasında Ayrıntılı Hata Bilgisi

### Bulgu:

Authorization header'ını kaldırdığımda uygulama doğru şekilde:

```http
401 Unauthorized
```

döndürdü.

Ancak response içerisinde framework ve hata detaylarının da bulunduğunu gözlemledim.

### Varlık:

* Backend uygulaması
* Framework bilgileri
* Hata yönetimi mekanizması

### Zafiyet:

Kullanıcıya gereğinden fazla teknik hata bilgisi gösterilmesi.

### Saldırı senaryosu:

Saldırgan authentication gerektiren endpoint'lere farklı şekillerde istek göndererek hata response'larını inceler.

Response içerisindeki framework ve backend bilgilerini kullanarak uygulamanın teknolojileri hakkında bilgi toplayabilir.

### Etki:

**Confidentiality:** Orta

Backend hakkında teknik bilgi açığa çıkmaktadır.

**Integrity:** Doğrudan etki gözlemlemedim.

**Availability:** Doğrudan etki gözlemlemedim.

### Business Impact:

Teknik bilgi sızıntısı saldırganın uygulamayı tanımasını kolaylaştırabilir.

### Olasılık:

**Yüksek**

Çünkü response içerisinde teknik hata bilgilerinin görülebildiğini doğrudan gözlemledim.

### Risk:

**Orta**

Doğrudan kullanıcı verisine erişim sağlamasa da diğer zafiyetlerle birlikte kullanılabilecek ek bilgi sağlar.

### Çözüm:

Authentication hatalarında kullanıcıya yalnızca gerekli ve genel hata mesajı gösterilmeli, framework ve stack trace bilgileri response'a eklenmemelidir.

---

# 5. Hatalı HTTP Methodunda Backend Bilgilerinin Açığa Çıkması

### Bulgu:

Bir basket isteğinde HTTP methodunu `GET` yerine `POST` yaptığımda uygulama `500 Internal Server Error` verdi.

Response içerisinde backend dosya yolları ve stack trace bilgileri görüldü.

### Varlık:

* Backend uygulaması
* Sunucu dosya yapısı
* Framework bilgileri

### Zafiyet:

Beklenmeyen bir HTTP isteğinde ayrıntılı backend hata bilgilerinin response içerisinde gösterilmesi.

### Saldırı senaryosu:

Saldırgan farklı HTTP methodlarını deneyerek uygulamanın endpoint davranışlarını inceler.

Hatalı isteklerde dönen response'ları analiz ederek backend yapısı hakkında bilgi toplamaya çalışabilir.

### Etki:

**Confidentiality:** Orta

Backend dosya yapısı ve framework bilgileri açığa çıkabilir.

**Integrity:** Doğrudan etki gözlemlemedim.

**Availability:** Doğrudan etki gözlemlemedim.

### Business Impact:

Açığa çıkan teknik bilgiler başka güvenlik açıklarının araştırılmasını kolaylaştırabilir.

### Olasılık:

**Yüksek**

Test sırasında ayrıntılı backend hata bilgisinin response'ta bulunduğunu doğrudan gözlemledim.

### Risk:

**Orta**

Tek başına doğrudan sistem ele geçirilmesine neden olduğu gösterilmemiştir ancak saldırı yüzeyi hakkında bilgi sağlayabilir.

### Çözüm:

* Production ortamında ayrıntılı stack trace gösterilmemeli.
* Hatalar server-side loglanmalı.
* Kullanıcıya genel hata mesajı dönülmeli.
* Beklenmeyen HTTP methodları güvenli şekilde ele alınmalı.

---

# 📌 Genel Risk Özeti

Yaptığım değerlendirmede bulguların etkilerinin aynı olmadığını gördüm.

Özellikle:

### En önemli etki alanları

**Confidentiality – Gizlilik**

Başka kullanıcıya ait basket verilerine erişim ve teknik bilgilerin açığa çıkması gizlilik açısından önemlidir.

**Integrity – Bütünlük**

SQL Injection'ın gerçek anlamda sömürülebilmesi durumunda veri değiştirme riski oluşabilir. Ancak benim testimde böyle bir değişiklik gerçekleştirilmedi.

**Availability – Erişilebilirlik**

Yaptığım testlerde doğrudan bir Availability etkisi gözlemlemedim.

**Business Impact – İş Etkisi**

Gerçek bir ticari uygulamada kullanıcı verilerinin açığa çıkması veya veritabanının etkilenmesi; müşteri güveni, operasyonlar, itibar ve olası hukuki/finansal süreçler açısından sonuç doğurabilir.

---

# 🧠 Bu Çalışmadan Öğrendiklerim

Bu çalışmada bir güvenlik açığını değerlendirirken yalnızca:

> "Açık var."

dememin yeterli olmadığını öğrendim.

Bunun yerine şu soruları sormam gerekiyor:

```text
Ne etkileniyor?
     ↓
Varlık

Hangi güvenlik problemi var?
     ↓
Zafiyet

Nasıl kötüye kullanılabilir?
     ↓
Saldırı senaryosu

Neye zarar verebilir?
     ↓
Confidentiality
Integrity
Availability

Kuruma ne kaybettirebilir?
     ↓
Business Impact

Gerçekleşme ihtimali ne?
     ↓
Olasılık

Bunların tamamını düşününce:
     ↓
Risk

Nasıl azaltılır?
     ↓
Çözüm
```

## 🎯 Sonuç

Risk değerlendirmesinin sadece teknik bir değerlendirme olmadığını öğrendim.

Bir güvenlik bulgusunu değerlendirirken **teknik zafiyet + etkilenen varlık + saldırı senaryosu + CIA etkisi + business impact + olasılık** birlikte düşünülmelidir.

Özellikle **Confidentiality, Integrity ve Availability** kavramlarının bir güvenlik bulgusunun etkisini anlamak için temel bir çerçeve oluşturduğunu öğrendim.
