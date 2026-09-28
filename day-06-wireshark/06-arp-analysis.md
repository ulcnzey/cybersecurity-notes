# ARP Analysis

## 1. ARP Nedir?

ARP (Address Resolution Protocol), IPv4 ağlarında bir IP adresinin hangi MAC adresine karşılık geldiğini öğrenmek için kullanılan bir protokoldür.

Yerel ağda bir cihaz başka bir cihazla iletişim kurmak istediğinde, hedef cihazın IP adresini biliyor olabilir ancak Ethernet iletişimi için hedef MAC adresine ihtiyaç duyar.

ARP bu IP → MAC eşleşmesini bulmamı sağlar.

Temel mantık:

```text
IP Address
    ↓
ARP
    ↓
MAC Address
```

---

## 2. ARP Request Nedir?

ARP Request, bir cihazın yerel ağdaki belirli bir IP adresinin hangi MAC adresine ait olduğunu sormasıdır.

Örneğin benim laboratuvar ortamımda Wireshark'ta şu ARP Request paketini gördüm:

```text
Who has 10.0.3.2? Tell 10.0.3.15
```

Bu paketin anlamı:

> "10.0.3.2 IP adresine sahip cihaz hangisi? Cevabını 10.0.3.15 adresine gönder."

Burada:

* Gönderen IP: `10.0.3.15`
* Sorulan IP: `10.0.3.2`

ARP Request'in amacı hedef IP adresinin MAC adresini öğrenmektir.

---

## 3. ARP Reply Nedir?

ARP Reply, ARP Request'e verilen cevaptır.

Benim yakaladığım cevap:

```text
10.0.3.2 is at 52:55:0a:00:03:02
```

Bu cevap bize şunu gösteriyor:

* IP: `10.0.3.2`
* MAC: `52:55:0a:00:03:02`

Yani eşleşme:

```text
10.0.3.2
    ↓
52:55:0a:00:03:02
```

---

## 4. IP Adresinden MAC Adresi Nasıl Bulunur?

Bir cihaz yerel ağda başka bir IP adresiyle iletişim kurmak istediğinde öncelikle ARP ile bu IP adresinin MAC adresini öğrenebilir.

Süreç şu şekilde gerçekleşir:

```text
Kali
10.0.3.15
    │
    │ ARP Request
    │ "Who has 10.0.3.2?"
    ↓
Yerel Ağ
    │
    │ ARP Reply
    │ "10.0.3.2 is at 52:55:0a:00:03:02"
    ↓
Kali
```

Daha sonra öğrenilen IP-MAC eşleşmesi işletim sisteminin ARP/komşu önbelleğinde tutulabilir.

---

## 5. ARP Cache Nedir?

ARP Cache, işletim sisteminin daha önce öğrendiği IP ve MAC adresi eşleşmelerini geçici olarak tuttuğu tablodur.

Kali Linux'ta bu bilgileri görmek için:

```bash
ip neigh
```

komutunu kullandım.

Belirli bir IP adresini kontrol etmek için ise:

```bash
ip neigh show 10.0.3.2
```

komutunu kullandım.

Aldığım çıktı:

```text
10.0.3.2 dev eth1 lladdr 52:55:0a:00:03:02 STALE
```

Bu satırın anlamı:

* `10.0.3.2` → IP adresi
* `dev eth1` → İletişim arayüzü
* `lladdr 52:55:0a:00:03:02` → MAC adresi
* `STALE` → Kayıt mevcut ancak yakın zamanda aktif olarak doğrulanmamış

Bu nedenle Wireshark'ta gördüğüm ARP Reply ile işletim sisteminin tuttuğu IP-MAC eşleşmesinin aynı olduğunu doğruladım.

---

## 6. Wireshark'ta ARP Analizi

Wireshark'ta ARP paketlerini görmek için Display Filter olarak:

```text
arp
```

filtresini kullandım.

İlk olarak bir ARP Request gördüm:

```text
Who has 10.0.3.2? Tell 10.0.3.15
```

Ardından buna karşılık gelen ARP Reply paketini gördüm:

```text
10.0.3.2 is at 52:55:0a:00:03:02
```

Bu iki paketi karşılaştırdığımda ARP'nin IP adresinden MAC adresi öğrenme sürecini doğrudan ağ trafiği üzerinde gözlemledim.

---

## 7. ARP Request ve ARP Reply Karşılaştırması

| Özellik          | ARP Request             | ARP Reply                          |
| ---------------- | ----------------------- | ---------------------------------- |
| Amaç             | MAC adresini sormak     | MAC adresini bildirmek             |
| Gönderen IP      | `10.0.3.15`             | `10.0.3.2`                         |
| Hedef/Sorulan IP | `10.0.3.2`              | `10.0.3.2`                         |
| MAC bilgisi      | Öğrenilmeye çalışılıyor | `52:55:0a:00:03:02`                |
| Wireshark örneği | `Who has 10.0.3.2?`     | `10.0.3.2 is at 52:55:0a:00:03:02` |

---

## 8. ARP Spoofing Nedir?

ARP Spoofing, saldırganın ağdaki cihazların ARP tablolarına yanlış IP-MAC eşleşmeleri yerleştirmeye çalıştığı bir saldırı türüdür.

Normal durumda:

```text
10.0.3.2
    ↓
52:55:0a:00:03:02
```

gibi doğru bir eşleşme bulunur.

ARP Spoofing durumunda saldırgan, cihazların yanlış bir eşleşmeye inanmasını sağlamaya çalışabilir.

Örneğin:

```text
10.0.3.2
    ↓
SALDIRGANIN MAC ADRESİ
```

gibi sahte bir eşleşme oluşabilir.

Bu durum başarılı olursa saldırgan, trafiğin kendi üzerinden geçmesini sağlayarak Man-in-the-Middle (MITM) saldırıları gerçekleştirmeye çalışabilir.

---

## 9. ARP Spoofing Belirtileri

ARP Spoofing şüphesinde aşağıdaki durumlar incelenebilir:

* Aynı IP adresinin farklı MAC adresleriyle eşleşmesi
* Beklenmeyen ARP Reply paketleri
* Normalden fazla ARP trafiği
* Aynı MAC adresinin birden fazla IP ile ilişkilendirilmesi
* ARP Cache içerisinde beklenmeyen değişiklikler
* Ağdaki cihazların IP-MAC eşleşmelerinin sürekli değişmesi

Ancak bu belirtilerin tek başına saldırı kanıtı olmadığını, ağın normal davranışının da dikkate alınması gerektiğini öğrendim.

---

## 10. Kullandığım Komutlar ve Filtreler

ARP paketlerini Wireshark'ta filtrelemek için:

```text
arp
```

ARP/komşu önbelleğini görmek için:

```bash
ip neigh
```

Belirli bir IP adresinin kaydını görmek için:

```bash
ip neigh show 10.0.3.2
```

---

## 11. Laboratuvar Sonucu

Bu çalışmada Wireshark kullanarak gerçek ARP Request ve ARP Reply paketlerini inceledim.

Gözlemlediğim işlem:

```text
10.0.3.15
    │
    │ ARP Request
    │ "Who has 10.0.3.2?"
    ↓
10.0.3.2
    │
    │ ARP Reply
    │ "10.0.3.2 is at
    │ 52:55:0a:00:03:02"
    ↓
IP → MAC eşleşmesi
```

Daha sonra Kali Linux üzerinde:

```bash
ip neigh show 10.0.3.2
```

komutunu çalıştırarak aynı IP-MAC eşleşmesini işletim sisteminin komşu önbelleğinde de gördüm:

```text
10.0.3.2 dev eth1 lladdr 52:55:0a:00:03:02 STALE
```

Bu çalışma sayesinde ARP'nin yalnızca teorik olarak ne yaptığını değil, ağ üzerinde gerçekleşen Request ve Reply paketleri üzerinden nasıl çalıştığını gözlemledim.
