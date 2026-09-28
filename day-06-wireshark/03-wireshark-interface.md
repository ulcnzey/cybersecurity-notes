# 03 - Wireshark Interface and Basic Usage

## 1. Wireshark Interface

Bu bölümde Wireshark'ın temel arayüzünü ve paket analizinde kullanılan bölümleri uygulamalı olarak inceledim.

Wireshark'ta öncelikle hangi ağ arayüzünden trafik yakalayacağımı belirledim.

Benim Kali Linux sistemimde ağ arayüzlerini kontrol ettiğimde aktif olarak kullandığım arayüzün `eth1` olduğunu gördüm.

`eth1` arayüzünün IP adresi:

```text
10.0.3.15/24
```

Ağ geçidi:

```text
10.0.3.2
```

Bu nedenle Wireshark üzerinde paket yakalamak için `eth1` arayüzünü kullandım.

---

## 2. Interface List

Wireshark'ın başlangıç ekranında farklı ağ arayüzlerini gördüm:

* `eth0`
* `eth1`
* `docker0`
* diğer sanal arayüzler

Burada her arayüz farklı bir ağ bağlantısını temsil edebilir.

Benim aktif trafik gördüğüm arayüz:

```text
eth1
```

Bir arayüzün yanında paket sayısının artması, o arayüz üzerinden trafik geçtiğini gösterir.

Bu nedenle paket yakalama işleminde doğru arayüzü seçmek önemlidir.

---

## 3. Packet List

`eth1` üzerinden paket yakalamayı başlattığımda Wireshark'ın üst bölümünde **Packet List** alanını gördüm.

Packet List, yakalanan paketleri satır satır gösterir.

Burada temel olarak:

* No.
* Time
* Source
* Destination
* Protocol
* Length
* Info

gibi bilgileri görebilirim.

Packet List bölümünü, ağ üzerinde gerçekleşen iletişimin genel görünümünü görmek için kullanıyorum.

---

## 4. Packet Details

Packet List içerisinden bir pakete tıkladığımda alt bölümde **Packet Details** alanı açıldı.

Bu bölüm seçtiğim paketin protokollerini ve protokol alanlarını ayrıntılı olarak gösteriyor.

Örneğin incelediğim bir DHCP paketinde sırasıyla:

```text
Frame
Ethernet II
Internet Protocol Version 4
User Datagram Protocol
Dynamic Host Configuration Protocol
```

katmanlarını gördüm.

Bu yapı bana bir paketin farklı protokol katmanlarından oluştuğunu gösterdi.

### Packet List ile Packet Details arasındaki fark

**Packet List:**

> Yakalanan paketleri genel olarak gösterir.

**Packet Details:**

> Seçtiğim tek bir paketin iç yapısını ve protokol bilgilerini ayrıntılı olarak gösterir.

Bu nedenle Packet List'i genel görünümü, Packet Details'i ise seçtiğim paketin ayrıntılı analizini yapmak için kullanıyorum.

<img width="1082" height="504" alt="image" src="https://github.com/user-attachments/assets/ce6016bd-f9f2-40ca-9f10-9377f38a70b1" />


---

## 5. Packet Bytes

Packet Details bölümünün altında **Packet Bytes** alanını inceledim.

Burada paketin ham hexadecimal verilerini gördüm.

Örneğin incelediğim pakette:

```text
0a 00 03 0f
```

değeri `10.0.3.15` IP adresine karşılık geliyordu.

Benzer şekilde:

```text
0a 00 03 02
```

değeri `10.0.3.2` adresine karşılık geliyordu.

Bu bölüm, Wireshark'ın paketi bizim için yorumlamadan önceki ham verisini görmemi sağlıyor.

Bu nedenle:

```text
Packet Details = Wireshark tarafından yorumlanmış veri
Packet Bytes = Ham hexadecimal veri
```

şeklinde düşünebilirim.

---

## 6. First Packet Analysis

İlk incelediğim paketlerden biri DHCP Request paketiydi.

Packet Details bölümünde şu bilgileri gördüm:

### Ethernet

```text
Source MAC: 08:00:27:7d:bb:61
Destination MAC: 52:55:0a:00:03:02
```

### IPv4

```text
Source IP: 10.0.3.15
Destination IP: 10.0.3.2
Protocol: UDP (17)
```

### UDP

```text
Source Port: 68
Destination Port: 67
```

Bu pakette Kali Linux'un DHCP istemci portu olan `68` numaralı UDP kaynak portundan, DHCP sunucu portu olan `67` numaralı UDP hedef porta istek gönderdiğini gördüm.

Burada şunu daha iyi anladım:

```text
IP adresi  → Hangi cihaz?
Port       → Hangi iletişim noktası/hizmet?
Protocol   → İletişim nasıl gerçekleştiriliyor?
```

---

# 7. Display Filter

Wireshark'ta **Display Filter** alanını kullandım.

Display Filter, daha önce yakalanmış paketler arasından sadece görmek istediğim paketleri filtrelememi sağlar.

Örneğin:

```text
arp
```

filtresini kullandığımda Packet List içerisinde sadece ARP paketlerini gördüm.

Burada önemli nokta, Display Filter'ın paketleri silmemesidir.

Yani:

```text
Paketleri yakala
        ↓
Display Filter uygula
        ↓
Sadece istediğim paketleri göster
```

şeklinde çalışır.

<img width="1362" height="197" alt="image" src="https://github.com/user-attachments/assets/63a48409-8c5f-4b11-9440-7bfc3fed08bd" />


---

# 8. Capture Filter

Daha sonra **Capture Filter** kavramını inceledim.

Capture Filter, paketler yakalanmadan önce hangi paketlerin yakalanacağını belirlemek için kullanılır.

<img width="957" height="565" alt="image" src="https://github.com/user-attachments/assets/f013a7c1-e9d0-4331-8988-fa019281781b" />


Wireshark'ın:

**Capture → Capture Options**

bölümünden `eth1` arayüzünü seçtim.

**Capture Filter for selected interfaces** alanına:

```text
arp
```

yazdım.

Filtre yeşil renkte göründü ve Wireshark bunun geçerli bir Capture Filter olduğunu kabul etti.

Daha sonra yeni bir yakalama başlattım.

Sonuç olarak yalnızca ARP paketlerinin yakalandığını gördüm.

Yakalamada:

```text
Captured packets: 2
Capture Filter: arp
Dropped packets: 0
```

bilgilerini gördüm.

### Display Filter ve Capture Filter farkı

Bu iki filtre arasındaki farkı uygulamalı olarak gördüm.

**Capture Filter:**

> Paket daha yakalanmadan önce hangi paketleri yakalayacağımı belirler.

**Display Filter:**

> Paketler yakalandıktan sonra hangilerini ekranda göstereceğimi belirler.

Kısaca:

```text
Capture Filter → YAKALA 📥

Display Filter → GÖSTER 👀
```

---

# 9. Statistics

Wireshark'ın **Statistics** menüsünü inceledim.

İlk olarak:

**Statistics → Capture File Properties**

bölümünü açtım.

Burada yakaladığım dosyanın özelliklerini gördüm.

Örneğin:

```text
Format: pcapng
Interface: eth1
Capture Filter: arp
Captured Packets: 2
Displayed Packets: 2
Dropped Packets: 0
```

bilgilerini gördüm.

Bu bölüm sayesinde yakalanan trafiğin genel durumunu sayısal olarak inceleyebildiğimi gördüm.

Örneğin:

* Kaç paket yakalandı?
* Kaç paket gösteriliyor?
* Paket kaybı oldu mu?
* Yakalama ne kadar sürdü?
* Toplam veri miktarı ne kadar?

gibi sorulara cevap verebilirim.

<img width="797" height="601" alt="image" src="https://github.com/user-attachments/assets/1d0437a5-b22c-4d21-889a-f2f5712051f6" />


---

# 10. Conversations

Daha sonra:

**Statistics → Conversations**

bölümünü inceledim.

Conversations bölümünün ağ üzerindeki iletişimleri iki uç arasındaki iletişim şeklinde gruplandırdığını gördüm.

Örneğin Ethernet seviyesinde:

```text
Address A ↔ Address B
```

şeklinde iletişim çiftleri görülebilir.

Burada:

* hangi adreslerin iletişim kurduğu,
* kaç paket gönderildiği,
* ne kadar veri taşındığı

gibi bilgiler incelenebilir.

Bu nedenle Conversations bölümünü:

> "Kim kiminle iletişim kuruyor?"

sorusuna cevap veren bölüm olarak düşünebilirim.

<img width="813" height="624" alt="image" src="https://github.com/user-attachments/assets/653e0b35-9203-40ed-aac4-d03a7101d022" />


---

# 11. Endpoints

Son olarak:

**Statistics → Endpoints**

bölümünü inceledim.

Endpoints bölümü yakalanan trafik içerisinde görülen ağ uçlarını/adreslerini gösterir.

Farklı sekmeler üzerinden:

* Ethernet → MAC adresleri
* IPv4 → IPv4 adresleri
* IPv6 → IPv6 adresleri

gibi bilgiler incelenebilir.

Benim yakalamamda sadece:

```text
arp
```

Capture Filter'ı kullanıldığı için IPv4 Endpoints sekmesinde herhangi bir kayıt görmedim.

Bunun nedeni yakaladığım trafiğin ARP olması ve yaptığım filtrenin yalnızca ARP paketlerini yakalamasıydı.

Bu durum bana kullanılan Capture Filter'ın analiz sonuçlarını doğrudan etkilediğini gösterdi.

---

# 12. Section 3 Summary

Bu bölümde Wireshark'ın temel arayüzünü uygulamalı olarak öğrendim.

Özellikle şu bölümleri inceledim:

```text
Interface List
      ↓
Packet List
      ↓
Packet Details
      ↓
Packet Bytes
      ↓
Display Filter
      ↓
Capture Filter
      ↓
Statistics
      ↓
Conversations
      ↓
Endpoints
```

En önemli öğrendiğim ayrımlardan biri **Display Filter ile Capture Filter arasındaki fark** oldu.

```text
Capture Filter
→ Paket yakalanmadan önce filtreleme

Display Filter
→ Paket yakalandıktan sonra görüntüleme
```

Ayrıca bir paketi incelerken:

```text
MAC → Yerel ağdaki cihazları belirleme
IP  → Kaynak ve hedef cihazları belirleme
Port → İletişimin hangi hizmet/uygulama üzerinden gerçekleştiğini görme
Protocol → Kullanılan iletişim protokolünü belirleme
```

şeklinde düşünmeye başladım.

Bu bölümün sonunda Wireshark'ta sadece paketleri görmek yerine, **paketin nereden geldiğini, nereye gittiğini, hangi protokolleri kullandığını ve trafiğin nasıl filtrelenip gruplanabileceğini** anlamaya başladım.
