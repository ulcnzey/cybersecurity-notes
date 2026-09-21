# 🌐 Ağ Saldırıları

Bu bölümde ağlar ve ağ üzerinden gerçekleşebilecek temel saldırı türlerini araştırdım.

Saldırı türlerini ezberlemek yerine, saldırının **neyi hedeflediğini ve nasıl bir mantığa sahip olduğunu** anlamaya çalıştım.

---
<img width="678" height="452" alt="image" src="https://github.com/user-attachments/assets/af2e9e84-9473-414c-b653-aeba27b1ac64" />

## 1. DoS — Denial of Service

**DoS (Denial of Service)**, bir sistemin veya hizmetin normal kullanıcılar tarafından kullanılamaz hale gelmesini amaçlayan saldırıdır.

Saldırgan sistemi aşırı istek veya işlemlerle meşgul etmeye çalışır.

### Basitçe:

> **DoS = Sistemi kullanılamaz hale getirmeye çalışma.**

En çok etkilediği CIA bileşeni:

**Availability (Erişilebilirlik)**

---

## 2. DDoS — Distributed Denial of Service

**DDoS**, DoS saldırısının birçok farklı kaynaktan gerçekleştirilmesidir.

Saldırı çok sayıda cihazdan geldiği için saldırıyı tek bir kaynağa bağlamak daha zor olabilir.

Bu cihazlar bazen saldırganın kontrol ettiği bir **botnet** içerisinde bulunabilir.

### DoS ve DDoS farkı:

* **DoS:** Daha sınırlı/tekil kaynaklardan gelen saldırı
* **DDoS:** Dağıtılmış birçok kaynaktan gelen saldırı

### Akılda tut:

> **DDoS = Dağıtılmış saldırıyla hizmeti kullanılamaz hale getirme.**

En çok etkilediği CIA bileşeni:

**Availability**

---

## 3. Brute Force

**Brute Force**, bir hesabın parolasını bulmak amacıyla çok sayıda parola kombinasyonunun denenmesidir.

Örneğin saldırgan tek bir hesap üzerinde birçok farklı parola deneyebilir.

```text
Hesap
  ↓
123456
password
qwerty
...
```

### Akılda tut:

> **Brute Force = Bir hesapta çok sayıda parola denemek.**

---

## 4. Password Spraying

**Password Spraying**, az sayıda yaygın parolanın çok sayıda kullanıcı hesabında denenmesidir.

Örneğin:

```text
Kullanıcı 1 → yaygın parola
Kullanıcı 2 → yaygın parola
Kullanıcı 3 → yaygın parola
Kullanıcı 4 → yaygın parola
```

### Brute Force ile farkı:

**Brute Force:**

> 1 hesap + çok parola

**Password Spraying:**

> Çok hesap + az sayıda yaygın parola

### Akılda tut:

> **Password Spraying = Aynı parolayı birçok hesaba yaymak.**

---

## 5. MITM — Man-in-the-Middle

**MITM (Man-in-the-Middle)** saldırısında saldırgan, iki taraf arasındaki iletişimin arasına girmeye çalışır.

Normal iletişim:

```text
Kullanıcı ↔ Sunucu
```

MITM durumunda:

```text
Kullanıcı ↔ Saldırgan ↔ Sunucu
```

Saldırgan iletişimi izlemeye veya değiştirmeye çalışabilir.

Bu nedenle:

* Confidentiality (Gizlilik)
* Integrity (Bütünlük)

etkilenebilir.

### Akılda tut:

> **MITM = İletişimin arasına gir.**

---

## 6. Sniffing

**Sniffing**, ağ üzerinden geçen veri trafiğinin yakalanması ve incelenmesidir.

Saldırgan ağ trafiğini izlemeye çalışabilir.

Özellikle yeterince korunmayan iletişimlerde hassas bilgilerin açığa çıkması riski oluşabilir.

### MITM ile farkı:

**Sniffing:**

> Trafiği yakalama/izleme

**MITM:**

> İletişimin arasına girme

MITM sırasında sniffing yapılabilir ancak iki kavram aynı değildir.

### Akılda tut:

> **Sniffing = Trafiği izle.**

---

## 7. Spoofing

**Spoofing**, saldırganın kendisini başka bir kaynak veya kimlik gibi göstermeye çalışmasıdır.

Farklı türleri olabilir:

* IP Spoofing
* DNS Spoofing
* Email Spoofing
* MAC Spoofing

Örneğin saldırgan sahte bir e-posta adresi kullanarak güvenilir bir kişiden mesaj geliyormuş gibi görünmeye çalışabilir.

### Akılda tut:

> **Spoofing = Taklit et.**

---

# 🧠 Saldırıları Kolay Hatırlama

Ağ saldırılarını ilk öğrenirken şu kısa kelimeler benim için daha anlaşılır oldu:

| Saldırı           | Kısa çağrışım     |
| ----------------- | ----------------- |
| DoS               | 💥 Düşür          |
| DDoS              | 🌐 Dağıt          |
| Brute Force       | 🔑 Çok dene       |
| Password Spraying | 🔑 Çok hesaba yay |
| MITM              | 🧍 Araya gir      |
| Sniffing          | 👀 İzle           |
| Spoofing          | 🎭 Taklit et      |

---

# 🔐 CIA Triad ile İlişkisi

Saldırıların güvenlik açısından hangi alanı etkileyebileceğini düşünmek de önemlidir.

| Saldırı           | Etkilenebilecek alan        |
| ----------------- | --------------------------- |
| DoS               | Availability                |
| DDoS              | Availability                |
| Brute Force       | Confidentiality             |
| Password Spraying | Confidentiality             |
| MITM              | Confidentiality / Integrity |
| Sniffing          | Confidentiality             |
| Spoofing          | Confidentiality / Integrity |

Bu tablo saldırıların yalnızca tek bir CIA bileşenini etkileyebileceği anlamına gelmez. Etki, saldırının nasıl gerçekleştirildiğine ve sonucuna göre değişebilir.

---

# 🧠 Kendi Öğrenme Notlarım

Bu bölümde benim için en önemli ayrım **Brute Force ve Password Spraying** arasındaki fark oldu.

* Brute Force → bir hesap üzerinde çok sayıda parola denenmesi
* Password Spraying → az sayıda yaygın parolanın birçok hesapta denenmesi

Ayrıca:

* Sniffing → trafiği izlemek
* MITM → iletişimin arasına girmek
* Spoofing → başka bir kaynakmış gibi görünmek

şeklinde düşünebilirim.

DoS ve DDoS ise temel olarak hizmetin erişilebilirliğini etkilemeye yönelik saldırılardır.

---

# 🎯 Kısa Özet

```text
AĞ SALDIRILARI

DoS
→ Sistemi kullanılamaz hale getirmeye çalışma

DDoS
→ Dağıtılmış kaynaklarla sistemi kullanılamaz hale getirme

Brute Force
→ Bir hesapta çok sayıda parola deneme

Password Spraying
→ Aynı/yaygın parolaları birçok hesapta deneme

MITM
→ İletişimin arasına girme

Sniffing
→ Ağ trafiğini izleme

Spoofing
→ Kimlik/kaynak taklidi yapma
```

---
# 🔎 Ağ Saldırılarını Mantığıyla Anlama

Bu bölümde her saldırıyı sadece tanımıyla değil; ne olduğu, genel olarak nasıl gerçekleştiği, neyi hedeflediği, nasıl fark edilebileceği ve temel olarak nasıl önlenebileceği açısından incelemeye çalıştım.

---

## 💥 1. DoS — Denial of Service

### Saldırı adı:

**DoS (Denial of Service)**

### Nedir?

Bir sistemin veya hizmetin normal kullanıcılar tarafından kullanılamaz hale gelmesini amaçlayan saldırıdır.

### Nasıl çalışır?

Saldırgan, hedef sistemin kapasitesini aşacak şekilde yoğun istek veya işlem oluşmasına neden olmaya çalışır.

Sistem kaynakları bu yükle meşgul olduğunda gerçek kullanıcıların hizmete erişmesi zorlaşabilir.

### Hedefi nedir?

**Hizmetin erişilebilirliğini (Availability) bozmak.**

### Nasıl fark edilebilir?

* Normalden çok daha fazla trafik görülmesi
* Sunucunun aşırı kaynak tüketmesi
* Web sitesinin yavaşlaması veya erişilememesi
* Aynı servise yönelik olağandışı istek artışı

### Temel korunma yöntemi:

* Trafik izleme
* Rate limiting
* Firewall ve güvenlik kuralları
* Trafik filtreleme
* Yük dengeleme
* DDoS koruma hizmetleri

### 🧠 Akılda tut:

> **DoS = Sistemi aşırı yükle → kullanıcı hizmete ulaşamasın.**

---

# 🌐 2. DDoS — Distributed Denial of Service

### Saldırı adı:

**DDoS (Distributed Denial of Service)**

### Nedir?

Bir hizmetin çok sayıda farklı kaynaktan gelen yoğun trafik veya isteklerle kullanılamaz hale getirilmeye çalışılmasıdır.

### Nasıl çalışır?

Saldırı tek bir cihazdan değil, çok sayıda kaynaktan gelir.

Bu kaynaklar saldırganın kontrol ettiği ele geçirilmiş cihazlardan oluşan bir **botnet** olabilir.

```text
Cihaz ──┐
Cihaz ──┤
Cihaz ──┤
Cihaz ──┼──→ Hedef sistem
Cihaz ──┤
Cihaz ──┘
```

### Hedefi nedir?

Temel hedef **Availability (Erişilebilirlik)** değerini bozmaktır.

### Nasıl fark edilebilir?

* Çok sayıda farklı kaynaktan anormal trafik
* Trafikte ani ve büyük artış
* Sunucuda yüksek CPU/bant genişliği kullanımı
* Servisin yavaşlaması veya tamamen erişilememesi

### Temel korunma yöntemi:

* DDoS koruma servisleri
* Trafik filtreleme
* Rate limiting
* CDN ve yük dengeleme
* Ağ trafiğinin sürekli izlenmesi

### 🧠 Akılda tut:

> **DDoS = Çok sayıda kaynak → aynı hedef → hizmet kullanılamıyor.**

---

# 🔑 3. Brute Force

### Saldırı adı:

**Brute Force**

### Nedir?

Bir hesabın parolasını bulmak amacıyla çok sayıda parola veya parola kombinasyonunun denenmesidir.

### Nasıl çalışır?

Saldırgan bir hesap için art arda farklı parola tahminleri yapmaya çalışır.

Mantığı:

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

Genellikle **kullanıcı hesabının ele geçirilmesi** hedeflenir.

Bu nedenle başarılı olduğunda:

**Confidentiality** ve hesap güvenliği etkilenebilir.

### Nasıl fark edilebilir?

* Aynı hesaba çok sayıda başarısız giriş
* Kısa sürede art arda giriş denemeleri
* Olağandışı IP adreslerinden giriş denemeleri
* Hesap kilitlenmeleri
* SIEM üzerinde başarısız giriş alarmı

### Temel korunma yöntemi:

* Güçlü ve benzersiz parolalar
* MFA
* Rate limiting
* Hesap kilitleme veya geçici geciktirme
* Başarısız giriş denemelerini izleme

### 🧠 Akılda tut:

> **Brute Force = Bir hesabı çok farklı anahtarla denemek.**

---

# 🔑 4. Password Spraying

### Saldırı adı:

**Password Spraying**

### Nedir?

Az sayıda yaygın parolanın çok sayıda kullanıcı hesabında denenmesidir.

### Nasıl çalışır?

Brute Force'ta:

```text
1 hesap → çok parola
```

Password Spraying'de:

```text
çok hesap → aynı/yaygın parola
```

Örneğin saldırgan birçok hesabı hedefleyip aynı yaygın parolayı denemeye çalışabilir.

### Hedefi nedir?

Kullanıcı hesaplarını ele geçirmek.

### Nasıl fark edilebilir?

* Aynı zaman aralığında birçok hesaba başarısız giriş
* Aynı kaynaktan çok sayıda kullanıcı adına giriş denemesi
* Birçok hesapta aynı tür başarısız kimlik doğrulama kayıtları
* SIEM üzerinde anormal login pattern'leri

### Temel korunma yöntemi:

* MFA
* Güçlü parola politikası
* Yaygın ve ele geçirilmiş parolaların engellenmesi
* Giriş denemelerinin izlenmesi
* Anormal kimlik doğrulama davranışlarının tespit edilmesi

### 🧠 Akılda tut:

> **Password Spraying = Aynı anahtarı birçok kapıda denemek.**

---

# 🧍 5. MITM — Man-in-the-Middle

### Saldırı adı:

**MITM (Man-in-the-Middle)**

### Nedir?

İki taraf arasındaki iletişimin arasına girerek iletişimi izleme veya değiştirme girişimidir.

### Nasıl çalışır?

Normalde:

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
* Veriyi değiştirmeye çalışmak

Bu nedenle **Confidentiality** ve **Integrity** etkilenebilir.

### Nasıl fark edilebilir?

* Sertifika uyarıları
* Beklenmeyen bağlantı davranışları
* Güvenilmeyen ağlarda şüpheli trafik
* Ağ üzerinde anormal yönlendirme davranışları
* Güvenlik sistemlerindeki anormal trafik kayıtları

### Temel korunma yöntemi:

* HTTPS/TLS kullanmak
* Sertifika uyarılarını dikkate almak
* Güvenilir ağlar kullanmak
* VPN gibi güvenli bağlantı mekanizmalarından yararlanmak
* Ağ güvenliğini izlemek

### 🧠 Akılda tut:

> **MITM = İki kişinin konuşmasının arasına üçüncü kişi girdi.**

---

# 👀 6. Sniffing

### Saldırı adı:

**Sniffing**

### Nedir?

Ağ üzerinden geçen veri trafiğinin yakalanması ve incelenmesidir.

### Nasıl çalışır?

Saldırgan ağ trafiğini gözlemlemeye ve paketlerdeki bilgileri analiz etmeye çalışır.

Korunmayan veya zayıf korunan iletişimlerde hassas bilgiler açığa çıkabilir.

### Hedefi nedir?

* Ağ trafiği
* Kullanıcı bilgileri
* Oturum bilgileri
* Hassas veriler

Temel olarak **Confidentiality** hedef alınabilir.

### Nasıl fark edilebilir?

Sniffing pasif olarak gerçekleştirilebildiği için doğrudan fark edilmesi her zaman kolay değildir.

Ancak:

* Ağ izleme sistemleri
* Anormal ağ davranışları
* Güvenlik sensörleri
* Trafik analizleri

yardımcı olabilir.

### Temel korunma yöntemi:

* HTTPS/TLS
* Şifreli iletişim
* Güvenli Wi-Fi
* Ağ segmentasyonu
* Ağ trafiğinin izlenmesi

### 🧠 Akılda tut:

> **Sniffing = Ağdaki konuşmayı dinlemek.**

---

# 🎭 7. Spoofing

### Saldırı adı:

**Spoofing**

### Nedir?

Bir saldırganın kendisini başka bir kişi, cihaz, adres veya hizmet gibi göstermeye çalışmasıdır.

### Nasıl çalışır?

Saldırgan iletişimde kullanılan kimlik veya adres bilgilerinin sahte görünmesini sağlamaya çalışır.

Spoofing farklı seviyelerde gerçekleşebilir:

* IP spoofing
* DNS spoofing
* Email spoofing
* MAC spoofing

### Hedefi nedir?

Güvenilir bir kaynakmış gibi görünerek kullanıcıyı veya sistemi yanıltmak.

### Nasıl fark edilebilir?

* Kaynak bilgilerindeki tutarsızlıklar
* DNS kayıtlarındaki anormallikler
* E-posta doğrulama kontrollerindeki başarısızlıklar
* Ağ güvenlik sistemlerindeki anormal trafik
* Beklenmeyen kimlik veya adres bilgileri

### Temel korunma yöntemi:

* Kimlik doğrulama mekanizmaları
* E-posta doğrulama mekanizmaları
* DNS güvenliği
* Ağ filtreleme
* Güvenlik sistemlerinde kaynak doğrulama

### 🧠 Akılda tut:

> **Spoofing = “Ben başkasıyım” diye taklit etmek.**

---

# 🧠 Hepsini Tekrar Karşılaştıralım

| Saldırı           | Nedir?                             | Ana fikir    |
| ----------------- | ---------------------------------- | ------------ |
| DoS               | Hizmeti aşırı yükleme              | 💥 Düşür     |
| DDoS              | Dağıtılmış aşırı yükleme           | 🌐 Dağıt     |
| Brute Force       | Çok parola denemesi                | 🔑 Çok dene  |
| Password Spraying | Yaygın parolayı çok hesapta deneme | 🔑 Yay       |
| MITM              | İletişimin arasına girme           | 🧍 Araya gir |
| Sniffing          | Trafiği izleme                     | 👀 Dinle     |
| Spoofing          | Kimlik/kaynak taklidi              | 🎭 Taklit et |

---

# 🎯 Benim İçin En Önemli Ayrımlar

### DoS vs DDoS

> **DoS:** Hizmeti aşırı yükle

> **DDoS:** Bunu dağıtılmış çok sayıda kaynaktan yap

---

### Brute Force vs Password Spraying

> **Brute Force:** 1 hesap + çok parola

> **Password Spraying:** Çok hesap + az/yaygın parola

---

### MITM vs Sniffing

> **MITM:** Araya gir

> **Sniffing:** Trafiği izle

---

### Sniffing vs Spoofing

> **Sniffing:** Dinlemeye/izlemeye çalış

> **Spoofing:** Başka biriymiş gibi görünmeye çalış

---

# 🧩 Gerçek Hayattan Benzetme

Bir kafede olduğunu düşünelim:

**DoS:**
Bir kişi kafedeki bütün masaları dolduruyor → gerçek müşteriler oturamıyor.

**DDoS:**
Bunu aynı anda yüzlerce kişi yapıyor.

**Brute Force:**
Bir kapının şifresini farklı kombinasyonlarla sürekli deniyorsun.

**Password Spraying:**
Aynı anahtarı birçok kapıda deniyorsun.

**MITM:**
Sen ve arkadaşın konuşurken üçüncü kişi aranıza giriyor.

**Sniffing:**
Üçüncü kişi konuşmanızı gizlice dinliyor.

**Spoofing:**
Üçüncü kişi arkadaşınmış gibi davranıyor.

Bu benzetmeler saldırıların teknik ayrıntılarından çok **temel mantığını hatırlamak için** kullanılabilir.

---

# 📝 Kendi Kendime Soracağım Sorular

1. Bir saldırı hizmeti kullanılamaz hale getiriyorsa hangi saldırı türleri akla gelir?
2. Brute Force ile Password Spraying arasındaki temel fark nedir?
3. MITM neden "araya girme" olarak düşünülebilir?
4. Sniffing ile MITM arasındaki fark nedir?
5. Spoofing neden "taklit" olarak düşünülebilir?
6. Hangi saldırılar özellikle Confidentiality'yi etkileyebilir?
7. Hangi saldırılar Availability üzerinde belirgin etki oluşturabilir?
8. Bir SOC analisti bu saldırıları tespit etmek için neden log ve ağ trafiğini izler?

---

# 📌 Kısa Hatırlatma

```text
DoS
→ Düşür

DDoS
→ Dağıt ve düşür

Brute Force
→ Çok şifre dene

Password Spraying
→ Bir şifreyi çok hesapta dene

MITM
→ Araya gir

Sniffing
→ Dinle / izle

Spoofing
→ Taklit et
```

Bu bölümde amacım saldırıların isimlerini ezberlemekten çok, **hangi saldırının ne yaptığına dair zihinsel bir model oluşturmak** oldu.


# 📚 Kaynaklar

* NIST — Cybersecurity Glossary
* CISA — Cybersecurity Resources
* NIST — Computer Security Resource Center

Bu notlar, temel kavramları öğrenmek amacıyla kendi ifadelerimle hazırlanmıştır.
