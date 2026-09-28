# 10. UDP Trafiğini İncele

## 1. UDP Nedir?

UDP (User Datagram Protocol), ağ üzerinde verilerin bir bağlantı kurulmadan gönderilmesini sağlayan bir taşıma katmanı protokolüdür.

TCP'den farklı olarak UDP'de iletişim başlamadan önce 3-Way Handshake yapılmaz. Ayrıca TCP'deki gibi teslim garantisi, sıralama ve yeniden gönderim mekanizmaları bulunmaz.

Bu nedenle UDP daha az kontrol mekanizmasına sahip, daha basit bir protokoldür.

---

## 2. TCP ve UDP Arasındaki Fark

| Özellik             | TCP               | UDP                                             |
| ------------------- | ----------------- | ----------------------------------------------- |
| Bağlantı            | Bağlantılı        | Bağlantısız                                     |
| 3-Way Handshake     | Var               | Yok                                             |
| Teslim garantisi    | Var               | Yok                                             |
| Paket sıralaması    | Var               | Yok                                             |
| Yeniden gönderim    | Var               | Yok                                             |
| Kontrol mekanizması | Fazla             | Daha az                                         |
| Kullanım            | Web, SSH, FTP vb. | DNS, DHCP, VoIP, gerçek zamanlı uygulamalar vb. |

TCP daha fazla kontrol mekanizmasına sahip olduğu için güvenilir veri aktarımı gereken durumlarda kullanılır.

UDP ise bağlantı kurulması ve kontrol işlemlerinin daha az olması nedeniyle özellikle hızlı ve gerçek zamanlı iletişimin önemli olduğu uygulamalarda tercih edilebilir.

---

## 3. Wireshark'ta UDP Trafiğini Görüntüleme

Wireshark'ta yalnızca UDP paketlerini görmek için aşağıdaki display filter'ı kullandım:

```text
udp
```

Bu filtre, yakalanmış paketler arasından yalnızca UDP protokolünü kullanan paketleri göstermektedir.

---

## 4. Yakalanan UDP Paketleri

UDP filtresini uyguladığımda aşağıdaki paketleri gördüm:

### Frame 1 - DHCP Request

```text
10.0.3.15 → 10.0.3.2
DHCP 324
DHCP Request - Transaction ID 0x1b419760
```

Frame 1'in UDP detaylarını incelediğimde:

* Source IP: `10.0.3.15`
* Destination IP: `10.0.3.2`
* Source Port: `68`
* Destination Port: `67`
* UDP Length: `290`
* UDP Payload: `282 bytes`
* Protocol: `UDP`
* Üst katman protokolü: `DHCP`

Paket yapısı şu şekildeydi:

```text
Ethernet
   ↓
IPv4
   ↓
UDP
   ↓
DHCP
```

Bu pakette bilgisayarım DHCP Request mesajını UDP üzerinden gönderiyordu.

---

### Frame 2 - DHCP ACK

```text
10.0.3.2 → 255.255.255.255
DHCP 590
DHCP ACK - Transaction ID 0x1b419760
```

Bu paket DHCP sunucusundan gelen bir **DHCP ACK** mesajıdır.

DHCP iletişiminde UDP kullanılması, bilgisayarın ağ yapılandırmasını otomatik olarak almasını sağlar.

---

### Frame 9 - DNS Query

```text
fd17:625c:f037:3:ac4d:81b9:d683:7bd6
→
fd17:625c:f037:3::3

DNS 101
Standard query 0x3646
PTR 2.3.0.10.in-addr.arpa
```

Bu pakette Wireshark tarafından trafik **DNS** olarak tanımlandı.

DNS sorgularının büyük bölümü UDP üzerinden gerçekleştirilebilir.

---

# 5. Soruların Cevapları

## 1. UDP neden bağlantısız bir protokoldür?

UDP iletişim başlamadan önce TCP'deki gibi bir bağlantı kurma işlemi gerçekleştirmez.

Örneğin TCP'de:

```text
SYN
 ↓
SYN/ACK
 ↓
ACK
```

şeklinde 3-Way Handshake yapılır.

UDP'de ise böyle bir bağlantı kurma aşaması bulunmaz. Veri doğrudan gönderilir.

Bu nedenle UDP'ye **connectionless (bağlantısız)** protokol denir.

---

## 2. TCP neden daha fazla kontrol mekanizmasına sahiptir?

TCP'nin temel amacı verinin güvenilir şekilde karşı tarafa ulaşmasını sağlamaktır.

Bu nedenle TCP'de:

* Sequence Number
* Acknowledgment
* Retransmission
* Flow Control
* Congestion Control
* Connection State

gibi mekanizmalar bulunur.

UDP'de bu mekanizmaların büyük bölümü bulunmaz. Bu nedenle UDP daha basit bir taşıma protokolüdür.

---

## 3. UDP hangi uygulamalarda tercih edilir?

UDP özellikle gerçek zamanlı iletişim veya düşük protokol yükünün önemli olduğu uygulamalarda kullanılabilir.

Örnekler:

* DNS
* DHCP
* VoIP
* Online oyunlar
* Gerçek zamanlı ses ve video uygulamaları
* Bazı yayın ve akış uygulamaları

Burada önemli olan nokta, UDP'nin her durumda TCP'den daha hızlı olduğu anlamına gelmemesidir. UDP'nin avantajı, bağlantı kurulması ve güvenilirlik mekanizmaları gibi ek işlemleri taşıma katmanında zorunlu kılmamasıdır.

---

## 4. DNS neden çoğunlukla UDP kullanır?

DNS sorguları genellikle küçük bir istek ve cevap şeklindedir.

UDP kullanıldığında:

* Bağlantı kurulması gerekmez.
* 3-Way Handshake yapılmaz.
* Ek kontrol trafiği daha azdır.
* Küçük sorgular hızlı şekilde gönderilebilir.

Bu nedenle klasik DNS sorgularında UDP yaygın olarak kullanılır.

Ancak DNS yalnızca UDP kullanmaz. Bazı durumlarda TCP de kullanılabilir.

---

## 5. UDP trafiğinin güvenlik analizindeki önemi nedir?

UDP bağlantısız olduğu için trafik analizinde özellikle dikkat edilmesi gereken bir protokoldür.

Wireshark ile UDP trafiğini incelerken:

* Hangi IP adresleri arasında iletişim olduğunu,
* Hangi UDP servislerinin kullanıldığını,
* Beklenmeyen UDP portlarını,
* Olağandışı yüksek UDP trafiğini,
* DNS ve DHCP gibi normal UDP trafiğini

inceleyebilirim.

Beklenmeyen UDP trafiği bazı durumlarda tarama, yanlış yapılandırma veya başka bir güvenlik olayının göstergesi olabilir. Ancak tek bir UDP paketi görülmesi doğrudan saldırı olduğu anlamına gelmez. Trafiğin normal olup olmadığını anlamak için kaynak, hedef, port, paket sıklığı ve uygulama bağlamı birlikte değerlendirilmelidir.

---

# 6. Kendi Gözlemim

Bu çalışmada Wireshark'ta:

```text
udp
```

filtresini kullanarak UDP paketlerini ayırdım.

Kendi yakaladığım trafikte DHCP Request, DHCP ACK ve DNS sorgusu gibi UDP tabanlı iletişimleri gördüm.

Özellikle Frame 1 üzerinde UDP katmanını açarak kaynak ve hedef portlarını inceledim:

```text
Source Port      : 68
Destination Port : 67
UDP Length       : 290
Payload          : 282 bytes
```

Bu çalışma sayesinde bir paketin yalnızca "UDP" olarak görünmesinin yeterli olmadığını, UDP'nin üzerinde çalışan uygulama protokolünü de incelemek gerektiğini gördüm.

Örneğin:

```text
UDP
 ├── DHCP
 └── DNS
```

gibi farklı uygulamalar UDP taşıma protokolünü kullanabilir.

Bu nedenle ağ trafiği analizinde **taşıma katmanı ile uygulama katmanını birlikte değerlendirmek** önemlidir.
