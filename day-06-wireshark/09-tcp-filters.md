# 9. TCP Filtreleri

Bu bölümde Wireshark üzerinde TCP trafiğini daha kolay inceleyebilmek için farklı display filter'ları denedim. Her filtrenin hangi paketleri gösterdiğini ve güvenlik analizinde ne amaçla kullanılabileceğini inceledim.

Analiz ortamımdaki Kali Linux IP adresi:

```text
10.0.3.15
```

Hedef sistem:

```text
10.0.3.2
```

---

## 1. `tcp`

### Filtrenin amacı

```text
tcp
```

filtresi, yakalanan trafik içerisindeki **TCP paketlerini** göstermektedir.

### Gözlemlediğim paketler

Örneğin:

```text
Frame 11
10.0.3.15 → 10.0.3.2
34080 → 53 [SYN]

Frame 12
10.0.3.15 → 10.0.3.2
34080 → 22 [SYN]

Frame 13
10.0.3.15 → 10.0.3.2
34080 → 443 [SYN]
```

<img width="1079" height="492" alt="image" src="https://github.com/user-attachments/assets/9c8ed048-dfa9-48b9-985a-3e39c0fcd6e1" />


Bu paketlerin tamamında protokol olarak TCP görülmektedir.

### Güvenlik analizinde kullanımı

Bu filtre ile ağdaki TCP trafiğini diğer protokollerden ayırabilirim. Böylece TCP bağlantılarını, kullanılan portları ve TCP flag'lerini daha kolay inceleyebilirim.

---

## 2. `tcp.port == 80`

### Filtrenin amacı

```text
tcp.port == 80
```

filtresi, kaynak veya hedef TCP portu **80** olan paketleri göstermektedir.

Port 80 genellikle HTTP trafiği ile ilişkilidir.

### Gözlemlediğim paketler

```text
Frame 14
10.0.3.15 → 10.0.3.2
34080 → 80 [SYN]

Frame 15
10.0.3.15 → 10.0.3.2
34082 → 80 [SYN]
```

Bu paketlerde Kali makinemden `10.0.3.2` adresindeki 80 numaralı porta TCP bağlantısı başlatılmaya çalışıldığını gördüm.

### Güvenlik analizinde kullanımı

Bu filtre ile HTTP ile ilişkili TCP trafiğini hızlı şekilde ayırabilirim. Web servislerine yönelik bağlantıları ve bu bağlantıların nasıl gerçekleştiğini incelemek için kullanılabilir.

---

## 3. `tcp.port == 443`

### Filtrenin amacı

```text
tcp.port == 443
```

filtresi, kaynak veya hedef TCP portu **443** olan paketleri göstermektedir.

Port 443 genellikle HTTPS trafiği ile ilişkilidir.

### Gözlemlediğim paket

```text
Frame 13
10.0.3.15 → 10.0.3.2
34080 → 443 [SYN]
```

Bu pakette Kali makinemin `10.0.3.2` adresindeki 443 numaralı TCP portuna bağlantı başlatmaya çalıştığını gördüm.

### Güvenlik analizinde kullanımı

Bu filtre ile HTTPS ile ilişkili TCP bağlantılarını ayırabilirim. HTTPS trafiğinin kendisi şifreli olsa bile bağlantı kurulumu, IP adresleri ve port bilgileri gibi ağ seviyesindeki bilgileri incelemek mümkündür.

---

## 4. `tcp.flags.syn == 1`

### Filtrenin amacı

```text
tcp.flags.syn == 1
```

filtresi, TCP başlığında **SYN flag'i aktif olan paketleri** göstermektedir.

SYN, TCP bağlantısının başlatılmasında kullanılan flag'dir.

### Gözlemlediğim paketler

```text
Frame 11
10.0.3.15 → 10.0.3.2
34080 → 53 [SYN]

Frame 12
10.0.3.15 → 10.0.3.2
34080 → 22 [SYN]

Frame 13
10.0.3.15 → 10.0.3.2
34080 → 443 [SYN]
```

Ayrıca port 80 için de SYN paketleri yakaladım:

```text
Frame 14
10.0.3.15 → 10.0.3.2
34080 → 80 [SYN]

Frame 15
10.0.3.15 → 10.0.3.2
34082 → 80 [SYN]
```

Bu paketlerde `10.0.3.15` adresinden `10.0.3.2` adresine farklı TCP portlarına bağlantı başlatma isteği gönderildiğini gördüm.

### Güvenlik analizinde kullanımı

SYN paketleri TCP bağlantı başlangıçlarını incelemek için kullanılabilir.

Bir sistemin kısa sürede çok sayıda farklı porta SYN göndermesi, bağlama göre **port taraması gibi davranışların incelenmesini** gerektirebilir.

Tek başına bir SYN paketi saldırı anlamına gelmez. Trafiğin tamamı ve bağlantının devamındaki paketler birlikte değerlendirilmelidir.

---

## 5. `tcp.flags.reset == 1`

### Filtrenin amacı

```text
tcp.flags.reset == 1
```

filtresi, TCP **RST (Reset)** flag'i aktif olan paketleri göstermektedir.

RST, TCP bağlantısının sıfırlanması için kullanılır.

### Gözlemlediğim paketler

```text
Frame 19
10.0.3.2 → 10.0.3.15
53 → 34080 [RST, ACK]

Frame 20
10.0.3.2 → 10.0.3.15
80 → 34080 [RST, ACK]

Frame 21
10.0.3.2 → 10.0.3.15
443 → 34080 [RST, ACK]
```

Burada daha önce gönderilen TCP SYN paketlerinden sonra `10.0.3.2` tarafından RST, ACK yanıtları geldiğini gördüm.

Örneğin:

```text
10.0.3.15 → 10.0.3.2
34080 → 443 [SYN]
```

sonrasında:

```text
10.0.3.2 → 10.0.3.15
443 → 34080 [RST, ACK]
```

paketini gördüm.

### Güvenlik analizinde kullanımı

RST paketleri; reddedilen, sıfırlanan veya beklenmedik şekilde sonlandırılan TCP bağlantılarını incelemek için kullanılabilir.

Çok sayıda RST paketi görülmesi durumunda bağlantıların neden sıfırlandığı araştırılabilir.

---

## 6. `ip.addr == 10.0.3.15`

### Filtrenin amacı

```text
ip.addr == 10.0.3.15
```

filtresi, kaynak veya hedef IP adresi **10.0.3.15** olan paketleri göstermektedir.

Bu adres benim Kali Linux makinemin analiz sırasında kullandığım IP adresidir.

### Gözlemlediğim paketler

```text
Frame 1
10.0.3.15 → 10.0.3.2
DHCP Request
```

```text
Frame 11
10.0.3.15 → 10.0.3.2
34080 → 53 [SYN]
```

```text
Frame 12
10.0.3.15 → 10.0.3.2
34080 → 22 [SYN]
```

```text
Frame 13
10.0.3.15 → 10.0.3.2
34080 → 443 [SYN]
```

Bu filtre sayesinde Kali makinemin dahil olduğu ağ trafiğini ayırabildim.

### Güvenlik analizinde kullanımı

Belirli bir IP adresinin ağ üzerindeki iletişimini incelemek için kullanılabilir.

Örneğin bir SOC analisti belirli bir istemcinin hangi sistemlerle iletişim kurduğunu, hangi protokolleri kullandığını ve hangi portlara bağlantı kurduğunu incelemek için bu tür filtrelerden yararlanabilir.

---

# Genel Değerlendirme

Bu bölümde Wireshark üzerinde TCP trafiğini filtrelemek için farklı filtreler kullandım.

Özellikle:

```text
tcp
```

ile TCP trafiğini,

```text
tcp.port == 80
```

ile 80 numaralı portu,

```text
tcp.port == 443
```

ile 443 numaralı portu,

```text
tcp.flags.syn == 1
```

ile SYN paketlerini,

```text
tcp.flags.reset == 1
```

ile RST paketlerini,

```text
ip.addr == 10.0.3.15
```

ile kendi IP adresimin dahil olduğu trafiği filtreledim.

Bu çalışma sayesinde Wireshark'ta tüm paketleri tek tek incelemek yerine, ilgilendiğim protokole, porta, TCP flag'ine veya IP adresine göre trafiği daraltabileceğimi öğrendim.

Özellikle SYN ve RST filtrelerinin, TCP bağlantılarının nasıl başlatıldığını ve bazı bağlantıların nasıl sıfırlandığını incelemek açısından faydalı olduğunu gözlemledim.
