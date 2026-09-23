# 02 - Lab Network Setup

## 1. Amaç

Bu çalışmanın amacı, Kali Linux ve Metasploitable 2 sanal makineleri arasında kontrollü ve izole bir laboratuvar ağı oluşturmaktır.

Bu aşamada;

* Sanal makinelerin aynı ağa bağlanması,
* IP adreslerinin belirlenmesi,
* ağ arayüzlerinin incelenmesi,
* routing bilgilerinin kontrol edilmesi,
* iki makine arasındaki ağ bağlantısının doğrulanması

işlemleri gerçekleştirilmiştir.

Bu işlemler, sonraki aşamalarda gerçekleştirilecek ağ keşfi ve servis analizleri için temel oluşturmaktadır.

---

## 2. Lab Network Yapısı

Laboratuvar ortamında iki sanal makine kullanılmıştır:

| Makine           | Rol                        | IP Adresi       |
| ---------------- | -------------------------- | --------------- |
| Kali Linux       | Test ve analiz makinesi    | `192.168.56.10` |
| Metasploitable 2 | Eğitim amaçlı hedef makine | `192.168.56.20` |

İki sanal makine VirtualBox üzerinde oluşturulan `cyber-lab` isimli **Internal Network** ağına bağlanmıştır.

```text
                    cyber-lab
                 Internal Network
                        |
              +---------+---------+
              |                   |
              v                   v
        Kali Linux         Metasploitable 2
      192.168.56.10         192.168.56.20
```

Bu yapı, güvenlik testlerinin izole bir laboratuvar ortamında gerçekleştirilmesini sağlar.

---

## 3. Internal Network

VirtualBox üzerinde her iki sanal makinenin:

**Settings → Network → Adapter 1**

bölümünde bağlantı türü:

```text
Internal Network
```

olarak belirlenmiş ve ağ adı:

```text
cyber-lab
```

olarak seçilmiştir.

### Internal Network Kullanım Amacı

Internal Network, VirtualBox içerisindeki sanal makinelerin kendi aralarında iletişim kurabilmesini sağlayan izole bir ağ yapısıdır.

Bu laboratuvarda Internal Network kullanılmasının temel amacı, Metasploitable 2 üzerinde gerçekleştirilecek güvenlik testlerinin gerçek sistemlerden ve üçüncü taraf ağlardan ayrılmasıdır.

Laboratuvarın temel iletişim yapısı:

```text
Kali Linux
    |
    |  cyber-lab
    |
Metasploitable 2
```

şeklindedir.

> Güvenlik testleri yalnızca yetkili ve kontrollü laboratuvar ortamlarında gerçekleştirilmelidir.

---

## 4. IP Adresleme

Laboratuvar ağı:

```text
192.168.56.0/24
```

olarak yapılandırılmıştır.

Sanal makinelerin IP adresleri:

```text
Kali Linux        → 192.168.56.10/24
Metasploitable 2  → 192.168.56.20/24
```

şeklindedir.

Her iki IP adresi de `192.168.56.0/24` ağı içerisinde bulunduğundan, sanal makineler aynı subnet üzerinde yer almaktadır.

### `/24` Nedir?

`/24`, CIDR gösterimidir ve aşağıdaki subnet maskesine karşılık gelir:

```text
255.255.255.0
```

Bu yapı içerisinde ilk 24 bit ağ bölümünü belirtirken kalan bölüm host adresleri için kullanılır.

Laboratuvarda kullanılan ağ:

```text
Network: 192.168.56.0/24
```

şeklindedir.

---

## 5. Kali Linux Ağ Bilgilerinin Kontrolü

Kali Linux üzerindeki ağ arayüzlerini görüntülemek için aşağıdaki komut kullanılmıştır:

```bash
ip addr
```

Belirli bir ağ arayüzünün bilgilerini görüntülemek için:

```bash
ip addr show eth0
```

komutu kullanılabilir.

Bu çalışmada `eth0` arayüzünün:

```text
192.168.56.10/24
```

IPv4 adresine sahip olduğu doğrulanmıştır.

### Temel Kavramlar

* `eth0`: Ağ arayüzünün adı
* `inet`: IPv4 adresini gösterir
* `192.168.56.10`: Kali Linux'un IPv4 adresidir
* `/24`: Subnet bilgisidir

---

## 6. Routing Bilgilerinin Kontrolü

Kali Linux'un ağ trafiğini nasıl yönlendirdiğini görmek için:

```bash
ip route
```

komutu kullanılmıştır.

Örneğin:

```text
192.168.56.0/24 dev eth0 src 192.168.56.10
```

şeklindeki bir kayıt, `192.168.56.0/24` ağına ulaşmak için `eth0` arayüzünün kullanılacağını göstermektedir.

Routing tablosunun incelenmesi, ağ bağlantısı sırasında kullanılan yolların anlaşılması ve olası bağlantı problemlerinin analiz edilmesi açısından önemlidir.

---

## 7. Metasploitable 2 Ağ Bilgilerinin Kontrolü

Metasploitable 2 üzerinde ağ yapılandırmasını görüntülemek için:

```bash
ifconfig
```

veya:

```bash
ip addr
```

komutları kullanılabilir.

Bu çalışmada Metasploitable 2'nin `eth0` arayüzünde:

```text
192.168.56.20/24
```

IPv4 adresi kullanılmıştır.

Böylece Kali Linux ve Metasploitable 2'nin aynı `192.168.56.0/24` ağı üzerinde bulunduğu doğrulanmıştır.

---

## 8. Loopback Arayüzü

Ağ yapılandırması incelenirken `lo` isimli bir arayüz görülebilir.

Loopback arayüzü, sistemin kendi içerisinde iletişim kurması için kullanılır.

Standart IPv4 loopback adresi:

```text
127.0.0.1
```

şeklindedir.

`127.0.0.1`, uzak bir sistemin adresi değildir. Sistemin kendisini ifade eder.

Bu nedenle Metasploitable 2'ye Kali Linux üzerinden erişmek için:

```text
192.168.56.20
```

adresi kullanılmıştır.

---

## 9. Ağ Bağlantısının Test Edilmesi

Kali Linux ile Metasploitable 2 arasındaki ağ bağlantısını doğrulamak için `ping` komutu kullanılmıştır.

Kali Linux üzerinde:

```bash
ping -c 4 192.168.56.20
```

komutu çalıştırılmıştır.

Komutun bölümleri:

* `ping`: Hedef sisteme ICMP Echo Request göndererek bağlantıyı test eder.
* `-c 4`: Dört adet paket gönderilmesini belirtir.
* `192.168.56.20`: Metasploitable 2'nin IP adresidir.

---

## 10. Ping Sonucunun Değerlendirilmesi

Bağlantı testi sonucunda:

```text
4 packets transmitted, 4 received, 0% packet loss
```

sonucu elde edilmiştir.

Bu sonuç:

* 4 paket gönderildiğini,
* 4 paket için cevap alındığını,
* paket kaybının gerçekleşmediğini

göstermektedir.

Dolayısıyla Kali Linux ile Metasploitable 2 arasındaki temel ağ bağlantısının başarılı olduğu doğrulanmıştır.

Ping çıktısında görülen:

```text
time=... ms
```

değeri ise paketin hedefe gönderilip cevabının alınması için geçen yaklaşık süreyi ifade eder.

---

## 11. Gateway Kavramı

Gateway, bir ağdan başka bir ağa geçiş sağlayan ağ cihazı veya adresidir.

Örneğin:

```text
Kali
  |
Gateway
  |
Başka Ağ
```

şeklinde bir iletişim yapısı bulunabilir.

Bu laboratuvarda Kali Linux ve Metasploitable 2 aynı:

```text
192.168.56.0/24
```

ağında bulunduğundan, iki makinenin birbirleriyle iletişim kurması için ayrı bir gateway üzerinden geçmesi gerekmemektedir.

---

## 12. IP Adreslerinin Manuel Olarak Verilmesi

VirtualBox üzerinde kullanılan `Internal Network` yapısında otomatik DHCP servisi bulunmadığı durumda sanal makineler otomatik IPv4 adresi alamayabilir.

Bu nedenle laboratuvar ortamında IP adresleri manuel olarak atanmıştır.

### Kali Linux

```text
192.168.56.10/24
```

### Metasploitable 2

```text
192.168.56.20/24
```

Bu adresler aynı subnet içerisinde bulunduğu için iki sistem arasında doğrudan ağ iletişimi sağlanmıştır.

> Bu IP yapılandırması geçici olarak uygulanmıştır. Sanal makinelerin yeniden başlatılması durumunda adreslerin tekrar atanması gerekebilir.

---

## 13. Kullanılan Temel Komutlar

### Ağ arayüzlerini görüntüleme

```bash
ip addr
```

### Belirli bir ağ arayüzünü görüntüleme

```bash
ip addr show eth0
```

### Routing tablosunu görüntüleme

```bash
ip route
```

### Metasploitable 2 ağ bilgilerini görüntüleme

```bash
ifconfig
```

### Ağ bağlantısını test etme

```bash
ping -c 4 192.168.56.20
```

---

## 14. Laboratuvar Son Durumu

| Parametre                | Değer              |
| ------------------------ | ------------------ |
| VirtualBox Network       | `cyber-lab`        |
| Network Type             | `Internal Network` |
| Network Address          | `192.168.56.0/24`  |
| Kali Linux               | `192.168.56.10/24` |
| Metasploitable 2         | `192.168.56.20/24` |
| Kali Interface           | `eth0`             |
| Metasploitable Interface | `eth0`             |
| Connectivity Test        | Başarılı           |
| Packet Loss              | `%0`               |

---

## 15. Sonuç

Bu çalışmada Kali Linux ve Metasploitable 2 sanal makineleri aynı `cyber-lab` isimli Internal Network üzerine bağlanmıştır.

Ağ yapılandırması incelenerek her iki sistemin IP adresleri belirlenmiş ve aynı `192.168.56.0/24` subnet'i içerisinde oldukları doğrulanmıştır.

Kali Linux üzerinden Metasploitable 2'ye gerçekleştirilen `ping` testi sonucunda %0 paket kaybı elde edilmiştir.

Böylece iki sanal makine arasındaki temel ağ bağlantısının çalıştığı doğrulanmış ve sonraki aşamada gerçekleştirilecek ağ keşfi ve servis taramaları için laboratuvar ortamı hazır hale getirilmiştir.
<img width="722" height="222" alt="image" src="https://github.com/user-attachments/assets/12393b6d-2f66-47bc-9214-6567cd6570c3" />

<img width="660" height="434" alt="image" src="https://github.com/user-attachments/assets/d72106f8-a866-4cd3-9ebd-4585037c5bdd" />
