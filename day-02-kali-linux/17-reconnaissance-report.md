# 17. Nmap – Küçük Keşif (Reconnaissance) Raporu

## 🎯 Raporun Amacı

Bu raporun amacı, kendi Kali Linux test ortamım üzerinde gerçekleştirdiğim temel Nmap çalışmalarını düzenli bir şekilde belgelemektir.

Çalışmada;

* Hedefin erişilebilir olup olmadığı,
* Açık ve kapalı portlar,
* Çalışan servisler,
* Servis sürümü,
* İşletim sistemi tespiti,
* Nmap parametreleri

incelenmiştir.

> **Not:** Bu çalışma yalnızca kendi kontrolümdeki Kali Linux test ortamında gerçekleştirilmiştir.

---

# 1. Hedef Bilgisi

**Hedef:** `localhost`

**IPv4:** `127.0.0.1`

`localhost`, çalıştığım bilgisayarın kendisini ifade eder.

`127.0.0.1` ise IPv4 loopback adresidir.

Bu nedenle bu çalışmada ağ üzerindeki başka bir bilgisayar yerine kendi Kali Linux sistemimi test ettim.

---

# 2. Host Discovery

İlk olarak hedefin erişilebilir olup olmadığını kontrol ettim:

```bash id="k7kqf3"
nmap localhost
```

Sonuçta:

```text id="2x5h3c"
Host is up
```

sonucunu gördüm.

Bu, hedef sistemin erişilebilir olduğunu gösterdi.

---

# 3. Temel Port Taraması

Temel Nmap taramasında varsayılan olarak 1000 TCP portu kontrol edildi.

Sonuç:

```text id="y5z0qk"
Not shown: 1000 closed tcp ports (reset)
```

Bu test sırasında açık port bulunmadı.

### Sonuç

| Durum             | Sonuç |
| ----------------- | ----- |
| Host              | Up    |
| Taranan TCP portu | 1000  |
| Açık port         | 0     |
| Kapalı port       | 1000  |

---

# 4. Servis ve Sürüm Tespiti

Daha sonra:

```bash id="q2g7nd"
nmap -sV localhost
```

komutunu kullandım.

İlk çalıştırmada açık port bulunmadığı için servis bilgisi gösterilmedi.

Daha sonra kontrollü bir test yapmak amacıyla kendi Kali sistemimde Python HTTP sunucusu çalıştırdım:

```bash id="4z4f2m"
python3 -m http.server 8000
```

Ardından tekrar:

```bash id="7x9n1c"
nmap -sV localhost
```

çalıştırdım.

Bu kez:

```text id="a4v6x1"
PORT     STATE SERVICE VERSION
8000/tcp open  http    SimpleHTTPServer 0.6 (Python 3.13.7)
```

sonucunu elde ettim.

### Bulgum

```text id="b0w8n2"
8000/tcp
    ↓
Açık port
    ↓
HTTP servisi
    ↓
SimpleHTTPServer
    ↓
Python 3.13.7
```

Bu deney, çalışan bir servisin ilgili portu dinlediğini ve Nmap'in bu servisi tespit edebildiğini göstermiştir.

---

# 5. İşletim Sistemi Tespiti

İşletim sistemi tespiti için:

```bash id="n8r2c4"
nmap -O localhost
```

komutunu kullandım.

Sonuçta:

```text id="s6v3k1"
Too many fingerprints match this host to give specific OS details
```

mesajı görüldü.

Nmap belirli bir işletim sistemi söyleyemedi.

Bu sonuç bir hata olarak değerlendirilmemelidir.

Nmap işletim sistemi tespitini ağ davranışlarından ve TCP/IP özelliklerinden yararlanarak tahmin eder. Yeterli ayırt edici bilgi bulunmadığında kesin bir sonuç veremeyebilir.

Ayrıca:

```text id="m9q4x2"
Network Distance: 0 hops
```

sonucu görüldü.

Bunun nedeni hedefin `localhost` olmasıdır. Hedef aynı sistem üzerinde bulunduğu için ağ mesafesi 0 hop olarak görülmektedir.

---

# 6. Kullanılan Nmap Parametreleri

Çalışma sırasında aşağıdaki parametreleri öğrendim:

| Parametre | Görevi                  |
| --------- | ----------------------- |
| `-sV`     | Servis ve sürüm tespiti |
| `-O`      | İşletim sistemi tespiti |
| `-p`      | Belirli portları tarama |
| `-sT`     | TCP Connect Scan        |
| `-sn`     | Host discovery          |

Örnek:

```bash id="v4p2y7"
nmap -p 8000 -sV localhost
```

Bu komut belirli bir portu kontrol eder ve açık olması durumunda servis/sürüm bilgisini tespit etmeye çalışır.

---

# 7. Güvenlik Açısından Değerlendirme

Nmap sonuçlarını incelerken önemli bir ayrım öğrendim:

> **Açık port, tek başına güvenlik açığı değildir.**

Örneğin `8000/tcp` portunun açık olması, benim test ortamımda Python HTTP sunucusunun çalıştığını gösteriyordu.

Bir güvenlik uzmanı daha sonra şu soruları sorar:

* Bu servis gerekli mi?
* Hangi sürüm çalışıyor?
* Servis güncel mi?
* Kimler erişebiliyor?
* Güvenli şekilde yapılandırılmış mı?
* İnternete açık mı?
* Logları izleniyor mu?

Bu nedenle Nmap sonuçları güvenlik değerlendirmesinin başlangıç noktalarından biridir.

---

# 8. Bulguların Özeti

| Kontrol               | Sonuç                                             |
| --------------------- | ------------------------------------------------- |
| Host erişilebilirliği | Host up                                           |
| Temel port taraması   | 1000 TCP portu kapalı                             |
| Servis tespiti        | Test sırasında 8000/tcp HTTP olarak tespit edildi |
| Servis sürümü         | SimpleHTTPServer 0.6 / Python 3.13.7              |
| OS detection          | Kesin işletim sistemi tespit edilemedi            |
| Network Distance      | 0 hop                                             |
| Hedef                 | `localhost (127.0.0.1)`                           |

---

# 9. Çalışmadan Çıkardığım Sonuç

Bu çalışma sırasında Nmap'in yalnızca port tarayan bir araç olmadığını gördüm.

Nmap ile;

```text id="u7c1m5d"
Hedef
  ↓
Host keşfi
  ↓
Portlar
  ↓
Servisler
  ↓
Servis sürümleri
  ↓
İşletim sistemi hakkında tahmin
  ↓
Güvenlik değerlendirmesi
```

şeklinde bir keşif süreci gerçekleştirilebildiğini öğrendim.

Özellikle Python HTTP sunucusunu kendim çalıştırıp daha sonra Nmap ile tekrar taramam, **servis → port → Nmap sonucu** arasındaki ilişkiyi anlamam açısından faydalı oldu.

---

## 🧠 Kendi Öğrendiklerim

Bu çalışmanın başında port, servis ve Nmap kavramlarını birbirinden ayrı düşünüyordum.

Çalışmanın sonunda ise aralarındaki ilişkiyi daha net anlayabildim:

> **IP adresi hedef sistemi, port iletişim noktasını, servis ise o port üzerinden çalışan uygulamayı ifade eder.**

Ayrıca Nmap'in verdiği sonuçların doğrudan "sistem güvenli/güvensiz" şeklinde yorumlanmaması gerektiğini öğrendim.

Nmap sonuçları, daha kapsamlı bir güvenlik değerlendirmesinin başlangıç verilerinden biridir.

---

## ✅ Bölüm Kontrol Listesi

* [x] Hedef belirledim.
* [x] Host erişilebilirliğini kontrol ettim.
* [x] Port durumlarını inceledim.
* [x] Servis ve sürüm tespiti yaptım.
* [x] Kontrollü bir HTTP servisi oluşturdum.
* [x] İşletim sistemi tespitini denedim.
* [x] Nmap sonuçlarını güvenlik açısından yorumladım.
* [x] Bulguları rapor formatında düzenledim.

---

## 📌 Sonraki Bölüm

**18. Bölüm – Kali Linux Araçlarını Kategorilere Ayırma**

Bir sonraki bölümde Kali Linux içerisinde bulunan güvenlik araçlarını kullanım amaçlarına göre kategorilere ayıracağım.
