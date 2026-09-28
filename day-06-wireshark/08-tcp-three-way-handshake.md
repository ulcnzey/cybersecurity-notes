# 8. TCP 3-Way Handshake

## 1. TCP Nedir?

TCP (Transmission Control Protocol), iki cihaz arasında güvenilir ve bağlantı tabanlı iletişim sağlayan bir taşıma katmanı protokolüdür.

TCP ile veri aktarımından önce iki taraf arasında bir bağlantı kurulması gerekir. Bu bağlantının kurulması **3-Way Handshake** olarak adlandırılır.

Temel akış:

```text
Client → Server : SYN
Server → Client : SYN + ACK
Client → Server : ACK
```

Bu üç adım tamamlandıktan sonra TCP bağlantısı üzerinden veri aktarımı başlayabilir.

---

## 2. TCP 3-Way Handshake

### 1. SYN

İstemci sunucuya bağlantı başlatmak istediğini bildirir.

```text
Client → Server
[SYN]
```

SYN (Synchronize) flag'i TCP bağlantısının başlatılması sırasında kullanılır.

---

### 2. SYN/ACK

Sunucu istemciden gelen SYN paketini onaylar ve kendi SYN bilgisini gönderir.

```text
Server → Client
[SYN, ACK]
```

Buradaki:

* `SYN` → Sunucu da bağlantıyı başlatmak için kendi sequence bilgisini gönderir.
* `ACK` → İstemciden gelen SYN'in alındığını onaylar.

---

### 3. ACK

İstemci sunucunun SYN/ACK paketini aldığını bildirir.

```text
Client → Server
[ACK]
```

Bu üç adım tamamlandığında TCP bağlantısı kurulmuş olur.

---

# 3. Wireshark ile TCP Paketlerini İnceleme

Wireshark üzerinde TCP paketlerini incelemek için aşağıdaki display filter'ı kullandım:

```text
tcp.flags.syn == 1
```

Bu filtre TCP SYN flag'i bulunan paketleri göstermektedir.

Gerçek bir TCP bağlantı denemesinde aşağıdaki paketi yakaladım:

### Frame 11

```text
10.0.3.15 → 10.0.3.2
TCP
34080 → 53
[SYN]
Seq=0
Win=1024
Len=0
MSS=1460
```

Bu paket Kali bilgisayarımın `10.0.3.2` adresindeki hedefe TCP bağlantısı başlatmaya çalıştığını göstermektedir.

Paketin önemli bilgileri:

| Alan               | Değer     |
| ------------------ | --------- |
| Source IP          | 10.0.3.15 |
| Destination IP     | 10.0.3.2  |
| Source Port        | 34080     |
| Destination Port   | 53        |
| Protocol           | TCP       |
| Flag               | SYN       |
| Sequence Number    | 0         |
| TCP Segment Length | 0         |
| MSS                | 1460      |

Bu paket benim için TCP bağlantısının ilk adımını gerçek bir ağ trafiği üzerinde görmemi sağladı.

---

# 4. SYN Sonrasında RST, ACK

Aynı TCP konuşmasında hedef tarafından aşağıdaki paket gönderildi:

### Frame 19

```text
10.0.3.2 → 10.0.3.15
TCP
53 → 34080
[RST, ACK]
Seq=1
Ack=1
Win=65535
Len=0
```

Wireshark TCP detaylarında:

```text
Flags: 0x014 (RST, ACK)
```

şeklinde görüldü.

Bu paket **normal 3-Way Handshake'in ikinci adımı olan SYN/ACK değildir.**

Gerçekleşen trafik:

```text
10.0.3.15                 10.0.3.2

     SYN ───────────────────→

         ←──────────── RST, ACK
```

Dolayısıyla bu bağlantı denemesinde:

```text
SYN
SYN/ACK
ACK
```

şeklindeki üçlü tamamlanmamıştır.

Bu örnek sayesinde TCP bağlantı kurulamadığında `RST` flag'inin gerçek bir pakette nasıl göründüğünü de incelemiş oldum.

---

# 5. TCP Flag'leri

## SYN

`SYN` (Synchronize), TCP bağlantısının başlatılmasında kullanılır.

```text
Client → Server
[SYN]
```

---

## ACK

`ACK` (Acknowledgment), alınan TCP segmentinin onaylandığını belirtir.

3-Way Handshake'in üçüncü adımında:

```text
Client → Server
[ACK]
```

şeklinde kullanılır.

---

## RST

`RST` (Reset), TCP bağlantısının resetlenmesini belirtir.

İncelediğim örnekte:

```text
[RST, ACK]
```

paketi gördüm.

Bu paket normal handshake'in devam etmediğini ve bağlantının resetlendiğini gösterdi.

---

## FIN

`FIN` (Finish), TCP bağlantısının normal şekilde sonlandırılması için kullanılır.

Bağlantının kapatılması sırasında taraflardan biri artık veri göndermeyeceğini FIN flag'i ile bildirir.

---

## PSH

`PSH` (Push), alınan verinin uygulamaya mümkün olduğunca hızlı şekilde aktarılması gerektiğini belirtmek için kullanılır.

---

## URG

`URG` (Urgent), TCP segmentinde acil olarak değerlendirilmesi gereken veri bulunduğunu belirtmek için kullanılan flag'dir.

---

# 6. TCP Handshake ile İlgili Öğrendiğim Önemli Nokta

Bu çalışmada TCP bağlantısının yalnızca "bağlantı kuruldu" şeklinde düşünülmemesi gerektiğini gördüm.

Wireshark üzerinde gerçek paketleri incelediğimde:

```text
SYN → SYN/ACK → ACK
```

normal bağlantı kurulma sürecini,

```text
SYN → RST/ACK
```

ise bağlantının normal şekilde kurulmadığı bir durumu gösterebiliyor.

Bu nedenle Wireshark'ta sadece TCP paketinin bulunmasına değil, **TCP flag'lerinin ve paketlerin sırasının birlikte incelenmesine** dikkat etmek gerekir.

---

# 7. Lab Durumu

3-Way Handshake'in tamamını yakalamak için daha önce kullandığım izole `cyber-lab` ağı üzerinden Metasploitable 2 ile bağlantı kurmayı da denedim.

Kali tarafında:

```text
eth0 → cyber-lab
eth1 → NAT
```

şeklinde bir yapı bulunmaktadır.

Kali'nin lab IP'si:

```text
192.168.56.10/24
```

olarak ayarlandı.

Ancak Metasploitable 2 tarafında `eth0` üzerinde beklenen `192.168.56.20` adresi oluşmadığı için iki makine arasında bağlantı kurulamadı.

Bu nedenle bu çalışma sırasında **tam SYN → SYN/ACK → ACK üçlüsünü henüz yakalayamadım**.

Buna rağmen Wireshark üzerinde gerçek bir:

```text
[SYN]
```

ve ardından:

```text
[RST, ACK]
```

paketini inceleyerek TCP bağlantı başlangıcı ve TCP reset davranışını gözlemledim.

---

# 8. Sonuç

Bu çalışmada TCP'nin bağlantı tabanlı çalışma mantığını ve 3-Way Handshake sürecini öğrendim.

Temel akış:

```text
SYN
 ↓
SYN/ACK
 ↓
ACK
 ↓
TCP Connection Established
```

Ayrıca Wireshark üzerinde gerçek TCP paketlerini inceleyerek:

* SYN
* ACK
* RST
* FIN
* PSH
* URG

flag'lerinin ne amaçla kullanıldığını araştırdım.

Özellikle `SYN` ve `RST, ACK` paketlerini gerçek ağ trafiğinde gözlemledim.

> Not: Tam 3-Way Handshake'in (`SYN → SYN/ACK → ACK`) yakalanması, lab ağındaki Metasploitable 2 bağlantısı düzeltildikten sonra ayrıca gerçekleştirilecektir.
