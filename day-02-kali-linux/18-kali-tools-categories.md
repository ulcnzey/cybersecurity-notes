# 18. Kali Linux Araçlarının Kategorilere Ayrılması

## 🎯 Görevin Amacı

Kali Linux içerisinde çok sayıda siber güvenlik aracı bulunmaktadır.

Bu araçları tek tek ezberlemek yerine, kullanım amaçlarına göre kategorilere ayırmak siber güvenlik çalışmalarında hangi aracın ne zaman kullanılacağını anlamayı kolaylaştırır.

Bu bölümde Kali Linux araçlarını temel kullanım alanlarına göre inceleyeceğim.

---

# 1. Information Gathering – Bilgi Toplama

Bu kategorideki araçlar hedef hakkında bilgi edinmek ve keşif yapmak amacıyla kullanılır.

### Örnek araçlar

* **Nmap** → Host, port ve servis keşfi
* **theHarvester** → Açık kaynaklardan bilgi toplama
* **Recon-ng** → OSINT ve reconnaissance çalışmaları
* **Maltego** → Varlıklar arasındaki ilişkileri görselleştirme

### Öğrendiğim

Bu kategorinin amacı doğrudan saldırı gerçekleştirmek değil, hedef hakkında teknik ve açık kaynak bilgileri toplamaktır.

Bu aşama genellikle güvenlik değerlendirmesinin başlangıç noktalarından biridir.

---

# 2. Vulnerability Analysis – Güvenlik Açığı Analizi

Bu kategoride sistemlerde veya servislerde bulunabilecek güvenlik açıklarının tespit edilmesine yardımcı olan araçlar bulunur.

### Örnek araçlar

* **Nikto** → Web sunucusu güvenlik kontrolleri
* **Nuclei** → Bilinen güvenlik sorunları ve yanlış yapılandırmalar için template tabanlı kontroller
* **Greenbone/OpenVAS** → Güvenlik açığı tarama ve değerlendirme

### Öğrendiğim

Güvenlik açığı taraması sonucunda bir bulgu elde edilmesi, bunun otomatik olarak doğrulanmış bir güvenlik açığı olduğu anlamına gelmez.

Bulguların ayrıca değerlendirilmesi ve doğrulanması gerekir.

---

# 3. Web Application Analysis – Web Uygulama Güvenliği

Web uygulamalarının güvenliğini değerlendirmek için kullanılan araçlardır.

### Örnek araçlar

* **Burp Suite** → HTTP/HTTPS trafiğini inceleme ve web güvenlik testleri
* **OWASP ZAP** → Web uygulaması güvenlik testi
* **Gobuster** → Dizin ve kaynak keşfi
* **Nikto** → Web sunucusu kontrolleri

### Öğrendiğim

Web uygulamalarında istemci ile sunucu arasındaki HTTP/HTTPS iletişimi önemli bir inceleme alanıdır.

Örneğin bir web uygulamasında:

```text
Kullanıcı
   ↓
Tarayıcı
   ↓
HTTP / HTTPS
   ↓
Web Sunucusu
   ↓
Uygulama
   ↓
Veritabanı
```

şeklinde bir yapı bulunabilir.

Güvenlik uzmanı bu iletişimin farklı noktalarını inceleyebilir.

---

# 4. Password Attacks – Parola Güvenliği

Bu kategoride kimlik doğrulama sistemlerinin güvenliğini değerlendirmek için kullanılan araçlar bulunur.

### Örnek araçlar

* **John the Ripper** → Parola hash'lerinin güvenliğini değerlendirme
* **Hashcat** → Hash tabanlı parola güvenliği testleri
* **Hydra** → Yetkili ortamlarda kimlik doğrulama mekanizmalarının güvenlik testi

### Öğrendiğim

Parola güvenliği yalnızca parolanın uzunluğuyla ilgili değildir.

Ayrıca:

* Güçlü parola politikaları
* Çok faktörlü kimlik doğrulama
* Hesap kilitleme
* Rate limiting
* Güvenli parola saklama

gibi mekanizmalar da önemlidir.

Bu araçlar yalnızca **izin verilen sistemlerde ve laboratuvar ortamlarında** kullanılmalıdır.

---

# 5. Wireless / Wi-Fi Security – Kablosuz Ağ Güvenliği

Kablosuz ağların güvenliğini değerlendirmek için kullanılan araçlardır.

### Örnek araçlar

* **Aircrack-ng** → Kablosuz ağ güvenlik testleri
* **Kismet** → Kablosuz ağ keşfi ve izleme
* **Wifite** → Yetkili kablosuz ağ güvenlik testlerinin otomasyonu

### Öğrendiğim

Kablosuz ağ güvenliğinde:

* Şifreleme
* Kimlik doğrulama
* Ağ yapılandırması
* Erişim noktaları
* Kablosuz istemciler

gibi konular önemlidir.

Kablosuz ağ testleri yalnızca sahibinin izni bulunan ağlarda yapılmalıdır.

---

# 6. Sniffing & Spoofing – Trafik İzleme ve Kimlik Taklidi

Bu kategoride ağ trafiğini analiz etmek ve ağ iletişimini incelemek için kullanılan araçlar bulunur.

### Örnek araçlar

* **Wireshark** → Ağ paketlerini analiz etme
* **tcpdump** → Komut satırından paket yakalama
* **Ettercap** → Yetkili ağ güvenlik testleri ve trafik analizi

### Öğrendiğim

Ağ trafiği aşağıdaki gibi düşünülebilir:

```text
Bilgisayar
    ↓
Ağ
    ↓
Router / Switch
    ↓
Sunucu
```

Paket analiz araçları, bu iletişimin teknik olarak incelenmesine yardımcı olur.

---

# 7. Exploitation Tools – Exploitation

Bu kategoride güvenlik açıklarının kontrollü ve yetkili ortamlarda doğrulanmasına yönelik araçlar bulunur.

### Örnek araç

* **Metasploit Framework** → Güvenlik açıklarının kontrollü ortamlarda doğrulanması ve güvenlik testleri

Bu tür araçlar gerçek sistemlerde izinsiz kullanılmamalıdır.

Bir güvenlik uzmanı bu araçları genellikle:

* Laboratuvar ortamında
* Kendi sistemlerinde
* CTF ortamlarında
* Yazılı izin verilen testlerde

kullanır.

---

# 8. Forensics – Adli Bilişim

Kali Linux, adli bilişim çalışmalarında kullanılabilecek çeşitli araçlar da içerir.

### Örnek araçlar

* **Autopsy** → Dijital delil inceleme
* **Sleuth Kit** → Dosya sistemi ve dijital delil analizi
* **Binwalk** → Dosya ve firmware analizi

### Öğrendiğim

Adli bilişimde amaç yalnızca dosyaları bulmak değildir.

Aynı zamanda:

* Delilin korunması
* Dosya sistemi analizi
* Zaman çizelgesi oluşturma
* Metadata inceleme
* Silinmiş verilerin araştırılması

gibi işlemler yapılabilir.

Bu alan benim bölümüm olan **Adli Bilişim Mühendisliği** ile doğrudan ilişkilidir.

---

# 9. Reverse Engineering – Tersine Mühendislik

Bu kategoride yazılım ve dosyaların nasıl çalıştığını anlamaya yönelik araçlar bulunur.

### Örnek araçlar

* **Ghidra** → Reverse engineering ve binary analysis
* **Radare2** → Binary analiz ve tersine mühendislik
* **strings** → Dosyalardaki okunabilir metinleri inceleme

### Öğrendiğim

Tersine mühendislikte amaç, bir yazılımın kaynak koduna sahip olmadan çalışma mantığını anlamaya çalışmaktır.

Bu alan özellikle:

* Malware analizi
* Binary analizi
* Zararlı yazılım araştırması
* Yazılım güvenliği

gibi konularda önemlidir.

---

# 📊 Genel Kategori Tablosu

| Kategori               | Örnek Araç      | Temel Amaç                                    |
| ---------------------- | --------------- | --------------------------------------------- |
| Information Gathering  | Nmap            | Bilgi toplama ve keşif                        |
| Vulnerability Analysis | Nuclei          | Güvenlik açığı / yanlış yapılandırma kontrolü |
| Web Security           | Burp Suite      | Web uygulaması analizi                        |
| Password Attacks       | John the Ripper | Parola güvenliği değerlendirmesi              |
| Wireless Security      | Aircrack-ng     | Kablosuz ağ güvenlik testleri                 |
| Sniffing & Spoofing    | Wireshark       | Ağ trafiği analizi                            |
| Exploitation           | Metasploit      | Kontrollü güvenlik testi                      |
| Forensics              | Autopsy         | Dijital delil analizi                         |
| Reverse Engineering    | Ghidra          | Binary ve yazılım analizi                     |

---

# 🧠 Nmap Çalışmamla Bağlantısı

Day 2 boyunca özellikle Nmap üzerinde çalıştım.

Öğrendiğim temel süreç:

```text
Hedef
  ↓
Host Discovery
  ↓
Port Discovery
  ↓
Service Detection
  ↓
OS Detection
  ↓
Bulguların Yorumlanması
  ↓
Güvenlik Değerlendirmesi
```

Bu süreç **Information Gathering / Reconnaissance** alanıyla ilişkilidir.

---

# 🔐 Savunma Perspektifi

Kali Linux araçlarını öğrenirken araçların yalnızca saldırı amacıyla düşünülmemesi gerektiğini öğrendim.

Aynı araçlar savunma amacıyla da kullanılabilir.

Örneğin:

```text
Nmap
 ↓
Kendi sistemimde açık portları kontrol et
 ↓
Gereksiz servisleri belirle
 ↓
Servisleri sınırlandır
 ↓
Saldırı yüzeyini azalt
```

Bu nedenle bir siber güvenlik uzmanının yalnızca araçları çalıştırmayı değil, elde edilen sonuçları yorumlamayı da bilmesi gerekir.

---

# 🧠 Kendi Öğrendiklerim

Bu bölümde Kali Linux'un tek bir araçtan oluşmadığını, farklı siber güvenlik alanlarına yönelik çok sayıda araç içerdiğini öğrendim.

Özellikle araçları kullanım amaçlarına göre kategorilere ayırmanın öğrenme sürecini kolaylaştırdığını gördüm.

Benim için en önemli bağlantılardan biri:

> **Adli bilişim + siber güvenlik + dijital delil analizi**

alanlarının birbirinden tamamen bağımsız olmadığını görmek oldu.

Ayrıca Kali Linux'taki araçların güçlü olması nedeniyle bunların yalnızca **yetkili ve kontrollü ortamlarda** kullanılması gerektiğini öğrendim.

---

# ✅ Day 2 Kontrol Listesi

* [x] Kali Linux'u tanıdım.
* [x] Sistem bilgilerini inceledim.
* [x] Linux dosya sistemini öğrendim.
* [x] Temel Linux komutlarını kullandım.
* [x] Kullanıcı ve yetkileri inceledim.
* [x] Ağ yapılandırmasını öğrendim.
* [x] Ping ve ICMP kavramlarını öğrendim.
* [x] Nmap'i tanıdım.
* [x] İlk Nmap taramamı yaptım.
* [x] Port kavramını öğrendim.
* [x] Servis ve sürüm tespiti yaptım.
* [x] OS detection çalıştırdım.
* [x] Açık portları yorumladım.
* [x] Risk analizi yaptım.
* [x] Nmap parametrelerini öğrendim.
* [x] Nmap'i savunma perspektifinden değerlendirdim.
* [x] Küçük bir reconnaissance raporu hazırladım.
* [x] Kali Linux araçlarını kategorilere ayırdım.

---

# 🎯 Day 2 Sonucu

Day 2 sonunda Kali Linux üzerinde temel seviyede:

**Linux → Ağ → Port → Servis → Nmap → Reconnaissance → Güvenlik değerlendirmesi**

bağlantısını kurabildiğimi düşünüyorum.

Bir sonraki aşamada öğrendiğim kavramları daha fazla uygulama yaparak pekiştirmem gerekiyor.
