# ICMP and Ping Analysis

## 1. ICMP Nedir?

ICMP (Internet Control Message Protocol), IP ağlarında hata, durum ve erişilebilirlik bilgilerini iletmek için kullanılan bir protokoldür.

ICMP, TCP veya UDP gibi uygulama verilerini taşımak için kullanılmaz. Daha çok ağ bağlantısının kontrol edilmesi ve ağ sorunlarının tespit edilmesi amacıyla kullanılır.

ICMP'nin kullanım alanlarından bazıları:

* Ağ bağlantısının kontrol edilmesi
* Hedef cihazın erişilebilirliğinin test edilmesi
* Ağ hataları hakkında bilgi verilmesi
* Ağ sorunlarının teşhis edilmesi
* Ağ trafiğinin ve bağlantı durumunun analiz edilmesi

---

## 2. Ping Nedir?

`ping`, bir hedef cihazın ağ üzerinden erişilebilir olup olmadığını kontrol etmek için kullanılan bir araçtır.

Ping işlemi ICMP Echo Request ve ICMP Echo Reply mesajlarını kullanır.

Temel çalışma mantığı:

```text
Kali
10.0.3.15
    │
    │ ICMP Echo Request
    ↓
10.0.3.2
    │
    │ ICMP Echo Reply
    ↓
Kali
10.0.3.15
```

Ben laboratuvar ortamımda aşağıdaki komut ile ping testi gerçekleştirdim:

```bash
ping -c 4 10.0.3.2
```

Daha sonra Wireshark üzerinde bu iletişimi analiz ettim.

---

## 3. Wireshark'ta ICMP Filtreleme

ICMP paketlerini görmek için Wireshark'ın Display Filter alanında:

```text
icmp
```

filtresini kullandım.

Bu filtre sayesinde yakalanan trafik içerisinden yalnızca ICMP paketlerini görüntüledim.

---

## 4. ICMP Echo Request

İlk olarak Frame 5'i inceledim.

Frame 5'in temel bilgileri:

* Frame size: `98 bytes`
* Source MAC: `08:00:27:7d:bb:61`
* Destination MAC: `52:55:0a:00:03:02`
* Source IP: `10.0.3.15`
* Destination IP: `10.0.3.2`
* Protocol: `ICMP (1)`
* ICMP Type: `8`
* Code: `0`
* Sequence Number: `1`

Wireshark'ta ICMP bölümü şu şekilde görünüyordu:

```text
Type: 8 (Echo (ping) request)
Code: 0
```

Type 8, ICMP Echo Request anlamına gelir.

Bu paket ile `10.0.3.15` adresindeki Kali cihazı `10.0.3.2` adresindeki hedefe cevap vermesi için bir ping isteği gönderdi.

Kısaca:

```text
10.0.3.15
    │
    │ Echo Request
    │ Type 8
    ↓
10.0.3.2
```

---

## 5. ICMP Echo Reply

Daha sonra Frame 6'yı inceledim.

Frame 6'nın temel bilgileri:

* Frame size: `98 bytes`
* Source MAC: `52:55:0a:00:03:02`
* Destination MAC: `08:00:27:7d:bb:61`
* Source IP: `10.0.3.2`
* Destination IP: `10.0.3.15`
* Protocol: `ICMP (1)`
* ICMP Type: `0`
* Code: `0`
* Sequence Number: `1`

Wireshark'ta:

```text
Type: 0 (Echo (ping) reply)
Code: 0
```

şeklinde görünüyordu.

Type 0, ICMP Echo Reply anlamına gelir.

Bu paket, `10.0.3.2` adresindeki cihazın ping isteğine cevap verdiğini gösterir.

```text
10.0.3.2
    │
    │ Echo Reply
    │ Type 0
    ↓
10.0.3.15
```

---

## 6. Echo Request ve Echo Reply Karşılaştırması

| Özellik         | Echo Request | Echo Reply  |
| --------------- | ------------ | ----------- |
| Frame           | 5            | 6           |
| Source IP       | `10.0.3.15`  | `10.0.3.2`  |
| Destination IP  | `10.0.3.2`   | `10.0.3.15` |
| ICMP Type       | `8`          | `0`         |
| Code            | `0`          | `0`         |
| Sequence Number | `1`          | `1`         |
| Frame Size      | `98 bytes`   | `98 bytes`  |

Request ve Reply paketlerini karşılaştırdığımda kaynak ve hedef adreslerinin tersine döndüğünü gördüm.

Ayrıca her iki paketin Sequence Number değerinin `1` olduğunu gördüm. Bu değer, Request ve Reply paketlerinin birbirleriyle ilişkilendirilmesine yardımcı olur.

Wireshark, Frame 6 içerisinde:

```text
[Request frame: 5]
```

bilgisini göstererek bu paketin Frame 5'e cevap olduğunu da belirtiyordu.

---

## 7. Ping Neden Ağ Sorunlarını Tespit Etmek İçin Kullanılır?

Ping, bir cihazdan başka bir cihaza ulaşılabilirlik testi yapmamı sağlar.

Temel olarak şu soruları kontrol edebilirim:

```text
Hedefe ulaşabiliyor muyum?
        ↓
Hedef cevap veriyor mu?
        ↓
Cevap ne kadar sürede geliyor?
```

Benim analizimde:

```text
Echo Request
     ↓
Echo Reply
     ↓
Response time: 0.822 ms
```

değerini gördüm.

Bu, test sırasında hedef cihazdan cevap alındığını ve Wireshark'ın Request ile Reply arasındaki cevap süresini yaklaşık `0.822 ms` olarak hesapladığını gösterdi.

Ping cevabının alınamaması ise her zaman hedef cihazın kapalı olduğu anlamına gelmez. ICMP'nin güvenlik duvarı tarafından engellenmesi veya ağdaki başka bir bağlantı probleminin bulunması gibi farklı nedenler olabilir.

---

## 8. ICMP'nin Güvenlik Açısından Önemi

ICMP yalnızca bağlantı testi için kullanılmaz. Güvenlik analizlerinde de incelenebilen bir protokoldür.

ICMP trafiği incelenirken şu durumlara dikkat edilebilir:

* Beklenmeyen ICMP trafiği
* Normalden fazla ICMP paketi
* Çok sayıda Echo Request
* Ağdaki cihazlara yönelik yoğun ping trafiği
* Olağandışı ICMP iletişimi
* ICMP üzerinden şüpheli veri taşınması

Örneğin çok sayıda farklı IP adresine gönderilen Echo Request paketleri ağ keşfi veya tarama faaliyeti açısından incelenebilir.

Ancak tek başına ICMP veya ping trafiğinin görülmesi bir saldırı olduğu anlamına gelmez. Trafiğin miktarı, hedefleri, zamanlaması ve ağın normal davranışı birlikte değerlendirilmelidir.

---

## 9. Laboratuvar Analizi

Bu çalışmada kendi laboratuvar ortamımda `10.0.3.15` adresindeki Kali Linux cihazımdan `10.0.3.2` adresine ping gönderdim.

Wireshark'ta:

```text
icmp
```

filtresini kullanarak ICMP paketlerini görüntüledim.

Ardından:

```text
Frame 5 → Echo Request
Frame 6 → Echo Reply
```

paketlerini analiz ettim.

Elde ettiğim iletişim:

```text
10.0.3.15
    │
    │ ICMP Echo Request
    │ Type 8
    ↓
10.0.3.2
    │
    │ ICMP Echo Reply
    │ Type 0
    ↓
10.0.3.15
```

Wireshark ayrıca:

```text
Response time: 0.822 ms
```

bilgisini gösterdi.

Bu çalışma sayesinde ICMP'nin teorik yapısını ve ping işleminin ağ üzerinde oluşturduğu gerçek paket trafiğini birlikte incelemiş oldum.
