# 🔎 4. İlk Paket Analizi

Bu bölümde Wireshark kullanarak yakaladığım gerçek bir ağ paketini inceledim. Amacım, bir paketin içerisindeki temel ağ bilgilerini bulmak ve bu bilgilerin ne anlama geldiğini anlamaktı.

İncelediğim paket **Frame 13** ve paket türü **DHCP Request**.

---

## 📦 İncelediğim Paket

Wireshark'ta Frame 13'ü seçtiğimde aşağıdaki bilgileri gördüm:

| Alan             | Değer               |
| ---------------- | ------------------- |
| Source MAC       | `08:00:27:7d:bb:61` |
| Destination MAC  | `52:55:0a:00:03:02` |
| Source IP        | `10.0.3.15`         |
| Destination IP   | `10.0.3.2`          |
| Protocol         | `UDP`               |
| Source Port      | `68`                |
| Destination Port | `67`                |
| Packet Length    | `324 bytes`         |
| Timestamp        | `60.232685545`      |

<img width="1351" height="284" alt="image" src="https://github.com/user-attachments/assets/79a1c3eb-473e-454e-a5a5-bf4160aab3fd" />


---

## 1. Source MAC

**Değer:**

`08:00:27:7d:bb:61`

Source MAC, paketi gönderen cihazın ağ kartına ait MAC adresidir.

MAC adresi, cihazların yerel ağ içerisindeki iletişiminde kullanılan donanım adresidir.

Bu pakette kaynak MAC adresi benim Kali Linux makinemin ağ arayüzüne aittir.

---

## 2. Destination MAC

**Değer:**

`52:55:0a:00:03:02`

Destination MAC, Ethernet frame'inin yerel ağ üzerinde gönderildiği hedef MAC adresidir.

Yani bu paket Ethernet seviyesinde bu MAC adresine gönderilmiştir.

---

## 3. Source IP

**Değer:**

`10.0.3.15`

Source IP, paketi gönderen cihazın IP adresidir.

Benim Kali Linux makinem bu ağ arayüzünde `10.0.3.15` IP adresini kullanmaktadır.

Bu nedenle paket:

```text
10.0.3.15
```

adresinden gönderilmiştir.

---

## 4. Destination IP

**Değer:**

`10.0.3.2`

Destination IP, paketin gönderildiği hedef IP adresidir.

Bu pakette hedef IP:

```text
10.0.3.2
```

olarak görülmektedir.

---

## 5. Protocol

**Değer:**

`UDP`

Wireshark'ta paketin IPv4 bölümünde:

```text
Protocol: UDP (17)
```

bilgisini gördüm.

UDP, taşıma katmanında kullanılan bir protokoldür.

İncelediğim paket DHCP Request olduğu için DHCP iletişiminin UDP üzerinden gerçekleştiğini gördüm.

Paketin temel yapısını şu şekilde düşünebilirim:

```text
Ethernet
   ↓
IPv4
   ↓
UDP
   ↓
DHCP
```

---

## 6. Source Port

**Değer:**

`68`

Source Port, paketin gönderildiği kaynak iletişim portudur.

DHCP'de:

```text
UDP 68 → DHCP Client
```

olarak kullanılır.

Bu nedenle bu pakette `68` numaralı port kaynak port olarak görülmektedir.

---

## 7. Destination Port

**Değer:**

`67`

Destination Port, paketin gönderildiği hedef iletişim portudur.

DHCP'de:

```text
UDP 67 → DHCP Server
```

olarak kullanılır.

Bu nedenle paketin iletişimini şu şekilde gösterebilirim:

```text
10.0.3.15:68
      ↓
10.0.3.2:67
```

---

## 8. Packet Length

**Değer:**

`324 bytes`

Wireshark'ta Frame bölümünde:

```text
324 bytes on wire
324 bytes captured
```

bilgisini gördüm.

Bu değer, yakalanan Ethernet frame'inin toplam boyutunu göstermektedir.

Paketin farklı katmanlarında farklı uzunluk değerleri de bulunmaktadır:

```text
Frame → 324 bytes
IPv4  → 310 bytes
UDP   → 290 bytes
```

Bu değerlerin farklı olması normaldir çünkü her katman kendi başlık bilgilerini içerir.

Bu çalışma için paket uzunluğu olarak **324 bytes** değerini kullandım.

---

## 9. Timestamp

**Değer:**

`60.232685545`

Timestamp bilgisini Wireshark'ın Packet List bölümündeki **Time** sütunundan aldım.

Bu değer, paketin yakalama işleminin başlangıcından yaklaşık **60,23 saniye sonra** yakalandığını göstermektedir.

Timestamp sayesinde paketlerin ağ trafiğinde hangi sırada ve hangi zaman aralığında gerçekleştiğini inceleyebilirim.

---

## 🔍 Paketi Bir Bütün Olarak Okumak

İncelediğim paketi şu şekilde özetleyebilirim:

```text
Source MAC
08:00:27:7d:bb:61
        ↓
Source IP
10.0.3.15
        ↓
Source Port
68
        ↓
UDP
        ↓
Destination Port
67
        ↓
Destination IP
10.0.3.2
        ↓
Destination MAC
52:55:0a:00:03:02
```

Paketin:

```text
Protocol      → UDP
Packet Length → 324 bytes
Timestamp     → 60.232685545
```

olduğunu gördüm.

Bu analiz sayesinde Wireshark'ta tek bir pakete baktığımda **paketi kimin gönderdiğini, nereye gönderdiğini, hangi protokolü ve portları kullandığını, paket boyutunu ve yakalanma zamanını** belirleyebildiğimi öğrendim.

<img width="1085" height="509" alt="image" src="https://github.com/user-attachments/assets/6f21c518-8580-4cc9-aa91-96d09501a9d1" />

---

## 🧠 Bu Bölümden Çıkardığım Sonuç

Bu çalışmada bir paketin sadece "veri" olmadığını, içerisinde iletişimin farklı katmanlarına ait birçok bilgi bulunduğunu gördüm.

Özellikle şu ayrımı öğrenmiş oldum:

```text
MAC Address → Yerel ağdaki cihaz iletişimi
IP Address  → Ağ seviyesinde kaynak ve hedef
Port        → İletişim noktası / hizmet
Protocol    → Kullanılan iletişim protokolü
Length      → Paketin boyutu
Timestamp   → Paketin yakalanma zamanı
```

Bu bilgiler daha sonraki bölümlerde **ARP, ICMP, TCP, UDP, DNS ve şüpheli trafik analizi** yaparken temel olarak kullanılacaktır.
