# 06 — Linux Ağ Yapılandırması

## 🎯 Çalışmanın Amacı

Bu çalışmada Kali Linux sisteminin temel ağ yapılandırması incelenmiştir.

Çalışma kapsamında aşağıdaki komutlar kullanılmıştır:

```bash id="a8j6m3"
ip addr
ip route
ip neigh
```

Bu komutlarla ağ arayüzleri, IP adresleri, yönlendirme bilgileri ve yerel ağdaki komşu cihazlara ait bilgiler incelenmiştir.

> **Not:** Tüm uygulamalar kendi Kali Linux sanal makinem üzerinde gerçekleştirilmiştir.

---

# 1. Ağ Yapılandırması Nedir?

Bir bilgisayarın ağ üzerinden iletişim kurabilmesi için çeşitli ağ yapılandırma bilgilerinin bulunması gerekir.

Bunlar arasında:

* IP adresi
* Ağ arayüzü
* Ağ maskesi
* Varsayılan ağ geçidi (default gateway)
* MAC adresi
* Yönlendirme bilgileri

gibi bilgiler bulunur.

Siber güvenlik açısından bu bilgilerin anlaşılması; ağ analizi, trafik inceleme, sistem keşfi ve güvenlik testleri için temel oluşturmaktadır.

---

# 2. `ip addr` — Ağ Arayüzlerini ve IP Adreslerini Görüntüleme

Sistemde bulunan ağ arayüzlerini ve bunlara ait IP bilgilerini görüntülemek için:

```bash id="9o6o4n"
ip addr
```

komutu kullanılmıştır.

Bu komut sonucunda sistemdeki ağ arayüzleri ve bunlara ait çeşitli bilgiler görüntülenmiştir.

### Önemli bilgiler

#### `lo`

`lo`, loopback ağ arayüzüdür.

Genellikle:

```text id="8g9xwd"
127.0.0.1
```

IP adresi ile ilişkilidir.

Loopback arayüzü bilgisayarın kendi üzerinde iletişim kurmasını sağlar.

#### `eth0`, `enp0s3` veya benzeri arayüzler

Sistemin ağ bağlantısını sağlayan fiziksel veya sanal ağ arayüzlerinden biri olabilir.

Kullanılan sanal makine ve ağ yapılandırmasına göre arayüz adı değişebilir.

Bu arayüz altında `inet` ile başlayan satırda IPv4 adresi görülebilir.

### 📸 `ip addr` çıktısı


<img width="635" height="366" alt="image" src="https://github.com/user-attachments/assets/45282c27-cbc1-4572-852e-e77f9dc89bc5" />


---

# 3. IP Adresi Nedir?

IP adresi, bir cihazın ağ üzerindeki iletişim adresidir.

IPv4 adresleri örneğin:

```text id="z8t8zq"
192.168.x.x
```

gibi bir yapıya sahip olabilir.

Yerel ağlarda kullanılan adresler ile internette kullanılan genel IP adresleri birbirinden farklı olabilir.

Bu çalışmada kullanılan Kali Linux sanal makinesinin ağ üzerindeki IP adresi `ip addr` komutu ile incelenmiştir.

> **Güvenlik notu:** Gerçek IP ve MAC adresleri herkese açık bir GitHub deposunda paylaşılmadan önce değerlendirilmelidir. Gerekli durumlarda ekran görüntülerindeki bilgiler maskelenebilir.

---

# 4. `ip route` — Yönlendirme Bilgilerini Görüntüleme

Sistemin ağ trafiğini hangi yollar üzerinden yönlendirdiğini görmek için:

```bash id="f5y4uo"
ip route
```

komutu kullanılmıştır.

Örneğin bir sistemde aşağıdakine benzer bir kayıt bulunabilir:

```text id="vfdj08"
default via 192.168.x.x dev eth0
```

Buradaki:

```text id="azb4es"
default via
```

ifadesi varsayılan ağ geçidini belirtir.

---

# 5. Default Gateway Nedir?

**Default gateway**, bilgisayarın kendi yerel ağı dışındaki ağlara ulaşmak için kullandığı çıkış noktasıdır.

Basitleştirilmiş şekilde:

```text id="f6klh5"
Kali Linux
    |
    | Yerel ağ
    |
Default Gateway
    |
    | Diğer ağlar
    |
İnternet
```

Örneğin bilgisayar internetteki bir sunucuya ulaşmak istediğinde, hedef aynı yerel ağda değilse trafik genellikle varsayılan gateway üzerinden yönlendirilir.

### 📸 `ip route` çıktısı


<img width="563" height="84" alt="image" src="https://github.com/user-attachments/assets/ebf76011-200d-47ce-b219-fe1b8f4ee704" />


---

# 6. `ip addr` ve `ip route` Arasındaki Fark

Bu iki komut birbirinden farklı bilgiler sağlar.

| Komut      | Gösterdiği temel bilgi                                |
| ---------- | ----------------------------------------------------- |
| `ip addr`  | Ağ arayüzleri ve IP adresleri                         |
| `ip route` | Ağ trafiğinin hangi yollar üzerinden yönlendirileceği |

Kısaca:

```text id="9u0rj8"
ip addr
↓
"Benim ağ bilgilerim ne?"

ip route
↓
"Paketi nereye ve hangi yol üzerinden göndereceğim?"
```

---

# 7. `ip neigh` — Ağ Komşularını Görüntüleme

Yerel ağdaki komşuluk bilgilerini görmek için:

```bash id="1f8r1k"
ip neigh
```

komutu kullanılmıştır.

Bu komut, sistemin yerel ağ üzerinde bildiği komşularla ilgili bilgileri görüntüleyebilir.

Örneğin:

```text id="w8j4o1"
192.168.x.x dev eth0 lladdr XX:XX:XX:XX:XX:XX REACHABLE
```

gibi bir kayıt görülebilir.

Buradaki bilgiler arasında:

* IP adresi
* Ağ arayüzü
* MAC adresi
* Komşuluk durumu

bulunabilir.

### 📸 `ip neigh` çıktısı


<img width="540" height="77" alt="image" src="https://github.com/user-attachments/assets/a9726acf-e77b-4720-9bd6-85c5e7928893" />


---

# 8. MAC Adresi Nedir?

MAC adresi, bir ağ arayüzüyle ilişkilendirilen donanımsal/ağ seviyesindeki adreslerden biridir.

Genellikle şu biçime benzer:

```text id="4c8f2x"
XX:XX:XX:XX:XX:XX
```

Yerel ağ iletişiminde cihazların birbirleriyle iletişim kurmasında MAC adresleri önemli rol oynar.

IP adresi ile MAC adresi aynı şey değildir.

Basit şekilde:

```text id="yk3h2v"
IP adresi
↓
Ağın üzerinde kullanılan adres

MAC adresi
↓
Ağ arayüzünün bağlantı katmanındaki adresi
```

---

# 9. ARP Nedir?

**ARP (Address Resolution Protocol)**, IPv4 ağlarında bir IP adresinin yerel ağdaki ilgili MAC adresinin bulunmasına yardımcı olan protokoldür.

Basitleştirilmiş bir örnek:

```text id="3k5jts"
IP adresi
    ↓
"Bu IP hangi cihazda?"
    ↓
ARP
    ↓
MAC adresi
```

Linux sistemindeki komşuluk bilgileri bu eşleştirmeler hakkında bilgi sağlayabilir.

Bu nedenle:

```bash id="j9t8si"
ip neigh
```

komutu ağ analizi açısından önemlidir.

---

# 10. Siber Güvenlik Açısından Değerlendirme

Ağ yapılandırma bilgilerinin anlaşılması siber güvenlik çalışmalarının temelini oluşturur.

Bir güvenlik uzmanının sistem hakkında aşağıdaki sorulara cevap verebilmesi gerekir:

* Sistem hangi IP adresini kullanıyor?
* Hangi ağ arayüzleri aktif?
* Varsayılan gateway nedir?
* Ağ trafiği hangi rotalar üzerinden yönlendiriliyor?
* Yerel ağda bilinen komşu cihazlar var mı?
* IP adresleri ile MAC adresleri arasında hangi eşleşmeler bulunuyor?

Bu bilgiler ileride gerçekleştirilecek ağ keşfi ve güvenlik analizlerinin anlaşılmasını kolaylaştırır.

---

# 11. Kullanılan Komutların Özeti

| Komut      | Görevi                                         |
| ---------- | ---------------------------------------------- |
| `ip addr`  | Ağ arayüzlerini ve IP adreslerini görüntüler.  |
| `ip route` | Yönlendirme tablosunu görüntüler.              |
| `ip neigh` | Yerel ağ komşularına ait bilgileri görüntüler. |

---

# 12. Öğrenilenler

Bu çalışma sonunda:

* Linux'ta ağ arayüzleri incelendi.
* `ip addr` komutunun amacı öğrenildi.
* IP adresinin ağ iletişimindeki rolü öğrenildi.
* Loopback arayüzü ve `127.0.0.1` kavramı tekrar edildi.
* `ip route` ile yönlendirme bilgileri incelendi.
* Default gateway kavramı öğrenildi.
* `ip neigh` ile komşuluk bilgileri incelendi.
* IP ve MAC adresleri arasındaki fark öğrenildi.
* ARP'nin temel çalışma amacı öğrenildi.
* Ağ yapılandırmasının siber güvenlik açısından önemi değerlendirildi.

---

# 📝 Genel Değerlendirme

Bu çalışma ile Kali Linux sisteminin ağ yapılandırması temel seviyede incelenmiştir.

`ip addr`, `ip route` ve `ip neigh` komutlarının farklı amaçlara hizmet ettiği görülmüştür.

Özellikle IP adresi, MAC adresi, ağ arayüzü, gateway ve ARP kavramlarının anlaşılması; ilerleyen aşamalarda gerçekleştirilecek ağ keşfi ve Nmap çalışmalarının daha doğru yorumlanması açısından temel oluşturmaktadır.

Bir sonraki aşamada **ping ve ICMP** incelenecek, ardından Nmap ile kontrollü şekilde ilk ağ taraması gerçekleştirilecektir.
