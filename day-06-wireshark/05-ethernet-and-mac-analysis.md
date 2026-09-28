# 🌐 5. Ethernet ve MAC Adreslerini İnceleme

Bu bölümde Wireshark kullanarak daha önce incelediğim **Frame 13** üzerinden Ethernet katmanını analiz ettim.

Amacım Source MAC, Destination MAC, Ethernet Frame, EtherType kavramlarını anlamak ve IPv4 ile IPv6 trafiğinin nasıl ayırt edildiğini öğrenmekti.

---

## 📦 İncelediğim Paket

Wireshark'ta Frame 13'ü seçtiğimde Ethernet II bölümünde şu bilgileri gördüm:

```text
Ethernet II
    Source: 08:00:27:7d:bb:61
    Destination: 52:55:0a:00:03:02
    Type: IPv4 (0x0800)
```
<img width="1334" height="620" alt="image" src="https://github.com/user-attachments/assets/9f0e5a4a-4558-4183-9240-9494a17e40b2" />


### Paket bilgileri

| Alan            | Değer               |
| --------------- | ------------------- |
| Source MAC      | `08:00:27:7d:bb:61` |
| Destination MAC | `52:55:0a:00:03:02` |
| EtherType       | `0x0800`            |
| Üst protokol    | IPv4                |

---

## 1. Source MAC Nedir?

Source MAC, Ethernet frame'ini gönderen cihazın MAC adresidir.

İncelediğim pakette:

```text
08:00:27:7d:bb:61
```

Source MAC olarak görülmektedir.

Bu adres, paketin Ethernet seviyesindeki kaynağını gösterir.

---

## 2. Destination MAC Nedir?

Destination MAC, Ethernet frame'inin yerel ağ üzerinde gönderildiği hedef MAC adresidir.

İncelediğim pakette:

```text
52:55:0a:00:03:02
```

Destination MAC olarak görülmektedir.

Bu nedenle Ethernet seviyesindeki iletişim şu şekilde görülebilir:

```text
08:00:27:7d:bb:61
          ↓
    Ethernet Frame
          ↓
52:55:0a:00:03:02
```

---

## 3. MAC Adresi ile IP Adresi Arasındaki Fark

MAC adresi ve IP adresi aynı şey değildir.

İncelediğim pakette:

```text
MAC:
08:00:27:7d:bb:61

IP:
10.0.3.15
```

şeklinde iki farklı adres türü bulunmaktadır.

### MAC Address

MAC adresi, Ethernet ve yerel ağ iletişiminde kullanılan adres türüdür.

### IP Address

IP adresi, ağ katmanında kullanılan mantıksal adres türüdür.

Basit olarak:

```text
MAC → Yerel ağdaki iletişim
IP  → Ağ katmanındaki iletişim
```

Bu nedenle aynı pakette hem MAC hem de IP adreslerini görebilirim.

---

## 4. Ethernet Frame Nedir?

Ethernet Frame, Ethernet ağında taşınan veri birimidir.

İncelediğim Frame 13 toplam:

```text
324 bytes
```

uzunluğundadır.

Basitleştirilmiş olarak Ethernet frame yapısını şöyle düşünebilirim:

```text
┌─────────────────────────────┐
│ Destination MAC             │
├─────────────────────────────┤
│ Source MAC                  │
├─────────────────────────────┤
│ EtherType                   │
├─────────────────────────────┤
│ Payload                     │
│                             │
│ IPv4                        │
│   ↓                         │
│ UDP                         │
│   ↓                         │
│ DHCP                        │
└─────────────────────────────┘
```

Yani Ethernet frame'i, içerisinde daha üst katman protokollerini taşıyan bir yapı olarak düşünebilirim.

---

## 5. EtherType Nedir?

EtherType, Ethernet frame'i içerisinde hangi üst katman protokolünün taşındığını belirten alandır.

İncelediğim Frame 13'te Wireshark:

```text
Type: IPv4 (0x0800)
```

göstermektedir.

Buradaki:

```text
0x0800
```

değeri IPv4'ü belirtmektedir.

Bu nedenle:

```text
EtherType = 0x0800
        ↓
      IPv4
```

şeklinde yorumlayabilirim.

---

## 6. IPv4 ve IPv6 Trafiği Nasıl Ayırt Edilir?

EtherType değerine bakarak IPv4 ve IPv6 trafiğini ayırt edebilirim.

| Trafik | EtherType |
| ------ | --------- |
| IPv4   | `0x0800`  |
| IPv6   | `0x86DD`  |

Benim incelediğim pakette:

```text
Type: IPv4 (0x0800)
```

olduğu için bu paketin IPv4 trafiği taşıdığını belirledim.

Wireshark'ta ayrıca:

```text
Internet Protocol Version 4
```

ifadesini de gördüm.

IPv6 paketlerinde ise Wireshark'ta:

```text
Internet Protocol Version 6
```

ifadesi görülür.

---

## 🔍 Frame 13'ün Katman Yapısı

İncelediğim paketin yapısını şu şekilde özetleyebilirim:

```text
Ethernet II
│
├── Source MAC
│   └── 08:00:27:7d:bb:61
│
├── Destination MAC
│   └── 52:55:0a:00:03:02
│
└── EtherType
    └── 0x0800 → IPv4
            │
            ├── Source IP: 10.0.3.15
            ├── Destination IP: 10.0.3.2
            │
            └── UDP
                 ├── Source Port: 68
                 └── Destination Port: 67
                      │
                      └── DHCP Request
```

Bu paket üzerinde Ethernet'ten başlayarak daha üst katmanlara doğru ilerleyebildiğimi gördüm:

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

## 🧠 Bu Bölümden Çıkardığım Sonuç

Bu çalışmada Ethernet frame'in ağ iletişimindeki rolünü daha iyi anladım.

Özellikle şu ayrımı öğrendim:

```text
MAC Address → Ethernet / yerel ağ iletişimi
IP Address  → Network Layer
EtherType   → Ethernet frame içindeki üst protokolü belirtir
```

Ayrıca Wireshark'ta `Ethernet II` bölümündeki **Type** alanına bakarak paketin IPv4 veya IPv6 taşıyıp taşımadığını anlayabileceğimi öğrendim.

İncelediğim gerçek pakette:

```text
Type: IPv4 (0x0800)
```

olduğu için Frame 13'ün IPv4 trafiği taşıdığını doğruladım.

---

## 📸 Ekran Görüntüsü

Bu bölümde analiz ettiğim Frame 13'ün Wireshark ekran görüntüsünü rapora ekledim.

Ekran görüntüsünde özellikle aşağıdaki bilgilerin görünmesine dikkat ettim:

* `Ethernet II`
* Source MAC
* Destination MAC
* `Type: IPv4 (0x0800)`
* IPv4 bilgileri
* UDP bilgileri
* DHCP Request
