# 23. Güvenlik Olayı Raporu

## 1. Executive Summary

Bu çalışmada, analiz edilen PCAP dosyası Wireshark kullanılarak incelendi ve ağ trafiği içerisindeki DNS iletişimleri, IP adresleri ve zaman bilgileri üzerinden olay analizi gerçekleştirildi.

Analiz sırasında özellikle `10.0.3.15` ve `10.0.3.3` IP adresleri arasında gerçekleşen DNS trafiği gözlemlendi.

İnceleme sırasında aşağıdaki DNS olayları tespit edildi:

* `10.0.3.15 → 10.0.3.3` yönünde `example.com` için A kaydı sorgusu
* `10.0.3.3 → 10.0.3.15` yönünde `firefox.settings.services.mozilla.com` için DNS response
* İkinci DNS response içerisinde `mozilla.map.fastly.net` CNAME kaydı ve `199.232.17.91` IP adresi gözlemlendi.

Bu aşamada yalnızca PCAP içerisinde gerçekten gözlemlenen trafik rapora dahil edilmiştir. Henüz doğrulanmamış TCP, HTTP veya saldırı aktiviteleri varsayılmamıştır.

---

# 2. Scope

## 2.1 Analiz Edilen PCAP

Bu çalışma kapsamında eğitim amaçlı bir PCAP dosyası Wireshark üzerinden analiz edildi.

PCAP içerisindeki paketler;

* IP adresleri
* DNS trafiği
* Protokoller
* Portlar
* Timestamp bilgileri
* Kaynak ve hedef iletişimleri

üzerinden incelendi.

## 2.2 IP Adresleri

Analiz sırasında gözlemlenen IP adresleri:

| IP Adresi   | Rol                                   |
| ----------- | ------------------------------------- |
| `10.0.3.15` | DNS sorgusu yapan istemci             |
| `10.0.3.3`  | DNS iletişiminde görülen hedef/sunucu |

## 2.3 Protokoller

İnceleme sırasında doğrulanmış olarak:

* DNS

protokolü gözlemlendi.

TCP, HTTP, HTTPS ve diğer protokoller için analiz sırasında elde edilen gerçek paket bilgileri ayrıca değerlendirilmelidir.

## 2.4 Zaman Aralığı

İncelenen paketler içerisinde şu timestamp değerleri gözlemlendi:

* `343.908320941`
* `1086.812787990`

Bu değerler Wireshark'ın PCAP içerisindeki paket zamanlarını göstermektedir.

---

# 3. Methodology

PCAP analizi sırasında temel olarak Wireshark kullanıldı.

## 3.1 Wireshark

Wireshark ile PCAP içerisindeki paketleri tek tek inceleyerek;

* Source
* Destination
* Protocol
* Length
* Info
* Timestamp

alanlarını kontrol ettim.

Bu alanlar üzerinden ağdaki iletişimin hangi cihazlar arasında gerçekleştiğini anlamaya çalıştım.

## 3.2 Display Filter

Analiz sırasında belirli trafik türlerini ayırmak için Wireshark'ın Display Filter özelliğini kullandım.

Özellikle DNS trafiğini incelemek için:

```text
dns
```

filtresinden yararlanılabilir.

Belirli bir IP adresine ait trafiği incelemek için:

```text
ip.addr == 10.0.3.15
```

gibi filtreler kullanılabilir.

Belirli bir DNS sorgusunu incelemek için:

```text
dns.qry.name
```

alanı üzerinden paketler değerlendirilebilir.

## 3.3 Packet Details

İlgilendiğim paketleri seçerek Packet Details bölümünü inceledim.

Burada özellikle;

* DNS Query
* DNS Response
* Query ID
* A Record
* CNAME
* IP Address

gibi bilgileri kontrol ettim.

## 3.4 Statistics

Wireshark'ın Statistics bölümünün ağ trafiğinin genel yapısını anlamak için kullanılabileceğini öğrendim.

Özellikle;

* Protocol Hierarchy
* Conversations
* Endpoints

bölümleri kaynak-hedef iletişimini ve kullanılan protokolleri değerlendirmek için kullanılabilir.

## 3.5 Conversations

Conversations bölümü üzerinden cihazlar arasındaki iletişimlerin daha toplu şekilde incelenebileceğini değerlendirdim.

Bu bölüm özellikle hangi IP adreslerinin birbirleriyle iletişim kurduğunu anlamak açısından önemlidir.

## 3.6 Follow TCP Stream

TCP trafiği tespit edildiğinde `Follow TCP Stream` özelliği kullanılarak ilgili TCP iletişiminin bütünlüğü incelenebilir.

Ancak mevcut analizde rapora yalnızca gerçekten gözlemlediğim paketler dahil edildiği için doğrulanmamış bir TCP stream sonucu rapora eklenmemiştir.

---

# 4. Findings

## Finding 1 – DNS A Record Query for example.com

### Açıklama

PCAP analizi sırasında `10.0.3.15` adresinden `10.0.3.3` adresine gönderilen bir DNS sorgusu gözlemledim.

Paket içerisinde:

```text
Standard query 0x41c5 A example.com
```

bilgisi yer almaktadır.

### Kaynak

```text
10.0.3.15
```

### Hedef

```text
10.0.3.3
```

### Protokol

```text
DNS
```

### Port

DNS trafiği olduğu için standart DNS iletişiminde kullanılan port:

```text
53
```

Not: Bu paket için port bilgisinin Packet Details bölümünden ayrıca doğrulanması gerekir.

### Timestamp

```text
343.908320941
```

### Teknik Analiz

Paket içerisinde `example.com` alan adı için bir `A` kaydı sorgusu gerçekleştirildiğini gözlemledim.

`A` kaydı, bir domain adının IPv4 adresini öğrenmek amacıyla kullanılır.

Bu nedenle paket, istemcinin `example.com` alan adının IPv4 adresini DNS üzerinden öğrenmeye çalıştığını göstermektedir.

### Güvenlik Etkisi

Tek başına normal bir DNS sorgusu olması nedeniyle bu paket doğrudan kötü amaçlı aktivite olarak değerlendirilemez.

Ancak DNS trafiğinin güvenlik analizinde önemli olmasının nedeni, saldırganların da DNS iletişimini;

* C2 iletişimi,
* DNS tunneling,
* kötü amaçlı domain çözümleme

gibi amaçlarla kullanabilmesidir.

### Risk

```text
Düşük / Bağlama Bağlı
```

Bu değerlendirme yalnızca gözlemlenen pakete dayanmaktadır. Tek başına `example.com` sorgusu kötü amaçlı bir davranışı kanıtlamamaktadır.

### Önerilen Aksiyon

* DNS sorgularının düzenli olarak izlenmesi
* Şüpheli domainlerin tespit edilmesi
* Bilinen kötü amaçlı domainlerin engellenmesi
* DNS loglarının merkezi olarak tutulması

önerilebilir.

---

## Finding 2 – Firefox Settings Service DNS Response

### Açıklama

PCAP içerisinde `10.0.3.3` adresinden `10.0.3.15` adresine gönderilen bir DNS response gözlemledim.

Paket içerisinde:

```text
Standard query response 0x6738 A firefox.settings.services.mozilla.com CNAME mozilla.map.fastly.net A 199.232.17.91
```

bilgisi bulunmaktadır.

### Kaynak

```text
10.0.3.3
```

### Hedef

```text
10.0.3.15
```

### Protokol

```text
DNS
```

### Port

```text
53
```

Port bilgisinin Packet Details bölümünden ayrıca doğrulanması gerekir.

### Timestamp

```text
1086.812787990
```

### Teknik Analiz

DNS response içerisinde:

```text
firefox.settings.services.mozilla.com
```

alan adı görülmektedir.

Bu alan adı için:

```text
CNAME → mozilla.map.fastly.net
```

yönlendirmesi ve:

```text
A → 199.232.17.91
```

IPv4 adresi gözlemlenmiştir.

Burada DNS çözümleme işleminin yalnızca doğrudan bir IP adresi döndürmekten ibaret olmadığını, CNAME gibi kayıtların da kullanılabildiğini gözlemledim.

### Güvenlik Etkisi

Gözlemlenen alan adı Mozilla Firefox servisleriyle ilişkili görünmektedir. Ancak yalnızca PCAP içerisindeki bu DNS kaydına bakarak güvenli veya kötü amaçlı şeklinde kesin bir karar vermek doğru değildir.

Güvenlik analizinde domain, IP, zaman, bağlantı sıklığı ve diğer trafik türleri birlikte değerlendirilmelidir.

### Risk

```text
Düşük / Bağlama Bağlı
```

### Önerilen Aksiyon

* DNS sorgularının geçmiş trafik ile birlikte incelenmesi
* Çözümlenen IP adreslerinin diğer bağlantılarla ilişkilendirilmesi
* Şüpheli domain/IP korelasyonu yapılması
* Gerektiğinde Threat Intelligence kaynaklarıyla kontrol edilmesi

---

## Finding 3 – DNS Trafiğinin Güvenlik Analizindeki Önemi

### Açıklama

PCAP analizi sırasında DNS trafiğinin ağ olaylarının zaman çizelgesini oluşturmak ve istemcilerin hangi domainlerle iletişim kurmaya çalıştığını anlamak açısından önemli olduğunu gözlemledim.

Örneğin:

```text
10.0.3.15 → 10.0.3.3
DNS
example.com
```

ve:

```text
10.0.3.3 → 10.0.3.15
DNS
firefox.settings.services.mozilla.com
```

şeklinde DNS iletişimleri gözlemledim.

### Kaynak

```text
10.0.3.15
10.0.3.3
```

### Hedef

```text
10.0.3.3
10.0.3.15
```

### Protokol

```text
DNS
```

### Port

```text
53
```

### Timestamp

```text
343.908320941
1086.812787990
```

### Teknik Analiz

DNS trafiğini incelerken yalnızca domain adına bakmanın yeterli olmadığını öğrendim.

Bir SOC analisti;

* hangi cihazın sorguyu yaptığını,
* hangi DNS sunucusunun kullanıldığını,
* hangi domainin sorgulandığını,
* sorgunun ne zaman gerçekleştiğini,
* response içerisinde hangi IP adreslerinin döndüğünü,
* aynı domain veya IP ile daha sonra başka bağlantılar kurulup kurulmadığını

birlikte değerlendirmelidir.

### Güvenlik Etkisi

DNS trafiği normal kullanıcı aktivitelerinin yanında saldırı aktiviteleri hakkında da önemli bilgiler sağlayabilir.

Bu nedenle DNS trafiği, olay müdahalesi sırasında incelenebilecek önemli veri kaynaklarından biridir.

### Risk

```text
Bağlama Bağlı
```

Mevcut paketler tek başına bir saldırının gerçekleştiğini göstermemektedir.

### Önerilen Aksiyon

DNS trafiğinin;

* IP trafiği,
* TCP bağlantıları,
* HTTP/HTTPS trafiği,
* zaman çizelgesi

ile birlikte korelasyonunun yapılması önerilir.

---

# 5. IOC Listesi

Analiz sırasında gözlemlenen IOC ve ilgili göstergeler aşağıdadır.

| IOC Türü | Değer                                   | Açıklama                                 |
| -------- | --------------------------------------- | ---------------------------------------- |
| IP       | `10.0.3.15`                             | DNS sorgusu yapan istemci                |
| IP       | `10.0.3.3`                              | DNS iletişiminde görülen adres           |
| IP       | `199.232.17.91`                         | DNS response içerisinde görülen A record |
| Domain   | `example.com`                           | DNS A query                              |
| Domain   | `firefox.settings.services.mozilla.com` | DNS response içerisinde görülen domain   |
| Domain   | `mozilla.map.fastly.net`                | CNAME kaydı                              |
| Port     | `53`                                    | DNS için kullanılan standart port        |
| Protocol | `DNS`                                   | Gözlemlenen protokol                     |

---

# 6. Timeline

PCAP içerisinde gözlemlediğim olayları timestamp değerlerine göre kronolojik olarak sıraladım.

| Time             | Source      | Destination | Protocol | Event                                                                                |
| ---------------- | ----------- | ----------- | -------- | ------------------------------------------------------------------------------------ |
| `343.908320941`  | `10.0.3.15` | `10.0.3.3`  | DNS      | `example.com` için A kaydı sorgusu                                                   |
| `1086.812787990` | `10.0.3.3`  | `10.0.3.15` | DNS      | `firefox.settings.services.mozilla.com` için DNS response; CNAME ve A kaydı içeriyor |

Bu zaman çizelgesini oluştururken olayların gerçek PCAP timestamp değerlerini kullandım.

Özellikle farklı DNS Query ID değerleri bulunduğu için bu iki paketin birbirinin doğrudan response'u olduğunu varsaymadım.

---

# 7. Recommendations

Analiz sonucunda ağ güvenliğini artırmak için aşağıdaki uygulamaların kullanılabileceğini değerlendirdim.

### 1. DNS Monitoring

DNS sorguları merkezi olarak izlenmeli ve şüpheli domainler tespit edilmelidir.

### 2. DNS Logging

DNS sorguları ve response kayıtları tutulmalıdır.

### 3. IOC Correlation

Tespit edilen IP ve domainler diğer ağ olaylarıyla ilişkilendirilmelidir.

### 4. Network Segmentation

Kritik sistemler birbirinden ayrılmalı ve ağ segmentleri arasında gerekli olmayan iletişimler sınırlandırılmalıdır.

### 5. Traffic Monitoring

Ağ trafiğinde olağandışı bağlantı sayısı, port kullanımı ve veri transferleri takip edilmelidir.

### 6. HTTP/HTTPS Analysis

Şüpheli durumlarda HTTP ve TLS trafiği DNS olaylarıyla birlikte değerlendirilmelidir.

### 7. Centralized Logging

Firewall, DNS, endpoint ve network cihazlarından alınan loglar merkezi bir sistemde toplanmalıdır.

### 8. SOC Correlation

Tek bir paket yerine farklı olayların zaman, IP, domain ve port bilgileri üzerinden korelasyonu yapılmalıdır.

### 9. Threat Intelligence

Şüpheli domain ve IP adresleri güvenilir Threat Intelligence kaynakları üzerinden kontrol edilmelidir.

### 10. Incident Timeline

Şüpheli bir olay tespit edildiğinde olaydan önceki ve sonraki trafik de incelenerek kronolojik bir olay akışı oluşturulmalıdır.

---

# 8. Conclusion

Bu çalışmada bir PCAP dosyasını Wireshark kullanarak güvenlik olayı perspektifinden incelemeyi amaçladım.

Analiz sırasında özellikle DNS trafiğini inceleyerek;

* kaynak ve hedef IP'leri,
* DNS sorgularını,
* DNS response yapılarını,
* domainleri,
* CNAME kayıtlarını,
* A kayıtlarını,
* timestamp bilgilerini

inceledim.

PCAP analizinde yalnızca tek bir pakete bakarak kesin bir güvenlik kararı verilmemesi gerektiğini öğrendim. Bir olayın anlamlandırılabilmesi için IP, domain, port, protokol, zaman ve bağlantı davranışlarının birlikte değerlendirilmesi gerekiyor.

Bu çalışmanın benim için önemli kısmı, Wireshark'ı yalnızca paket görüntüleme aracı olarak değil, bir güvenlik olayını araştırırken kullanılan bir analiz aracı olarak düşünmeye başlamam oldu.

Özellikle SOC analizi açısından DNS trafiğinin olayın başlangıç noktalarını ve istemcinin iletişim kurmaya çalıştığı domainleri anlamada önemli bir kaynak olduğunu gözlemledim.

Bu raporun mevcut PCAP incelemesinde gerçekten gözlemlediğim verilerle sınırlandırılması benim için önemliydi. Henüz doğrulamadığım TCP, HTTP veya başka güvenlik olaylarını rapora eklemedim.
