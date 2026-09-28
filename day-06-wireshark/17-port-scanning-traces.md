# 17. Port Scanning Traces

## 1. Port Taraması Nedir?

Port taraması, bir sistem üzerindeki TCP veya UDP portlarının erişilebilirlik durumunu belirlemek için yapılan bir ağ keşif işlemidir.

Bir bilgisayar üzerindeki farklı portlar farklı servislerle ilişkili olabilir. Örneğin:

* 21 → FTP
* 22 → SSH
* 23 → Telnet
* 25 → SMTP
* 53 → DNS
* 80 → HTTP
* 443 → HTTPS

Port taraması sayesinde bir sistemde hangi portların erişilebilir olduğu hakkında bilgi edinilebilir.

Bu çalışmada yalnızca kendi laboratuvar ortamımdaki hedef üzerinde test yaptım.

---

## 2. Nmap ile SYN Scan

Daha önce Nmap kullanmayı öğrendiğim için bu bölümde Nmap tarafından oluşturulan ağ trafiğini Wireshark üzerinden incelemek istedim.

Wireshark'ta `eth1` arayüzünde yeni bir capture başlattım.

Daha sonra Kali Linux üzerinde aşağıdaki komutu çalıştırdım:

```bash
sudo nmap -sS -p 21,22,23,25,53,80,443 10.0.3.2
```

Burada:

* `-sS` → SYN Scan
* `-p` → taranacak portları belirtir
* `10.0.3.2` → laboratuvar hedefidir

Nmap sonucunda hedef sistemin ayakta olduğu görüldü:

```text
Host is up (0.00035s latency).
```

Taranan portların tamamı `filtered` olarak raporlandı:

```text
21/tcp  filtered ftp
22/tcp  filtered ssh
23/tcp  filtered telnet
25/tcp  filtered smtp
53/tcp  filtered domain
80/tcp  filtered http
443/tcp filtered https
```

---

## 3. SYN Scan Nasıl Çalışır?

SYN Scan sırasında istemci, hedef portlara TCP SYN paketleri gönderir.

Genel olarak ağ üzerinde şu yapı görülebilir:

```text
Scanner
10.0.3.15
   |
   | SYN
   v
Target
10.0.3.2
```

Bir portun açık olması durumunda normalde SYN/ACK yanıtı alınabilir.

Kapalı bir port için RST/ACK yanıtı görülebilir.

Filtreleme durumunda ise yanıtın engellenmesi veya port durumunun belirlenmesini zorlaştıran bir ağ filtresi bulunabilir.

Bu nedenle `filtered` sonucu doğrudan "port kapalı" anlamına gelmez.

---

## 4. Wireshark'ta Gözlemlediğim Paket

Nmap taramasından sonra Wireshark'ta aşağıdaki filtreyi kullandım:

```text
ip.src == 10.0.3.15 && ip.dst == 10.0.3.2 && tcp.flags.syn == 1
```

Yakalanan paketlerden bir tanesi:

```text
5    0.072194227    10.0.3.15    10.0.3.2    TCP    58
49713 → 443 [SYN] Seq=0 Win=1024 Len=0 MSS=1460
```

Bu paketi şu şekilde yorumladım:

* Kaynak IP: `10.0.3.15`
* Hedef IP: `10.0.3.2`
* Kaynak port: `49713`
* Hedef port: `443`
* TCP flag: `SYN`
* Sequence Number: `0`
* TCP segment length: `0`
* MSS: `1460`
* Paket uzunluğu: `58 bytes`

Buradaki `49713` kaynak portu Kali tarafından kullanılan geçici bir porttur.

Asıl kontrol edilen port ise:

```text
443
```

portudur.

Dolayısıyla paket:

```text
10.0.3.15:49713 → 10.0.3.2:443 [SYN]
```

şeklinde okunabilir.

Bu paket, Kali sisteminin hedefteki TCP/443 portuna bağlantı başlatma isteği gönderdiğini gösterir.

---

## 5. Wireshark ile Nmap Arasındaki İlişki

Nmap bize taramanın sonucunu özetlerken, Wireshark ağ üzerinde gerçekleşen paketleri gösterir.

Nmap çıktısı:

```text
21/tcp  filtered
22/tcp  filtered
23/tcp  filtered
25/tcp  filtered
53/tcp  filtered
80/tcp  filtered
443/tcp filtered
```

Wireshark'ta ise gerçek paket yapısını görebildim:

```text
10.0.3.15:49713 → 10.0.3.2:443 [SYN]
```

Bu nedenle Nmap ile Wireshark'ın farklı seviyelerde bilgi sağladığını gördüm.

Nmap:

> "Taramanın sonucu nedir?"

sorusuna cevap verir.

Wireshark:

> "Bu sonucu oluştururken ağ üzerinde hangi paketler oluştu?"

sorusunu incelememi sağlar.

---

## 6. Port Scan Ağ Üzerinde Nasıl Anlaşılabilir?

Bir SYN port taramasında aşağıdaki trafik deseni dikkat çekebilir:

```text
Tek kaynak IP
     |
     +---- SYN → Hedef:21
     +---- SYN → Hedef:22
     +---- SYN → Hedef:23
     +---- SYN → Hedef:25
     +---- SYN → Hedef:53
     +---- SYN → Hedef:80
     +---- SYN → Hedef:443
```

Özellikle kısa zaman aralığında aynı kaynak IP'nin aynı hedef IP üzerindeki çok sayıda farklı porta SYN göndermesi port taraması açısından incelenebilecek bir davranıştır.

Ancak tek bir SYN paketi tek başına port taraması kanıtı değildir. Normal uygulamalar da TCP bağlantısı başlatmak için SYN kullanır.

---

## 7. Port Scan, Port Sweep ve Network Sweep

### Port Scan

Bir hedef IP üzerindeki birçok portun kontrol edilmesidir.

```text
1 IP → Çok sayıda port
```

Örneğin:

```text
10.0.3.15 → 10.0.3.2:21
10.0.3.15 → 10.0.3.2:22
10.0.3.15 → 10.0.3.2:80
10.0.3.15 → 10.0.3.2:443
```

### Port Sweep

Aynı portun birden fazla IP adresinde kontrol edilmesidir.

```text
Çok sayıda IP → 1 port
```

Örneğin:

```text
10.0.3.15 → 10.0.3.2:22
10.0.3.15 → 10.0.3.3:22
10.0.3.15 → 10.0.3.4:22
```

### Network Sweep

Bir ağdaki aktif cihazların keşfedilmesine yönelik daha geniş bir taramadır.

Burada amaç tek bir porttan ziyade ağ üzerinde hangi sistemlerin erişilebilir olduğunu belirlemek olabilir.

---

## 8. SYN Scan ve TCP Connect Scan

### SYN Scan

SYN Scan'de TCP bağlantısının ilk aşaması kullanılır.

Genel yapı:

```text
Client → SYN → Server
Client ← SYN/ACK ← Server
```

Tarama yapan araç, bağlantının tamamını kurmadan port hakkında bilgi toplamaya çalışabilir.

Ben bu çalışmada:

```bash
sudo nmap -sS -p 21,22,23,25,53,80,443 10.0.3.2
```

komutuyla SYN Scan gerçekleştirdim.

### TCP Connect Scan

TCP Connect Scan'de normal TCP bağlantısının kurulması hedeflenir.

Genel yapı:

```text
Client → SYN
Server → SYN/ACK
Client → ACK
```

Bu nedenle Wireshark'ta tam TCP bağlantı kurulumu görülebilir.

---

## 9. Güvenlik Açısından Genel Göstergeler

Bir SOC analisti port taraması şüphesi olan bir trafiği incelerken şu davranışlara dikkat edebilir:

* Aynı kaynak IP'den çok sayıda SYN paketi gelmesi
* Aynı hedef üzerinde birçok farklı portun kısa sürede denenmesi
* Çok sayıda hedef IP üzerinde aynı portun denenmesi
* Kısa zaman aralığında yoğun bağlantı girişimleri
* Birçok porta gönderilen SYN paketlerine benzer yanıtların alınması
* Kaynak ve hedef IP'lerin zaman içerisindeki tekrar eden ilişkileri

Bu göstergelerin hiçbiri tek başına kötü niyetli faaliyet kanıtı değildir. Ağ keşfi ve güvenlik taramaları yetkili sistem yöneticileri veya güvenlik ekipleri tarafından da gerçekleştirilebilir.

---

## 10. Bu Çalışmada Öğrendiklerim

Bu çalışmada Nmap ile yaptığım port taramasının Wireshark açısından nasıl görünebileceğini inceledim.

Özellikle:

* SYN Scan'in temel mantığını öğrendim.
* Nmap'in `-sS` seçeneğini kullandım.
* TCP SYN paketlerini Wireshark ile filtreledim.
* `Source IP`, `Destination IP`, `Source Port` ve `Destination Port` alanlarını yorumladım.
* `SYN` flag'inin TCP bağlantısındaki rolünü inceledim.
* `filtered` durumunun doğrudan "kapalı port" anlamına gelmediğini öğrendim.
* Port Scan, Port Sweep ve Network Sweep arasındaki farkı öğrendim.
* Nmap'in sonuç bilgisi ile Wireshark'ın paket seviyesindeki bilgisinin birbirinden farklı olduğunu gördüm.

Bu çalışmada kendi laboratuvar ortamımda yaptığım tarama sonucunda Wireshark'ta şu gerçek SYN paketini gözlemledim:

```text
10.0.3.15:49713 → 10.0.3.2:443 [SYN]
```

Bu paket, port taramasının ağ seviyesinde oluşturabileceği izlerden birini doğrudan görmemi sağladı.
