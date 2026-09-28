# 20. IOC Çıkarma

## 1. IOC Nedir?

IOC (Indicator of Compromise), bir sistemde veya ağ trafiğinde güvenlik açısından incelenmesi gereken bir duruma işaret edebilecek göstergelerdir.

Bu bölümde bir önceki bölümde analiz ettiğim PCAP dosyasından mümkün olduğunca fazla IOC çıkarmaya çalıştım.

Amacım, PCAP içerisindeki önemli bilgileri daha düzenli bir şekilde toplamak ve gerektiğinde bu bilgileri başka güvenlik analizlerinde kullanabilecek hale getirmekti.

IOC olarak özellikle şu bilgileri incelemeye çalıştım:

* IP adresleri
* Domainler
* Portlar
* Timestamp bilgileri
* User-Agent
* URI
* HTTP Host
* DNS Query
* TLS bilgileri

---

# 2. IOC Çıkarma Yaklaşımım

IOC çıkarırken PCAP içerisindeki her bilgiyi doğrudan IOC olarak değerlendirmedim.

Öncelikle trafiği inceleyerek güvenlik analizi açısından anlamlı olabilecek bilgileri belirledim.

İzlediğim genel süreç:

```text
PCAP
  ↓
Ağ trafiğini inceleme
  ↓
IP adreslerini belirleme
  ↓
Domainleri belirleme
  ↓
Port ve protokolleri belirleme
  ↓
DNS / HTTP / TLS bilgilerini inceleme
  ↓
Timestamp bilgilerini kaydetme
  ↓
IOC tablosunu oluşturma
```

Buradaki amacım, daha önce yaptığım PCAP analizindeki bulguları daha yapılandırılmış bir hale getirmekti.

---

# 3. IP Adresi IOC'leri

İlk olarak PCAP içerisinde dikkat çeken kaynak ve hedef IP adreslerini belirledim.

## Source IP

```text
SOURCE_IP
```

## Destination IP

```text
DESTINATION_IP
```

Birden fazla IP adresi bulunması durumunda bunları aşağıdaki tabloda topladım.

| Tür            | IP Adresi    | Gözlem   |
| -------------- | ------------ | -------- |
| Source IP      | `IP_ADDRESS` | `GÖZLEM` |
| Source IP      | `IP_ADDRESS` | `GÖZLEM` |
| Destination IP | `IP_ADDRESS` | `GÖZLEM` |
| Destination IP | `IP_ADDRESS` | `GÖZLEM` |

IP adreslerini incelerken özellikle hangi kaynak IP'nin hangi hedeflerle iletişim kurduğuna dikkat ettim.

---

# 4. Domain IOC'leri

Daha sonra DNS ve HTTP trafiğinde görülen domainleri inceledim.

DNS sorgularını görmek için Wireshark'ta:

```text
dns.qry.name
```

filtresini kullandım.

HTTP Host bilgilerini incelemek için ise HTTP paketlerinin içerisindeki Host alanına baktım.

## Domainler

| Domain   | Kaynak | Tür       | Gözlem   |
| -------- | ------ | --------- | -------- |
| `DOMAIN` | DNS    | DNS Query | `GÖZLEM` |
| `DOMAIN` | HTTP   | HTTP Host | `GÖZLEM` |

### Domain

```text
DOMAIN
```

Domainleri değerlendirirken özellikle beklenmeyen veya olağandışı görünen domainlere dikkat ettim.

Ancak bir domainin tanımadığım bir domain olması tek başına kötü amaçlı olduğu anlamına gelmediği için diğer trafik bilgileriyle birlikte değerlendirdim.

---

# 5. Port ve Protokol IOC'leri

PCAP içerisindeki iletişimlerde kullanılan portları ve protokolleri de IOC tablosuna ekledim.

|   Port | Protocol   | Source IP    | Destination IP | Gözlem   |
| -----: | ---------- | ------------ | -------------- | -------- |
| `PORT` | `PROTOCOL` | `IP_ADDRESS` | `IP_ADDRESS`   | `GÖZLEM` |
| `PORT` | `PROTOCOL` | `IP_ADDRESS` | `IP_ADDRESS`   | `GÖZLEM` |

## Port

```text
PORT
```

## Protocol

```text
PROTOCOL
```

Port bilgisini değerlendirirken portun hangi servise karşılık geldiğine de baktım.

Örneğin:

```text
80  → HTTP
443 → HTTPS
53  → DNS
22  → SSH
```

Bu bilgiler sayesinde yalnızca port numarasını değil, iletişimin hangi servisle ilişkili olduğunu da anlamaya çalıştım.

---

# 6. Timestamp Bilgileri

IOC çıkarırken zaman bilgisini de kaydetmenin önemli olduğunu öğrendim.

Çünkü bir ağ olayını incelerken sadece **ne olduğunu** değil, **ne zaman gerçekleştiğini** de bilmek gerekir.

Belirlediğim önemli zaman bilgileri:

| Timestamp   | Olay    | Kaynak IP    | Hedef IP     |
| ----------- | ------- | ------------ | ------------ |
| `TIMESTAMP` | `EVENT` | `IP_ADDRESS` | `IP_ADDRESS` |
| `TIMESTAMP` | `EVENT` | `IP_ADDRESS` | `IP_ADDRESS` |

### Timestamp

```text
TIMESTAMP
```

Timestamp bilgilerini kullanarak olayların sıralamasını ve birbirleriyle olan ilişkisini inceleyebilirim.

Örneğin:

```text
DNS Query
   ↓
DNS Response
   ↓
TCP Connection
   ↓
HTTP Request
```

şeklinde bir olay zinciri varsa bunların zaman sırasını incelemek olayın anlaşılmasına yardımcı olabilir.

---

# 7. User-Agent

HTTP trafiğinde User-Agent bilgisini de inceledim.

User-Agent, HTTP isteğini gerçekleştiren istemci hakkında bilgi verebilir.

### User-Agent

```text
USER_AGENT
```

### IOC Tablosu

| Alan       | Değer        | Gözlem   |
| ---------- | ------------ | -------- |
| User-Agent | `USER_AGENT` | `GÖZLEM` |

User-Agent bilgisini tek başına kötü amaçlı bir gösterge olarak değerlendirmedim. Ancak beklenmeyen bir istemci veya olağandışı bir User-Agent görülmesi durumunda ilgili HTTP trafiğinin daha ayrıntılı incelenebileceğini düşündüm.

---

# 8. URI

HTTP isteklerinde kullanılan URI bilgilerini de inceledim.

### URI

```text
/URI
```

### IOC Tablosu

| Method | URI    | Host   | Kaynak IP    | Hedef IP     |
| ------ | ------ | ------ | ------------ | ------------ |
| `GET`  | `/URI` | `HOST` | `IP_ADDRESS` | `IP_ADDRESS` |
| `POST` | `/URI` | `HOST` | `IP_ADDRESS` | `IP_ADDRESS` |

URI bilgisini incelerken özellikle:

* Olağandışı path'ler
* Beklenmeyen endpointler
* Şüpheli parametreler
* Çok sayıda başarısız istek

gibi durumlara dikkat ettim.

---

# 9. HTTP Host

HTTP paketlerindeki Host bilgisini de IOC tablosuna ekledim.

### HTTP Host

```text
HOST_DOMAIN
```

| Alan      | Değer         |
| --------- | ------------- |
| HTTP Host | `HOST_DOMAIN` |

HTTP Host bilgisini DNS sorguları ve hedef IP adresleriyle birlikte değerlendirmek, istemcinin hangi web servisine ulaşmaya çalıştığını anlamama yardımcı oldu.

---

# 10. DNS Query

DNS trafiğinde gerçekleştirilen sorguları ayrıca çıkardım.

### DNS Query

```text
DNS_QUERY
```

| Timestamp   | Source IP   | DNS Server      | DNS Query |
| ----------- | ----------- | --------------- | --------- |
| `TIMESTAMP` | `SOURCE_IP` | `DNS_SERVER_IP` | `DOMAIN`  |

DNS Query bilgisini incelerken özellikle tekrar eden ve olağandışı görünen sorgulara dikkat ettim.

---

# 11. TLS Bilgileri

PCAP içerisinde TLS trafiği bulunuyorsa TLS paketlerini de incelemeye çalıştım.

İncelediğim bilgiler:

* TLS version
* Server Name (SNI)
* Certificate bilgileri
* Destination IP
* Destination Port

Özellikle SNI bilgisinin, şifreli HTTPS trafiğinde hangi domain ile iletişim kurulmaya çalışıldığını anlamak açısından faydalı olabileceğini öğrendim.

### TLS Bilgileri

| Alan             | Değer              |
| ---------------- | ------------------ |
| TLS Version      | `TLS_VERSION`      |
| SNI              | `SNI_DOMAIN`       |
| Destination IP   | `IP_ADDRESS`       |
| Destination Port | `443`              |
| Certificate      | `CERTIFICATE_INFO` |

Eğer PCAP içerisinde TLS bilgisi bulunmuyorsa bu alanı:

```text
TLS bilgisi tespit edilmedi.
```

şeklinde belirteceğim.

---

# 12. Genel IOC Tablosu

Analiz sonunda elde ettiğim IOC'leri tek bir tabloda toplamaya çalıştım.

| IOC Türü   | Değer        | Protocol   | Kaynak   | Hedef         | Timestamp | Açıklama |
| ---------- | ------------ | ---------- | -------- | ------------- | --------- | -------- |
| IP         | `IP_ADDRESS` | `TCP`      | `SOURCE` | `DESTINATION` | `TIME`    | `GÖZLEM` |
| Domain     | `DOMAIN`     | `DNS`      | `SOURCE` | `DNS_SERVER`  | `TIME`    | `GÖZLEM` |
| Port       | `PORT`       | `PROTOCOL` | `SOURCE` | `DESTINATION` | `TIME`    | `GÖZLEM` |
| User-Agent | `USER_AGENT` | `HTTP`     | `SOURCE` | `DESTINATION` | `TIME`    | `GÖZLEM` |
| URI        | `/URI`       | `HTTP`     | `SOURCE` | `DESTINATION` | `TIME`    | `GÖZLEM` |
| HTTP Host  | `HOST`       | `HTTP`     | `SOURCE` | `DESTINATION` | `TIME`    | `GÖZLEM` |
| DNS Query  | `DOMAIN`     | `DNS`      | `SOURCE` | `DNS_SERVER`  | `TIME`    | `GÖZLEM` |
| SNI        | `SNI_DOMAIN` | `TLS`      | `SOURCE` | `DESTINATION` | `TIME`    | `GÖZLEM` |

---

# 13. IOC'lerin Güvenlik Analizindeki Önemi

Bu çalışmada IOC çıkarmanın sadece bilgileri bir tabloya yazmak olmadığını anladım.

Çıkardığım IOC'ler daha sonraki güvenlik analizlerinde tekrar kullanılabilir.

Örneğin bir IP adresi şüpheli olarak değerlendirildiyse:

```text
Şüpheli IP
    ↓
Diğer bağlantıları ara
    ↓
DNS trafiğini incele
    ↓
HTTP/TLS trafiğini incele
    ↓
Zaman çizelgesini oluştur
    ↓
İlgili diğer IOC'leri ilişkilendir
```

Bu nedenle IOC'leri mümkün olduğunca olayın diğer parçalarıyla ilişkilendirmeye çalıştım.

---

# 14. Sonuç

Bu çalışmada incelediğim PCAP içerisinden mümkün olduğunca fazla IOC çıkarmaya çalıştım.

Özellikle:

* IP adresleri
* Domainler
* Portlar
* Protokoller
* Timestamp bilgileri
* User-Agent
* URI
* HTTP Host
* DNS Query
* TLS bilgileri

üzerinde çalıştım.

Bu çalışmayla birlikte PCAP analizinden elde edilen ham bilgilerin daha sonra güvenlik operasyonlarında kullanılabilecek **yapılandırılmış IOC bilgilerine dönüştürülebileceğini** öğrendim.

Benim için önemli olan noktalardan biri de IOC'lerin tek başına değerlendirilmemesi gerektiği oldu. Bir IP adresi, domain veya port ancak bağlantılı ağ trafiği ve olayın bağlamı ile birlikte incelendiğinde daha anlamlı hale geliyor.

Bu nedenle IOC çıkarma sürecini, PCAP analizinin devamı ve güvenlik olaylarının daha sonra araştırılabilmesi için önemli bir aşama olarak değerlendirdim.
