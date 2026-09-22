# 07 - Ping ve ICMP


---

# 🎯 1. Bu Çalışmada Ne Öğreniyorum?

Bu bölümde bir bilgisayarın ağ üzerinden başka bir cihaza ulaşıp ulaşamadığını **ping** kullanarak kontrol etmeyi öğrendim.

Önce kendi bilgisayarımı:

```bash
ping 127.0.0.1
```

ile test ettim.

Daha sonra kendi ağ geçidimi:

```bash
ping GATEWAY_IP
```

ile test ettim.

Bu iki işlem sayesinde hem **loopback** kavramını hem de **default gateway** kavramını uygulamalı olarak öğrendim.

---

# 🌐 2. Ping Nedir?

En basit haliyle:

> **Ping, bir hedefe ağ üzerinden ulaşabiliyor muyum diye kontrol etmeye yarayan bir araçtır.**

Birine telefon açtığımı düşünürsem:

```text
Ben → "Beni duyuyor musun?"
Karşı taraf → "Evet, duyuyorum."
```

Ping de benzer şekilde çalışır:

```text
Benim bilgisayarım
       │
       │ "Orada mısın?"
       ↓
     Hedef
       │
       │ "Evet, buradayım."
       ↓
Benim bilgisayarım
```

Ping işlemi sırasında **ICMP** kullanılır.

---

# 📡 3. ICMP Nedir?

ICMP:

**Internet Control Message Protocol**

anlamına gelir.

ICMP, IP ağlarında kontrol ve hata mesajlarının iletilmesi için kullanılan bir protokoldür.

Ping sırasında temel olarak iki mesaj türünü görürüz:

### ICMP Echo Request

Bilgisayarın hedefe gönderdiği:

> "Beni duyuyor musun?"

mesajıdır.

### ICMP Echo Reply

Hedefin gönderdiği:

> "Evet, seni duyuyorum."

cevabıdır.

Bunu şöyle aklımda tutuyorum:

```text
Echo Request
"Orada mısın?"
       ↓
      Hedef
       ↓
Echo Reply
"Buradayım."
```

---

# 🏠 4. 127.0.0.1 Nedir?

İlk ping testimde:

```bash
ping 127.0.0.1
```

komutunu kullandım.

Peki `127.0.0.1` nedir?

**Kendi bilgisayarımı ifade eder.**

Yani başka bir bilgisayarı veya interneti test etmiyorum.

Kendi bilgisayarıma:

> "Sen kendine ulaşabiliyor musun?"

diye soruyorum.

---

# 🔄 5. Loopback Nedir?

`127.0.0.1`, **loopback adreslerinden biridir.**

Loopback kelimesini:

> **"Kendine geri dönmek"**

şeklinde düşünebilirim.

Linux'ta loopback ağ arayüzünün adı genellikle:

```text
lo
```

şeklindedir.

Basit mantık:

```text
┌──────────────────────┐
│     Kali Linux       │
│                      │
│   lo (Loopback)      │
│        ↓             │
│    127.0.0.1         │
│        ↓             │
│     Kendisi          │
└──────────────────────┘
```

### 🧠 Akılda tut:

> **127.0.0.1 = Benim bilgisayarım**

> **lo = Loopback arayüzüm**

---

# 🧪 6. İlk Uygulama: Kendi Bilgisayarımı Test Etmek

Terminalde:

```bash
ping 127.0.0.1
```

komutunu çalıştırdım.


<img width="518" height="249" alt="image" src="https://github.com/user-attachments/assets/95d01dd1-ab70-4840-a854-e22991e2f258" />


### 🔎 Çıktıda Ne Arıyorum?

Çıktıda buna benzer satırlar gördüm:

```text
64 bytes from 127.0.0.1: ...
```

Buradaki önemli nokta:

```text
from 127.0.0.1
```

ifadesidir.

Bu, `127.0.0.1` adresinden cevap geldiğini gösterir.

Yani bilgisayarım kendi loopback adresine gönderdiği isteğe cevap verebiliyor.

---

# ⏱️ 7. Ping Çıktısındaki `time` Nedir?

Ping çıktısında buna benzer bir değer görebilirim:

```text
time=0.030 ms
```

Buradaki:

```text
time
```

isteğin gönderilip cevabın alınmasına kadar geçen süreyi gösterir.

`ms`:

**milisaniye**

demektir.

Örneğin:

```text
time=0.030 ms
```

çok kısa bir cevap süresidir.

127.0.0.1 kendi bilgisayarım olduğu için bu sürenin çok düşük olması normaldir.

---

# 📦 8. Paket Kaybı Nedir?

Ping'i durdurduğumda Linux genellikle bir özet gösterir.

Örneğin:

```text
3 packets transmitted
3 packets received
0% packet loss
```

Bu durumda:

```text
Gönderilen paket → 3
Alınan cevap     → 3
Kayıp            → %0
```

anlamına gelir.

### 🧠 Basitçe:

> **Packet loss = Gönderdiğim paketlerden ne kadarı cevap olarak geri dönmedi?**

---

# 🛑 9. Ping Nasıl Durdurulur?

Linux'ta ping komutu normalde sürekli çalışabilir.

Durdurmak için:

```text
Ctrl + C
```

kullandım.

Bu işlemden sonra ping istatistiklerini görebilirim.

---

# 🚪 10. Default Gateway Nedir?

Şimdi kendi bilgisayarımızdan çıkıp **yerel ağımızdaki başka bir cihaza** bakıyoruz.

Burada karşımıza:

> **Default Gateway**

kavramı çıkıyor.

Gateway'i bir **kapı** gibi düşünebiliriz.

```text
       🌍 İnternet
           ↑
           │
     ┌───────────┐
     │  Gateway  │
     └───────────┘
           ↑
           │
     ┌───────────┐
     │   Kali    │
     └───────────┘
```

Kali Linux'un başka ağlara ulaşması gerektiğinde paketler çoğunlukla default gateway üzerinden gönderilir.

---

# 🔎 11. Gateway Adresini Nasıl Öğrenirim?

Gateway bilgisini görmek için:

```bash
ip route
```

komutunu kullandım.

Çıktıda:

```text
default via X.X.X.X
```

şeklinde bir satır bulunur.

Buradaki:

```text
X.X.X.X
```

benim **default gateway** adresimdir.

> ⚠️ Gateway adresi her bilgisayarda aynı olmak zorunda değildir. Bu nedenle gerçek IP adresimi burada elle yazmak yerine terminal ekran görüntüsünde gösteriyorum.

---

# 📡 12. Gateway'e Ping Atmak

Gateway adresimi öğrendikten sonra:

```bash
ping GATEWAY_IP
```

komutuyla gateway'i test ettim.


<img width="562" height="417" alt="image" src="https://github.com/user-attachments/assets/cd85f621-ce13-4f97-b6ff-f191bf31b2b3" />


Bu testte artık kendime ping atmıyorum.

Şu iletişimi test ediyorum:

```text
Kali Linux
    │
    │ ICMP
    ↓
Gateway
```

Yani:

> **Kali Linux ile yerel ağ geçidi arasında iletişim kurulabiliyor mu?**

sorusunu kontrol ediyorum.

---

# 🔬 13. İki Test Arasındaki Fark

Buradaki fark benim için önemli:

| Komut             | Hedef              | Ne Test Ediliyor?   |
| ----------------- | ------------------ | ------------------- |
| `ping 127.0.0.1`  | Kendi bilgisayarım | Loopback            |
| `ping GATEWAY_IP` | Ağ geçidi          | Yerel ağ bağlantısı |

Bunu şöyle aklımda tutabilirim:

```text
127.0.0.1
    ↓
"Kendimi test ediyorum."

Gateway
    ↓
"Ağdaki kapıya ulaşabiliyor muyum?"
```

---

# ❗ 14. Ping Başarısızsa Cihaz Kesinlikle Kapalı mıdır?

**Hayır.**

Bu siber güvenlik açısından önemli bir noktadır.

Örneğin bir bilgisayara ping attım ve cevap alamadım.

Hemen:

> "Bilgisayar kapalı."

demem doğru olmaz.

Çünkü başka nedenler olabilir:

* Firewall ICMP'yi engelliyor olabilir.
* Hedef cihaz ping cevaplarını kapatmış olabilir.
* Ağ bağlantısında problem olabilir.
* Routing problemi olabilir.
* Hedef cihaz gerçekten kapalı olabilir.

Bu yüzden:

> ❌ **Ping yok = cihaz kesin kapalı**

demek yanlıştır.

Daha doğru yaklaşım:

> ✅ **Ping cevabı alınamadı. Bunun nedeni ayrıca araştırılmalıdır.**

---

# 🛡️ 15. Siber Güvenlik Açısından Neden Önemli?

Siber güvenlikte ağ üzerinde çalışan bir sistem hakkında bilgi toplarken ilk olarak ağın temel yapısını anlamamız gerekir.

Örneğin:

```text
Hedef
  ↓
Ulaşılabilir mi?
  ↓
IP adresi ne?
  ↓
Ağ bağlantısı nasıl?
  ↓
Hangi servisler çalışıyor?
  ↓
Hangi portlar açık?
```

Ping bu zincirin yalnızca **temel bir parçasıdır**.

Bir sistemin ping'e cevap vermesi veya vermemesi, sistem hakkında tek başına yeterli bilgi vermez.

Bu yüzden ilerleyen bölümlerde **Nmap** kullanarak açık portlar ve çalışan servisler hakkında daha fazla bilgi edinmeyi öğreneceğim.

---

# 🧠 16. Bu Bölümden Akılda Kalması Gerekenler

### `ping`

> Bir hedefe ağ üzerinden ulaşılabilirliği kontrol eder.

### `ICMP`

> Ping'in kullandığı temel protokoldür.

### `127.0.0.1`

> Kendi bilgisayarımı ifade eder.

### `lo`

> Loopback ağ arayüzüdür.

### `ip route`

> Routing ve default gateway bilgilerini gösterir.

### `Default Gateway`

> Kendi ağımızın dışındaki ağlara ulaşırken kullanılan ağ geçididir.

### `Packet Loss`

> Gönderilen paketlerden cevap alınamayanların oranıdır.

---

# 🧩 17. Öğrendiklerimi Birleştiriyorum

Şimdiye kadar öğrendiğim ağ mantığını şöyle düşünebilirim:

```text
             🌍 İNTERNET
                  ↑
                  │
           ┌─────────────┐
           │   GATEWAY   │
           └─────────────┘
                  ↑
                  │
           Yerel Ağ
                  ↑
                  │
           ┌─────────────┐
           │ KALI LINUX  │
           └─────────────┘
                  │
                  ↓
             127.0.0.1
             "Kendim"
```

Böylece:

```text
ping 127.0.0.1
```

→ **Kendi bilgisayarımı test ederim.**

```text
ping GATEWAY_IP
```

→ **Kali ile ağ geçidi arasındaki iletişimi test ederim.**

---

# 📝 18. Kişisel Notum

Bu çalışmadan önce `ping` komutunu yalnızca "internet bağlantısını kontrol eden bir komut" olarak düşünüyordum.

Bu çalışmadan sonra ping'in aslında daha temel bir ağ kontrolü olduğunu öğrendim.

Özellikle:

**127.0.0.1 → kendi bilgisayarım**

ve

**Gateway → ağımın çıkış kapısı**

mantığını öğrendim.

Ayrıca ping'e cevap alınamamasının her zaman hedef sistemin kapalı olduğu anlamına gelmediğini öğrendim.

Bu bilgiler, ileride Nmap gibi araçlarla ağ keşfi yaparken temel oluşturmaktadır.

---

# ✅ Bölüm Kontrol Listesi

* [x] Ping kavramını öğrendim.
* [x] ICMP'nin ne olduğunu öğrendim.
* [x] `127.0.0.1` adresini öğrendim.
* [x] Loopback kavramını öğrendim.
* [x] `lo` arayüzünü öğrendim.
* [x] `ping 127.0.0.1` komutunu çalıştırdım.
* [x] Default Gateway kavramını öğrendim.
* [x] `ip route` ile gateway bilgisini kontrol ettim.
* [x] Gateway'e ping attım.
* [x] Ping başarısızlığının tek başına cihazın kapalı olduğunu göstermediğini öğrendim.

---

## 🎯 Sıradaki Konu

Bir sonraki bölümde:

**Nmap — Network Mapper**

konusuna geçeceğim.

Burada artık:

```text
IP adresi
   ↓
Portlar
   ↓
Servisler
   ↓
Nmap
```

mantığını öğrenerek kendi Kali Linux sistemimde ilk Nmap taramamı gerçekleştireceğim.

> **Not:** Nmap çalışmaları yalnızca kendi bilgisayarım, kendi sanal makinelerim veya izin verilen laboratuvar sistemleri üzerinde gerçekleştirilecektir.
