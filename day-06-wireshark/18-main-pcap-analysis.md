# 18. Main PCAP Analysis

## 1. Amaç

Bu çalışmada kendi kontrollü laboratuvar ortamımda farklı ağ trafiği türlerini tek bir PCAP kaydı içerisinde topladım ve Wireshark kullanarak analiz ettim.

Bu çalışmanın amacı, daha önce ayrı ayrı öğrendiğim protokollerin gerçek ağ trafiğinde nasıl göründüğünü birlikte incelemek ve bir PCAP dosyasını temel seviyede analiz edebilmektir.

İncelediğim temel trafik türleri:

* ICMP
* TCP
* UDP
* DNS
* HTTP

---

## 2. Laboratuvar Ortamı

Ana PCAP kaydını Kali Linux üzerinde, Wireshark kullanarak `eth1` arayüzünden oluşturdum.

### Ağ Bilgileri

* Kali Linux IP: `10.0.3.15`
* Yerel hedef: `10.0.3.2`
* DNS sunucusu: `10.0.3.3`
* Wireshark arayüzü: `eth1`

Tüm trafik kontrollü laboratuvar ortamında oluşturuldu.

---

## 3. ICMP Trafik Analizi

İlk olarak hedef sistem ile bağlantıyı test etmek için:

```bash
ping -c 4 10.0.3.2
```

komutunu kullandım.

Ping sonucunda 4 paket gönderildi ve 4 paket alındı.

```text
4 packets transmitted, 4 received, 0% packet loss
```

Wireshark üzerinde `icmp` filtresini kullanarak ICMP paketlerini inceledim.

### Frame 13 — Echo Request

```text
13  11.086364260
10.0.3.15 → 10.0.3.2
ICMP
98 bytes
Echo (ping) request
id=0x0004, seq=1/256, ttl=64
```

Bu paket Kali sistemimden `10.0.3.2` adresine gönderilen ICMP Echo Request paketidir.

### Frame 14 — Echo Reply

```text
14  11.086945824
10.0.3.2 → 10.0.3.15
ICMP
98 bytes
Echo (ping) reply
id=0x0004, seq=1/256, ttl=255
```

Bu paket hedef sistemin gönderdiğim Echo Request'e verdiği cevaptır.

Wireshark iki paketi birbirine bağlayarak:

```text
Frame 13 → reply in 14
Frame 14 → request in 13
```

bilgisini göstermektedir.

İki paket arasındaki süre yaklaşık `0.582 ms` olarak hesaplanabilir.

### ICMP Akışı

```text
10.0.3.15
    |
    | Echo Request
    v
10.0.3.2
    |
    | Echo Reply
    v
10.0.3.15
```

---

## 4. TCP Trafik Analizi

TCP trafiği oluşturmak için:

```bash
curl http://10.0.3.2
```

komutunu kullandım.

Bağlantı kurulamadı:

```text
curl: (7) Failed to connect to 10.0.3.2 port 80 after 2003 ms:
Could not connect to server
```

Wireshark üzerinde `tcp.port == 80` filtresini kullanarak oluşan TCP paketlerini inceledim.

### Frame 105 — SYN

```text
105  155.143139122
10.0.3.15 → 10.0.3.2
TCP
40014 → 80
[SYN]
Seq=0
```

Bu paket TCP bağlantısını başlatmak için gönderilen SYN paketidir.

### Frame 106 — SYN Retransmission

```text
106  156.154209541
10.0.3.15 → 10.0.3.2
TCP
40014 → 80
[TCP Retransmission] [SYN]
```

İlk SYN paketine beklenen cevap gelmediği için SYN paketi tekrar gönderilmiştir.

### Frame 107 — RST, ACK

```text
107  157.146121930
10.0.3.2 → 10.0.3.15
TCP
80 → 40014
[RST, ACK]
Seq=1 Ack=1
```

Sonrasında hedef sistem tarafından RST, ACK paketi gönderilmiştir.

Bu durumda TCP bağlantısı tamamlanmamıştır.

### Gözlenen TCP Akışı

```text
10.0.3.15                         10.0.3.2
    |                                 |
    |---------- SYN ----------------->|  Frame 105
    |                                 |
    |---------- SYN ----------------->|  Frame 106
    |        Retransmission           |
    |                                 |
    |<--------- RST, ACK -------------|  Frame 107
    |                                 |
    X TCP bağlantısı kurulamadı
```

---

## 5. UDP Trafik Analizi

Ana PCAP içerisinde UDP trafiğini de oluşturdum.

UDP, TCP'den farklı olarak bağlantı kurulumu gerçekleştirmeden veri aktarımı yapabilir.

Özellikle DNS ve DHCP gibi protokollerde UDP kullanıldığını gözlemledim.

Örnek olarak yakaladığım DHCP trafiğinde:

```text
10.0.3.15 → 10.0.3.2
DHCP
324 bytes
DHCP Request
```

şeklinde bir UDP trafiği gözlemledim.

---

## 6. DNS Trafik Analizi

DNS trafiğini incelemek için:

```bash
nslookup example.com
```

komutunu kullandım.

DNS sunucusu olarak:

```text
10.0.3.3
```

kullanıldı.

### Frame 49 — DNS Query

```text
49  144.545663434
10.0.3.15 → 10.0.3.3
DNS
88 bytes
Standard query 0x64a5
A contile.services.mozilla.com
```

Bu paket Kali sistemimin DNS sunucusuna `contile.services.mozilla.com` alan adının IPv4 adresini sorduğunu göstermektedir.

Buradaki `A` kayıt tipi IPv4 adresi sorgulamak için kullanılır.

### Frame 51 — DNS Response

```text
51  144.641998327
10.0.3.3 → 10.0.3.15
DNS
140 bytes
Standard query response 0x64a5
A contile.services.mozilla.com
CNAME mozilla.map.fastly.net
A 199.232.17.91
```

DNS sunucusu sorguya cevap olarak:

```text
contile.services.mozilla.com
        ↓
mozilla.map.fastly.net
        ↓
199.232.17.91
```

ilişkisini döndürmüştür.

### Frame 50 — DNS Query

```text
50  144.564829468
10.0.3.15 → 10.0.3.3
DNS
79 bytes
Standard query 0xe891
A spocs.getpocket.com
```

### Frame 53 — DNS Response

```text
53  144.706749992
10.0.3.3 → 10.0.3.15
DNS
131 bytes
Standard query response 0xe891
A spocs.getpocket.com
CNAME mozilla.map.fastly.net
A 199.232.17.91
```

Bu örneklerde DNS Transaction ID değerlerini kullanarak sorgu ve cevap paketlerini eşleştirdim.

---

## 7. HTTP Trafik Analizi

HTTP trafiği oluşturmak için:

```bash
curl http://detectportal.firefox.com/success.txt
```

komutunu kullandım.

Terminalde:

```text
success
```

sonucunu aldım.

### Frame 243 — HTTP Request

```text
243  741.088260147
10.0.3.15 → 199.232.17.91
HTTP
153 bytes
GET /success.txt HTTP/1.1
```

Paket detaylarında:

```http
GET /success.txt HTTP/1.1
Host: detectportal.firefox.com
User-Agent: curl/8.15.0
Accept: */*
```

bilgilerini gördüm.

| Alan             | Değer                      |
| ---------------- | -------------------------- |
| Method           | GET                        |
| URI              | `/success.txt`             |
| HTTP Version     | HTTP/1.1                   |
| Host             | `detectportal.firefox.com` |
| User-Agent       | `curl/8.15.0`              |
| Source IP        | `10.0.3.15`                |
| Destination IP   | `199.232.17.91`            |
| Destination Port | `80`                       |

Wireshark bu isteğin cevabının **Frame 245** olduğunu gösterdi.

### Frame 245 — HTTP Response

```text
245  741.154392263
199.232.17.91 → 10.0.3.15
HTTP
422 bytes
HTTP/1.1 200 OK
```

Sunucu:

```text
HTTP/1.1 200 OK
Content-Type: text/plain
```

şeklinde cevap verdi.

### HTTP Akışı

```text
Kali                                      Web Server
10.0.3.15                              199.232.17.91
   |                                         |
   |---- GET /success.txt ----------------->|
   |                                         |
   |<----------- HTTP/1.1 200 OK -----------|
   |                                         |
```

---

## 8. Genel Trafik Özeti

Ana PCAP içerisinde farklı protokollerin farklı amaçlarla kullanıldığını gözlemledim.

| Protokol | Gözlem                   | Örnek         |
| -------- | ------------------------ | ------------- |
| ICMP     | Echo Request / Reply     | Frame 13-14   |
| TCP      | SYN, retransmission, RST | Frame 105-107 |
| UDP      | Bağlantısız trafik       | DHCP / DNS    |
| DNS      | Query / Response         | Frame 49-51   |
| HTTP     | GET / 200 OK             | Frame 243-245 |

Bu trafiklerin birlikte incelenmesi, ağ iletişimini farklı protokoller üzerinden değerlendirmemi sağladı.

---

## 9. Güvenlik Açısından Değerlendirme

Bu PCAP kontrollü laboratuvar ortamımda oluşturulduğu için gözlemlediğim trafiklerin büyük bölümü beklenen test trafiğidir.

Bununla birlikte Wireshark üzerinde bazı davranışların güvenlik analizinde neden önemli olabileceğini gördüm.

### TCP SYN

```text
[SYN]
```

tek başına saldırı anlamına gelmez. Normal bir TCP bağlantısının başlangıcı olabilir.

### TCP Retransmission

```text
[TCP Retransmission]
```

ağ gecikmesi, paket kaybı veya cevap alınamaması gibi farklı nedenlerle ortaya çıkabilir.

### RST

```text
[RST, ACK]
```

kapalı veya filtrelenmiş servisler ve bağlantı sorunları gibi normal durumlarda da görülebilir.

### DNS

DNS sorguları normal ağ davranışıdır. Ancak güvenlik analizinde olağandışı domainler, yüksek DNS hacmi veya beklenmeyen DNS sunucuları gibi davranışlar ayrıca incelenebilir.

Bu nedenle tek bir pakete bakarak saldırı sonucu çıkarmak yerine kaynak, hedef, protokol, port, zamanlama ve trafik yoğunluğu gibi bilgilerin birlikte değerlendirilmesi gerektiğini öğrendim.

---

## 10. Öğrendiğim Temel Noktalar

Bu ana PCAP çalışması sırasında:

* ICMP Echo Request ve Echo Reply paketlerini ayırt ettim.
* TCP SYN ve SYN retransmission davranışını gözlemledim.
* TCP RST, ACK paketinin rolünü gördüm.
* UDP trafiğini TCP'den ayırdım.
* DNS Query ve Response paketlerini Transaction ID ile eşleştirdim.
* DNS A ve CNAME kayıtlarını inceledim.
* HTTP GET isteğinin yapısını analiz ettim.
* HTTP response içerisindeki `200 OK` durum kodunu gördüm.
* Terminalde kullandığım komutların ağ üzerinde oluşturduğu gerçek paketleri Wireshark üzerinden takip ettim.

---

## 11. Sonuç

Bu çalışmada kendi laboratuvar ortamımda oluşturduğum ana PCAP kaydını Wireshark kullanarak analiz ettim.

Farklı protokollerin ağ üzerinde nasıl göründüğünü gerçek paketler üzerinden incelemek, teorik olarak öğrendiğim bilgileri paket seviyesinde anlamamı sağladı.

Özellikle bir işlemin sadece terminalde görünen sonucuna bakmak yerine, bunun arka planda hangi paketlerden oluştuğunu incelemenin ağ güvenliği analizi açısından önemli olduğunu gördüm.
