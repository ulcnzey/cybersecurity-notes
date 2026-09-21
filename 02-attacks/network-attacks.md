# 4. Siber Saldırı Türleri

Bu bölümde temel siber saldırı türlerini; ne oldukları, genel olarak nasıl çalıştıkları, hedefleri, nasıl fark edilebilecekleri ve temel korunma yöntemleri açısından inceledim.

Saldırıları ezberlemek yerine, her saldırı için şu soruları cevaplamaya çalıştım:

* **Nedir?**
* **Nasıl çalışır?**
* **Hedefi nedir?**
* **Nasıl fark edilebilir?**
* **Temel korunma yöntemi nedir?**

Saldırıları üç ana gruba ayırdım:

1. Ağ ve sistem saldırıları
2. Web saldırıları
3. Kullanıcı odaklı saldırılar

---

# 🌐 4.1 Ağ ve Sistem Saldırıları

## 1. DoS — Denial of Service

### Saldırı adı:

**DoS (Denial of Service)**

### Nedir?

Bir sistemin veya hizmetin normal kullanıcılar tarafından kullanılamaz hale getirilmesini amaçlayan saldırıdır.

### Nasıl çalışır?

Hedef sistemin kaynaklarını aşırı miktarda istek veya işlemle meşgul etmeye çalışır. Sistem bu yükü karşılayamazsa normal kullanıcıların hizmete erişimi zorlaşabilir.

### Hedefi nedir?

Temel hedef **Availability (Erişilebilirlik)** değerini bozmaktır.

### Nasıl fark edilebilir?

* Trafikte ani artış
* Sunucuda aşırı kaynak kullanımı
* Web sitesinin yavaşlaması
* Hizmete erişilememesi
* Olağandışı istek yoğunluğu

### Temel korunma yöntemi:

* Rate limiting
* Trafik izleme
* Firewall kuralları
* Yük dengeleme
* DDoS koruma çözümleri

### 🧠 Akılda tut:

> **DoS = Sistemi aşırı yükle → hizmet kullanılamasın.**

---

# 2. DDoS — Distributed Denial of Service

### Saldırı adı:

**DDoS (Distributed Denial of Service)**

### Nedir?

Bir hizmetin çok sayıda farklı kaynaktan gelen yoğun trafik veya isteklerle kullanılamaz hale getirilmeye çalışılmasıdır.

### Nasıl çalışır?

Saldırı tek bir kaynaktan değil, birçok farklı cihaz veya sistemden gelebilir.

Bu cihazlar bazen saldırganın kontrolündeki **botnet** içerisinde bulunabilir.

```text
Cihaz ──┐
Cihaz ──┤
Cihaz ──┤
Cihaz ──┼──→ Hedef sunucu
Cihaz ──┤
Cihaz ──┘
```

### Hedefi nedir?

**Availability (Erişilebilirlik)**

### Nasıl fark edilebilir?

* Çok sayıda kaynaktan ani trafik
* Bant genişliğinin hızlı şekilde tükenmesi
* Sunucu kaynaklarının aşırı kullanılması
* Servisin yavaşlaması veya erişilememesi

### Temel korunma yöntemi:

* DDoS koruma servisleri
* Trafik filtreleme
* Rate limiting
* CDN
* Yük dengeleme
* Ağ trafiğinin izlenmesi

### 🧠 Akılda tut:

> **DDoS = Çok sayıda kaynak → tek hedef → hizmet kullanılamıyor.**

---

# 3. Brute Force

### Saldırı adı:

**Brute Force**

### Nedir?

Bir hesabın parolasını bulmak için çok sayıda parola veya parola kombinasyonunun denenmesidir.

### Nasıl çalışır?

Saldırgan genellikle belirli bir hesap üzerinde art arda farklı parola tahminleri yapmaya çalışır.

```text
Hesap
 ↓
Parola 1 ❌
Parola 2 ❌
Parola 3 ❌
...
Doğru parola
```

### Hedefi nedir?

Kullanıcı hesabının ele geçirilmesi.

### Nasıl fark edilebilir?

* Çok sayıda başarısız giriş
* Kısa sürede art arda login denemeleri
* Olağandışı IP adreslerinden girişler
* Hesap kilitlenmeleri
* SIEM üzerinde başarısız giriş alarmları

### Temel korunma yöntemi:

* Güçlü ve benzersiz parolalar
* MFA
* Rate limiting
* Başarısız giriş denemelerini sınırlandırma
* Giriş kayıtlarının izlenmesi

### 🧠 Akılda tut:

> **Brute Force = Bir hesabı birçok anahtarla denemek.**

---

# 4. Password Spraying

### Saldırı adı:

**Password Spraying**

### Nedir?

Az sayıda yaygın parolanın çok sayıda kullanıcı hesabında denenmesidir.

### Nasıl çalışır?

Brute Force:

```text
1 hesap → çok parola
```

Password Spraying:

```text
çok hesap → az/yaygın parola
```

### Hedefi nedir?

Kullanıcı hesaplarını ele geçirmek.

### Nasıl fark edilebilir?

* Aynı zaman aralığında birçok hesaba başarısız giriş
* Aynı kaynaktan farklı kullanıcı hesaplarına giriş denemeleri
* Birçok hesapta benzer başarısız login kayıtları
* SIEM üzerinde anormal kimlik doğrulama davranışları

### Temel korunma yöntemi:

* MFA
* Güçlü parola politikası
* Yaygın/ele geçirilmiş parolaların engellenmesi
* Kimlik doğrulama kayıtlarının izlenmesi
* Anormal giriş davranışlarının tespit edilmesi

### 🧠 Akılda tut:

> **Password Spraying = Aynı anahtarı birçok kapıda denemek.**

---

# 5. MITM — Man-in-the-Middle

### Saldırı adı:

**Man-in-the-Middle (MITM)**

### Nedir?

İki taraf arasındaki iletişimin arasına girerek iletişimi izleme veya değiştirme girişimidir.

### Nasıl çalışır?

Normal iletişim:

```text
Kullanıcı ↔ Sunucu
```

MITM durumunda:

```text
Kullanıcı ↔ Saldırgan ↔ Sunucu
```

Saldırgan iki tarafın doğrudan iletişim kurduğunu düşünmesine neden olmaya çalışabilir.

### Hedefi nedir?

* İletişimi izlemek
* Hassas bilgileri elde etmek
* Verileri değiştirmeye çalışmak

Bu nedenle **Confidentiality** ve **Integrity** etkilenebilir.

### Nasıl fark edilebilir?

* Sertifika uyarıları
* Beklenmeyen bağlantı davranışları
* Güvenilmeyen ağlarda anormal trafik
* Ağ güvenlik sistemlerindeki şüpheli davranışlar

### Temel korunma yöntemi:

* HTTPS/TLS kullanmak
* Sertifika uyarılarını dikkate almak
* Güvenilir ağları kullanmak
* Güvenli bağlantı mekanizmaları kullanmak
* Ağ trafiğini izlemek

### 🧠 Akılda tut:

> **MITM = İletişimin arasına gir.**

---

# 6. Sniffing

### Saldırı adı:

**Sniffing**

### Nedir?

Ağ üzerinden geçen veri trafiğinin yakalanması ve incelenmesidir.

### Nasıl çalışır?

Saldırgan ağ trafiğini gözlemlemeye ve paketlerdeki bilgileri analiz etmeye çalışır.

Özellikle şifrelenmemiş veya yeterince korunmayan iletişimlerde hassas bilgiler açığa çıkabilir.

### Hedefi nedir?

* Ağ trafiği
* Kullanıcı bilgileri
* Oturum bilgileri
* Hassas veriler

Temel olarak **Confidentiality (Gizlilik)** etkilenebilir.

### Nasıl fark edilebilir?

Sniffing pasif olarak gerçekleştirilebildiği için doğrudan fark edilmesi her zaman kolay değildir.

* Ağ izleme sistemleri
* Trafik analizleri
* Güvenlik sensörleri
* Anormal ağ davranışları

tespit sürecinde yardımcı olabilir.

### Temel korunma yöntemi:

* HTTPS/TLS
* Şifreli iletişim
* Güvenli Wi-Fi
* Ağ segmentasyonu
* Ağ trafiğinin izlenmesi

### 🧠 Akılda tut:

> **Sniffing = Ağ trafiğini dinle/izle.**

---

# 7. Spoofing

### Saldırı adı:

**Spoofing**

### Nedir?

Saldırganın kendisini başka bir kişi, cihaz, adres veya hizmet gibi göstermeye çalışmasıdır.

### Nasıl çalışır?

Saldırgan iletişimde kullanılan kimlik veya adres bilgilerinin sahte görünmesini sağlamaya çalışır.

Örnekleri:

* IP Spoofing
* DNS Spoofing
* Email Spoofing
* MAC Spoofing

### Hedefi nedir?

Güvenilir bir kaynakmış gibi görünerek kullanıcıyı veya sistemi yanıltmak.

### Nasıl fark edilebilir?

* Kaynak bilgilerindeki tutarsızlıklar
* DNS kayıtlarındaki anormallikler
* E-posta doğrulama kontrollerinin başarısız olması
* Ağ güvenlik sistemlerindeki anormal trafik

### Temel korunma yöntemi:

* Güçlü kimlik doğrulama
* E-posta doğrulama mekanizmaları
* DNS güvenliği
* Ağ filtreleme
* Kaynak doğrulama

### 🧠 Akılda tut:

> **Spoofing = “Ben başkasıyım” diye taklit etmek.**

---

# 🌍 4.2 Web Saldırıları

## 8. SQL Injection — SQLi

### Saldırı adı:

**SQL Injection**

### Nedir?

Web uygulamasının kullanıcıdan aldığı veriyi güvenli şekilde işlememesi sonucunda, saldırganın SQL sorgusunun davranışını değiştirmeye çalışmasıdır.

### Nasıl çalışır?

Web uygulaması kullanıcı girdisini doğrudan veya güvenli olmayan şekilde SQL sorgusuna dahil ederse saldırgan bu girdiyi kötüye kullanmaya çalışabilir.

```text
Kullanıcı
   ↓
Web uygulaması
   ↓
SQL sorgusu
   ↓
Veritabanı
```

### Hedefi nedir?

🗄️ **Veritabanı**

Başarılı olduğunda:

* Yetkisiz veri erişimi
* Veri değiştirme
* Veri silme
* Hassas bilgilerin açığa çıkması

gibi sonuçlar oluşabilir.

### Nasıl fark edilebilir?

* Web uygulaması logları
* Anormal veritabanı sorguları
* Beklenmeyen hata mesajları
* Veritabanı erişim kayıtları
* WAF alarmları

### Temel korunma yöntemi:

* Parameterized Queries / Prepared Statements
* Güvenli kodlama
* Input validation
* Veritabanı hesabına minimum yetki
* WAF

### 🧠 Akılda tut:

> **SQL Injection = Veritabanına giden sorguyu manipüle etmeye çalışma.**

---

# 9. XSS — Cross-Site Scripting

### Saldırı adı:

**XSS**

### Nedir?

Saldırganın web uygulamasına eklediği içeriğin başka bir kullanıcının tarayıcısında istenmeyen script olarak çalışmasına neden olmaya çalışmasıdır.

### Nasıl çalışır?

Kullanıcıdan alınan veri güvenli şekilde işlenmez veya gösterilmezse saldırgan kötü amaçlı script içeriği yerleştirmeye çalışabilir.

### Hedefi nedir?

🖥️ **Kullanıcının web tarayıcısı**

### Nasıl fark edilebilir?

* Uygulama davranışındaki anormallikler
* Güvenlik taramalarındaki bulgular
* Web uygulaması logları
* WAF alarmları
* Beklenmeyen script davranışları

### Temel korunma yöntemi:

* Output encoding
* Input validation
* Content Security Policy (CSP)
* Güvenli web framework'leri
* Güvenli cookie ayarları

### 🧠 Akılda tut:

> **XSS = Web uygulaması üzerinden tarayıcıda script çalıştırmaya çalışma.**

---

# 10. CSRF — Cross-Site Request Forgery

### Saldırı adı:

**CSRF**

### Nedir?

Kullanıcının bir web sitesindeki mevcut oturumunu kötüye kullanarak, kullanıcıya istemediği bir işlem yaptırmaya çalışma saldırısıdır.

### Nasıl çalışır?

Kullanıcı bir web uygulamasında oturum açmıştır.

Saldırgan, kullanıcının tarayıcısını kullanarak uygulamaya istenmeyen bir istek gönderilmesini sağlamaya çalışır.

### Hedefi nedir?

👤 **Kullanıcının mevcut oturumu ve yetkisi**

### Nasıl fark edilebilir?

* Beklenmeyen işlem kayıtları
* Şüpheli istek kaynakları
* Web sunucusu logları
* Kullanıcı tarafından başlatılmamış işlemler
* Güvenlik testleri

### Temel korunma yöntemi:

* CSRF token
* SameSite cookie
* Origin/Referer kontrolleri
* Uygun oturum yönetimi

### 🧠 Akılda tut:

> **CSRF = Kullanıcının yetkisini kullanarak istemediği işlem yaptırmaya çalışma.**

---

# 11. Directory Traversal

### Saldırı adı:

**Directory Traversal**

### Nedir?

Web uygulamasının dosya yollarını güvenli şekilde kontrol etmemesi nedeniyle normalde erişilmemesi gereken dosya veya klasörlere ulaşmaya çalışma saldırısıdır.

### Nasıl çalışır?

Saldırgan uygulamanın kullandığı dosya yolunu manipüle ederek izin verilen dizinin dışına çıkmaya çalışır.

### Hedefi nedir?

📁 **Sunucunun dosya sistemi**

### Nasıl fark edilebilir?

* Şüpheli dosya yolu istekleri
* Web sunucusu logları
* Beklenmeyen dosya erişimleri
* Dosya erişim hataları
* WAF alarmları

### Temel korunma yöntemi:

* Dosya yollarını güvenli şekilde doğrulamak
* Allowlist kullanmak
* Path normalization
* En az yetki prensibi
* Kullanıcı girdisini doğrudan dosya yolu olarak kullanmamak

### 🧠 Akılda tut:

> **Directory Traversal = İzin verilen klasörün dışına çıkmaya çalışma.**

---

# 12. File Inclusion

### Saldırı adı:

**File Inclusion**

### Nedir?

Web uygulamasının kullanıcı kontrollü dosya/path bilgisini güvenli şekilde işlememesi sonucunda, saldırganın uygulamaya istenmeyen bir dosyayı dahil ettirmeye çalışmasıdır.

### Nasıl çalışır?

Uygulama hangi dosyanın yükleneceğini kullanıcıdan alıyorsa ve yeterli kontrol yapılmıyorsa saldırgan bu mekanizmayı kötüye kullanmaya çalışabilir.

Temel türleri:

* **LFI:** Local File Inclusion
* **RFI:** Remote File Inclusion

### Hedefi nedir?

📄 **Uygulamanın dosya dahil etme mekanizması**

### Nasıl fark edilebilir?

* Şüpheli dosya/path istekleri
* Web sunucusu logları
* Beklenmeyen dosya yükleme davranışları
* Uygulama hataları
* Güvenlik tarama sonuçları

### Temel korunma yöntemi:

* Allowlist kullanmak
* Kullanıcı girdisini doğrudan dosya yolu olarak kullanmamak
* Güvenli path kontrolü
* Gereksiz dosya dahil etme özelliklerini kapatmak
* En az yetki prensibi

### 🧠 Akılda tut:

> **File Inclusion = Uygulamaya istenmeyen dosya dahil ettirmeye çalışma.**

---

# 🎣 4.3 Kullanıcı Odaklı Saldırılar

## 13. Phishing

### Saldırı adı:

**Phishing (Oltalama)**

### Nedir?

Kullanıcıyı kandırarak parola, kişisel bilgi veya başka hassas bilgileri elde etmeye çalışan sosyal mühendislik saldırısıdır.

### Nasıl çalışır?

Saldırgan sahte bir e-posta, mesaj, web sitesi veya başka bir iletişim kanalı kullanarak güvenilir bir kaynaktan geliyormuş gibi davranır.

Örneğin:

> “Hesabınız kapatılacaktır. Giriş yapmak için bağlantıya tıklayın.”

### Hedefi nedir?

👤 **Kullanıcı**

Amaç:

* Kimlik bilgilerini ele geçirmek
* Hesapları ele geçirmek
* Zararlı yazılım çalıştırmak
* Hassas bilgi elde etmek

### Nasıl fark edilebilir?

* Şüpheli gönderen adresi
* Beklenmeyen bağlantılar
* Aciliyet baskısı
* Yazım veya içerik tutarsızlıkları
* Şüpheli ek dosyalar
* Alan adı farklılıkları

### Temel korunma yöntemi:

* Göndereni doğrulamak
* Şüpheli bağlantılara tıklamamak
* MFA kullanmak
* E-posta güvenlik filtreleri
* Kullanıcı farkındalık eğitimi

### 🧠 Akılda tut:

> **Phishing = Sahte mesajla kullanıcıyı kandır.**

---

# 14. Spear Phishing

### Saldırı adı:

**Spear Phishing**

### Nedir?

Belirli bir kişi veya gruba özel hazırlanan hedefli phishing saldırısıdır.

### Nasıl çalışır?

Saldırgan hedef hakkında bilgi toplar ve mesajını kişiye özel hale getirir.

Örneğin:

> “Zeynep, staj evrakların ekte.”

gibi kişiye özel görünen bir mesaj hazırlanabilir.

### Hedefi nedir?

🎯 **Belirli kişi veya grup**

### Nasıl fark edilebilir?

* Beklenmeyen kişiselleştirilmiş e-postalar
* Gönderici bilgilerinde tutarsızlık
* Şüpheli bağlantı veya ek
* Normal iş akışına uymayan talepler

### Temel korunma yöntemi:

* Göndericiyi farklı bir kanaldan doğrulamak
* MFA
* E-posta filtreleme
* Kullanıcı farkındalığı
* Hassas işlemler için doğrulama prosedürü

### 🧠 Akılda tut:

> **Spear Phishing = Hedefli phishing.**

---

# 15. Whaling

### Saldırı adı:

**Whaling**

### Nedir?

Üst düzey yöneticiler veya yüksek değerli hedeflere yönelik hedefli phishing saldırısıdır.

### Nasıl çalışır?

Saldırgan hedefin görevini ve şirket içindeki rolünü dikkate alarak oldukça inandırıcı bir mesaj hazırlamaya çalışır.

### Hedefi nedir?

🐋 **Üst düzey yöneticiler / yüksek değerli hedefler**

### Nasıl fark edilebilir?

* Olağandışı ödeme talepleri
* Acil ve gizli işlem talepleri
* Beklenmeyen dosya veya bağlantılar
* Gönderici bilgilerindeki tutarsızlıklar

### Temel korunma yöntemi:

* Kritik işlemlerde ikinci doğrulama
* MFA
* E-posta güvenliği
* Yönetici farkındalık eğitimi
* Ödeme prosedürleri

### 🧠 Akılda tut:

> **Whaling = Büyük/hedef değeri yüksek kişiyi hedefleyen phishing.**

---

# 16. Social Engineering

### Saldırı adı:

**Social Engineering (Sosyal Mühendislik)**

### Nedir?

İnsanların güven, merak, korku, yardım etme isteği veya aciliyet duygusu gibi özelliklerini kullanarak onları belirli bir işlemi yapmaya veya bilgi vermeye ikna etmeye çalışma yöntemidir.

### Nasıl çalışır?

Saldırgan teknik bir açığı kullanmak yerine insan davranışını manipüle etmeye çalışır.

Kendisini örneğin:

* IT çalışanı
* Banka çalışanı
* Yönetici
* Kargo görevlisi

gibi gösterebilir.

### Hedefi nedir?

👤 **İnsan**

### Nasıl fark edilebilir?

* Gereksiz kişisel bilgi talepleri
* Aciliyet baskısı
* Yetki dışı işlem talepleri
* Kimlik doğrulamayı reddetme
* Şüpheli telefon/e-posta/mesajlar

### Temel korunma yöntemi:

* Güvenlik farkındalığı
* Kimlik doğrulama
* Kritik işlemlerde ikinci kontrol
* Yetki prosedürleri
* Şüpheli talepleri raporlama

### 🧠 Akılda tut:

> **Social Engineering = Sistemi değil, insanı kandır.**

---

# 17. Business Email Compromise — BEC

### Saldırı adı:

**Business Email Compromise (BEC)**

### Nedir?

Saldırganın bir şirket çalışanı, yönetici veya iş ortağı gibi davranarak para transferi veya hassas bilgi aktarımı gibi işlemler yaptırmaya çalıştığı saldırı türüdür.

### Nasıl çalışır?

Saldırgan sahte bir iş e-postası kullanabilir veya ele geçirilmiş bir hesabın güvenilirliğinden yararlanabilir.

Örneğin:

> “CEO'dan gelmiş gibi görünen acil ödeme talebi.”

### Hedefi nedir?

* Para
* Şirket bilgileri
* İş süreçleri
* Çalışanlar
* İş ortakları

### Nasıl fark edilebilir?

* Beklenmeyen ödeme talepleri
* Hesap veya banka bilgilerinin değiştirildiğinin bildirilmesi
* Acil/gizli işlem talepleri
* Olağandışı e-posta davranışları
* E-posta başlıklarındaki veya gönderici bilgilerindeki tutarsızlıklar

### Temel korunma yöntemi:

* Ödeme taleplerinde ikinci doğrulama
* MFA
* E-posta güvenliği
* Çalışan farkındalık eğitimi
* Kritik işlemlerde prosedür uygulanması

### 🧠 Akılda tut:

> **BEC = İş e-postası/güven ilişkisini kullanarak işlem yaptır.**

---

# 🧠 Saldırıları Karıştırmamak İçin Kısa Harita

## 🌐 Ağ

```text
DoS
→ Düşür

DDoS
→ Dağıt + düşür

Brute Force
→ 1 hesap + çok parola

Password Spraying
→ Çok hesap + az/yaygın parola

MITM
→ Araya gir

Sniffing
→ Dinle / izle

Spoofing
→ Taklit et
```

## 🌍 Web

```text
SQL Injection
→ 🗄️ Veritabanı

XSS
→ 🖥️ Tarayıcı

CSRF
→ 👤 Kullanıcının yetkisi

Directory Traversal
→ 📁 Klasör sınırını aş

File Inclusion
→ 📄 Dosya dahil ettir
```

## 🎣 Kullanıcı

```text
Phishing
→ Genel oltalama

Spear Phishing
→ 🎯 Belirli kişi

Whaling
→ 🐋 Üst düzey kişi

Social Engineering
→ 🧠 İnsan psikolojisini manipüle et

BEC
→ 💼 İş/e-posta güvenini kötüye kullan
```

---

# 🔎 En Önemli Karşılaştırmalar

### DoS vs DDoS

**DoS:** Hizmeti kullanılamaz hale getirmeye çalışma.

**DDoS:** Aynı amacın çok sayıda dağıtılmış kaynaktan gerçekleştirilmesi.

---

### Brute Force vs Password Spraying

**Brute Force:**

> 1 hesap + çok parola

**Password Spraying:**

> Çok hesap + az/yaygın parola

---

### MITM vs Sniffing

**MITM:**

> İletişimin arasına girme.

**Sniffing:**

> Trafiği izleme/yakalama.

---

### SQL Injection vs XSS

**SQL Injection:**

> Veritabanını hedefler.

**XSS:**

> Kullanıcının tarayıcısını hedefler.

---

### XSS vs CSRF

**XSS:**

> Tarayıcıda istenmeyen script çalıştırmaya çalışma.

**CSRF:**

> Kullanıcının mevcut yetkisiyle istenmeyen işlem yaptırmaya çalışma.

---

### Directory Traversal vs File Inclusion

**Directory Traversal:**

> Dosya/klasör sınırının dışına çıkmaya çalışma.

**File Inclusion:**

> Uygulamaya istenmeyen dosya dahil ettirmeye çalışma.

---

### Phishing vs Spear Phishing vs Whaling

**Phishing:**

> Genel hedef.

**Spear Phishing:**

> Belirli kişi/grup.

**Whaling:**

> Üst düzey/yüksek değerli hedef.

---

# 🧠 Genel Öğrenme Notum

Bu bölümde saldırıların isimlerini ezberlemekten çok, saldırının **neyi hedeflediğini** anlamanın daha önemli olduğunu öğrendim.

Bir saldırıyla karşılaştığımda kendime şu soruları sorabilirim:

```text
1. Saldırgan neyi hedefliyor?
        ↓
2. Sisteme mi?
   Ağa mı?
   Web uygulamasına mı?
   Kullanıcıya mı?
        ↓
3. Ne yapmaya çalışıyor?
        ↓
4. Hangi güvenlik özelliği etkileniyor?
   Confidentiality?
   Integrity?
   Availability?
        ↓
5. Bunu nasıl tespit edebilirim?
        ↓
6. Nasıl önleyebilirim?
```

Bu yaklaşımın saldırı isimlerini tek tek ezberlemekten daha faydalı olduğunu düşünüyorum.

---

# 📌 Kısa Özet

```text
AĞ
→ DoS
→ DDoS
→ Brute Force
→ Password Spraying
→ MITM
→ Sniffing
→ Spoofing

WEB
→ SQL Injection
→ XSS
→ CSRF
→ Directory Traversal
→ File Inclusion

KULLANICI
→ Phishing
→ Spear Phishing
→ Whaling
→ Social Engineering
→ BEC
```

> **Temel mantık: Saldırının adını değil, önce neyi hedeflediğini ve ne yapmaya çalıştığını anlamaya çalış.**
