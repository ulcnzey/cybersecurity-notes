# 🛡️ SOC Nedir?

## 1. SOC Nedir?

**SOC (Security Operations Center)**, bir kurumun bilgi sistemlerini ve ağlarını güvenlik açısından sürekli izleyen, güvenlik olaylarını tespit eden, analiz eden ve olaylara müdahale edilmesini sağlayan güvenlik operasyon merkezidir.

SOC yalnızca fiziksel bir oda değildir. Bir SOC içerisinde:

* İnsanlar
* Güvenlik araçları
* İzleme sistemleri
* Süreçler
* Olay müdahale yöntemleri

birlikte çalışır.

SOC'un temel amacı:

> **Güvenlik olaylarını mümkün olduğunca erken fark etmek, analiz etmek ve uygun müdahaleyi gerçekleştirmektir.**

Basit olarak:

```text
İzle
 ↓
Tespit et
 ↓
Analiz et
 ↓
Müdahale et
 ↓
İyileştir / Öğren
```

---

# 👩‍💻 2. SOC Analyst Ne Yapar?

**SOC Analyst**, SOC içerisinde güvenlik olaylarını inceleyen ve güvenlik tehditlerini araştıran kişidir.

Bir SOC Analyst'in görevleri arasında:

* Güvenlik sistemlerini izlemek
* Gelen alertleri incelemek
* Logları analiz etmek
* Şüpheli aktiviteleri araştırmak
* Olayın gerçek bir tehdit olup olmadığını değerlendirmek
* Gerekirse incident oluşturmak
* Olayın etkisini ve kapsamını araştırmak
* Olay müdahale ekibine bilgi sağlamak
* Yapılan olaylardan öğrenilen bilgileri güvenlik süreçlerine aktarmak

bulunabilir.

SOC Analyst'in sürekli yaptığı işlerden biri şudur:

> **"Bu alert gerçekten bir güvenlik olayı mı, yoksa normal bir aktivite mi?"**

Örneğin:

```text
Alert:
03:00 → Kullanıcı hesabıyla giriş yapıldı
       ↓
SOC Analyst inceler
       ↓
Kullanıcı normalde Türkiye'den giriş yapıyor
       ↓
Bu giriş başka bir ülkeden
       ↓
Şüpheli aktivite olabilir
```

Ancak burada hemen "hesap saldırıya uğramış" sonucuna varılmaz.

Önce olayın diğer bilgileri incelenir.

---

# 📊 3. SIEM Nedir?

**SIEM (Security Information and Event Management)**, farklı sistemlerden gelen güvenlik olaylarının ve logların merkezi olarak toplanmasına, analiz edilmesine ve güvenlik ekiplerinin olayları izlemesine yardımcı olan güvenlik çözümüdür.

Bir şirkette çok sayıda sistem olabilir:

```text
Sunucular
   ↓
Firewall
   ↓
VPN
   ↓
Active Directory
   ↓
Web uygulamaları
   ↓
Endpoint cihazları
   ↓
SIEM
```

SIEM bu sistemlerden gelen logları merkezi bir yerde toplayabilir.

Daha sonra belirli kurallara göre şüpheli davranışlar için alert oluşturabilir.

Örneğin:

```text
10 başarısız giriş
       +
Başarılı giriş
       +
Farklı ülke
       ↓
Şüpheli aktivite
       ↓
Alert
```

SIEM, SOC Analyst'in önemli araçlarından biridir.

### Kısaca

> **SIEM = Güvenlik loglarını merkezi olarak toplama, ilişkilendirme ve analiz etmeye yardımcı olan sistem.**

---

# 📝 4. Log Nedir?

**Log**, bir sistemde gerçekleşen olayların kayıt altına alınmış bilgisidir.

Örneğin bir sistem şu bilgileri loglayabilir:

```text
Kullanıcı: zeynep
IP: 192.168.1.20
Zaman: 03:02
İşlem: Login
Sonuç: Success
```

Başka bir örnek:

```text
User: admin
Action: Failed Login
Time: 03:15
Source IP: 185.xxx.xxx.xxx
```

Loglarda sisteme göre farklı bilgiler bulunabilir.

Örneğin:

* Tarih ve saat
* Kullanıcı
* IP adresi
* İşlem
* Başarılı / başarısız durum
* Cihaz
* Kaynak
* Hedef
* Uygulama
* Hata bilgileri

bulunabilir.

### Kısaca

> **Log = Sistemde gerçekleşen olayların kayıtlarıdır.**

SOC Analyst için loglar çok önemlidir çünkü olayın ne olduğunu anlamaya yardımcı olurlar.

---

# 🚨 5. Alert Nedir?

**Alert**, bir sistemin veya güvenlik aracının şüpheli veya belirli bir kurala uyan aktivite tespit ettiğinde oluşturduğu uyarıdır.

Örneğin:

```text
03:00
Kullanıcı hesabıyla
yurt dışından başarılı giriş
```

sistem bunu şüpheli olarak değerlendirirse:

```text
🚨 ALERT
Unusual Login Detected
```

oluşturabilir.

Ancak çok önemli bir nokta vardır:

> **Alert = Kesin saldırı demek değildir.**

Bir alert yanlış alarm olabilir.

Örneğin kullanıcı gerçekten tatile gitmiş olabilir.

Bu nedenle SOC Analyst alert'i araştırır.

```text
Alert
 ↓
İnceleme
 ↓
Gerçek tehdit mi?
 ↓
Evet → Incident
Hayır → False Positive
```

---

# 🚨 6. Incident Nedir?

**Incident**, güvenliği etkileyen veya etkileme ihtimali bulunan bir güvenlik olayıdır.

Örneğin:

* Yetkisiz erişim
* Hesap ele geçirilmesi
* Malware bulaşması
* Veri sızıntısı
* Şüpheli sistem hareketleri
* Kimlik bilgilerinin kötüye kullanılması

birer security incident olabilir.

Ancak her alert incident değildir.

Örneğin:

```text
Alert
↓
Kullanıcı 03:00'te Almanya'dan giriş yaptı
↓
Araştırma
↓
Kullanıcı gerçekten Almanya'da mı?
↓
Evet
↓
Normal aktivite
```

Bu durumda alert oluşmuş olabilir ancak gerçek bir güvenlik incident'ı olmayabilir.

### Kısaca

> **Incident = Güvenlik açısından araştırılması ve/veya müdahale edilmesi gereken olay.**

---

# 🛠️ 7. Incident Response Nedir?

**Incident Response**, bir güvenlik olayının tespit edilmesinden sonra olayın kontrol altına alınması, etkisinin azaltılması, ortadan kaldırılması ve sistemlerin güvenli şekilde normale döndürülmesi için uygulanan süreçtir.

Basitleştirilmiş şekilde:

```text
Hazırlık
   ↓
Tespit
   ↓
Analiz
   ↓
Müdahale
   ↓
Kontrol altına alma
   ↓
Kurtarma
   ↓
Öğrenme / İyileştirme
```

Örneğin bir kullanıcının hesabının ele geçirildiğinden şüpheleniliyorsa:

* Hesap incelenebilir.
* Oturumlar sonlandırılabilir.
* Şifre sıfırlanabilir.
* MFA kontrol edilebilir.
* İlgili cihaz incelenebilir.
* Diğer şüpheli aktiviteler araştırılabilir.
* Olayın nasıl gerçekleştiği analiz edilebilir.

Amaç yalnızca saldırıyı durdurmak değildir.

Aynı zamanda:

> **"Bu olay nasıl gerçekleşti ve tekrar yaşanmaması için ne yapabiliriz?"**

sorusunun da cevaplanması gerekir.

---

# 🧠 8. Threat Intelligence Nedir?

**Threat Intelligence**, siber tehditler hakkında toplanan ve güvenlik kararlarında kullanılabilecek bilgi ve analizlerdir.

Örneğin:

* Bilinen saldırgan davranışları
* Zararlı yazılım bilgileri
* Saldırı teknikleri
* Şüpheli IP adresleri
* Zararlı domainler
* Tehdit aktörlerinin kullandığı yöntemler
* Güvenlik açıkları
* IOC'ler (Indicators of Compromise)

gibi bilgiler threat intelligence kapsamında kullanılabilir.

Buradaki amaç yalnızca bilgi toplamak değildir.

Bu bilgilerin güvenlik ekiplerinin karar vermesine yardımcı olması önemlidir.

Örneğin:

```text
Threat Intelligence
       ↓
Bilinen saldırı yöntemi
       ↓
Şirket loglarıyla karşılaştırma
       ↓
Benzer davranış var mı?
       ↓
SOC Analyst araştırması
```

### Kısaca

> **Threat Intelligence = Siber tehditler hakkında toplanan, analiz edilen ve güvenlik kararlarında kullanılabilen bilgi.**

---

# 🔍 9. Senaryo: Gece 03.00'te Yurt Dışından Giriş

Senaryo:

> Bir şirketin çalışan hesabıyla gece saat 03.00'te sisteme giriş yapılmıştır. Aynı kullanıcı normalde yalnızca Türkiye'den giriş yapmaktadır ancak bu kez farklı bir ülkeden bağlantı görülmüştür.

Bu olay SOC açısından **incelenmeye değer bir şüpheli aktivite** olabilir.

Ancak yalnızca bu bilgiye bakarak hesabın ele geçirildiğini söylemek doğru değildir.

SOC Analyst'in amacı daha fazla bilgi toplamaktır.

---

# 🔎 10. SOC Ekibi Hangi Bilgileri İnceler?

## 10.1 Kullanıcı Bilgileri

Öncelikle hangi kullanıcı hesabının kullanıldığı incelenir.

Örneğin:

* Kullanıcı kim?
* Görevi nedir?
* Hesap kritik yetkilere sahip mi?
* Admin hesabı mı?
* Daha önce benzer girişleri olmuş mu?

Özellikle yüksek yetkili hesaplarda olayın önemi daha yüksek olabilir.

---

## 10.2 Giriş Zamanı

Girişin gerçekleştiği saat incelenir.

```text
03:00
```

saatindeki giriş:

* Kullanıcının çalışma saatleriyle uyumlu mu?
* Kullanıcı o saatte çalışıyor mu?
* Daha önce bu saatlerde giriş yapmış mı?

gibi sorular sorulabilir.

---

## 10.3 IP Adresi

Giriş yapılan **IP adresi** incelenir.

Örneğin:

```text
Source IP:
185.xxx.xxx.xxx
```

Araştırılabilecek bilgiler:

* IP hangi ülkeye ait?
* Hangi ağ / internet sağlayıcısına ait?
* Daha önce şirkette görülmüş mü?
* Şüpheli olarak biliniyor mu?
* VPN veya proxy kullanımıyla ilişkili olabilir mi?

IP adresinin tek başına kötü niyetli olduğunu kanıtlamadığını unutmamak gerekir.

---

## 10.4 Coğrafi Konum

Kullanıcının normal giriş yaptığı konum ile yeni giriş karşılaştırılabilir.

Örneğin:

```text
Normal:
Türkiye 🇹🇷

Yeni:
Almanya 🇩🇪
```

Ancak burada da dikkatli olunmalıdır.

IP tabanlı konum bilgisi her zaman kesin fiziksel konum anlamına gelmez.

VPN, proxy, mobil ağlar veya kurumsal ağlar farklı konum gösterebilir.

---

# 🔐 10.5 Authentication Bilgileri

SOC Analyst şu bilgileri inceleyebilir:

* Başarılı giriş var mı?
* Başarısız giriş denemeleri olmuş mu?
* Kaç tane başarısız giriş olmuş?
* MFA kullanılmış mı?
* MFA doğrulaması gerçekleşmiş mi?
* Şüpheli MFA davranışı var mı?
* Parola yakın zamanda değiştirilmiş mi?

Örneğin:

```text
02:55 → 5 başarısız giriş
02:59 → Başarılı giriş
03:00 → MFA doğrulaması
```

gibi bir zaman çizelgesi olayın araştırılmasına yardımcı olabilir.

---

# 🕒 10.6 Önceki ve Sonraki Aktiviteler

SOC Analyst yalnızca login olayına bakmaz.

Olaydan **önce ve sonra** gerçekleşen aktiviteler de incelenir.

Örneğin:

```text
02:55 → Başarısız girişler
02:59 → Başarılı giriş
03:01 → Profil bilgilerine erişim
03:04 → Dosya indirme
03:07 → Yeni cihaz ekleme
```

Bu olaylar birlikte değerlendirildiğinde daha anlamlı bir tablo ortaya çıkabilir.

---

# 💻 10.7 Kullanılan Cihaz

Girişin hangi cihazdan yapıldığı incelenebilir.

Örneğin:

* Cihaz türü
* İşletim sistemi
* Browser
* Device ID
* Daha önce bilinen cihaz olup olmadığı

gibi bilgiler değerlendirilebilir.

Örneğin kullanıcı normalde şirket laptopundan giriş yapıyorsa ancak bu kez daha önce görülmemiş bir cihaz kullanılmışsa olay daha ayrıntılı incelenebilir.

---

# 🌐 10.8 Kullanıcı Agent Bilgisi

HTTP tabanlı sistemlerde **User-Agent** gibi bilgiler client hakkında fikir verebilir.

Örneğin:

```text
Browser:
Chrome

Operating System:
Windows
```

Ancak User-Agent bilgileri tek başına güvenilir kimlik kanıtı değildir.

Bu nedenle diğer bilgilerle birlikte değerlendirilir.

---

# 📂 10.9 Kullanıcının Daha Sonra Ne Yaptığı

Başarılı login sonrasında hangi işlemlerin yapıldığı incelenir.

Örneğin:

* Dosyalara erişildi mi?
* Hassas verilere erişildi mi?
* Yeni kullanıcı oluşturuldu mu?
* Yetki değişikliği yapıldı mı?
* Şifre değiştirildi mi?
* E-posta ayarları değiştirildi mi?
* Büyük miktarda veri indirildi mi?

Bu bilgiler olayın etkisini anlamaya yardımcı olabilir.

---

# 🧩 10.10 Diğer Sistemlerde Benzer Aktivite Var mı?

SOC Analyst olayın yalnızca bir sistemde olup olmadığına da bakabilir.

Örneğin:

```text
VPN
 ↓
Active Directory
 ↓
Email
 ↓
Cloud
 ↓
Endpoint
```

aynı kullanıcıyla ilgili başka şüpheli aktiviteler var mı?

Bu nedenle SIEM'in önemi ortaya çıkar.

Farklı sistemlerden gelen loglar bir araya getirilerek olayın daha geniş bir resmi oluşturulabilir.

---

# 🧠 11. Bu Senaryoda Genel Analiz

Olayı şöyle düşünebiliriz:

```text
03:00
Yurt dışından giriş
      ↓
Alert
      ↓
Kullanıcı kim?
      ↓
IP adresi ne?
      ↓
Cihaz tanınıyor mu?
      ↓
MFA gerçekleşti mi?
      ↓
Öncesinde başarısız giriş var mı?
      ↓
Login sonrasında ne yapıldı?
      ↓
Başka sistemlerde benzer hareket var mı?
      ↓
Normal aktivite mi?
      ↓
Yoksa Incident mı?
```

Buradaki en önemli nokta:

> **SOC Analyst tek bir belirtiye bakarak karar vermez. Birden fazla log ve olay bilgisini bir araya getirerek değerlendirme yapar.**

---

# 🔗 12. Kavramları Birbirine Bağlayalım

Bu bölümdeki kavramları şöyle düşünebilirim:

```text
Sistemler
   ↓
Loglar
   ↓
SIEM
   ↓
Şüpheli davranış
   ↓
Alert
   ↓
SOC Analyst
   ↓
Analiz
   ↓
Incident?
   ↓
Incident Response
```

Threat Intelligence ise bu sürecin farklı aşamalarında analizleri destekleyebilir:

```text
Threat Intelligence
        ↓
Bilinen tehdit bilgileri
        ↓
SIEM / SOC Analizi
        ↓
Şüpheli aktivitenin değerlendirilmesi
```

---

# 📝 Kendi Öğrenme Notlarım

Bu bölümden öğrendiğim temel kavramlar:

* **SOC** → Güvenlik operasyonlarının yürütüldüğü yapı.
* **SOC Analyst** → Güvenlik olaylarını izleyen ve analiz eden kişi.
* **SIEM** → Logları merkezi olarak toplama ve analiz etmeye yardımcı olan sistem.
* **Log** → Sistemde gerçekleşen olayların kaydı.
* **Alert** → Şüpheli veya belirli kurallara uyan aktivite için oluşturulan uyarı.
* **Incident** → Güvenlik açısından ele alınması gereken olay.
* **Incident Response** → Güvenlik olayına müdahale süreci.
* **Threat Intelligence** → Siber tehditler hakkında analizlerde kullanılabilecek bilgi.

En önemli ayrım:

> **Alert her zaman incident değildir.**

Bir alert oluşturulduğunda SOC Analyst önce olayı araştırır ve bunun gerçek bir güvenlik olayı olup olmadığını değerlendirir.

---

# 🎯 Kısa Özet

```text
SOC
↓
Sistemleri izler

SIEM
↓
Logları toplar ve analiz etmeye yardımcı olur

Log
↓
Gerçekleşen olayların kaydıdır

Alert
↓
Şüpheli aktivite uyarısıdır

Incident
↓
Güvenlik açısından ele alınması gereken olaydır

Incident Response
↓
Olayı analiz etme ve müdahale etme sürecidir

Threat Intelligence
↓
Tehditler hakkında güvenlik kararlarını destekleyen bilgidir
```

Gece 03.00'te yurt dışından yapılan şüpheli girişte SOC ekibi özellikle:

```text
Kullanıcı
IP adresi
Zaman
Konum
Cihaz
Authentication
MFA
Başarısız girişler
Sonraki aktiviteler
Diğer sistemlerdeki loglar
```

gibi bilgileri birlikte inceleyebilir.

> **Amaç yalnızca "giriş oldu" demek değil, bu girişin normal mi yoksa güvenlik açısından şüpheli mi olduğunu anlamaktır.**

---

# 📚 Kaynaklar

* NIST — Computer Security Incident Handling Guide
* NIST — Cybersecurity Framework
* CISA — Cybersecurity Resources
* MITRE ATT&CK
* Microsoft Security
* Google Cloud Security
