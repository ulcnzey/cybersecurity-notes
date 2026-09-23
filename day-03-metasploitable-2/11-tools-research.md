# Zafiyet Analizi Araçları

## 1. Amaç

Bu çalışmanın amacı, siber güvenlik değerlendirmelerinde kullanılan temel keşif ve zafiyet analizi araçlarının görevlerini öğrenmek ve bu araçların Metasploitable 2 laboratuvar ortamında elde edilen sonuçlara nasıl katkı sağladığını incelemektir.

Bu kapsamda aşağıdaki araç ve teknolojiler değerlendirilmiştir:

* Nmap
* Nmap Scripting Engine (NSE)
* Nikto
* OpenVAS / Greenbone
* SearchSploit

Çalışmanın temel amacı araçları yalnızca kullanmak değil, **hangi aracın hangi aşamada ve hangi amaçla kullanıldığını anlamaktır.**

---

# 2. Güvenlik Değerlendirmesinde Araçların Konumu

Bir güvenlik değerlendirmesi tek bir araç kullanılarak gerçekleştirilmez.

Genel süreç şu şekilde düşünülebilir:

```text
Hedef Sistem
     │
     ▼
Port Keşfi
     │
     ▼
Servis ve Sürüm Tespiti
     │
     ▼
Servislerin Ayrıntılı İncelenmesi
     │
     ▼
Zafiyet Araştırması
     │
     ▼
CVE / Exploit Araştırması
     │
     ▼
Doğrulama
     │
     ▼
Risk Değerlendirmesi
     │
     ▼
Düzeltme Önerileri
```

Bu süreçte her araç farklı bir probleme odaklanır.

| Araç                | Temel kullanım amacı                       |
| ------------------- | ------------------------------------------ |
| Nmap                | Port ve servis keşfi                       |
| Nmap NSE            | Servisler hakkında ayrıntılı bilgi toplama |
| Nikto               | Web sunucusu ve web yapılandırması analizi |
| OpenVAS / Greenbone | Kapsamlı zafiyet taraması                  |
| SearchSploit        | Bilinen exploit kayıtlarını araştırma      |

---

# 3. Nmap

Nmap, ağ üzerindeki sistemleri keşfetmek ve açık portları belirlemek için kullanılan bir ağ tarama aracıdır.

Bu çalışmanın önceki aşamalarında Metasploitable 2 üzerinde Nmap kullanılmıştır.

Temel tarama:

```bash
nmap 192.168.56.20
```

Servis ve sürüm tespiti:

```bash
nmap -sV 192.168.56.20
```

Nmap sonucunda Metasploitable 2 üzerinde çok sayıda açık TCP portu tespit edilmiştir.

Örnek servisler:

```text
21/tcp    ftp
22/tcp    ssh
23/tcp    telnet
25/tcp    smtp
53/tcp    domain
80/tcp    http
139/tcp   netbios-ssn
445/tcp   microsoft-ds
3306/tcp  mysql
5432/tcp  postgresql
5900/tcp  vnc
8180/tcp  http
```

`-sV` kullanıldığında servis sürümleri hakkında daha ayrıntılı bilgi elde edilmiştir.

Örneğin:

```text
21/tcp    vsftpd 2.3.4
80/tcp    Apache httpd 2.2.8
139/tcp   Samba
3306/tcp  MySQL 5.0.51a
8180/tcp  Apache Tomcat 5.5
```

Nmap'in bu aşamadaki temel görevi:

> **Hedef sistemin ağ üzerindeki saldırı yüzeyini keşfetmek ve çalışan servisleri belirlemektir.**

---

# 4. Nmap Scripting Engine (NSE)

Nmap Scripting Engine, Nmap'in servisler hakkında daha ayrıntılı bilgi toplamasını sağlayan betik sistemidir.

Varsayılan NSE betiklerini çalıştırmak için:

```bash
nmap -sC 192.168.56.20
```

komutu kullanılmıştır.

Bu taramada yalnızca portların açık olduğu görülmemiş, aynı zamanda çeşitli servisler hakkında ek bilgiler elde edilmiştir.

Örneğin FTP için:

```text
ftp-anon: Anonymous FTP login allowed
```

sonucu elde edilmiştir.

SMB için:

```text
message_signing: disabled
```

sonucu görülmüştür.

Ayrıca:

```text
OS: Unix (Samba 3.0.20-Debian)
Computer name: metasploitable
Domain name: localdomain
```

gibi sistem bilgileri elde edilmiştir.

SMTP servisinde:

```text
SSLv2 supported
```

bilgisi görülmüştür.

RPC taramasında NFS ve diğer RPC servisleri belirlenmiştir.

Bu nedenle NSE:

> **Portların arkasındaki servisler hakkında daha ayrıntılı teknik bilgi elde edilmesini sağlayan bir analiz katmanıdır.**

---

# 5. Nikto

Nikto, özellikle web sunucularını güvenlik açısından incelemek için kullanılan bir web sunucusu tarama aracıdır.

Kullanılan Nikto sürümü:

```text
Nikto 2.5.0
```

Sürüm kontrolü:

```bash
nikto -Version
```

Hedef web sunucusunun taranması:

```bash
nikto -h http://192.168.56.20
```

Çıktının dosyaya kaydedilmesi:

```bash
nikto -h http://192.168.56.20 -output nikto-report.txt
```

---

# 6. Nikto Tarama Sonucu

Nikto taraması sonucunda:

```text
Target IP: 192.168.56.20
Target Port: 80
```

bilgileri elde edilmiştir.

Web sunucusu:

```text
Apache/2.2.8 (Ubuntu) DAV/2
```

olarak tespit edilmiştir.

PHP sürümü ise HTTP header üzerinden:

```text
PHP/5.2.4-2ubuntu5.10
```

olarak görülmüştür.

Tarama sonucunda:

```text
8910 requests: 0 error(s) and 27 item(s) reported
```

sonucu elde edilmiştir.

Buradaki **27 item**, 27 adet doğrulanmış güvenlik açığı anlamına gelmez.

Bu öğeler arasında:

* Yapılandırma eksiklikleri,
* Bilgi ifşaları,
* Eski yazılım sürümleri,
* Erişilebilir dosyalar,
* Directory indexing,
* Web sunucusu özellikleri,
* Daha ileri doğrulama gerektiren potansiyel güvenlik sorunları

bulunmaktadır.

---

# 7. Nikto ile Tespit Edilen Web Sunucusu Bulguları

## 7.1 PHP Sürümünün Açıklanması

Nikto:

```text
Retrieved x-powered-by header:
PHP/5.2.4-2ubuntu5.10
```

sonucunu vermiştir.

Bu, web sunucusunun PHP sürümünü HTTP response header içerisinde açıkladığını göstermektedir.

Sürüm bilgisinin açığa çıkması, saldırganların kullanılan yazılım bileşenlerini daha kolay belirlemesine yardımcı olabilir.

Bu nedenle gereksiz teknoloji ve sürüm bilgilerinin dışarıya açıklanmaması bir hardening konusu olarak değerlendirilebilir.

---

# 8. Eksik HTTP Güvenlik Header'ları

Nikto iki önemli HTTP güvenlik header'ının bulunmadığını bildirmiştir.

### X-Frame-Options

```text
The anti-clickjacking X-Frame-Options header is not present.
```

`X-Frame-Options`, web sayfalarının başka siteler tarafından frame veya iframe içerisinde görüntülenmesini sınırlandırmak için kullanılabilen bir güvenlik header'ıdır.

Header'ın bulunmaması, clickjacking gibi saldırı senaryolarında ek güvenlik kontrolünün bulunmaması anlamına gelebilir.

---

### X-Content-Type-Options

Nikto:

```text
The X-Content-Type-Options header is not set.
```

sonucunu vermiştir.

Modern web uygulamalarında:

```http
X-Content-Type-Options: nosniff
```

gibi bir header kullanılması, tarayıcıların MIME türüyle ilgili bazı yanlış yorumlamalarını sınırlandırmaya yardımcı olabilir.

Bu bulgu doğrudan bir sistem ele geçirme sonucu değildir; web güvenlik yapılandırması açısından değerlendirilmelidir.

---

# 9. Apache Sürümünün Eski Olması

Nikto:

```text
Apache/2.2.8 appears to be outdated
```

uyarısını vermiştir.

Daha önce Nmap ile de aynı Apache sürümü tespit edilmiştir:

```text
Apache httpd 2.2.8
```

Bu durum, kullanılan web sunucusu bileşeninin eski olduğunu göstermektedir.

Eski yazılım sürümleri:

* Bilinen güvenlik açıklarına sahip olabilir,
* Üretici desteğinin dışında olabilir,
* Güvenlik güncellemelerinden yararlanamayabilir.

Ancak yalnızca sürümün eski olması, belirli bir CVE'nin hedef sistemde kesin olarak bulunduğunu kanıtlamaz.

Bu nedenle sürüm bilgisi sonraki **CVE araştırması** aşamasında ayrıca incelenmelidir.

---

# 10. Apache MultiViews

Nikto:

```text
Apache mod_negotiation is enabled with MultiViews
```

sonucunu vermiştir.

MultiViews, Apache'nin içerik müzakeresi mekanizmalarından biridir.

Nikto bu yapılandırmanın bazı durumlarda dosya adlarının tahmin edilmesini veya keşfedilmesini kolaylaştırabileceğini belirtmektedir.

Bu nedenle:

```text
MultiViews
    ↓
Dosya keşfi açısından ek bilgi
    ↓
Saldırı yüzeyinin genişlemesi
```

şeklinde değerlendirme yapılabilir.

Bu bulgu tek başına sistemin ele geçirilebildiğini göstermez.

---

# 11. HTTP TRACE Metodunun Aktif Olması

Nikto:

```text
HTTP TRACE method is active
```

sonucunu vermiştir.

HTTP TRACE, HTTP isteklerinin test ve tanılama amacıyla sunucu tarafından geri döndürülmesini sağlayan bir HTTP metodudur.

Nikto bunu Cross-Site Tracing (XST) ile ilişkilendirmektedir.

Buradaki temel güvenlik değerlendirmesi:

> Gereksiz HTTP metodlarının açık bırakılması web sunucusu hardening açısından incelenmelidir.

TRACE metodunun aktif olması tek başına başarılı bir XST saldırısının gerçekleştiğini kanıtlamaz.

---

# 12. phpinfo.php

Nikto aşağıdaki dosyayı tespit etmiştir:

```text
/phpinfo.php
```

ve:

```text
Output from the phpinfo() function was found.
```

sonucunu vermiştir.

`phpinfo()` PHP çalışma ortamı hakkında çok sayıda teknik bilgi gösterebilir.

Bu bilgiler arasında:

* PHP sürümü,
* Yüklü modüller,
* Sunucu bilgileri,
* Yapılandırma değerleri,
* Environment bilgileri

gibi veriler bulunabilir.

Bu nedenle üretim ortamında gereksiz şekilde erişilebilir bırakılan `phpinfo.php` dosyaları bilgi ifşası açısından risk oluşturabilir.

---

# 13. phpinfo.php Erişiminin Doğrulanması

Nikto bulgusu `curl` kullanılarak ayrıca kontrol edilmiştir.

Kullanılan komut:

```bash
curl -I http://192.168.56.20/phpinfo.php
```

Sonuç:

```text
HTTP/1.1 200 OK
Date: Wed, 23 Sep 2026 11:27:59 GMT
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
Content-Type: text/html
```

Bu sonuç, `phpinfo.php` URL'sinin HTTP üzerinden erişilebilir olduğunu doğrulamaktadır.

> `curl -I` HEAD isteği gönderdiği için bu test dosyanın HTTP üzerinden erişilebilir olduğunu doğrular; phpinfo sayfasının tüm içeriğinin görüntülendiğini tek başına kanıtlamaz.

Bu ayrım, güvenlik raporlamasında önemlidir.

---

# 14. Directory Indexing

Nikto aşağıdaki dizinlerde directory indexing tespit etmiştir:

```text
/doc/
/test/
/icons/
```

Örneğin:

```text
/doc/: Directory indexing found
```

Directory indexing, bir dizin içerisinde varsayılan bir index sayfası bulunmadığında sunucunun dosya ve klasör listesini göstermesine neden olabilir.

Bu durum:

* Dosya adlarının öğrenilmesi,
* Kullanılmayan dosyaların keşfedilmesi,
* Dokümantasyonların görülmesi,
* Eski veya test dosyalarının ortaya çıkması

gibi bilgi toplama riskleri oluşturabilir.

---

# 15. `/doc/` Dizinine Erişim

Nikto tarafından tespit edilen `/doc/` yolu ayrıca `curl` ile kontrol edilmiştir.

Kullanılan komut:

```bash
curl -I http://192.168.56.20/doc/
```

Sonuç:

```text
HTTP/1.1 200 OK
Date: Wed, 23 Sep 2026 11:28:05 GMT
Server: Apache/2.2.8 (Ubuntu) DAV/2
Content-Type: text/html;charset=UTF-8
```

Bu sonuç:

```text
/doc/
```

dizinine HTTP üzerinden erişilebildiğini doğrulamaktadır.

Nikto'nun directory indexing bulgusunun içerik listesini gerçekten gösterip göstermediğini doğrulamak için GET isteğiyle ayrıca içerik incelenebilir.

---

# 16. PHP Bilgi İfşası

Nikto, bazı özel query string değerleri üzerinden PHP'nin potansiyel olarak bilgi açığa çıkarabileceğini raporlamıştır.

Örneğin:

```text
/?=PHPB8B5F2A0-3C92-11d3-A3A9-4C7B08C10000
```

gibi istekler test edilmiştir.

Nikto bu durum için:

```text
PHP reveals potentially sensitive information
```

uyarısı vermiştir.

Bu bulgu, PHP'nin eski sürümüyle ilişkili bilgi ifşası davranışlarının araştırılması gerektiğini göstermektedir.

---

# 17. phpMyAdmin

Nikto aşağıdaki yolları tespit etmiştir:

```text
/phpMyAdmin/
/phpMyAdmin/changelog.php
/phpMyAdmin/ChangeLog
/phpMyAdmin/Documentation.html
/phpMyAdmin/README
```

phpMyAdmin, MySQL veritabanlarını web arayüzü üzerinden yönetmek için kullanılan bir uygulamadır.

Bu nedenle web saldırı yüzeyi:

```text
HTTP
 │
 ├── Apache
 │
 ├── PHP
 │
 └── phpMyAdmin
        │
        ▼
      MySQL
```

şeklinde düşünülebilir.

phpMyAdmin gibi yönetim arayüzlerinin gereksiz şekilde erişilebilir olması, yetkilendirme ve ağ erişim kontrollerinin ayrıca değerlendirilmesini gerektirir.

Nikto'nun önerisi doğrultusunda bu tür yönetim arayüzleri yalnızca yetkili kullanıcıların erişebileceği şekilde sınırlandırılmalıdır.

---

# 18. phpMyAdmin ChangeLog ve ETag Bilgisi

Nikto:

```text
/phpMyAdmin/ChangeLog
```

dosyasını da tespit etmiş ve ETag içerisinde aşağıdaki bilgileri raporlamıştır:

```text
inode: 92462
size: 40540
mtime: Tue Dec  9 12:24:00 2008
```

Bu tür metadata bilgilerinin açığa çıkması, sunucu dosya sistemi hakkında ek bilgi sağlayabilir.

Nikto ayrıca bu bulguyu:

```text
CVE-2003-1418
```

ile ilişkilendirmiştir.

Ancak bu rapor yalnızca Nikto'nun bir referansıdır. CVE'nin hedef sistemde doğrulanmış olduğu sonucuna varılmadan önce ilgili CVE'nin etkilenen sürümleri ve koşulları ayrıca araştırılmalıdır.

---

# 19. Apache Default Dosyaları

Nikto:

```text
/icons/README
```

dosyasını Apache default file olarak tespit etmiştir.

Bu tür varsayılan dosyalar:

* Gereksiz bilgi sağlayabilir,
* Sunucu teknolojisinin anlaşılmasını kolaylaştırabilir,
* Hardening eksikliği göstergesi olabilir.

Üretim ortamlarında kullanılmayan varsayılan dosyaların kaldırılması veya erişimin sınırlandırılması tercih edilebilir.

---

# 20. `wp-config.php` Benzeri Dosya Tespiti

Nikto:

```text
/#wp-config.php#
```

dosyasını tespit etmiş ve bu dosyanın kimlik bilgileri içerebileceğini belirtmiştir.

Bu tür yedek veya geçici dosyaların web kök dizininde erişilebilir olması ciddi bir bilgi ifşası riski oluşturabilir.

Özellikle yapılandırma dosyalarının:

* Veritabanı kullanıcı adı,
* Veritabanı parolası,
* Secret key,
* API anahtarı

gibi hassas bilgiler içerebilmesi nedeniyle web üzerinden erişilebilir olmaması gerekir.

Ancak burada da Nikto'nun tespitinin dosyanın gerçekten hassas içerik döndürdüğünü tek başına kanıtlamadığı unutulmamalıdır.

---

# 21. Nikto ve Curl Sonuçlarının Birlikte Değerlendirilmesi

Bu çalışmada bazı Nikto bulguları `curl` kullanılarak ayrıca doğrulanmıştır.

| Bulgu              | Nikto         | Curl doğrulaması                | Sonuç                        |
| ------------------ | ------------- | ------------------------------- | ---------------------------- |
| Ana HTTP servisi   | Tespit edildi | `200 OK`                        | Doğrulandı                   |
| `phpinfo.php`      | Tespit edildi | `200 OK`                        | URL erişimi doğrulandı       |
| `/doc/`            | Tespit edildi | `200 OK`                        | URL erişimi doğrulandı       |
| Directory indexing | Tespit edildi | HEAD ile doğrulanmadı           | GET ile içerik incelenebilir |
| PHP sürümü         | Tespit edildi | `X-Powered-By` header'ı görüldü | Doğrulandı                   |
| Apache sürümü      | Tespit edildi | `Server` header'ında görüldü    | Doğrulandı                   |

Bu yaklaşım güvenlik testlerinde önemlidir.

Bir tarama aracının ürettiği sonuç mümkün olduğunda farklı bir yöntemle doğrulanmalıdır.

---

# 22. OpenVAS / Greenbone

OpenVAS, günümüzde Greenbone güvenlik ürünleri içerisinde kullanılan kapsamlı bir vulnerability scanning teknolojisidir.

Nmap veya Nikto'dan farklı olarak temel amacı yalnızca servis keşfi değildir.

Genel olarak:

```text
Hedef
  ↓
Servis keşfi
  ↓
Sürüm tespiti
  ↓
Bilinen zafiyetlerle karşılaştırma
  ↓
Zafiyet değerlendirmesi
  ↓
Risk / önem derecesi
  ↓
Raporlama
```

gibi daha kapsamlı bir süreç sağlar.

Bu nedenle OpenVAS / Greenbone, daha geniş kapsamlı vulnerability assessment süreçlerinde kullanılabilir.

Bu laboratuvarın bu aşamasında OpenVAS ile aktif tarama gerçekleştirilmemiştir. Araç, metodoloji açısından incelenmiştir.

---

# 23. SearchSploit

SearchSploit, Exploit Database içerisindeki exploit kayıtlarını komut satırından aramak için kullanılan bir araçtır.

Örneğin elimizde:

```text
vsFTPd 2.3.4
```

sürümü varsa:

```bash
searchsploit vsftpd 2.3.4
```

komutuyla ilgili exploit kayıtları araştırılabilir.

Benzer şekilde:

```bash
searchsploit apache 2.2.8
```

gibi aramalar yapılabilir.

SearchSploit'in amacı:

> **Bilinen exploit kayıtlarını araştırmak ve güvenlik araştırmasına yardımcı olmak**

şeklinde özetlenebilir.

SearchSploit sonucunda bir exploit kaydının bulunması:

> Hedef sistemin kesin olarak istismar edilebilir olduğu

anlamına gelmez.

İlgili exploit'in:

* Etkilenen sürümü,
* İşletim sistemi,
* Yapılandırma koşulları,
* Gerektirdiği erişim seviyesi,
* Etkilenen bileşen

gibi koşulları ayrıca incelenmelidir.

Bu çalışmada SearchSploit, araştırma amacıyla ele alınmakta olup gerçek sistemlere karşı izinsiz exploit çalıştırma amacı taşımamaktadır.

---

# 24. Araçların Karşılaştırılması

| Araç                | Odak Noktası             | Bu Çalışmadaki Rolü                                      |
| ------------------- | ------------------------ | -------------------------------------------------------- |
| Nmap                | Ağ keşfi                 | Açık portları belirlemek                                 |
| Nmap `-sV`          | Servis/sürüm             | Servis ve sürümleri belirlemek                           |
| Nmap NSE            | Ayrıntılı servis analizi | Servis yapılandırmalarını ve ek bilgileri incelemek      |
| Nikto               | Web güvenliği            | Apache/PHP/web dizinleri ve yapılandırmalarını incelemek |
| OpenVAS / Greenbone | Zafiyet taraması         | Kapsamlı vulnerability assessment yaklaşımını öğrenmek   |
| SearchSploit        | Exploit araştırması      | Bilinen exploit kayıtlarını araştırmak                   |

---

# 25. Güvenlik Analizindeki Doğru Yaklaşım

Araçların ürettiği sonuçları doğrudan güvenlik açığı olarak kabul etmek yerine aşağıdaki yöntem uygulanmalıdır:

```text
Araç çıktısı
     ↓
Bulgunun sınıflandırılması
     ↓
Manuel doğrulama
     ↓
Servis / sürüm kontrolü
     ↓
CVE araştırması
     ↓
Etkilenme koşullarının incelenmesi
     ↓
Etki analizi
     ↓
Risk değerlendirmesi
     ↓
Düzeltme önerisi
```

Örneğin:

```text
Nikto
 ↓
Apache 2.2.8 outdated
 ↓
Apache sürümü doğrulandı
 ↓
İlgili CVE'ler araştırılır
 ↓
CVE'nin etkilenen sürümleri incelenir
 ↓
Hedef sistem koşulları değerlendirilir
 ↓
Risk değerlendirilir
```

Bu yaklaşım, otomatik tarama sonuçlarının bilinçli şekilde yorumlanmasını sağlar.

---

# 26. Saldırgan ve Savunmacı Perspektifi

### Saldırgan perspektifi

Bir saldırgan için aşağıdaki bilgiler değerlidir:

```text
Apache sürümü
PHP sürümü
phpMyAdmin
Açık dizinler
phpinfo.php
Yapılandırma bilgileri
Eski bileşenler
```

Bu bilgiler saldırı yüzeyinin anlaşılmasını kolaylaştırabilir.

### Savunmacı perspektifi

Savunma tarafında ise aynı bilgiler şu sorulara dönüşür:

```text
Gereksiz dosyalar kaldırıldı mı?
Directory listing kapalı mı?
PHP sürümü güncel mi?
Apache güncel mi?
phpMyAdmin erişimi sınırlandırılmış mı?
Gereksiz HTTP metodları kapalı mı?
Security header'ları yapılandırılmış mı?
Hassas yapılandırma dosyaları web root dışında mı?
```

Bu nedenle aynı tarama sonucu hem saldırı hem savunma perspektifinden değerlendirilebilir.

---

# 27. Bu Çalışmada Öğrenilenler

Bu çalışma sonucunda:

* Nmap'in ağ keşfindeki rolü öğrenildi.
* Nmap NSE ile servislerin daha ayrıntılı incelenebildiği görüldü.
* Nikto'nun web sunucularına özel analiz yaptığı öğrenildi.
* Apache ve PHP sürümlerinin HTTP header'larından tespit edilebildiği görüldü.
* Eksik HTTP güvenlik header'larının tespit edilebildiği görüldü.
* Directory indexing'in saldırı yüzeyine nasıl bilgi sağlayabileceği öğrenildi.
* `phpinfo.php` gibi dosyaların bilgi ifşasına neden olabileceği görüldü.
* phpMyAdmin gibi yönetim arayüzlerinin ayrıca korunması gerektiği anlaşıldı.
* OpenVAS / Greenbone'un daha kapsamlı vulnerability assessment yaklaşımındaki yeri öğrenildi.
* SearchSploit'in bilinen exploit kayıtlarını araştırmak için kullanılabileceği öğrenildi.
* Otomatik araç çıktılarının doğrudan doğrulanmış güvenlik açığı olarak kabul edilmemesi gerektiği öğrenildi.
* Manuel doğrulamanın güvenlik değerlendirmesindeki önemi görüldü.

---

# 28. Sonuç

Bu çalışmada Nmap, Nmap NSE, Nikto, OpenVAS / Greenbone ve SearchSploit araçlarının güvenlik değerlendirme sürecindeki görevleri incelenmiştir.

Metasploitable 2 üzerinde yapılan gerçek testlerde Nmap ve NSE ile servisler ve yapılandırmalar hakkında bilgi toplanmış, Nikto ile web sunucusu daha ayrıntılı şekilde incelenmiştir.

Nikto sonucunda Apache ve PHP sürümleri, eksik güvenlik header'ları, HTTP TRACE, `phpinfo.php`, directory indexing, phpMyAdmin ve çeşitli potansiyel bilgi ifşası noktaları tespit edilmiştir.

Bazı bulgular `curl` kullanılarak ayrıca doğrulanmıştır.

Bu çalışmadan çıkarılan temel yaklaşım:

> **Güvenlik araçları sonuç üretir; güvenlik uzmanı ise bu sonuçları doğrular, bağlamlandırır ve risk açısından değerlendirir.**

Bu nedenle bir güvenlik değerlendirmesinde yalnızca tarama sonucuna bakmak yerine:

**Keşif → Analiz → Doğrulama → Zafiyet araştırması → Risk değerlendirmesi → Düzeltme**

zinciri takip edilmelidir.

---

## 29. Kanıtlar

Nikto tarama çıktısı:

<img width="648" height="538" alt="image" src="https://github.com/user-attachments/assets/f27d29f1-56dd-4f50-8b4c-45ab32e4c66a" />



Curl ile `phpinfo.php` erişim doğrulaması:

<img width="365" height="122" alt="image" src="https://github.com/user-attachments/assets/73208c04-7cf5-4814-806a-35afa7dd1a81" />


Curl ile `/doc/` erişim doğrulaması:

<img width="313" height="107" alt="image" src="https://github.com/user-attachments/assets/cdcce7a5-73e6-48ef-8869-2bead7add490" />


---

## 30. Kullanılan Kaynaklar

* Nmap Documentation
* Nmap Scripting Engine Documentation
* Nikto Documentation
* Greenbone / OpenVAS Documentation
* Exploit Database / SearchSploit
* OWASP Web Security Documentation
* MITRE CWE
* MITRE CVE
* National Vulnerability Database (NVD)
