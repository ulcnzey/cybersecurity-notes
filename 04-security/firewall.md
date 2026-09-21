# 🔥 Firewall Nedir?

**Firewall (Güvenlik Duvarı)**, ağ üzerinden gelen ve giden trafiği belirlenen güvenlik kurallarına göre kontrol eden bir güvenlik mekanizmasıdır.

Temel amacı, izin verilmeyen veya istenmeyen ağ iletişimini engellemek ve izin verilen trafiğin geçmesine izin vermektir.

Basit bir şekilde:

```text
İnternet
    ↓
🔥 Firewall
    ↓
Şirket Ağı
    ↓
Sunucular / Bilgisayarlar
```

Firewall'ı bir binanın girişindeki güvenlik kontrolü gibi düşünebilirim.

Ancak firewall sadece "iyi veya kötü" şeklinde karar veren sihirli bir sistem değildir. Kendisine tanımlanan kurallar ve yaptığı kontroller doğrultusunda trafik hakkında karar verir.

---

# 1. Firewall Ne İşe Yarar?

Firewall'ın temel görevi ağ trafiğini kontrol etmektir.

Örneğin bir firewall:

* Belirli IP adreslerinden gelen trafiği engelleyebilir.
* Belirli portlara erişimi kısıtlayabilir.
* Belirli protokollere izin verebilir veya engelleyebilir.
* Gelen bağlantıları kontrol edebilir.
* Giden bağlantıları kontrol edebilir.
* Ağlar arasındaki erişimi sınırlandırabilir.

Basitleştirilmiş bir örnek:

```text
İnternet
   ↓
Firewall
   ↓
Şirket Ağı
```

Firewall üzerinde:

```text
443 → İzin ver
23  → Engelle
Belirli IP → Engelle
```

gibi kurallar bulunabilir.

Buradaki kurallar şirketin güvenlik ihtiyaçlarına göre belirlenir.

> **Kısaca:** Firewall = Ağ trafiğini güvenlik kurallarına göre kontrol eder.

---

# 2. Donanımsal Firewall Nedir?

**Donanımsal firewall**, ağ trafiğini kontrol etmek için fiziksel bir ağ cihazı veya ağ güvenlik cihazı üzerinde çalışan firewall çözümüdür.

Örneğin bir şirketin internet bağlantısı ile iç ağı arasında konumlandırılabilir:

```text
                    Şirket Ağı
                       ↑
                       |
İnternet → [ Firewall Cihazı ]
```

Donanımsal firewall'lar özellikle kurumların ağlarında kullanılabilir.

Birden fazla bilgisayarın ve sunucunun trafiğini merkezi olarak kontrol etmeye yardımcı olabilir.

Kurumsal firewall cihazlarında trafik filtreleme dışında VPN, ağ segmentasyonu, erişim kontrolü ve bazı gelişmiş güvenlik özellikleri de bulunabilir.

> **Kısaca:** Donanımsal firewall = Ağ seviyesinde merkezi olarak çalışan fiziksel güvenlik çözümü.

---

# 3. Yazılımsal Firewall Nedir?

**Yazılımsal firewall**, bir bilgisayar veya sunucu üzerinde çalışan firewall yazılımıdır.

Örneğin bir işletim sisteminde çalışan firewall:

```text
İnternet
   ↓
Bilgisayar
   ↓
Yazılımsal Firewall
   ↓
Uygulamalar
```

gelen ve giden bağlantıları kontrol edebilir.

Örneğin bir uygulamanın internete bağlantı kurmasına izin verilebilir veya engellenebilir.

Yazılımsal firewall'ın önemli avantajlarından biri, tek bir cihazın ağ trafiğini kontrol edebilmesidir.

> **Kısaca:** Yazılımsal firewall = Cihaz üzerinde çalışan trafik kontrol mekanizması.

---

# 4. Firewall Hangi Kriterlere Göre Trafiği Engelleyebilir?

Firewall'ın türüne ve özelliklerine göre değerlendirdiği kriterler değişebilir.

Yaygın olarak:

### 🌐 IP Adresi

Örneğin belirli bir kaynak IP adresinden gelen trafik engellenebilir.

```text
Kaynak IP → 192.168.1.50
```

---

### 🔌 Port

Belirli portlara gelen bağlantılar engellenebilir.

Örneğin:

```text
Port 23 → Engelle
Port 443 → İzin ver
```

---

### 📡 Protokol

Belirli protokollere göre kurallar oluşturulabilir.

Örneğin:

```text
TCP
UDP
ICMP
```

gibi trafik türleri değerlendirilebilir.

---

### ↔️ Kaynak ve Hedef

Trafiğin:

* Nereden geldiği
* Nereye gittiği

kontrol edilebilir.

Örneğin:

```text
Şirket ağı → İnternet
İnternet → Şirket sunucusu
```

gibi farklı trafik akışlarına farklı kurallar uygulanabilir.

---

### 🚪 Yön

Firewall gelen ve giden trafiği farklı şekilde değerlendirebilir.

```text
Inbound  → İçeri gelen trafik
Outbound → Dışarı giden trafik
```

---

### 👤 Kullanıcı veya Uygulama

Daha gelişmiş firewall çözümleri, kullanıcıları veya uygulamaları da kurallarda dikkate alabilir.

Örneğin belirli bir uygulamanın internete erişimi sınırlandırılabilir.

---

# 5. Firewall ile Antivirus Arasındaki Fark Nedir?

Firewall ve antivirus aynı şey değildir.

İkisi de güvenlik amacıyla kullanılır ancak farklı problemlere odaklanırlar.

| Firewall                                                          | Antivirus                                             |
| ----------------------------------------------------------------- | ----------------------------------------------------- |
| Ağ trafiğini kontrol eder                                         | Zararlı yazılımları tespit etmeye/engellemeye çalışır |
| IP, port, protokol ve bağlantı gibi özellikleri değerlendirebilir | Dosya, süreç ve davranışları inceleyebilir            |
| Ağ erişimini sınırlar                                             | Malware'e karşı koruma sağlar                         |
| Ağ seviyesinde önemli rol oynar                                   | Cihaz/endpoint seviyesinde önemli rol oynar           |

Basit bir örnek:

```text
Firewall
    ↓
"Bu bağlantıya izin verilecek mi?"
```

Antivirus ise:

```text
Antivirus
    ↓
"Bu dosya veya davranış zararlı olabilir mi?"
```

sorusuna odaklanır.

Modern endpoint güvenlik çözümleri antivirus'ten daha geniş özelliklere sahip olabilir. Örneğin **EDR (Endpoint Detection and Response)** sistemleri cihaz üzerindeki olayları izleyerek şüpheli davranışların tespit edilmesine ve incelenmesine yardımcı olabilir.

> **Kısaca:** Firewall daha çok ağ trafiğine, antivirus ise zararlı yazılım ve endpoint üzerindeki tehditlere odaklanır.

---

# 6. Firewall Tek Başına Bir Sistemi Tamamen Güvenli Hale Getirir mi?

**Hayır.**

Firewall önemli bir güvenlik katmanıdır ancak tek başına yeterli değildir.

Çünkü bütün güvenlik problemleri ağ trafiğinin basitçe engellenmesiyle çözülemez.

Örneğin:

* Phishing
* Sosyal mühendislik
* Zararlı dosyaların çalıştırılması
* Çalınmış kullanıcı hesapları
* İç tehditler
* Güvenlik açıkları
* Yanlış yapılandırmalar
* Yetkisiz erişim

gibi birçok farklı risk bulunmaktadır.

Bu nedenle kurumlarda genellikle **katmanlı güvenlik (defense in depth)** yaklaşımı kullanılır.

Örneğin:

```text
              🔐 Güvenlik
                   │
      ┌────────────┼────────────┐
      ↓            ↓            ↓
   Firewall     Antivirus      MFA
      ↓            ↓            ↓
    Ağ          Endpoint      Hesap
      │            │            │
      └────────────┼────────────┘
                   ↓
              Kullanıcı
```

Buradaki amaç, bir güvenlik katmanı başarısız olduğunda diğer katmanların koruma sağlamaya devam etmesidir.

> **Kısaca:** Firewall önemlidir ama tek başına yeterli değildir.

---

# 🎯 Senaryo: Çalışan Phishing E-postasındaki Zararlı Dosyayı Açtı

## Durum

Bir şirketin firewall'ı bulunmaktadır.

Ancak çalışanlardan biri phishing e-postası alır ve e-postadaki zararlı dosyayı açar.

Soru:

> Firewall neden bu saldırıyı mutlaka engelleyememiş olabilir?

Bunun birkaç nedeni vardır.

---

## 1. Firewall'ın Temel Görevi Farklıdır

Firewall'ın temel görevi ağ trafiğini güvenlik kurallarına göre kontrol etmektir.

Phishing saldırısının önemli bir bölümü ise **insanın kandırılması** üzerine kuruludur.

Örneğin:

```text
Saldırgan
   ↓
Sahte e-posta
   ↓
Çalışan
   ↓
Zararlı dosya
   ↓
Dosya açılır
```

Firewall'ın bir kullanıcının kandırıldığını anlaması temel görevi değildir.

---

## 2. E-posta Trafiği İzin Verilen Trafik Olabilir

Şirketin çalışanlarının e-posta kullanması gerekiyorsa e-posta servisleri üzerinden gelen trafik tamamen engellenemez.

Örneğin:

```text
E-posta servisi
       ↓
Şirket
       ↓
Çalışan
```

Bu iletişimin var olması normaldir.

Saldırgan da normal görünen bir e-posta kanalını kullanarak zararlı içeriği kullanıcıya ulaştırabilir.

---

## 3. Zararlı Dosya Henüz Ağ Trafiği Olarak Görünmeyebilir

Firewall çoğunlukla ağ iletişimini kontrol eder.

Çalışan dosyayı bilgisayarına indirdikten sonra dosyayı açtığında gerçekleşen zararlı davranışların tamamı doğrudan firewall'ın görebileceği bir ağ trafiği problemi olmayabilir.

Örneğin:

```text
E-posta
   ↓
Dosya indirildi
   ↓
Çalışan dosyayı açtı
   ↓
Dosya çalıştı
   ↓
Sistem üzerinde değişiklik yapıldı
```

Burada endpoint güvenliği de devreye girmelidir.

---

## 4. Saldırgan Normal veya İzin Verilen Trafiği Kullanabilir

Saldırganın kullandığı iletişim kanalı, firewall tarafından otomatik olarak kötü amaçlı olarak sınıflandırılmayabilir.

Örneğin zararlı yazılım çalıştıktan sonra dışarıdaki bir sunucuyla iletişim kurmaya çalışabilir.

Eğer bu iletişim izin verilen bir trafik biçimine benziyorsa, yalnızca klasik firewall kurallarıyla bunu tespit etmek her zaman mümkün olmayabilir.

Bu nedenle modern güvenlik mimarilerinde yalnızca firewall'a güvenilmez.

---

# 🛡️ Bu Saldırıya Karşı Hangi Katmanlar Yardımcı Olabilir?

Phishing saldırılarına karşı birden fazla güvenlik katmanı kullanılabilir:

```text
                 Phishing
                    ↓
        ┌───────────┴───────────┐
        ↓                       ↓
 E-posta Güvenliği          Kullanıcı
        ↓                   Farkındalığı
        ↓                       ↓
     Firewall                Eğitim
        ↓
   Endpoint Security
        ↓
    Antivirus / EDR
        ↓
        MFA
        ↓
   Ağ ve Sistem Kontrolleri
```

Örneğin:

### 📧 E-posta Güvenliği

Şüpheli e-postaları, zararlı ekleri veya sahte bağlantıları tespit etmeye yardımcı olabilir.

### 🛡️ Antivirus / EDR

Zararlı dosya çalıştırıldığında veya şüpheli davranış gerçekleştiğinde bunu tespit etmeye ve engellemeye yardımcı olabilir.

### 🔐 MFA

Kullanıcının parolası ele geçirilse bile hesaba erişim için ek doğrulama katmanı sağlayabilir.

### 👩‍💻 Kullanıcı Farkındalığı

Çalışanın şüpheli e-postaları tanıması, saldırının başarılı olmasını engellemede önemli bir rol oynar.

---

# 🧠 Benim Öğrenme Notum

Bu senaryodan çıkardığım en önemli sonuç:

> **Firewall her türlü saldırıyı engelleyen bir sistem değildir. Firewall'ın temel görevi ağ trafiğini kontrol etmektir. Phishing gibi saldırılarda kullanıcı davranışı, e-posta güvenliği ve endpoint güvenliği gibi başka katmanlar da önemlidir.**

Bu yüzden:

```text
Firewall ≠ Tam Güvenlik
```

yerine:

```text
Firewall
   +
E-posta Güvenliği
   +
Antivirus / EDR
   +
MFA
   +
Kullanıcı Farkındalığı
   +
Güvenli Yapılandırma
   +
İzleme / Loglama
   =
Katmanlı Güvenlik
```

şeklinde düşünmeliyim.

---

# 🎯 Kısa Özet

* **Firewall**, ağ trafiğini güvenlik kurallarına göre kontrol eder.
* **Donanımsal firewall**, fiziksel ağ güvenlik cihazı üzerinde çalışabilir.
* **Yazılımsal firewall**, cihaz veya sunucu üzerinde çalışabilir.
* Firewall IP, port, protokol, kaynak, hedef ve yön gibi kriterlere göre trafik üzerinde karar verebilir.
* **Antivirus**, zararlı yazılımları tespit etmeye ve engellemeye odaklanır.
* Firewall tek başına tam güvenlik sağlamaz.
* Phishing saldırısı, insan faktörünü kullandığı için firewall tarafından mutlaka engellenmeyebilir.
* E-posta güvenliği, antivirus/EDR, MFA ve kullanıcı farkındalığı gibi ek güvenlik katmanları önemlidir.

> **Ana fikir:**
> 🔥 **Firewall bir güvenlik katmanıdır; bütün güvenlik sisteminin kendisi değildir.**
