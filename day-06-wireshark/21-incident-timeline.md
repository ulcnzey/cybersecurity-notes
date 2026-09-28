# 21. Olay Zaman Çizelgesi

## 1. Çalışmanın Amacı

Bu bölümde, analiz ettiğim PCAP dosyasındaki önemli ağ olaylarını **Wireshark üzerinden timestamp bilgilerine göre kronolojik olarak incelemeye** çalıştım.

Önceki bölümlerde DNS, TCP, HTTP ve IOC bilgilerini ayrı ayrı incelemiştim. Bu bölümde ise bu olayları zaman sırasına koyarak ağ trafiğinde nasıl bir akış olduğunu anlamaya çalıştım.

Buradaki temel sorum:

> Bir ağ olayı gerçekleştiğinde, bundan önce ve sonra hangi paketler gerçekleşiyor?

oldu.

---

# 2. Wireshark Üzerinden Zaman Çizelgesi Oluşturma

Öncelikle Wireshark'taki **Time** sütununu kullandım.

Paketleri zaman sırasına göre inceleyerek önemli paketlerin:

* Timestamp
* Source
* Destination
* Protocol
* Info

alanlarını karşılaştırdım.

Özellikle DNS paketlerini incelemek için:

```text
dns
```

filtresini kullandım.

Bu şekilde yalnızca DNS trafiğini göstererek sorgu ve cevap paketlerini daha rahat takip ettim.

---

# 3. Gözlemlediğim DNS Olayları

Wireshark'ta yaptığım inceleme sırasında `10.0.3.15` adresinin DNS sunucusu olarak görünen `10.0.3.3` adresine DNS sorguları gönderdiğini gözlemledim.

Örneğin aşağıdaki pakette:

```text
Time: 343.908320941
Source: 10.0.3.15
Destination: 10.0.3.3
Protocol: DNS
Info: Standard query 0x41c5 A example.com
```

şeklinde bir DNS sorgusu gördüm.

Bu paketten çıkardığım bilgiler:

| Alan              | Değer           |
| ----------------- | --------------- |
| Timestamp         | `343.908320941` |
| Kaynak IP         | `10.0.3.15`     |
| Hedef IP          | `10.0.3.3`      |
| Protokol          | DNS             |
| Sorgu tipi        | A               |
| Sorgulanan domain | `example.com`   |

Burada `10.0.3.15` adresindeki sistemin `example.com` domaininin IPv4 adresini öğrenmek için DNS sorgusu yaptığını gördüm.

---

# 4. DNS Response İncelemesi

Daha sonra Wireshark üzerinde DNS response paketlerini inceledim.

Örneğin aşağıdaki pakette:

```text
Time: 1086.812787990
Source: 10.0.3.3
Destination: 10.0.3.15
Protocol: DNS
Info: Standard query response 0x6738 A firefox.settings.services.mozilla.com CNAME mozilla.map.fastly.net A 199.232.17.91
```

bilgilerini gördüm.

Bu pakette:

| Alan      | Değer                                   |
| --------- | --------------------------------------- |
| Timestamp | `1086.812787990`                        |
| Kaynak IP | `10.0.3.3`                              |
| Hedef IP  | `10.0.3.15`                             |
| Protokol  | DNS                                     |
| Domain    | `firefox.settings.services.mozilla.com` |
| CNAME     | `mozilla.map.fastly.net`                |
| A Record  | `199.232.17.91`                         |

bilgilerini gözlemledim.

Burada DNS sunucusunun `firefox.settings.services.mozilla.com` için bir CNAME kaydı ve ardından `199.232.17.91` IP adresini döndürdüğünü gördüm.

Bu paket üzerinde yaptığım inceleme, DNS response içerisinde yalnızca tek bir IP adresi bulunmayabileceğini ve CNAME yönlendirmelerinin de görülebileceğini anlamama yardımcı oldu.

---

# 5. Olayları Kronolojik Olarak Değerlendirme

Wireshark'taki timestamp değerlerini kullanarak önemli olayları zaman sırasına göre değerlendirmeye başladım.

Şu ana kadar doğrudan gözlemlediğim olaylardan oluşan tablo:

| Zaman            | Kaynak      | Hedef       | Protokol | Olay                                                      |
| ---------------- | ----------- | ----------- | -------- | --------------------------------------------------------- |
| `343.908320941`  | `10.0.3.15` | `10.0.3.3`  | DNS      | `example.com` için A kaydı sorgusu                        |
| `1086.812787990` | `10.0.3.3`  | `10.0.3.15` | DNS      | `firefox.settings.services.mozilla.com` için DNS response |

Bu tabloyu oluştururken özellikle **Wireshark'taki gerçek timestamp değerlerini** kullandım.

---

# 6. DNS Olay Akışını Anlama

İncelediğim DNS paketlerinde temel iletişim yapısının:

```text
10.0.3.15
    │
    │ DNS Query
    ▼
10.0.3.3
    │
    │ DNS Response
    ▼
10.0.3.15
```

şeklinde olduğunu gördüm.

Burada:

* `10.0.3.15` → DNS sorgusunu yapan sistem
* `10.0.3.3` → DNS sunucusu olarak kullanılan sistem

şeklinde bir iletişim olduğunu gözlemledim.

Bu yapı sayesinde Wireshark'ta bir DNS olayını incelerken yalnızca domain adına bakmanın yeterli olmadığını, **kaynak, hedef, zaman ve sorgu/cevap ilişkisinin birlikte değerlendirilmesi gerektiğini** gördüm.

---

# 7. Olay Zaman Çizelgesinde Dikkat Ettiğim Noktalar

Zaman çizelgesini oluştururken her paketi tabloya eklemek yerine, analiz açısından anlamlı olan paketleri seçmeye çalıştım.

Özellikle şu bilgileri takip ettim:

```text
Timestamp
    ↓
Source IP
    ↓
Destination IP
    ↓
Protocol
    ↓
Packet Info
    ↓
Önceki / Sonraki paketlerle ilişki
```

Örneğin bir DNS sorgusu gördüğümde hemen ardından gelen veya aynı iletişimle ilişkili DNS response paketini incelemeye çalıştım.

Bu şekilde tek bir paketi izole olarak değerlendirmek yerine paketler arasındaki ilişkiyi anlamaya çalıştım.

---

# 8. Şüpheli Olaylarda Zaman Çizelgesi Nasıl Kullanılır?

Wireshark üzerinde şüpheli bir davranış tespit edildiğinde aynı yöntemi daha geniş bir olay için kullanabilirim.

Örneğin bir port taraması şüphesi varsa zaman çizelgesi şu şekilde oluşturulabilir:

```text
10:21:03
10.0.3.15 → 10.0.3.20
TCP SYN → Port 21

10:21:04
10.0.3.15 → 10.0.3.20
TCP SYN → Port 22

10:21:05
10.0.3.15 → 10.0.3.20
TCP SYN → Port 23

10:21:06
10.0.3.15 → 10.0.3.20
TCP SYN → Port 80
```

Burada önemli olan tek bir SYN paketi değildir.

**Aynı kaynak IP'nin kısa bir zaman aralığında farklı portlara bağlantı denemesi yapmasıdır.**

Bu nedenle Wireshark'ta zaman çizelgesi oluşturmak, olayın tek bir paketten mi yoksa birden fazla paketten oluşan bir davranıştan mı meydana geldiğini anlamama yardımcı olur.

---

# 9. Olayın Öncesi ve Sonrasını İnceleme

Şüpheli bir paket gördüğümde yalnızca o pakete bakmamaya çalıştım.

Örneğin bir DNS sorgusu için:

```text
Öncesi
  ↓
Kaynak sistemin önceki trafiği
  ↓
DNS Query
  ↓
DNS Response
  ↓
Sonraki TCP bağlantısı
  ↓
HTTP / TLS iletişimi
```

şeklinde bir akış olup olmadığını kontrol etmek gerekir.

Bu yaklaşım sayesinde DNS, TCP ve HTTP gibi farklı protokollerin aynı olay zinciri içerisinde birbirleriyle ilişkili olup olmadığını anlayabilirim.

---

# 10. Bu Çalışmadan Çıkardığım Sonuç

Bu bölümde Wireshark'taki gerçek paketlerin timestamp bilgilerini kullanarak olayları kronolojik olarak incelemeyi öğrendim.

Özellikle:

* Timestamp bilgisinin olay sırasını anlamadaki önemini
* Source ve Destination IP'lerin birlikte değerlendirilmesini
* DNS Query ve DNS Response arasındaki ilişkiyi
* CNAME ve A kayıtlarının DNS response içerisinde nasıl görülebildiğini
* Bir olayın yalnızca tek bir paket üzerinden değerlendirilmemesi gerektiğini

gözlemledim.

Benim için en önemli nokta, bir PCAP analizinde sadece:

> "Bu paket ne?"

sorusunu sormak yerine,

> "Bu paket ne zaman gerçekleşti, bundan önce ne oldu ve bundan sonra ne oldu?"

sorusunu da sormam gerektiğini anlamak oldu.

Bu nedenle olay zaman çizelgesini, Wireshark üzerinde yapılan paket analizini daha anlamlı bir **olay akışına dönüştüren** bir yöntem olarak değerlendirdim.
