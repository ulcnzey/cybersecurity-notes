# Wireshark Nedir?

## 1. Wireshark Nedir?

Wireshark, ağ üzerinde gerçekleşen iletişimi paket seviyesinde incelemeye yarayan bir ağ protokol analiz aracıdır.

Bilgisayarlar ve sunucular arasındaki iletişim tek parça halinde gerçekleşmez. Veriler ağ üzerinde paketler halinde taşınır. Wireshark sayesinde bu paketleri yakalayabilir, paketlerin içindeki iletişim bilgilerini inceleyebilir ve ağ trafiğinin nasıl gerçekleştiğini anlayabiliriz.

Bir paketi incelerken kaynak ve hedef IP adresi, port bilgileri, kullanılan protokol, zaman bilgisi ve paketin uzunluğu gibi birçok bilgi görülebilir.

Bu nedenle Wireshark'ı, ağ trafiğini daha ayrıntılı incelememizi sağlayan bir analiz aracı olarak düşünüyorum.

---

## 2. Wireshark Ne Amaçla Kullanılır?

Wireshark yalnızca siber saldırıları tespit etmek için kullanılmaz. Ağ iletişimini anlamak, ağ problemlerini araştırmak ve güvenlik olaylarını incelemek gibi farklı amaçlarla kullanılabilir.

Başlıca kullanım alanları:

* Ağ trafiğini gözlemlemek
* Paketleri ve kullanılan protokolleri analiz etmek
* Ağ bağlantılarındaki problemleri araştırmak
* IP ve port iletişimlerini incelemek
* DNS, HTTP, HTTPS, TCP, UDP, ARP ve ICMP gibi protokolleri analiz etmek
* Şüpheli ağ davranışlarını araştırmak
* Güvenlik olaylarının ağ üzerindeki izlerini incelemek
* Eğitim amacıyla ağ protokollerinin gerçek trafiğini görmek

Özellikle siber güvenlik açısından Wireshark, ağ üzerinde gerçekleşen bir olayın paket seviyesinde incelenmesine yardımcı olur.

---

## 3. Wireshark ve Nmap Arasındaki Fark

Nmap ve Wireshark ikisi de ağ güvenliği çalışmalarında kullanılan araçlardır ancak amaçları farklıdır.

Nmap daha çok ağ keşfi ve tarama amacıyla kullanılır. Örneğin bir hedef sistemde hangi portların açık olduğunu veya hangi servislerin çalıştığını belirlemek için kullanılabilir.

Wireshark ise ağ üzerinde gerçekleşen iletişimin kendisini paket seviyesinde incelemeye yarar.

Kısaca:

| Nmap                                            | Wireshark                                                   |
| ----------------------------------------------- | ----------------------------------------------------------- |
| Ağ ve sistem keşfi için kullanılır.             | Ağ trafiğini analiz etmek için kullanılır.                  |
| Port ve servisleri tespit etmeye yardımcı olur. | Paketleri ve protokolleri incelemeye yardımcı olur.         |
| Aktif tarama gerçekleştirebilir.                | Trafiği yakalar ve analiz eder.                             |
| "Hangi portlar açık?" sorusuna yardımcı olur.   | "Ağ üzerinde ne oluyor?" sorusunu incelemeye yardımcı olur. |

Örneğin Nmap ile bir sistemde `22/tcp` portunun açık olduğunu görebilirim. Wireshark ile ise bu sistemle gerçekleşen TCP iletişiminin paketlerini inceleyerek bağlantının nasıl kurulduğunu görebilirim.

Bu nedenle Nmap ve Wireshark birbirinin alternatifi değildir. Farklı amaçlar için kullanılabilen ve gerektiğinde birlikte değerlendirilebilen araçlardır.

---

## 4. Wireshark IDS/IPS midir?

Hayır. Wireshark bir IDS veya IPS değildir.

Wireshark'ın temel görevi ağ paketlerini yakalamak ve analiz etmektir. Paketleri inceleyerek bir güvenlik uzmanının veya ağ yöneticisinin trafiği anlamasına yardımcı olur.

IDS (Intrusion Detection System), ağ veya sistemlerdeki şüpheli davranışları tespit ederek alarm üretmek amacıyla kullanılan bir güvenlik sistemidir.

IPS (Intrusion Prevention System) ise tespit fonksiyonlarına ek olarak yapılandırmasına bağlı olarak belirli tehditleri engellemeye yönelik çalışabilir.

Wireshark ile IDS/IPS arasındaki temel farkı şöyle düşünebilirim:

```text
Wireshark
    ↓
Paketleri yakala
    ↓
Paketleri göster
    ↓
Analiz et
    ↓
Uzman yorumlar
```

Bu nedenle Wireshark'ı doğrudan bir saldırı önleme veya otomatik güvenlik sistemi olarak değerlendirmemeliyim.

---

## 5. Paket Nedir?

Ağ üzerinden gönderilen veriler paketler halinde taşınır.

Bir pakette yalnızca gönderilen veri bulunmaz. İletişimin gerçekleşebilmesi için kaynak, hedef, protokol ve çeşitli kontrol bilgileri gibi farklı bilgiler de bulunabilir.

Örneğin bir paketi incelerken:

* Kaynak IP
* Hedef IP
* Kaynak port
* Hedef port
* Protokol
* Paket uzunluğu
* Zaman bilgisi

gibi alanlarla karşılaşabilirim.

Wireshark'ın önemli özelliklerinden biri, bu bilgileri paket seviyesinde inceleyebilmemi sağlamasıdır.

---

## 6. Paket Yakalama ve Paket Analizi Arasındaki Fark

Paket yakalama ve paket analizi aynı şey değildir.

### Paket Yakalama

Ağ üzerinde gerçekleşen paketlerin alınması ve gerektiğinde bir dosyaya kaydedilmesidir.

```text
Ağ trafiği
    ↓
Paketler
    ↓
Yakalama
    ↓
PCAP / PCAPNG
```

### Paket Analizi

Yakalanan paketlerin incelenerek ne anlama geldiğinin anlaşılmasıdır.

```text
PCAP / PCAPNG
    ↓
Wireshark
    ↓
Paketleri inceleme
    ↓
IP / Port / Protokol / Zaman
    ↓
Trafiği yorumlama
```

Kısaca paket yakalamayı **veriyi toplamak**, paket analizini ise **toplanan veriyi anlamlandırmak** olarak düşünebilirim.

---

## 7. PCAP Nedir?

PCAP, ağ paketlerinin yakalanarak dosya içerisinde saklanmasını sağlayan bir paket yakalama formatıdır.

Bir ağ trafiğini daha sonra tekrar incelemek istediğimde, daha önce yakalanmış paketleri içeren bir PCAP dosyasını Wireshark ile açarak analiz edebilirim.

Örneğin:

```text
Ağ trafiği
    ↓
Paketlerin yakalanması
    ↓
PCAP dosyası
    ↓
Wireshark
    ↓
Paket analizi
```

Bu nedenle PCAP dosyaları özellikle olay inceleme ve eğitim çalışmalarında önemlidir. Gerçek zamanlı trafiği izlemek yerine daha önce kaydedilmiş bir trafik üzerinde analiz yapılmasına imkan sağlar.

---

## 8. PCAPNG Nedir?

PCAPNG, paket yakalama amacıyla kullanılan ve klasik PCAP formatına göre daha fazla yakalama bilgisi ve metadata destekleyebilen bir dosya formatıdır.

Hem PCAP hem de PCAPNG, yakalanmış ağ trafiğinin daha sonra analiz edilmesini sağlayan dosya formatlarıdır.

Burada önemli olan nokta, PCAP ve PCAPNG'nin birer saldırı türü olmadığı; ağ trafiğinin kaydedilmesini sağlayan dosya formatları olduğudur.

---

## 9. Wireshark Hangi İşletim Sistemlerinde Kullanılabilir?

Wireshark farklı işletim sistemlerinde kullanılabilir.

Başlıca:

* Windows
* Linux
* macOS

üzerinde kullanılmaktadır.

Bu staj çalışmasında ise siber güvenlik laboratuvarımızın bir parçası olarak Kali Linux üzerinde kullanacağım.

---

## 10. Wireshark Siber Güvenlikte Neden Önemlidir?

Siber güvenlik açısından ağ trafiğinin incelenmesi, bir sistemde gerçekleşen olayların anlaşılmasına yardımcı olabilir.

Örneğin şüpheli davranış gösteren bir bilgisayar için şu sorular araştırılabilir:

* Hangi IP adresleriyle iletişim kurdu?
* Hangi portlara bağlantı kurdu?
* Hangi protokolleri kullandı?
* Hangi DNS sorgularını yaptı?
* Hangi HTTP isteklerini gönderdi?
* Bağlantılar ne zaman gerçekleşti?
* Normalden farklı bir trafik oluştu mu?
* Çok sayıda bağlantı veya başarısız bağlantı denemesi var mı?
* Olağandışı miktarda veri transferi gerçekleşti mi?

Bu nedenle Wireshark, özellikle ağ trafiğinin incelenmesi ve güvenlik olaylarının araştırılması sırasında kullanılabilecek önemli araçlardan biridir.

Bir SOC analisti açısından düşünüldüğünde amaç yalnızca paketleri görmek değildir. Asıl amaç, paketlerden elde edilen bilgileri bir araya getirerek ağ üzerinde gerçekleşen davranışı anlamlandırmaktır.

---

## 11. Öğrendiğim Temel Mantık

Bu çalışmada Wireshark'ın temel mantığını şu şekilde özetleyebilirim:

```text
Ağ iletişimi
      ↓
Paketler
      ↓
Paket yakalama
      ↓
PCAP / PCAPNG
      ↓
Wireshark
      ↓
Paket analizi
      ↓
IP + Port + Protokol + Zaman
      ↓
Trafiği anlamlandırma
      ↓
Normal / Şüpheli davranış
```

Wireshark'ı öğrenirken benim için en önemli nokta, sadece programdaki paketleri görmek değil, **bir paketin ağ iletişimi hakkında ne anlattığını anlayabilmek**.

Bu nedenle sonraki aşamada Wireshark üzerinde gördüğüm paketleri daha iyi anlayabilmek için önce **OSI ve TCP/IP katmanlarını** öğrenmem gerekiyor.
