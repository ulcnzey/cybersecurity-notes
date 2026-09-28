# 16. Follow TCP Stream

## 1. TCP Stream Nedir?

TCP Stream, bir TCP bağlantısı sırasında istemci ve sunucu arasında gerçekleşen veri alışverişinin bütününü ifade eder.

TCP iletişiminde veriler tek bir paket halinde gönderilmez. Veri, birden fazla TCP segmentine bölünerek gönderilebilir. Wireshark bu paketleri aynı TCP bağlantısına ait oldukları için bir stream altında takip edebilir.

Bu nedenle TCP Stream'i, bir TCP bağlantısının başından sonuna kadar gerçekleşen veri akışı olarak değerlendirebilirim.

---

## 2. Follow TCP Stream Özelliği Nedir?

Wireshark'ta bir TCP paketine sağ tıklayıp:

**Follow → TCP Stream**

seçeneğini kullandığımda, seçtiğim paketin ait olduğu TCP bağlantısındaki veri akışını toplu şekilde inceleyebilirim.

Bu özellik sayesinde TCP bağlantısına ait paketleri tek tek incelemek yerine, aynı bağlantıdaki veri alışverişini bir bütün olarak görebilirim.

Wireshark bu bağlantıya bir **Stream Index** verir.

Benim incelediğim bağlantıda:

```text
Stream index: 25
```

bilgisini gördüm.

Frame 441 ise bu stream içerisindeki:

```text
Stream Packet Number: 4
```

numaralı paketti.

---

## 3. İncelediğim TCP Stream

İnceleme sırasında bir TCP paketine sağ tıklayarak:

**Follow → TCP Stream**

özelliğini kullandım.

Stream içerisinde okunabilir HTTP içeriği yerine büyük ölçüde binary ve anlamsız görünen karakterler olduğunu gördüm.

Örneğin akış içerisinde:

```text
spocs.getpocket.com
```

gibi okunabilir bir alan bulunuyordu. Bunun yanında verinin büyük bölümü okunabilir metin yerine binary/şifreli veri şeklindeydi.

Bu durumun nedenini anlamak için stream içerisindeki paketlerden Frame 441'i ayrıca inceledim.

---

## 4. Frame 441 Analizi

İncelediğim paket:

```text
Frame 441: 721 bytes on wire
721 bytes captured
Interface: eth1
```

Paketin Ethernet bilgileri:

```text
Source MAC: 08:00:27:7d:bb:61
Destination MAC: 52:55:0a:00:03:02
```

IPv4 bilgileri:

```text
Source IP: 10.0.3.15
Destination IP: 199.232.17.91
```

TCP bilgileri:

```text
Source Port: 43796
Destination Port: 443
Stream Index: 25
Stream Packet Number: 4
TCP Segment Length: 667 bytes
Flags: PSH, ACK
```

Bu bağlantının hedef portunun `443` olması ve üst katmanda TLS bulunması nedeniyle bunun HTTPS/TLS iletişimi olduğunu anlayabildim.

---

## 5. TLS Client Hello

Frame 441 içerisinde:

```text
TLSv1.3 Record Layer: Handshake Protocol: Client Hello
```

bilgisini gördüm.

Client Hello, TLS bağlantısının başlangıcında istemci tarafından gönderilen mesajdır.

İstemci bu mesaj içerisinde desteklediği TLS sürümleri, cipher suite'ler, key exchange bilgileri ve çeşitli TLS uzantıları hakkında bilgi gönderir.

Frame 441 içerisinde aşağıdaki bilgileri gözlemledim:

```text
Supported Versions:
TLS 1.3
TLS 1.2
```

Ayrıca:

```text
Cipher Suites: 17 suites
```

bilgisini gördüm.

---

## 6. SNI Bilgisi

Client Hello içerisinde:

```text
Extension: server_name
Name: spocs.getpocket.com
```

bilgisini gördüm.

SNI (Server Name Indication), TLS bağlantısının hangi sunucu adıyla ilişkilendirildiğini belirtmek için kullanılan bir TLS uzantısıdır.

Bu nedenle incelediğim bağlantıda istemcinin:

```text
spocs.getpocket.com
```

sunucu adıyla bağlantı kurmaya çalıştığını gözlemledim.

Ancak SNI bilgisinin görünmesi, HTTP isteğinin tamamının görülebileceği anlamına gelmez.

Örneğin aşağıdaki gibi HTTP içeriğini bu pakette doğrudan görmedim:

```http
GET /...
Cookie: ...
Authorization: ...
```

---

## 7. Key Share ve ALPN

Client Hello içerisinde:

```text
Extension: key_share
x25519, secp256r1
```

bilgisini gördüm.

Key Share, TLS 1.3 bağlantısında güvenli bir ortak anahtar oluşturulması sürecine katkı sağlayan bilgidir.

Ayrıca:

```text
Extension: application_layer_protocol_negotiation
```

uzantısının da bulunduğunu gördüm.

ALPN, TLS bağlantısının üzerinde hangi uygulama protokolünün kullanılacağının belirlenmesine yardımcı olur.

---

## 8. Encrypted Client Hello

İncelediğim Client Hello içerisinde:

```text
Extension: encrypted_client_hello
```

bilgisini de gördüm.

ECH (Encrypted Client Hello), Client Hello içerisindeki bazı bilgilerin ağ üzerinde daha az görünür olmasını sağlamak amacıyla kullanılan bir mekanizmadır.

Bu nedenle SNI bilgilerinin her TLS bağlantısında aynı şekilde görünür olacağını varsaymamak gerektiğini öğrendim.

---

## 9. Follow TCP Stream'de Neden Binary Veri Gördüm?

Follow TCP Stream penceresinde verilerin büyük bölümünü okunabilir metin olarak göremedim.

Bunun temel nedeni, incelediğim bağlantının TLS ile korunmasıdır.

İletişimi basitleştirerek şu şekilde düşünebilirim:

```text
HTTP
  ↓
TLS ile şifreleme
  ↓
TCP
  ↓
IP
  ↓
Ethernet
```

Wireshark TCP paketlerini bir araya getirerek TCP Stream'i gösterebilir.

Ancak TCP Stream'i birleştirmek, TLS tarafından şifrelenmiş uygulama verisinin otomatik olarak okunabilir hale gelmesini sağlamaz.

Bu nedenle benim incelememde:

```text
TCP Stream
    ↓
TLS verisi
    ↓
Büyük ölçüde binary/şifreli içerik
```

şeklinde bir sonuç ortaya çıktı.

---

## 10. HTTP Trafiğinde Follow TCP Stream Kullanımı

Eğer şifrelenmemiş HTTP trafiği incelenseydi, Follow TCP Stream içerisinde HTTP istek ve cevaplarını daha okunabilir şekilde görebilirdim.

Örneğin:

```http
GET /login HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
```

gibi bilgiler tek bir akış içerisinde incelenebilirdi.

Bu nedenle Follow TCP Stream, özellikle HTTP trafiğinin istemci ile sunucu arasındaki bütünlüğünü anlamak için kullanışlıdır.

HTTPS/TLS kullanıldığında ise uygulama içeriğinin önemli bölümü şifreli kalır.

---

## 11. Adli Bilişim ve Olay Müdahalesinde Önemi

Follow TCP Stream özelliğinin adli bilişim ve olay müdahalesinde önemli olduğunu düşünüyorum çünkü bir TCP bağlantısına ait paketleri birlikte değerlendirmeyi kolaylaştırıyor.

Bir olay incelemesinde:

* Kaynak IP
* Hedef IP
* Kaynak port
* Hedef port
* TCP bağlantısı
* İletişim zamanı
* Veri akışı
* Kullanılan uygulama protokolü
* TLS bilgileri
* HTTP içeriği varsa HTTP istek ve cevapları

birlikte değerlendirilebilir.

Bu sayede tek tek paketlere bakmak yerine belirli bir iletişimin bütününü incelemek mümkün olur.

Ancak bir TCP Stream'de binary veya şifreli veri görülmesi tek başına kötü amaçlı trafik olduğu anlamına gelmez. Trafiğin normal mi yoksa şüpheli mi olduğunu belirlemek için IP adresleri, zaman bilgileri, DNS kayıtları, TLS bilgileri ve diğer ağ trafiği ile birlikte değerlendirme yapılması gerekir.

---

## 12. İnceleme Sonucum

Bu çalışmada Wireshark'ın **Follow → TCP Stream** özelliğini kullanarak bir TCP bağlantısının veri akışını inceledim.

İncelediğim bağlantının:

```text
Source: 10.0.3.15:43796
Destination: 199.232.17.91:443
TCP Stream: 25
```

olduğunu gördüm.

Frame 441 içerisinde TLS 1.3 Client Hello mesajını inceledim.

Client Hello içerisinde:

* `spocs.getpocket.com` SNI bilgisi,
* TLS 1.3 ve TLS 1.2 desteklenen sürümleri,
* cipher suite'ler,
* `x25519` ve `secp256r1` key share bilgileri,
* ALPN,
* Encrypted Client Hello uzantısı

gibi bilgileri gözlemledim.

Follow TCP Stream penceresinde verilerin büyük bölümünün okunabilir olmamasının nedeninin TLS ile şifrelenmiş trafik olduğunu öğrendim.

Bu çalışma sayesinde **TCP Stream'in TCP paketlerini bir araya getirerek iletişimin bütününü incelemeyi sağladığını, ancak şifreli TLS verisinin otomatik olarak okunabilir HTTP içeriğine dönüşmediğini** uygulamalı olarak görmüş oldum.

---

## 13. Ekran Görüntüsü

<img width="582" height="569" alt="image" src="https://github.com/user-attachments/assets/465fbee4-4e34-4abb-939b-29a43a8ed3de" />

<img width="759" height="547" alt="image" src="https://github.com/user-attachments/assets/6b3d2a2a-0f02-41c8-837b-63b40ab4c76a" />

---

## Öğrendiklerim

* TCP Stream'in bir TCP bağlantısındaki veri akışını temsil ettiğini öğrendim.
* Follow TCP Stream özelliğinin aynı TCP bağlantısındaki paketleri birlikte incelemeyi sağladığını gördüm.
* HTTP trafiğinde bu özelliğin istek ve cevapların birlikte incelenmesinde kullanılabileceğini öğrendim.
* HTTPS/TLS trafiğinde TCP Stream'in şifrelenmiş veriyi gösterebildiğini ancak uygulama içeriğini otomatik olarak çözmediğini gördüm.
* Client Hello, SNI, cipher suite, key share ve ALPN gibi TLS bilgilerini gerçek bir paket üzerinde inceledim.
* TCP Stream'in adli bilişim ve olay müdahalesinde iletişimin bütününü değerlendirmek için kullanılabileceğini öğrendim.
