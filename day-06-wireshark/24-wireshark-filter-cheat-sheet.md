# 24. Wireshark Filter Cheat Sheet

## 1. Çalışmanın Amacı

Bu bölümde Wireshark'ın Display Filter özelliğini daha iyi kullanmayı amaçladım.

PCAP dosyası içerisinde binlerce paket bulunabileceği için bütün paketleri tek tek incelemek pratik değildir. Display Filter kullanarak yalnızca ilgilendiğim trafik türünü gösterebilir ve analiz sürecini hızlandırabilirim.

Bu çalışmada temel protokol, IP adresi, port ve TCP flag filtrelerini öğrendim. Ayrıca güvenlik analizinde kullanabileceğim ek filtreler de oluşturdum.

> Not: Bu filtreler **Display Filter** olarak Wireshark'ın üst kısmındaki filtre alanına yazılır.

---

# 2. Temel Protokol Filtreleri

## 2.1 `ip`

```text
ip
```

IPv4 kullanan paketleri gösterir.

IPv4 ağ trafiğini incelemek istediğimde kullanabilirim.

Örneğin:

```text
ip
```

filtresi ile yalnızca IPv4 paketlerini görüntüleyebilirim.

---

## 2.2 `ipv6`

```text
ipv6
```

IPv6 kullanan paketleri gösterir.

IPv4 yerine IPv6 trafiğini ayrı olarak incelemek istediğimde kullanabilirim.

---

## 2.3 `tcp`

```text
tcp
```

TCP protokolünü kullanan paketleri gösterir.

TCP bağlantılarını, flag'leri ve port bilgilerini incelemek için temel filtrelerden biridir.

---

## 2.4 `udp`

```text
udp
```

UDP kullanan paketleri gösterir.

DNS gibi UDP üzerinden çalışan trafiklerin incelenmesinde kullanılabilir.

---

## 2.5 `icmp`

```text
icmp
```

ICMP paketlerini gösterir.

Özellikle `ping` gibi bağlantı ve erişilebilirlik kontrollerinde kullanılan trafiği incelemek için kullanılabilir.

---

## 2.6 `arp`

```text
arp
```

ARP paketlerini gösterir.

ARP, IP adreslerinin yerel ağdaki MAC adresleriyle eşleştirilmesinde kullanılır.

ARP trafiğini inceleyerek yerel ağdaki cihazların IP-MAC çözümlemelerini gözlemleyebilirim.

---

## 2.7 `dns`

```text
dns
```

DNS trafiğini gösterir.

DNS sorgularını ve response paketlerini incelemek için kullanabilirim.

Örneğin daha önceki PCAP analizimde bu filtreyi kullanarak domain sorgularını incelemiştim.

---

## 2.8 `http`

```text
http
```

HTTP protokolüyle ilgili paketleri gösterir.

Web trafiğini incelerken kullanılabilir.

HTTP request ve response paketlerini ayırarak;

* URI
* Method
* Host
* Status Code
* User-Agent

gibi bilgileri inceleyebilirim.

---

## 2.9 `tls`

```text
tls
```

TLS protokolüyle ilgili paketleri gösterir.

HTTPS gibi şifreli iletişimlerde TLS trafiğini incelemek için kullanılabilir.

Şifreli içerik doğrudan okunamasa da TLS bağlantısının kurulması ve ilgili metadata incelenebilir.

---

# 3. Port Filtreleri

## 3.1 TCP Port 80

```text
tcp.port == 80
```

TCP üzerinde 80 numaralı portu kullanan trafiği gösterir.

Port 80 genellikle HTTP için kullanılır.

Bu filtreyi HTTP ile ilişkili TCP trafiğini incelemek için kullanabilirim.

---

## 3.2 TCP Port 443

```text
tcp.port == 443
```

TCP üzerinde 443 numaralı portu kullanan trafiği gösterir.

Port 443 genellikle HTTPS/TLS trafiğiyle ilişkilidir.

Şifreli web trafiğini incelemek için kullanılabilir.

---

## 3.3 UDP Port 53

```text
udp.port == 53
```

UDP üzerinde 53 numaralı portu kullanan trafiği gösterir.

Port 53 DNS ile yaygın olarak kullanılır.

DNS trafiğini port üzerinden filtrelemek istediğimde kullanabilirim.

---

# 4. IP Adresi Filtreleri

## 4.1 `ip.addr`

```text
ip.addr == 192.168.1.10
```

Belirtilen IP adresinin kaynak veya hedef olarak bulunduğu paketleri gösterir.

Yani hem:

```text
192.168.1.10 → başka cihaz
```

hem de:

```text
başka cihaz → 192.168.1.10
```

şeklindeki iletişimleri görebilirim.

Belirli bir cihazın tüm ağ trafiğini incelemek için kullanışlıdır.

---

## 4.2 `ip.src`

```text
ip.src == 192.168.1.10
```

Belirtilen IP adresini **kaynak** olarak kullanan paketleri gösterir.

Örneğin:

```text
192.168.1.10 → 192.168.1.20
```

trafiğini gösterirken:

```text
192.168.1.20 → 192.168.1.10
```

trafiğini göstermez.

---

## 4.3 `ip.dst`

```text
ip.dst == 192.168.1.10
```

Belirtilen IP adresini **hedef** olarak kullanan paketleri gösterir.

Örneğin:

```text
192.168.1.20 → 192.168.1.10
```

paketini gösterir.

---

# 5. TCP Flag Filtreleri

TCP flag'leri TCP bağlantısının durumunu anlamama yardımcı olur.

## 5.1 SYN

```text
tcp.flags.syn == 1
```

SYN flag'i aktif olan TCP paketlerini gösterir.

SYN, TCP bağlantısının başlatılmasında kullanılır.

Bu filtreyi özellikle yeni TCP bağlantılarının başlangıcını incelemek için kullanabilirim.

Örneğin bir bağlantının:

```text
SYN
↓
SYN/ACK
↓
ACK
```

şeklinde başlamasını gözlemleyebilirim.

---

## 5.2 RST

```text
tcp.flags.reset == 1
```

RST flag'i aktif olan TCP paketlerini gösterir.

RST, bir TCP bağlantısının resetlenmesi veya beklenmeyen şekilde sonlandırılması gibi durumlarda kullanılabilir.

Çok sayıda RST paketinin bulunması durumunda bağlantıların neden resetlendiğini ayrıca incelemek gerekebilir.

---

# 6. Kendi Eklediğim Filtreler

Temel filtrelere ek olarak güvenlik analizlerinde kullanabileceğim bazı filtreleri de inceledim.

## 6.1 DNS Query Name

```text
dns.qry.name
```

DNS query name alanı bulunan paketleri gösterir.

DNS sorgularını incelerken hangi domainlerin sorgulandığını görmek için kullanılabilir.

---

## 6.2 Belirli Bir Domain

```text
dns.qry.name == "example.com"
```

Belirli bir domain için yapılan DNS sorgularını filtreler.

Örneğin PCAP içerisinde `example.com` sorgusunu diğer DNS trafiğinden ayırmak için kullanabilirim.

---

## 6.3 TCP SYN Paketleri

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

SYN flag'i aktif, ACK flag'i aktif olmayan TCP paketlerini gösterir.

Bu filtreyi yeni TCP bağlantılarının başlangıç paketlerini daha net görmek için kullanabilirim.

Özellikle çok sayıda porta yönelik bağlantı başlangıcı olup olmadığını araştırırken faydalı olabilir.

---

## 6.4 Belirli Bir HTTP Method

```text
http.request.method == "POST"
```

HTTP POST request'lerini gösterir.

POST isteklerini inceleyerek istemcinin sunucuya veri gönderdiği HTTP iletişimlerini ayrı olarak analiz edebilirim.

---

## 6.5 HTTP Request

```text
http.request
```

HTTP request paketlerini gösterir.

Web uygulaması trafiğini incelerken istemcinin yaptığı HTTP isteklerini ayırmak için kullanılabilir.

---

## 6.6 HTTP Response

```text
http.response
```

HTTP response paketlerini gösterir.

Sunucunun istemciye gönderdiği HTTP response'larını incelemek için kullanılabilir.

---

## 6.7 Belirli HTTP Status Code

```text
http.response.code == 404
```

HTTP `404 Not Found` response'larını gösterir.

Web uygulaması analizinde bulunamayan kaynaklara yönelik istekleri incelemek için kullanılabilir.

---

## 6.8 Belirli Bir TCP Portuna Kaynak veya Hedef Olarak Bakmak

```text
tcp.port == 22
```

TCP üzerinde 22 numaralı portla ilişkili paketleri gösterir.

Port 22 genellikle SSH ile ilişkilidir.

Bu filtreyi belirli bir servisin ağ trafiğini incelemek için kullanabilirim.

---

# 7. Güvenlik Analizinde Filtreleri Birleştirmek

Wireshark'ta filtreleri `&&` ve `||` gibi operatörlerle birleştirebilirim.

Örneğin:

```text
ip.addr == 10.0.3.15 && dns
```

Bu filtre, `10.0.3.15` adresiyle ilişkili DNS trafiğini gösterir.

Bir başka örnek:

```text
tcp && tcp.port == 443
```

TCP üzerinden 443 numaralı portla ilişkili trafiği gösterir.

Birden fazla koşulu bir arada kullanmak, büyük PCAP dosyalarında yalnızca ilgilendiğim trafiği ayırmamı sağlar.

---

# 8. Filtreleri Kategorilere Ayırma

Öğrendiğim filtreleri şu şekilde gruplandırabilirim:

| Kategori     | Örnek                           |
| ------------ | ------------------------------- |
| Protokol     | `tcp`                           |
| IP versiyonu | `ip`, `ipv6`                    |
| IP adresi    | `ip.addr == 192.168.1.10`       |
| Kaynak IP    | `ip.src == 192.168.1.10`        |
| Hedef IP     | `ip.dst == 192.168.1.10`        |
| Port         | `tcp.port == 443`               |
| DNS          | `dns`                           |
| HTTP         | `http`                          |
| TLS          | `tls`                           |
| TCP Flag     | `tcp.flags.syn == 1`            |
| TCP Reset    | `tcp.flags.reset == 1`          |
| HTTP Method  | `http.request.method == "POST"` |
| HTTP Status  | `http.response.code == 404`     |

---

# 9. SOC Analizinde Filtre Kullanımı

Bir SOC analisti için filtreleri bilmenin önemli olduğunu gördüm.

Örneğin şüpheli bir bilgisayarı incelemeye başladığımda filtreleri rastgele kullanmak yerine olayın türüne göre ilerleyebilirim.

Örnek bir analiz sırası:

```text
ip.addr == ŞÜPHELİ_IP
```

Önce cihazın genel trafiğini inceleyebilirim.

Daha sonra:

```text
dns
```

ile DNS sorgularını kontrol edebilirim.

Ardından:

```text
tcp
```

ile TCP bağlantılarını inceleyebilirim.

Şüpheli bağlantı başlangıçlarını araştırmak için:

```text
tcp.flags.syn == 1
```

kullanabilirim.

HTTP trafiği varsa:

```text
http
```

ile web trafiğini ayırabilirim.

Şifreli web trafiğini incelemek için:

```text
tls
```

filtresini kullanabilirim.

Bu şekilde filtreleri tek başına ezberlemek yerine olayın ihtiyacına göre kullanabileceğimi öğrendim.

---

# 10. Kısa Cheat Sheet

| Filtre                          | Ne İşe Yarar?                                   |
| ------------------------------- | ----------------------------------------------- |
| `ip`                            | IPv4 paketlerini gösterir                       |
| `ipv6`                          | IPv6 paketlerini gösterir                       |
| `tcp`                           | TCP paketlerini gösterir                        |
| `udp`                           | UDP paketlerini gösterir                        |
| `icmp`                          | ICMP paketlerini gösterir                       |
| `arp`                           | ARP paketlerini gösterir                        |
| `dns`                           | DNS paketlerini gösterir                        |
| `http`                          | HTTP paketlerini gösterir                       |
| `tls`                           | TLS paketlerini gösterir                        |
| `tcp.port == 80`                | TCP 80 port trafiğini gösterir                  |
| `tcp.port == 443`               | TCP 443 port trafiğini gösterir                 |
| `udp.port == 53`                | UDP 53 port trafiğini gösterir                  |
| `ip.addr == X`                  | X IP'siyle ilişkili trafiği gösterir            |
| `ip.src == X`                   | X kaynak IP'li paketleri gösterir               |
| `ip.dst == X`                   | X hedef IP'li paketleri gösterir                |
| `tcp.flags.syn == 1`            | SYN paketlerini gösterir                        |
| `tcp.flags.reset == 1`          | RST paketlerini gösterir                        |
| `dns.qry.name`                  | DNS query name alanı bulunan paketleri gösterir |
| `http.request`                  | HTTP request'lerini gösterir                    |
| `http.response`                 | HTTP response'larını gösterir                   |
| `http.request.method == "POST"` | POST request'lerini gösterir                    |
| `http.response.code == 404`     | 404 response'larını gösterir                    |

---

# 11. Sonuç

Bu çalışmada Wireshark Display Filter özelliğini daha detaylı şekilde öğrendim.

Filtrelerin yalnızca belirli paketleri ekranda göstermek için değil, aynı zamanda güvenlik analizini sistematik hale getirmek için kullanılabileceğini gördüm.

Özellikle;

* IP filtreleriyle belirli cihazları,
* port filtreleriyle belirli servisleri,
* DNS filtresiyle domain sorgularını,
* HTTP filtresiyle web trafiğini,
* TLS filtresiyle şifreli iletişimi,
* TCP flag filtreleriyle bağlantı durumlarını

inceleyebileceğimi öğrendim.

En önemli öğrendiğim noktalardan biri, büyük bir PCAP dosyasını doğrudan bütün paketleriyle incelemek yerine önce **hangi soruya cevap aradığımı belirleyip uygun Display Filter'ı kullanmanın** analiz sürecini çok daha verimli hale getirmesidir.
