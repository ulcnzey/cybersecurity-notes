# 19. PCAP Olay Analizi – Ana Uygulama

## 1. Analizin Amacı

Bu bölümde eğitim amacıyla kullanılan bir PCAP dosyasını Wireshark üzerinden analiz ettim.

Bu uygulamadaki amacım, daha önce öğrendiğim DNS, TCP, HTTP, IP adresleri ve port bilgilerini tek bir ağ trafiği üzerinde bir araya getirerek gerçek bir ağ analizi sürecini anlamaktı.

Analiz sırasında özellikle şu noktaları inceledim:

* En aktif IP adresleri
* En çok iletişim kurulan IP adresleri
* Kullanılan portlar
* Kullanılan protokoller
* DNS sorguları
* DNS sunucuları
* TCP bağlantıları
* SYN ve RST paketleri
* HTTP istekleri ve cevapları
* Şüpheli olabilecek ağ davranışları

Bu çalışmayı eğitim ve analiz amacıyla gerçekleştirdim.

---

# 2. Genel Trafik Analizi

İlk olarak PCAP dosyasındaki genel ağ trafiğini inceleyerek hangi IP adreslerinin ve protokollerin daha aktif olduğunu anlamaya çalıştım.

Bu aşamada amacım henüz bir saldırı aramak değil, öncelikle ağın genel yapısını tanımaktı.

İncelediğim temel bilgiler:

* Kaynak IP
* Hedef IP
* Kaynak port
* Hedef port
* Protokol
* Paket sayısı
* İletişim sıklığı

## En Aktif IP Adresleri

| IP Adresi    | Gözlem   |
| ------------ | -------- |
| `IP_ADDRESS` | `GÖZLEM` |
| `IP_ADDRESS` | `GÖZLEM` |
| `IP_ADDRESS` | `GÖZLEM` |

Bu IP adreslerinin trafik içerisindeki yoğunluğunu karşılaştırarak hangi sistemlerin daha fazla iletişim gerçekleştirdiğini belirlemeye çalıştım.

## En Çok İletişim Kurulan IP'ler

| Kaynak IP    | Hedef IP     | Gözlem   |
| ------------ | ------------ | -------- |
| `IP_ADDRESS` | `IP_ADDRESS` | `GÖZLEM` |
| `IP_ADDRESS` | `IP_ADDRESS` | `GÖZLEM` |

Bu bölümde özellikle tek bir IP'nin çok sayıda farklı sistemle iletişim kurup kurmadığına dikkat ettim.

## En Çok Kullanılan Portlar

| Port   | Servis / Protokol | Gözlem   |
| ------ | ----------------- | -------- |
| `PORT` | `SERVICE`         | `GÖZLEM` |
| `PORT` | `SERVICE`         | `GÖZLEM` |

Port bilgileri sayesinde ağ üzerinde hangi servislerin kullanıldığını daha iyi anlayabildim.

## Kullanılan Protokoller

PCAP içerisinde gözlemlediğim protokoller:

* TCP
* UDP
* DNS
* HTTP
* `DİĞER_PROTOKOL`

Protokollerin dağılımını incelemek, ağ trafiğinin hangi amaçlarla kullanıldığını anlamama yardımcı oldu.

---

# 3. DNS Analizi

Daha önceki çalışmalarda DNS'in domain isimlerini IP adresleriyle eşleştirmek için kullanıldığını öğrenmiştim.

Bu bölümde ise DNS trafiğini doğrudan PCAP içerisinden inceleyerek hangi domainlerin sorgulandığını ve hangi DNS sunucularının kullanıldığını belirlemeye çalıştım.

Wireshark'ta öncelikle:

```text
dns
```

filtresini kullandım.

Domain sorgularını daha ayrıntılı incelemek için:

```text
dns.qry.name
```

filtresinden yararlandım.

## Sorgulanan Domainler

| Timestamp   | Kaynak IP    | DNS Sunucusu | Sorgulanan Domain |
| ----------- | ------------ | ------------ | ----------------- |
| `TIMESTAMP` | `IP_ADDRESS` | `IP_ADDRESS` | `DOMAIN`          |
| `TIMESTAMP` | `IP_ADDRESS` | `IP_ADDRESS` | `DOMAIN`          |

Bu sorgular üzerinden sistemin hangi domainlerle iletişim kurmaya çalıştığını gözlemledim.

## DNS Sunucuları

PCAP içerisinde kullanılan DNS sunucuları:

* `DNS_SERVER_IP`
* `DNS_SERVER_IP`

DNS sunucusunu belirlerken DNS request ve response paketlerindeki kaynak ve hedef IP adreslerini karşılaştırdım.

## Şüpheli Görünen DNS Sorguları

DNS sorgularını incelerken özellikle şu özelliklere dikkat ettim:

* Anlaşılması zor veya rastgele görünen domainler
* Beklenmeyen dış domainler
* Çok sık tekrarlanan DNS sorguları
* Olağandışı subdomain yapıları
* Sistemin normal kullanım amacıyla ilişkili görünmeyen domainler

İnceleme sırasında dikkatimi çeken domain:

```text
SUSPICIOUS_DOMAIN
```

Bu sorguyu şu nedenle incelemeye değer buldum:

```text
REASON
```

Ancak yalnızca bir DNS sorgusunun şüpheli görünmesi, tek başına saldırı veya malware olduğu anlamına gelmez. Bu nedenle ilgili domainin devamındaki ağ trafiğini de incelemek gerekir.

---

# 4. TCP Analizi

Daha sonra PCAP içerisindeki TCP trafiğini incelemeye geçtim.

TCP analizinde özellikle bağlantıların nasıl başlatıldığı ve sonlandırıldığına dikkat ettim.

Kullandığım temel filtre:

```text
tcp
```

TCP bağlantılarını daha detaylı incelemek için SYN paketlerini filtreledim:

```text
tcp.flags.syn == 1
```

Sadece bağlantı başlatmak için gönderilen SYN paketlerini görmek için:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

RST paketlerini incelemek için:

```text
tcp.flags.reset == 1
```

filtrelerini kullandım.

## TCP Bağlantıları

| Kaynak IP    | Kaynak Port | Hedef IP     | Hedef Port |
| ------------ | ----------: | ------------ | ---------: |
| `IP_ADDRESS` |      `PORT` | `IP_ADDRESS` |     `PORT` |
| `IP_ADDRESS` |      `PORT` | `IP_ADDRESS` |     `PORT` |

Bu bağlantıları incelerken hangi sistemin hangi servise bağlandığını anlamaya çalıştım.

## SYN Paketleri

SYN paketlerinin TCP bağlantısının başlangıcında kullanıldığını daha önce öğrenmiştim.

PCAP içerisindeki SYN paketlerini inceleyerek:

* Hangi IP'nin bağlantı başlattığını
* Hangi hedefe bağlantı kurulduğunu
* Hangi portlara bağlantı denenildiğini
* Kısa süre içerisinde çok sayıda bağlantı denenip denenmediğini

kontrol ettim.

Gözlemim:

```text
OBSERVATION
```

Özellikle tek bir kaynaktan çok sayıda farklı porta yönelik SYN paketleri görülmesi, port taraması ihtimalinin araştırılması gerektiğini düşündürebilir.

Ancak bunu kesin olarak saldırı olarak değerlendirmeden önce bağlantıların devamındaki paketleri de incelemek gerekir.

## RST Paketleri

RST paketlerini:

```text
tcp.flags.reset == 1
```

filtresiyle inceledim.

Gözlemim:

```text
OBSERVATION
```

RST paketlerinin bulunması tek başına kötü amaçlı bir davranış anlamına gelmez. Bağlantıların başarısız olması veya bir servisin bağlantıyı reddetmesi gibi normal durumlarda da RST paketleri görülebilir.

Bu nedenle RST paketlerini diğer TCP paketleriyle birlikte değerlendirdim.

---

# 5. HTTP Analizi

PCAP içerisinde HTTP trafiği bulunuyorsa, HTTP isteklerini ayrıca incelemeye çalıştım.

HTTP trafiğini filtrelemek için:

```text
http
```

ve HTTP isteklerini görmek için:

```text
http.request
```

filtrelerini kullandım.

HTTP analizinde şu bilgileri incelemeye odaklandım:

* Domain / Host
* URI
* HTTP Method
* Status Code
* User-Agent

## HTTP İstekleri

| Timestamp   | Kaynak IP    | Hedef IP     | Method | URI    |
| ----------- | ------------ | ------------ | ------ | ------ |
| `TIMESTAMP` | `IP_ADDRESS` | `IP_ADDRESS` | `GET`  | `/URI` |
| `TIMESTAMP` | `IP_ADDRESS` | `IP_ADDRESS` | `POST` | `/URI` |

## Domain

Gözlemlenen Host / Domain:

```text
HOST_DOMAIN
```

## HTTP Method

Gözlemlediğim HTTP methodları:

* GET
* POST
* `DİĞER_METHOD`

GET ve POST gibi methodların hangi URI'lere gönderildiğini inceleyerek istemci ile web sunucusu arasındaki iletişimi anlamaya çalıştım.

## HTTP Status Code

Gözlemlenen status code'lar:

* `200`
* `404`
* `DİĞER_STATUS_CODE`

Status code'ları inceleyerek HTTP isteklerinin başarılı olup olmadığını anlamaya çalıştım.

## User-Agent

Gözlemlenen User-Agent:

```text
USER_AGENT
```

User-Agent bilgisini de incelememin nedeni, HTTP isteğinin hangi istemci veya uygulama tarafından gönderildiği hakkında bilgi verebilmesidir.

---

# 6. Potansiyel Güvenlik Olayları

PCAP içerisindeki trafik üzerinde yaptığım incelemeler sonucunda dikkatimi çeken en az üç potansiyel güvenlik olayını belirledim.

Buradaki olayları doğrudan saldırı olarak değerlendirmedim. Çünkü yalnızca ağ trafiğine bakarak kesin bir saldırı sonucu çıkarmak her zaman mümkün değildir.

Bu nedenle bulguları **potansiyel güvenlik olayı** olarak değerlendirdim.

---

## 6.1 Potansiyel Olay 1 – Port Tarama İhtimali

### Bulgu:

Bir kaynak IP adresinden farklı portlara yönelik birden fazla TCP bağlantı denemesi gözlemledim.

### Kaynak IP:

`SOURCE_IP`

### Hedef IP:

`DESTINATION_IP`

### Port:

`PORT / MULTIPLE PORTS`

### Protokol:

TCP

### Timestamp:

`TIMESTAMP`

### Gözlenen davranış:

```text
OBSERVED_BEHAVIOR
```

Kaynak sistemin kısa bir zaman aralığında birden fazla porta bağlantı kurmaya çalıştığını gözlemledim.

### Neden şüpheli?

Bir sistemin çok sayıda farklı porta bağlantı denemesi, açık servisleri keşfetmek amacıyla yapılan port taramasıyla benzer bir davranış gösterebilir.

Ancak bunun kesin olarak port taraması olduğunu söylemeden önce bağlantıların tamamını ve ortamın amacını değerlendirmek gerekir.

### Olası güvenlik etkisi:

Eğer bu trafik yetkisiz bir taramayı temsil ediyorsa, saldırganın hedef sistemde hangi servislerin açık olduğunu keşfetmesine yardımcı olabilir.

### Önerilen aksiyon:

* Kaynak IP'nin yetkili olup olmadığını kontrol etmek
* Hedef sistemde açık servisleri incelemek
* Aynı kaynaktan gelen diğer trafiği kontrol etmek
* Başarılı bağlantı kurulup kurulmadığını incelemek

---

## 6.2 Potansiyel Olay 2 – Şüpheli DNS Aktivitesi

### Bulgu:

Beklenmeyen veya olağandışı görünen bir DNS sorgusu gözlemledim.

### Kaynak IP:

`SOURCE_IP`

### Hedef IP:

`DNS_SERVER_IP`

### Port:

53

### Protokol:

DNS / UDP

### Timestamp:

`TIMESTAMP`

### Gözlenen davranış:

Kaynak sistem tarafından şu domain için DNS sorgusu gönderildi:

```text
SUSPICIOUS_DOMAIN
```

### Neden şüpheli?

Bu domaini şu nedenle incelemeye değer buldum:

```text
REASON
```

Örneğin domain yapısının olağandışı olması, beklenmeyen bir dış domain olması veya çok sık sorgulanması daha ayrıntılı inceleme gerektirebilir.

### Olası güvenlik etkisi:

Şüpheli DNS trafiği bazı durumlarda:

* Malware iletişimi
* Command and Control (C2)
* Zararlı yönlendirmeler
* Veri sızdırma

gibi durumlarla ilişkili olabilir.

Ancak yalnızca DNS sorgusuna bakarak bu aktivitelerden birinin gerçekleştiğini söylemek mümkün değildir.

### Önerilen aksiyon:

* Domainin güvenilirliğini araştırmak
* DNS response içerisindeki IP adreslerini incelemek
* Domain ile yapılan sonraki bağlantıları takip etmek
* Aynı domain için tekrarlanan sorguları kontrol etmek

---

## 6.3 Potansiyel Olay 3 – Olağandışı TCP Davranışı

### Bulgu:

Bir TCP bağlantısında olağandışı bir bağlantı davranışı gözlemledim.

### Kaynak IP:

`SOURCE_IP`

### Hedef IP:

`DESTINATION_IP`

### Port:

`DESTINATION_PORT`

### Protokol:

TCP

### Timestamp:

`TIMESTAMP`

### Gözlenen davranış:

```text
OBSERVED_TCP_BEHAVIOR
```

Örneğin tekrarlanan bağlantı denemeleri ve bunların ardından RST paketlerinin gönderilmesi gibi bir davranış gözlemlenebilir.

### Neden şüpheli?

Çok sayıda başarısız bağlantı veya olağandışı RST trafiği;

* Servis keşfi
* Port taraması
* Başarısız bağlantı denemeleri
* Uygulama kaynaklı bağlantı problemleri

gibi farklı durumlarla ilişkili olabilir.

Bu nedenle davranışı tek başına saldırı olarak değerlendirmedim.

### Olası güvenlik etkisi:

Eğer davranış yetkisiz keşif faaliyetiyle ilişkiliyse, hedef sistemdeki servisler hakkında bilgi toplama amacı taşıyabilir.

### Önerilen aksiyon:

* TCP bağlantısının tamamını incelemek
* Hedef portta çalışan servisi belirlemek
* Aynı kaynak IP'nin diğer bağlantılarını incelemek
* Bağlantıların başarılı olup olmadığını kontrol etmek

---

# 7. Genel Değerlendirme

Bu uygulamada tek tek paketleri incelemenin yanında ağ trafiğini bir bütün olarak değerlendirmeye çalıştım.

İzlediğim genel analiz süreci şu şekilde oldu:

```text
PCAP
  ↓
Genel Trafik Analizi
  ↓
IP ve Port Analizi
  ↓
DNS Analizi
  ↓
TCP Analizi
  ↓
HTTP Analizi
  ↓
Şüpheli Davranışların Belirlenmesi
  ↓
Potansiyel Güvenlik Olaylarının Değerlendirilmesi
```

Bu çalışma sırasında benim için en önemli noktalardan biri, **tek bir paketin her zaman yeterli kanıt sağlamadığı** oldu.

Örneğin bir SYN paketi tek başına saldırı anlamına gelmez. Aynı şekilde bir DNS sorgusu veya RST paketi de tek başına kötü amaçlı bir aktivite olduğunu göstermez.

Bu nedenle bir olayın değerlendirilmesi sırasında:

* Kaynak
* Hedef
* Port
* Protokol
* Timestamp
* Paketlerin sıralaması
* Öncesindeki ve sonrasındaki trafik
* Ağın genel bağlamı

birlikte incelenmelidir.

---

# 8. Sonuç

Bu uygulama sayesinde Wireshark kullanarak bir PCAP dosyasını daha sistematik şekilde analiz etmeyi öğrendim.

Özellikle şu konuları tek bir çalışma içerisinde uygulama fırsatı buldum:

* IP adreslerini analiz etmek
* Portları incelemek
* Protokolleri belirlemek
* DNS sorgularını takip etmek
* TCP bağlantılarını incelemek
* SYN paketlerini analiz etmek
* RST paketlerini incelemek
* HTTP isteklerini analiz etmek
* Şüpheli ağ davranışlarını belirlemek
* Bulguları güvenlik olayı şeklinde dokümante etmek

Bu çalışmanın bana kazandırdığı en önemli bakış açısı, **Wireshark'ın sadece paketleri görmek için değil, ağdaki davranışları anlamak ve olaylar arasında bağlantı kurmak için de kullanılabileceği** oldu.

Özellikle bir güvenlik analisti gibi düşünürken tek bir pakete bakmak yerine, olayın tamamını ve paketlerin birbirleriyle olan ilişkisini değerlendirmem gerektiğini daha iyi anladım.
