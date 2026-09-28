# 11. DNS Trafiği Analizi

## 1. DNS Nedir?

DNS (Domain Name System), alan adlarının IP adresleriyle eşleştirilmesini sağlayan bir sistemdir.

İnsanlar web sitelerine genellikle alan adlarıyla erişir. Bilgisayarlar ise ağ iletişiminde IP adreslerini kullanır. DNS bu ikisi arasındaki eşleştirmeyi sağlar.

Örneğin:

```text
example.com
     ↓
IP adresi
```

DNS, network security ve SOC analizlerinde önemlidir. Çünkü bilgisayarların hangi alan adlarını sorguladığını inceleyerek ağ üzerindeki DNS trafiği hakkında bilgi edinilebilir.

---

## 2. DNS Query Nedir?

DNS Query, istemcinin DNS sunucusuna gönderdiği sorgudur.

Örneğin bir bilgisayar bir alan adının IP adresini öğrenmek istediğinde DNS sunucusuna bir sorgu gönderir.

Genel yapı:

```text
Client
   │
   │ DNS Query
   ↓
DNS Server
```

---

## 3. DNS Response Nedir?

DNS Response, DNS sunucusunun istemcinin sorgusuna verdiği cevaptır.

Genel yapı:

```text
Client
   ↑
   │ DNS Response
   │
DNS Server
```

Cevap başarılı olabileceği gibi sorgulanan kaydın bulunamadığını da gösterebilir.

---

# 4. Wireshark'ta DNS Trafiğini İnceleme

Wireshark'ta DNS paketlerini filtrelemek için:

```text
dns
```

display filter'ını kullandım.

Filtre sonucunda **Frame 9** numaralı DNS sorgusunu inceledim.

---

# 5. Frame 9 - DNS Query

Frame 9'un temel bilgileri:

```text
Frame: 9
Timestamp: 17.323338578
Protocol: DNS
Length: 101 bytes
```

DNS sorgusu:

```text
Standard query 0x3646
PTR 2.3.0.10.in-addr.arpa
```

### Ağ bilgileri

```text
Source IP:
fd17:625c:f037:3:ac4d:81b9:d683:7bd6

Destination IP:
fd17:625c:f037:3::3
```

UDP bilgileri:

```text
Source Port: 36674
Destination Port: 53
```

Bu pakette DNS iletişiminin UDP üzerinden gerçekleştirildiğini gördüm.

Paket yapısı:

```text
Ethernet
    ↓
IPv6
    ↓
UDP
    ↓
DNS
```

---

# 6. DNS Sorgusunun İçeriği

Frame 9 içerisinde:

```text
Transaction ID: 0x3646
Flags: 0x0100 Standard query
Questions: 1
Answer RRs: 0
Authority RRs: 0
Additional RRs: 0
```

bilgilerini gördüm.

Sorgu tipi:

```text
PTR
```

Sorgulanan alan:

```text
2.3.0.10.in-addr.arpa
```

Bu bir **PTR sorgusudur**.

PTR kayıtları ters DNS çözümlemesinde kullanılır. Yani normalde:

```text
Domain → IP
```

şeklindeki sorgunun tersine:

```text
IP → Domain / Hostname
```

şeklinde bilgi elde edilmeye çalışılır.

Buradaki:

```text
2.3.0.10
```

ifadesi:

```text
10.0.3.2
```

IP adresinin ters çevrilmiş halidir.

---

# 7. Frame 10 - DNS Response

Frame 9'daki sorgunun cevabı **Frame 10** içerisinde geldi.

Frame 10'un temel bilgileri:

```text
Frame: 10
Length: 136 bytes
```

Ağ bilgileri:

```text
Source IP:
fd17:625c:f037:3::3

Destination IP:
fd17:625c:f037:3:ac4d:81b9:d683:7bd6
```

UDP bilgileri:

```text
Source Port: 53
Destination Port: 36674
```

Frame 9'daki:

```text
Transaction ID: 0x3646
```

değeri Frame 10'da da:

```text
Transaction ID: 0x3646
```

olarak bulundu.

Bu nedenle Frame 10'un Frame 9'daki DNS sorgusuna ait cevap olduğunu doğruladım.

---

# 8. DNS Response Sonucu

Frame 10 içerisindeki önemli bilgiler:

```text
Flags: 0x8183
Standard query response, No such name

Questions: 1
Answer RRs: 0
Authority RRs: 1
Additional RRs: 0
```

DNS sunucusu:

```text
No such name
```

cevabını verdi.

Yani sorgulanan:

```text
2.3.0.10.in-addr.arpa
```

için bir cevap kaydı bulunamadı.

Bu nedenle:

```text
Answer RRs: 0
```

oldu ve herhangi bir IP adresi döndürülmedi.

---

# 9. DNS Analiz Özeti

İncelediğim DNS sorgusunun bilgilerini aşağıdaki şekilde çıkardım:

| Bilgi              | Değer                                  |
| ------------------ | -------------------------------------- |
| Sorgulanan Domain  | `2.3.0.10.in-addr.arpa`                |
| Kaynak IP          | `fd17:625c:f037:3:ac4d:81b9:d683:7bd6` |
| Hedef DNS Sunucusu | `fd17:625c:f037:3::3`                  |
| Sorgu Tipi         | `PTR`                                  |
| Cevap              | `No such name`                         |
| IP Adresi          | Yok                                    |
| Timestamp          | `17.323338578`                         |
| Query Frame        | `9`                                    |
| Response Frame     | `10`                                   |
| Transaction ID     | `0x3646`                               |
| Cevap süresi       | `0.014896440 saniye` ≈ `14.9 ms`       |

---

# 10. DNS Kayıt Türleri

## A Kaydı

A kaydı bir domain adını **IPv4 adresiyle** eşleştirir.

```text
Domain → IPv4
```

Örneğin:

```text
example.com → 192.0.2.10
```

---

## AAAA Kaydı

AAAA kaydı bir domain adını **IPv6 adresiyle** eşleştirir.

```text
Domain → IPv6
```

A kaydından temel farkı kullanılan IP adresi türüdür.

---

## CNAME Kaydı

CNAME (Canonical Name), bir domain adını başka bir domain adına yönlendiren DNS kaydıdır.

```text
www.example.com
        ↓
example.com
```

CNAME doğrudan IP adresi yerine başka bir alan adını gösterir.

---

## MX Kaydı

MX (Mail Exchange) kaydı, bir domain için kullanılacak mail sunucularını belirtir.

Örneğin:

```text
example.com
      ↓
Mail Server
```

E-posta sistemleri domain adına ait mail sunucularını bulmak için MX kayıtlarından yararlanabilir.

---

## PTR Kaydı

PTR kaydı ters DNS çözümlemesinde kullanılır.

```text
IP Address
    ↓
Hostname / Domain
```

Benim incelediğim Frame 9'da da PTR sorgusu kullanılmıştır.

---

# 11. DNS Cache Nedir?

DNS Cache, daha önce öğrenilmiş DNS bilgilerin geçici olarak saklanmasıdır.

Bir bilgisayar daha önce bir domain için DNS sorgusu yaptıysa, elde ettiği bilgi belirli bir süre cache içerisinde tutulabilir.

Böylece aynı domain tekrar sorgulandığında DNS sunucusuna yeniden başvurulması gerekmeyebilir.

Genel yapı:

```text
DNS Query
    ↓
DNS Server
    ↓
IP Address
    ↓
DNS Cache
```

DNS cache'in temel amacı gereksiz DNS sorgularını azaltmak ve DNS çözümleme işlemlerini hızlandırmaktır.

---

# 12. Güvenlik Açısından DNS Trafiği

DNS trafiği SOC analizlerinde önemli bir veri kaynağıdır.

Analiz sırasında:

* Sorgulanan domainler
* Kaynak IP adresleri
* DNS sunucuları
* Sorgu türleri
* Sorgu sıklığı
* DNS cevapları
* Başarısız sorgular

incelenebilir.

Özellikle normal olmayan veya beklenmeyen domain sorguları dikkat gerektirebilir.

Ancak tek başına bir DNS sorgusunun görülmesi kötü amaçlı faaliyet olduğu anlamına gelmez. Güvenlik analizinde sorgunun zamanı, kaynağı, hedefi, sıklığı ve diğer ağ trafiğiyle birlikte değerlendirilmesi gerekir.

---

# 13. Kendi Gözlemim

Bu çalışmada Wireshark'ta:

```text
dns
```

filtresini kullanarak DNS trafiğini inceledim.

Frame 9'da:

```text
PTR 2.3.0.10.in-addr.arpa
```

şeklinde bir DNS sorgusu gördüm.

Bu sorgunun cevabının Frame 10'da olduğunu ve iki pakette de aynı:

```text
Transaction ID: 0x3646
```

değerinin bulunduğunu kontrol ettim.

Frame 10'da DNS sunucusunun:

```text
No such name
```

cevabını verdiğini gördüm.

Bu nedenle sorgulanan kayıt için bir IP adresi dönmedi:

```text
Answer RRs: 0
```

Bu çalışma sayesinde DNS analizinde yalnızca domain adına bakmanın yeterli olmadığını; sorgu tipi, kaynak ve hedef IP, cevap durumu, transaction ID ve zaman bilgisinin de birlikte incelenmesi gerektiğini öğrendim.
