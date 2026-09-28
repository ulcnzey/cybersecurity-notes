# 22. SOC Analisti Perspektifi

## 1. Senaryo

Bu bölümde bir SOC analisti olduğumu düşünerek aşağıdaki senaryoyu değerlendirdim:

> Bir şirketin çalışan bilgisayarlarından birinin olağandışı davranış gösterdiği bildiriliyor.

Bu durumda doğrudan "bilgisayara saldırı olmuş" şeklinde bir sonuca varmak yerine, önce elimdeki ağ ve sistem verilerini toplamaya ve olağandışı davranışın ne olduğunu anlamaya çalışırım.

Öncelikle kaynağı, hedefi, zamanı, kullanılan protokolleri ve bağlantı davranışını inceleyerek olayın kapsamını belirlemeye çalışırım.

---

# 2. Olay İnceleme Yaklaşımım

Bir SOC analisti olarak ilk aşamada amacım saldırının ne olduğunu tahmin etmek değil, **kanıtları toplayarak olayın ne olduğunu anlamaktır.**

Genel yaklaşımımı şu şekilde sıralarım:

```text
Olay bildirimi
     ↓
Etkilenen cihazı belirleme
     ↓
Kaynak / hedef bilgilerini inceleme
     ↓
Ağ trafiğini analiz etme
     ↓
DNS / HTTP / HTTPS inceleme
     ↓
Port ve bağlantıları kontrol etme
     ↓
Zaman çizelgesi oluşturma
     ↓
IOC'leri belirleme
     ↓
Şüpheli davranışları ilişkilendirme
     ↓
Olayı değerlendirme
```

---

# 3. SOC Olay İnceleme Kontrol Listesi

## 1. Source IP

İlk olarak olağandışı davranış gösteren bilgisayarın **Source IP** adresini belirlerim.

Şunları kontrol ederim:

* Hangi cihaz bu IP'yi kullanıyor?
* IP adresi şirkette hangi kullanıcı veya sistemle ilişkili?
* IP daha önce de benzer davranış göstermiş mi?
* Aynı IP'den başka sistemlere bağlantılar var mı?

Source IP'yi belirlemek, olayın hangi cihazdan başladığını anlamak için ilk adımlardan biridir.

---

## 2. Destination IP

Daha sonra bilgisayarın hangi sistemlerle iletişim kurduğunu incelerim.

Özellikle:

* Hangi IP adreslerine bağlanılmış?
* İç ağdaki sistemlere mi?
* İnternetteki sistemlere mi?
* Aynı hedefe çok sayıda bağlantı var mı?
* Beklenmeyen bir IP ile iletişim kurulmuş mu?

gibi sorulara bakarım.

---

## 3. DNS Trafiği

DNS trafiğini özellikle incelemek isterim.

Çünkü bir bilgisayarın hangi domainleri çözümlemeye çalıştığı, hangi servislerle iletişim kurduğunu anlamama yardımcı olabilir.

Kontrol edeceğim bilgiler:

* DNS Server
* DNS Query
* Sorgulanan domain
* Sorgu sıklığı
* Domain yapısı
* DNS Response
* Çözümlenen IP adresleri

Özellikle beklenmeyen veya olağandışı domainleri daha ayrıntılı incelemek isterim.

---

## 4. HTTP Trafiği

HTTP trafiği varsa bunu ayrıca incelerim.

Kontrol edeceğim bilgiler:

* HTTP Host
* URI
* HTTP Method
* Status Code
* User-Agent
* Source IP
* Destination IP

Özellikle olağandışı URI'ler, beklenmeyen domainler veya çok sayıda başarısız HTTP isteği olup olmadığına bakarım.

---

## 5. HTTPS / TLS Trafiği

HTTPS trafiğinde içerik şifreli olduğu için HTTP'deki kadar detay göremeyebilirim.

Buna rağmen TLS trafiğinden bazı bilgiler elde edebilirim.

Örneğin:

* TLS Version
* SNI
* Destination IP
* Destination Port
* Certificate bilgileri
* Bağlantı zamanı

gibi bilgileri incelerim.

Özellikle HTTPS bağlantısının hangi domain ile ilişkili olduğunu anlamak için SNI bilgisi benim için önemli olabilir.

---

## 6. TCP Trafiği

TCP bağlantılarını inceleyerek bilgisayarın nasıl bağlantılar kurduğunu anlamaya çalışırım.

Kontrol edeceğim bilgiler:

* SYN
* SYN/ACK
* ACK
* FIN
* RST
* Kaynak port
* Hedef port
* Bağlantı süresi

Özellikle kısa bir zaman içerisinde çok sayıda bağlantı kurulması veya farklı portlara yönelik bağlantı denemeleri varsa bunu daha ayrıntılı araştırırım.

---

## 7. UDP Trafiği

Sadece TCP'ye odaklanmam.

UDP trafiğini de kontrol ederim.

Özellikle:

* DNS
* DHCP
* NTP
* Diğer UDP servisleri

üzerindeki trafiği incelerim.

Beklenmeyen bir UDP hedefi veya olağandışı trafik miktarı varsa ilgili bağlantıyı daha ayrıntılı araştırırım.

---

## 8. Kullanılan Portlar

Bilgisayarın hangi portlarla iletişim kurduğunu kontrol ederim.

Örneğin:

```text
22  → SSH
53  → DNS
80  → HTTP
443 → HTTPS
```

gibi yaygın servislerin yanı sıra beklenmeyen portları da incelemeye çalışırım.

Özellikle bir bilgisayarın çok sayıda farklı porta bağlantı kurması veya normalde kullanmadığı bir servise erişmesi dikkatimi çeker.

---

## 9. Zaman Bilgileri

Olayın ne zaman başladığını belirlemek benim için çok önemlidir.

Bu nedenle:

* İlk şüpheli bağlantı
* İlk DNS sorgusu
* İlk TCP bağlantısı
* İlk HTTP/HTTPS bağlantısı
* Son bağlantı
* Bağlantıların sıklığı

gibi zaman bilgilerini çıkarırım.

Daha sonra bunları bir **Incident Timeline** üzerinde sıralarım.

Örneğin:

```text
10:21:03 → DNS Query
10:21:05 → TCP Connection
10:21:07 → HTTP Request
10:21:10 → Yeni bağlantı
```

şeklinde bir olay akışı oluşturmaya çalışırım.

---

## 10. Veri Miktarı

Bağlantıların sadece sayısına değil, aktarılan veri miktarına da bakarım.

Özellikle:

* Download miktarı
* Upload miktarı
* Toplam byte
* Olağandışı büyük veri transferleri
* Kısa sürede gerçekleşen yoğun trafik

gibi bilgileri kontrol ederim.

Örneğin normalde çok az veri gönderen bir bilgisayarın aniden dışarıya büyük miktarda veri göndermesi daha ayrıntılı incelenmesi gereken bir durum olabilir.

---

## 11. Bağlantı Sayısı

Bilgisayarın kaç bağlantı oluşturduğunu kontrol ederim.

Özellikle:

* Toplam bağlantı sayısı
* Kısa sürede açılan bağlantılar
* Başarısız bağlantılar
* Aynı hedefe yapılan tekrar bağlantılar
* Farklı hedeflere yapılan bağlantılar

gibi bilgileri değerlendiririm.

Çok sayıda bağlantı olması tek başına kötü amaçlı davranış anlamına gelmez. Ancak diğer bulgularla birlikte anlamlı hale gelebilir.

---

## 12. User-Agent

HTTP trafiğinde User-Agent bilgisini kontrol ederim.

Çünkü User-Agent üzerinden isteğin hangi istemci veya uygulama tarafından gönderildiği hakkında bilgi edinebilirim.

Örneğin kullanıcının normalde kullandığı tarayıcı dışında beklenmeyen bir User-Agent görülmesi durumunda ilgili trafiği incelemeye devam ederim.

---

## 13. IOC Kontrolü

Önceki bölümde öğrendiğim IOC yaklaşımını burada da kullanırım.

Şüpheli görünen:

* IP adresleri
* Domainler
* URI'ler
* Portlar
* User-Agent'lar
* SNI bilgileri

gibi göstergeleri toplarım.

Daha sonra bunları diğer güvenlik kayıtlarıyla karşılaştırabilirim.

---

## 14. Önceki ve Sonraki Trafik

Şüpheli bir paket bulduğumda yalnızca o pakete bakmam.

Olayın:

**öncesini → olayın kendisini → sonrasını**

incelemeye çalışırım.

Örneğin:

```text
Normal trafik
      ↓
DNS Query
      ↓
Şüpheli IP çözümlemesi
      ↓
TCP bağlantısı
      ↓
HTTPS bağlantısı
      ↓
Yüksek veri transferi
```

gibi bir zincir varsa olayları birlikte değerlendirmek daha anlamlı olabilir.

---

## 15. Kullanıcı ve Cihaz Bilgileri

Ağ trafiğinin yanında olayın gerçekleştiği cihaz hakkında da bilgi toplarım.

Örneğin:

* Bilgisayar adı
* Kullanıcı hesabı
* İşletim sistemi
* Cihazın şirket ağı içerisindeki rolü
* Daha önceki benzer olaylar
* Cihazda çalışan uygulamalar

gibi bilgileri kontrol ederim.

Böylece ağ trafiğini cihazın normal kullanım davranışıyla karşılaştırabilirim.

---

# 4. Kontrol Listemin Özeti

Olay incelemesine başlarken kullanacağım temel kontrol listesi:

|  # | Kontrol         | İncelenecek Bilgi                   |
| -: | --------------- | ----------------------------------- |
|  1 | Source IP       | Olayı başlatan cihaz                |
|  2 | Destination IP  | İletişim kurulan sistem             |
|  3 | DNS             | Domain ve DNS sorguları             |
|  4 | HTTP            | Host, URI, Method, Status Code      |
|  5 | HTTPS/TLS       | SNI, TLS, Certificate               |
|  6 | TCP             | SYN, ACK, FIN, RST                  |
|  7 | UDP             | UDP bağlantıları ve servisler       |
|  8 | Portlar         | Kullanılan servisler                |
|  9 | Zaman           | Olayların kronolojik sırası         |
| 10 | Veri miktarı    | Upload / Download / Byte            |
| 11 | Bağlantı sayısı | Toplam ve tekrar eden bağlantılar   |
| 12 | User-Agent      | Kullanılan istemci                  |
| 13 | IOC             | IP, Domain, URI vb. göstergeler     |
| 14 | Önce / Sonra    | Olayın çevresindeki trafik          |
| 15 | Cihaz bilgileri | Kullanıcı, işletim sistemi ve cihaz |

---

# 5. Olayı Değerlendirme Mantığım

Bir SOC analisti olarak tek bir bulguya dayanarak kesin bir sonuca varmamaya çalışırım.

Örneğin:

```text
Şüpheli Domain
      +
Şüpheli IP
      +
Beklenmeyen Port
      +
Çok sayıda Bağlantı
      +
Olağandışı Veri Transferi
      +
Aynı zaman aralığında gerçekleşme
```

gibi birden fazla bulgunun aynı olay içerisinde bir araya gelip gelmediğine bakarım.

Bu nedenle olay analizinde **correlation (ilişkilendirme)** benim için önemli bir aşamadır.

---

# 6. Sonuç

Bu çalışmada kendimi bir SOC analisti yerine koyarak, olağandışı davranış gösteren bir şirket bilgisayarını incelerken hangi bilgileri toplamam gerektiğini belirledim.

Özellikle:

* Source IP
* Destination IP
* DNS
* HTTP
* HTTPS / TLS
* TCP
* UDP
* Portlar
* Zaman bilgileri
* Veri miktarı
* Bağlantı sayısı
* User-Agent
* IOC'ler
* Olay öncesi ve sonrası trafik
* Cihaz bilgileri

üzerinde durdum.

Bu çalışmadan çıkardığım en önemli sonuç, SOC analizinin yalnızca tek bir paketi veya tek bir IP adresini incelemekten ibaret olmadığı oldu.

Bir olayın anlamlandırılabilmesi için farklı kaynaklardan gelen bilgileri bir araya getirerek **olayın tamamını anlamaya çalışmak** gerekiyor.

Bu nedenle benim olay inceleme yaklaşımım:

```text
Kaynağı belirle
      ↓
Hedefi belirle
      ↓
Trafiği incele
      ↓
Zaman çizelgesi oluştur
      ↓
IOC'leri çıkar
      ↓
Bulguları ilişkilendir
      ↓
Olayı değerlendir
```

şeklinde olacaktır.
