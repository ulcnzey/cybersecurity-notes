# 🌐 Ağ Temelleri

Siber güvenlik öğrenirken ağ yapısını anlamak temel konulardan biridir. Çünkü birçok güvenlik olayı ağ üzerinden gerçekleşir.

Bir sisteme saldırı yapılması, bir kullanıcının web sitesine bağlanması, bir sunucunun başka bir sunucuyla iletişim kurması gibi işlemlerin arkasında çeşitli ağ kavramları bulunur.

Bu çalışmada temel ağ kavramlarını ve bir kullanıcının tarayıcısına `https://example.com` yazdığında arka planda gerçekleşen süreci inceledim.

---

## 1. IP Adresi

**IP (Internet Protocol) adresi**, ağ üzerindeki bir cihazın veya ağ arayüzünün iletişim sırasında kullanılmasını sağlayan mantıksal adrestir.

Bir cihazın ağ üzerinden başka bir cihazla iletişim kurabilmesi için hedefin IP adresinin bilinmesi gerekir.

Örnek IPv4 adresi:

```text
192.168.1.10
```

IP adreslerini bir evin veya binanın adresi gibi düşünebiliriz.

> **Kısaca:** IP adresi = Cihazın ağ üzerindeki mantıksal adresi.

---

## 2. MAC Adresi

**MAC (Media Access Control) adresi**, bir ağ arayüzünün yerel ağ iletişiminde kullanılan donanımsal adresidir.

Örneğin:

```text
00:1A:2B:3C:4D:5E
```

MAC adresleri özellikle aynı yerel ağ içerisindeki iletişimde önemlidir.

Basitçe:

* IP → Ağlar arasında mantıksal adresleme
* MAC → Yerel ağda cihazın ağ arayüzünü tanımlama

> **Kısaca:** MAC adresi = Yerel ağ iletişiminde kullanılan donanımsal adres.

---

## 3. IPv4

**IPv4**, IP adreslemenin en yaygın kullanılan sürümlerinden biridir.

IPv4 adresleri **32 bit** uzunluğundadır ve genellikle dört bölüm halinde yazılır.

Örnek:

```text
192.168.1.10
```

Her bölüm 0 ile 255 arasında olabilir.

IPv4 adreslerinin sayısı sınırlı olduğu için NAT ve IPv6 gibi teknolojiler önem kazanmıştır.

---

## 4. IPv6

**IPv6**, IPv4 adres alanının yetersiz kalması nedeniyle geliştirilen daha yeni IP sürümüdür.

IPv6 adresleri **128 bit** uzunluğundadır.

Örnek:

```text
2001:db8:85a3::8a2e:370:7334
```

IPv6 çok daha büyük bir adres alanı sağlar.

> **Kısaca:**
>
> * IPv4 → 32 bit
> * IPv6 → 128 bit

---

## 5. TCP

**TCP (Transmission Control Protocol)**, cihazlar arasındaki iletişimin güvenilir ve sıralı şekilde gerçekleştirilmesini sağlayan bir taşıma katmanı protokolüdür.

TCP bağlantı kurulmasını gerektirir ve gönderilen verilerin ulaşmasını kontrol eder.

Örneğin klasik HTTPS bağlantısını anlatırken TCP bağlantısının kurulması temel adımlardan biridir.

TCP'nin önemli özellikleri:

* Bağlantı yönelimlidir.
* Verilerin sırasını takip eder.
* Kayıp verilerin yeniden gönderilmesini sağlayabilir.
* Güvenilir iletişim sağlar.

TCP bağlantısının kurulması genellikle üç aşamalı el sıkışma ile anlatılır:

```text
Client → SYN → Server
Client ← SYN-ACK ← Server
Client → ACK → Server
```

> **Kısaca:** TCP = Güvenilir ve sıralı veri iletişimi.

---

## 6. UDP

**UDP (User Datagram Protocol)**, TCP'ye göre daha az bağlantı kontrolü yapan ve daha düşük ek yükle çalışan bir taşıma katmanı protokolüdür.

UDP:

* Bağlantı kurulmasını TCP gibi zorunlu tutmaz.
* Teslim garantisi sağlamaz.
* Paketlerin sırasını garanti etmez.
* Daha düşük ek yük sağlayabilir.

UDP; gerçek zamanlı iletişim ve modern bazı internet protokollerinde kullanılabilir.

Örneğin **HTTP/3**, QUIC protokolünü kullanır ve QUIC UDP üzerinden çalışır.

> **Kısaca:** UDP = Daha az kontrol, daha düşük ek yük.

---

## 7. Port

**Port**, ağ iletişiminde aynı cihaz üzerindeki farklı hizmetlerin birbirinden ayırt edilmesine yardımcı olan mantıksal numaradır.

Örneğin:

```text
22   → SSH
80   → HTTP
443  → HTTPS
```

Port fiziksel bir bağlantı noktası değildir.

Bir IP adresini binanın adresi, portu ise binadaki belirli bir daire veya hizmet gibi düşünebiliriz.

> **Kısaca:** IP = Hangi cihaz/ağ?
> Port = Hangi hizmet?

---

## 8. Protokol

**Protokol**, cihazların birbiriyle nasıl iletişim kuracağını belirleyen kurallar bütünüdür.

Örneğin:

* HTTP → Web iletişimi
* HTTPS → Güvenli web iletişimi
* DNS → Alan adı çözümleme
* DHCP → Ağ yapılandırması
* SSH → Güvenli uzaktan erişim

İki cihazın iletişim kurabilmesi için kullandıkları protokollerin ne şekilde çalıştığını anlamaları gerekir.

> **Kısaca:** Protokol = İletişimin kuralları.

---

## 9. Yönlendirici (Router)

**Router**, farklı ağlar arasındaki veri trafiğini yönlendiren cihazdır.

Örneğin evimizdeki cihazların internete çıkmasını sağlayan modem/router cihazı, yerel ağ ile internet arasındaki iletişimde önemli bir rol oynar.

Router'ın temel görevlerinden biri:

```text
Kaynak ağ → Router → Hedef ağ
```

şeklinde paketlerin uygun yöne gönderilmesini sağlamaktır.

> **Kısaca:** Router = Ağlar arasında yönlendirme yapar.

---

## 10. Anahtar (Switch)

**Switch**, özellikle yerel ağdaki cihazların birbirleriyle iletişim kurmasını sağlayan ağ cihazıdır.

Switch, yerel ağdaki cihazların **MAC adreslerinden** yararlanarak Ethernet çerçevelerini uygun porta yönlendirir.

Örneğin bir şirkette:

```text
Bilgisayar 1 ─┐
Bilgisayar 2 ─┤
Bilgisayar 3 ─┼── Switch
Yazıcı ───────┘
```

şeklinde bir yapı bulunabilir.

> **Kısaca:** Switch = Yerel ağdaki cihazları birbirine bağlar.

---

## 11. Güvenlik Duvarı (Firewall)

**Firewall**, ağ trafiğini belirlenen güvenlik kurallarına göre kontrol eden güvenlik mekanizmasıdır.

Örneğin bir firewall:

* Belirli IP adreslerini engelleyebilir.
* Belirli portlara erişimi kontrol edebilir.
* Belirli trafik türlerine izin verebilir veya engelleyebilir.

Basit bir örnek:

```text
İnternet
   ↓
Firewall
   ↓
Şirket Ağı
```

Firewall, ağ trafiğini kontrol ederek yetkisiz veya istenmeyen bağlantıların engellenmesine yardımcı olur.

> **Kısaca:** Firewall = Ağ trafiğini kurallara göre kontrol eder.

---

## 12. DNS

**DNS (Domain Name System)**, alan adlarını IP adresleriyle eşleştiren sistemdir.

İnsanlar:

```text
example.com
```

gibi alan adlarını hatırlamayı tercih eder.

Bilgisayarlar ise iletişim kurarken IP adreslerine ihtiyaç duyar.

Basitleştirilmiş şekilde:

```text
example.com
      ↓
     DNS
      ↓
   IP adresi
```

DNS sayesinde kullanıcıların her web sitesinin IP adresini ezberlemesi gerekmez.

Ancak DNS çözümlemesi her zaman yeniden yapılmak zorunda değildir. Tarayıcı, işletim sistemi veya DNS sunucusu daha önceki sonucu önbelleğe almış olabilir.

> **Kısaca:** DNS = Alan adını IP adresine çözümlemeye yardımcı olur.

---

## 13. DHCP

**DHCP (Dynamic Host Configuration Protocol)**, cihazların ağa bağlandığında gerekli ağ yapılandırmalarını otomatik olarak almasını sağlar.

Örneğin DHCP bir cihaza:

* IP adresi
* Alt ağ maskesi
* Varsayılan ağ geçidi
* DNS sunucusu

gibi bilgileri sağlayabilir.

Bu sayede ağdaki her cihaza bu bilgilerin manuel olarak girilmesi gerekmez.

> **Kısaca:** DHCP = Cihazın ağ ayarlarını otomatik almasına yardımcı olur.

---

## 14. NAT

**NAT (Network Address Translation)**, ağ adreslerinin başka adreslerle eşleştirilmesini sağlayan bir mekanizmadır.

Özellikle IPv4 ağlarında özel (private) IP adreslerinin internet üzerindeki genel (public) adres üzerinden iletişim kurmasında yaygın olarak kullanılır.

Örneğin ev ağında:

```text
Telefon       → 192.168.1.10
Laptop        → 192.168.1.11
Tablet        → 192.168.1.12
                     ↓
                  Router
                     ↓
              Public IP
                     ↓
                 İnternet
```

Burada NAT, iç ağdaki adreslerin internet tarafındaki iletişimle eşleştirilmesine yardımcı olabilir.

> **Kısaca:** NAT = Ağ adreslerinin dönüştürülmesine/eşleştirilmesine yardımcı olur.

---

## 15. Geçit (Gateway)

**Gateway**, bir ağdan başka bir ağa geçiş sağlayan ağ noktasıdır.

Ev ağında çoğu zaman **default gateway**, router'ın yerel ağ üzerindeki adresidir.

Örneğin:

```text
Laptop
   ↓
Default Gateway
   ↓
Router
   ↓
Internet
```

Bir cihaz hedefin kendi yerel ağında olmadığını düşündüğünde trafiği varsayılan ağ geçidine gönderebilir.

> **Kısaca:** Gateway = Başka bir ağa çıkış noktası.

---

## 16. HTTP

**HTTP (Hypertext Transfer Protocol)**, web istemcileri ile web sunucuları arasındaki iletişimde kullanılan bir uygulama katmanı protokolüdür.

Örneğin tarayıcı bir web sunucusuna HTTP isteği gönderebilir:

```text
GET /
```

Sunucu da bir HTTP cevabı döndürür:

```text
HTTP/1.1 200 OK
```

HTTP kendi başına iletişimi şifrelemez.

> **Kısaca:** HTTP = Web istemcisi ile sunucu arasındaki iletişim protokolü.

---

## 17. HTTPS

**HTTPS (HTTP Secure)**, HTTP iletişiminin **TLS** ile korunmuş halidir.

HTTPS sayesinde iletişimde temel olarak:

* Gizlilik
* Bütünlük
* Sunucu kimlik doğrulaması

sağlanmasına yardımcı olunur.

Web sitelerinde genellikle:

```text
HTTPS → Port 443
```

kullanılır.

Tarayıcı bir HTTPS bağlantısı kurarken sunucunun sertifikasını da doğrular.

> **Kısaca:** HTTPS = HTTP + TLS ile korunan web iletişimi.

---

## 18. SSH

**SSH (Secure Shell)**, uzak sistemlere güvenli şekilde erişmek ve yönetmek için kullanılan bir protokoldür.

Genellikle:

```text
SSH → Port 22
```

kullanılır.

Örneğin bir sistem yöneticisi uzaktaki bir Linux sunucusuna SSH üzerinden bağlanabilir.

SSH iletişimi şifreli olduğu için Telnet gibi şifrelenmemiş uzaktan erişim yöntemlerine göre daha güvenli bir yapı sağlar.

> **Kısaca:** SSH = Güvenli uzak erişim.

---

# 📌 Temel Ağ Kavramları Özet Tablosu

| Kavram   | Kısaca Görevi                                  |
| -------- | ---------------------------------------------- |
| IP       | Ağ üzerindeki mantıksal adresleme              |
| MAC      | Yerel ağdaki ağ arayüzü adresi                 |
| IPv4     | 32 bit IP adresleme                            |
| IPv6     | 128 bit IP adresleme                           |
| TCP      | Güvenilir ve sıralı iletişim                   |
| UDP      | Daha düşük ek yükle iletişim                   |
| Port     | Hizmetleri birbirinden ayırma                  |
| Protokol | İletişim kuralları                             |
| Router   | Ağlar arasında yönlendirme                     |
| Switch   | Yerel ağdaki cihazları bağlama                 |
| Firewall | Trafiği güvenlik kurallarına göre kontrol etme |
| DNS      | Alan adını IP adresine çözümleme               |
| DHCP     | Ağ yapılandırmasını otomatik sağlama           |
| NAT      | Ağ adreslerini dönüştürme/eşleme               |
| Gateway  | Başka bir ağa çıkış noktası                    |
| HTTP     | Web iletişimi                                  |
| HTTPS    | TLS ile korunan HTTP                           |
| SSH      | Güvenli uzak erişim                            |

---

# 🌐 `https://example.com` Yazdığımda Arka Planda Ne Oluyor?

Bir kullanıcının tarayıcıya:

```text
https://example.com
```

yazması basit görünse de arka planda birden fazla işlem gerçekleşir.

Bunu öğrenirken aşağıdaki genel akışı kullanabilirim:

```text
KULLANICI
    ↓
TARAYICI
    ↓
DNS
    ↓
IP ADRESİ
    ↓
YEREL AĞ / GATEWAY / ROUTER
    ↓
HEDEF SUNUCU
    ↓
TCP
    ↓
TLS
    ↓
HTTPS
    ↓
HTTP REQUEST
    ↓
WEB SUNUCUSU
    ↓
HTTP RESPONSE
    ↓
TARAYICI
    ↓
WEB SAYFASI
```

Şimdi bu süreci adım adım inceleyelim.

---

## 1. Kullanıcı URL'yi Girer

Kullanıcı tarayıcıya:

```text
https://example.com
```

yazar.

Tarayıcı URL'yi parçalara ayırır:

```text
https → Kullanılan protokol/şema
example.com → Alan adı
```

HTTPS için standart bağlantı noktası genellikle:

```text
443
```

numaralı porttur.

---

## 2. DNS ile IP Adresi Bulunur

Tarayıcının `example.com` ile iletişim kurabilmesi için hedef sunucunun IP adresine ihtiyaç vardır.

Bu nedenle DNS çözümlemesi yapılabilir.

Basitleştirilmiş olarak:

```text
example.com
      ↓
     DNS
      ↓
IP adresi
```

Ancak bu işlem her bağlantıda sıfırdan gerçekleşmek zorunda değildir.

Tarayıcı, işletim sistemi veya DNS altyapısındaki önbelleklerde daha önce alınmış bir sonuç bulunabilir.

---

## 3. Hedefe Ulaşmak İçin Ağ Üzerinden Yönlendirme Yapılır

Tarayıcı artık hedef IP adresini biliyorsa veri paketlerinin hedefe ulaştırılması gerekir.

Hedef aynı yerel ağda değilse cihaz genellikle trafiği **default gateway** üzerinden gönderir.

Örneğin:

```text
Laptop
   ↓
Switch / Wi-Fi
   ↓
Default Gateway
   ↓
Router
   ↓
Internet
   ↓
Hedef Sunucu
```

Bu noktada router'lar paketlerin farklı ağlar arasında yönlendirilmesinde rol oynar.

---

## 4. Yerel Ağda MAC Adresleri Kullanılabilir

Örneğin cihazın hedefi doğrudan yerel ağda değilse, cihazın ilk olarak paketi göndermesi gereken yer çoğu durumda default gateway'dir.

IPv4 kullanılan yerel ağlarda cihaz, gateway'in IP adresini ilgili MAC adresiyle eşleştirmek için **ARP** kullanabilir.

Böylece:

```text
IP adresi → MAC adresi
```

eşleştirmesi yapılabilir.

Burada önemli nokta:

> IP adresleri ağlar arasında mantıksal adresleme için, MAC adresleri ise yerel ağdaki Ethernet iletişiminde kullanılır.

---

## 5. TCP Bağlantısı Kurulabilir

Klasik HTTPS bağlantısını temel model olarak ele aldığımızda TCP bağlantısı kurulur.

TCP bağlantısı:

```text
Client → SYN → Server
Client ← SYN-ACK ← Server
Client → ACK → Server
```

şeklinde üç aşamalı el sıkışma ile başlar.

Böylece iki taraf TCP bağlantısını oluşturmuş olur.

---

## 6. TLS ile Güvenli Bağlantı Kurulur

HTTPS'in önemli kısmı burada başlar.

Tarayıcı ile sunucu arasında **TLS** bağlantısı kurulur.

Tarayıcı sunucunun dijital sertifikasını kontrol eder.

Bu süreçte amaçlardan biri, kullanıcının gerçekten bağlanmak istediği alan adıyla ilişkili sunucuyla güvenli iletişim kurduğunu doğrulamaktır.

Ayrıca taraflar iletişimi korumak için gerekli kriptografik bilgileri oluşturur.

Sonuç olarak:

```text
HTTP
  +
TLS
  ↓
HTTPS
```

şeklinde düşünebiliriz.

---

## 7. HTTP Request Gönderilir

TLS bağlantısı kurulduktan sonra HTTP isteği bu güvenli bağlantı üzerinden gönderilir.

Basitleştirilmiş bir HTTP isteği şöyle düşünülebilir:

```text
GET / HTTP/1.1
Host: example.com
```

Buradaki:

```text
GET
```

sunucudan bir kaynağın istenmesini ifade eder.

---

## 8. Web Sunucusu İsteği İşler

Web sunucusu gelen isteği değerlendirir.

İstenen kaynak bulunuyorsa uygun bir HTTP cevabı hazırlanır.

Örneğin:

```text
HTTP/1.1 200 OK
```

gibi bir durum kodu döndürülebilir.

---

## 9. HTTP Response Tarayıcıya Gönderilir

Sunucu, HTTP cevabını tarayıcıya gönderir.

Bu cevap içerisinde HTML gibi içerikler bulunabilir.

Basitleştirilmiş olarak:

```text
Tarayıcı
   ↓
HTTP Request
   ↓
Sunucu
   ↓
HTTP Response
   ↓
Tarayıcı
```

şeklinde düşünebiliriz.

---

## 10. Tarayıcı Web Sayfasını Oluşturur

Tarayıcı gelen HTML'i işler.

Sayfanın ihtiyaç duyduğu CSS, JavaScript, görseller ve diğer kaynaklar için ek HTTP istekleri de yapılabilir.

Sonunda kullanıcı web sayfasını ekranda görür.

```text
HTML
 ↓
CSS
 ↓
JavaScript
 ↓
Görseller
 ↓
WEB SAYFASI
```

---

# ⚠️ Önemli Not: HTTPS Her Zaman TCP Kullanmaz

Temel seviyede HTTPS anlatılırken genellikle:

```text
HTTPS → TCP → TLS → HTTP
```

şeklinde bir akış gösterilir.

Bu, klasik ve öğrenmek için çok faydalı bir modeldir.

Ancak modern web teknolojilerinde **HTTP/3**, **QUIC** protokolünü kullanır ve QUIC **UDP** üzerinde çalışır.

Bu nedenle daha doğru genel ifade:

```text
Klasik HTTPS:
HTTP → TLS → TCP → IP

HTTP/3:
HTTP/3 → QUIC → UDP → IP
```

şeklindedir.

Şimdilik temel mantığı öğrenirken TCP tabanlı HTTPS akışını anlamak yeterlidir. Daha sonra HTTP/2, HTTP/3 ve QUIC konularına ayrıca girilebilir.

---

# 🧠 Benim İçin Kısa Zihin Haritası

Ağ temellerini öğrenirken şu bağlantıyı aklımda tutabilirim:

```text
DOMAIN
  ↓
DNS
  ↓
IP
  ↓
ROUTER / GATEWAY
  ↓
NETWORK
  ↓
TCP veya UDP
  ↓
TLS
  ↓
HTTP
  ↓
SERVER
  ↓
RESPONSE
  ↓
BROWSER
```

Ve temel kavramları tek kelimeyle hatırlamak için:

```text
IP       → Adres
MAC      → Yerel kimlik
DNS      → İsim çözümleme
DHCP     → Ağ ayarı
Router   → Yönlendir
Switch   → Bağla
Gateway  → Çıkış
NAT      → Adres dönüştür
Port     → Hizmet
TCP      → Güvenilir iletişim
UDP      → Hafif iletişim
Firewall → Kontrol et
HTTP     → Web
HTTPS    → Güvenli web
SSH      → Uzak erişim
```

---

# ✍️ Kendi Öğrenme Notlarım

Bu konuyu çalışırken benim için en önemli nokta, kavramları tek tek ezberlemek yerine aralarındaki ilişkiyi anlamaktır.

Örneğin bir web sitesine bağlanırken:

> Önce alan adı kullanılır. DNS bu alan adının IP adresinin bulunmasına yardımcı olur. Daha sonra paketlerin hedefe ulaşması için ağ üzerinde yönlendirme yapılır. Klasik HTTPS bağlantısında TCP bağlantısı kurulur ve TLS ile güvenli iletişim oluşturulur. Ardından HTTP isteği sunucuya gönderilir. Sunucu HTTP cevabını tarayıcıya gönderir ve tarayıcı web sayfasını oluşturur.

Bu süreci anlayabilmek, daha sonra ağ güvenliği, web güvenliği, SOC ve saldırı analizleri gibi konuları öğrenirken temel oluşturacaktır.

---

# 🎯 Kısa Özet

Bu çalışmada şu temel kavramları öğrendim:

* IP ve MAC adresleri farklı amaçlarla kullanılır.
* IPv4 32 bit, IPv6 128 bittir.
* TCP güvenilir ve sıralı iletişim sağlar.
* UDP daha düşük ek yükle çalışan bir taşıma protokolüdür.
* Portlar cihaz üzerindeki hizmetlerin ayırt edilmesine yardımcı olur.
* Router farklı ağlar arasında yönlendirme yapar.
* Switch yerel ağdaki cihazları birbirine bağlar.
* DNS alan adlarının IP adresleriyle eşleştirilmesine yardımcı olur.
* DHCP ağ yapılandırmasını otomatik olarak sağlayabilir.
* NAT adreslerin dönüştürülmesine/eşleştirilmesine yardımcı olur.
* Gateway başka bir ağa çıkış noktasıdır.
* Firewall ağ trafiğini güvenlik kurallarına göre kontrol eder.
* HTTP web iletişiminde kullanılır.
* HTTPS, HTTP iletişimini TLS ile korur.
* SSH güvenli uzak erişim sağlar.

En önemli öğrendiğim akış ise:

```text
https://example.com
        ↓
       DNS
        ↓
    IP Adresi
        ↓
Gateway / Router
        ↓
      Ağ
        ↓
 TCP / QUIC
        ↓
       TLS
        ↓
      HTTP
        ↓
    Web Sunucusu
        ↓
  HTTP Response
        ↓
     Tarayıcı
```

Bu akış, siber güvenlikte ağ ve web güvenliğini anlamak için temel modellerden biridir.

---

# 📚 Kaynaklar

* NIST — National Institute of Standards and Technology
* CISA — Cybersecurity and Infrastructure Security Agency
* Cisco Networking Academy
* Cloudflare Learning Center
* RFC — Internet Engineering Task Force (IETF)
