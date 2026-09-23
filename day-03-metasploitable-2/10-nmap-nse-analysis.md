# Nmap NSE Analizi

## 1. Amaç

Bu çalışmanın amacı, **Nmap Scripting Engine (NSE)** kullanılarak Metasploitable 2 üzerinde çalışan servisler hakkında temel Nmap taramasından daha ayrıntılı bilgi elde etmektir.

Bu aşamada özellikle:

* Açık portlar üzerindeki servislerin daha ayrıntılı incelenmesi,
* Servislerin yapılandırmaları hakkında bilgi toplanması,
* SMB, FTP, SMTP, DNS, HTTP, MySQL, VNC ve diğer servislerin NSE çıktılarının değerlendirilmesi,
* Elde edilen bilgilerin güvenlik değerlendirmesine nasıl katkı sağladığının anlaşılması

hedeflenmiştir.

> **Not:** NSE çıktısında bir bulgunun görülmesi, tek başına ilgili sistemde kesin bir güvenlik açığı bulunduğu veya istismar edilebildiği anlamına gelmez. Bulguların ayrıca servis sürümü, yapılandırma, CVE bilgisi ve ortam koşullarıyla doğrulanması gerekir.

---

## 2. Nmap Scripting Engine (NSE) Nedir?

**Nmap Scripting Engine (NSE)**, Nmap'in ağ servisleri hakkında daha ayrıntılı bilgi toplamasını sağlayan bir betik sistemidir.

Standart Nmap taraması temel olarak:

> "Hangi portlar açık?"

sorusuna cevap verir.

`-sV` seçeneği kullanıldığında:

> "Bu portlarda hangi servis ve sürüm çalışıyor?"

sorusuna daha ayrıntılı cevap alınır.

NSE kullanıldığında ise:

> "Bu servis hakkında başka hangi teknik bilgiler elde edilebilir?"

sorusuna cevap aranır.

Bu nedenle NSE, keşif ve servislerin ayrıntılı incelenmesi aşamalarında önemli bir yardımcı mekanizmadır.

---

## 3. Kullanılan Komut

Bu çalışmada Nmap'in varsayılan NSE betikleri çalıştırılmıştır.

```bash
nmap -sC 192.168.56.20
```

Burada:

* `nmap` → Nmap aracını çalıştırır.
* `-sC` → Nmap'in varsayılan NSE betiklerini çalıştırır.
* `192.168.56.20` → Metasploitable 2'nin laboratuvar ortamındaki IP adresidir.

`-sC`, pratikte aşağıdaki kullanımla aynı amaç doğrultusundadır:

```bash
nmap --script=default 192.168.56.20
```

Bu tarama, temel port ve servis bilgilerinin yanında desteklenen servisler için varsayılan NSE betiklerinden ek bilgiler toplamıştır.

---

## 4. Genel Tarama Sonucu

Tarama sonucunda hedef sistemin aktif olduğu görülmüştür.

```text
Host is up (0.00088s latency).
Not shown: 977 closed tcp ports (reset)
```

Tarama sonucunda **23 TCP portunun açık** olduğu görülmüştür.

Önemli açık portlar:

|     Port | Servis        | NSE ile Elde Edilen Ek Bilgi              |
| -------: | ------------- | ----------------------------------------- |
|   21/tcp | FTP           | Anonymous erişim ve FTP sunucu bilgileri  |
|   22/tcp | SSH           | SSH host key bilgileri                    |
|   23/tcp | Telnet        | Servisin açık olduğu doğrulandı           |
|   25/tcp | SMTP          | SMTP komutları ve SSLv2 bilgisi           |
|   53/tcp | DNS           | BIND sürüm bilgisi                        |
|   80/tcp | HTTP          | Web sayfası başlığı                       |
|  111/tcp | RPC           | RPC servisleri ve NFS bilgileri           |
|  139/tcp | NetBIOS/SMB   | SMB güvenlik ve işletim sistemi bilgileri |
|  445/tcp | SMB           | SMB güvenlik ve işletim sistemi bilgileri |
| 3306/tcp | MySQL         | MySQL protokol ve sürüm bilgileri         |
| 5432/tcp | PostgreSQL    | SSL ve sertifika bilgileri                |
| 5900/tcp | VNC           | VNC protokol ve kimlik doğrulama bilgisi  |
| 8009/tcp | AJP           | AJP servisinin açık olduğu doğrulandı     |
| 8180/tcp | Apache Tomcat | Tomcat 5.5 bilgisi                        |

---

# 5. FTP Analizi

## 5.1 FTP Sunucu Bilgisi

NSE, 21/tcp üzerindeki FTP servisinden aşağıdaki bilgileri elde etmiştir:

```text
21/tcp open ftp
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 192.168.56.10
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
```

Bu çıktıdan:

* FTP servisinin açık olduğu,
* Bağlantının `192.168.56.10` adresindeki Kali makinesinden geldiği,
* Oturumun `ftp` kullanıcısı ile ilişkili olduğu,
* Kontrol bağlantısının açık metin üzerinden gerçekleştiği,
* Veri bağlantılarının da açık metin üzerinden gerçekleştiği,
* Sunucunun `vsFTPd 2.3.4` kullandığı

anlaşılmaktadır.

---

## 5.2 Anonymous FTP

NSE ayrıca şu sonucu vermiştir:

```text
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

Bu sonuç, FTP sunucusunun **anonymous erişime izin verdiğini** göstermektedir.

Anonymous FTP, kullanıcıların normal bir kişisel hesap kimlik bilgisi kullanmadan FTP servisine erişmesine olanak sağlayabilir.

Bu yapılandırma, erişim izinlerine bağlı olarak:

* Yetkisiz dosya erişimi,
* Bilgi ifşası,
* Dosya yükleme veya değiştirme

gibi güvenlik risklerine neden olabilir.

Ancak yalnızca anonymous erişimin açık olması, tek başına sistemin ele geçirildiğini göstermez.

---

# 6. SSH Analizi

22/tcp üzerinde SSH servisi çalışmaktadır.

NSE aşağıdaki SSH host key bilgilerini elde etmiştir:

```text
22/tcp open ssh
| ssh-hostkey:
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
```

Bu bilgiler SSH sunucusunun kullandığı anahtar türleri ve ilgili parmak izleri hakkında bilgi sağlar.

Host key bilgileri, servis tanımlama ve güvenlik değerlendirmesi açısından kullanılabilecek teknik bilgiler arasındadır.

---

# 7. SMTP Analizi

25/tcp üzerinde SMTP servisi bulunmaktadır.

NSE aşağıdaki SMTP komutlarını tespit etmiştir:

```text
smtp-commands:
metasploitable.localdomain,
PIPELINING,
SIZE 10240000,
VRFY,
ETRN,
STARTTLS,
ENHANCEDSTATUSCODES,
8BITMIME,
DSN
```

Burada dikkat çeken bilgiler:

* `VRFY`
* `STARTTLS`
* `ETRN`

gibi SMTP özelliklerinin desteklenmesidir.

Ayrıca NSE çıktısında SSLv2 desteği görülmektedir:

```text
sslv2:
  SSLv2 supported
```

SSLv2 günümüzde güvenli kabul edilmeyen eski bir protokoldür.

Bu nedenle SSLv2 desteğinin bulunması, servis yapılandırmasının güvenlik açısından ayrıca incelenmesi gereken bir bulgu olduğunu göstermektedir.

NSE çıktısında ayrıca sertifikanın:

```text
Not valid before: 2010-03-17
Not valid after: 2010-04-16
```

tarihleri arasında geçerli olduğu görülmektedir.

Bu durum laboratuvar sisteminin oldukça eski bir yazılım ve sertifika yapılandırmasına sahip olduğunu göstermektedir.

---

# 8. DNS Analizi

53/tcp üzerinde DNS servisi bulunmaktadır.

NSE:

```text
dns-nsid:
|_ bind.version: 9.4.2
```

sonucunu üretmiştir.

Buradan DNS servisinin **BIND 9.4.2** sürümünü kullandığı anlaşılmaktadır.

Servis sürümünün belirlenmesi, sonraki aşamada ilgili sürüm için:

* Bilinen güvenlik açıklarının,
* CVE kayıtlarının,
* Güncelleme durumunun,
* Güvenlik yapılandırmalarının

araştırılmasına olanak sağlar.

---

# 9. HTTP Analizi

80/tcp üzerinde HTTP servisi çalışmaktadır.

NSE:

```text
80/tcp open http
|_http-title: Metasploitable2 - Linux
```

sonucunu vermiştir.

Bu sonuç, HTTP servisinin erişilebilir olduğunu ve web uygulamasının sayfa başlığının:

```text
Metasploitable2 - Linux
```

olduğunu göstermektedir.

Daha önce yapılan HTTP analizinde Apache sürümü ve PHP bilgisi de elde edilmiştir.

```text
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
```

Bu bilgiler birlikte değerlendirildiğinde web servisinin oldukça eski yazılım bileşenleri kullandığı görülmektedir.

---

# 10. RPC ve NFS Analizi

111/tcp üzerinde `rpcbind` servisi çalışmaktadır.

NSE `rpcinfo` çıktısında aşağıdaki servisler görülmüştür:

```text
program version    port/proto   service
100000  2          111/tcp     rpcbind
100003  2,3,4      2049/tcp     nfs
100005  1,2,3      35339/tcp    mountd
100021  1,3,4      35520/tcp    nlockmgr
100024  1          48320/tcp    status
```

Bu sonuç:

* RPC mekanizmasının aktif olduğunu,
* NFS servisinin çalıştığını,
* `mountd`,
* `nlockmgr`,
* `status`

gibi yardımcı RPC servislerinin de bulunduğunu göstermektedir.

Özellikle NFS'nin ağ üzerinden erişilebilir olması, paylaşım izinlerinin ayrıca incelenmesini gerektirir.

Bu nedenle 111/tcp ve 2049/tcp, saldırı yüzeyinin değerlendirilmesi sırasında dikkate alınması gereken servislerdir.

---

# 11. SMB Analizi

139/tcp ve 445/tcp üzerinde SMB/Samba servisleri bulunmaktadır.

NSE:

```text
smb-security-mode:
  account_used: <blank>
  authentication_level: user
  challenge_response: supported
  message_signing: disabled (dangerous, but default)
```

sonucunu vermiştir.

Buradaki önemli bulgu:

```text
message_signing: disabled
```

ifadesidir.

SMB mesaj imzalama, SMB iletişiminin bütünlüğünü ve kimlik doğrulama bağlamını destekleyen güvenlik mekanizmalarından biridir.

Mesaj imzalamanın devre dışı olması, ortamın güvenlik yapılandırması açısından ayrıca değerlendirilmesi gereken bir durumdur.

NSE ayrıca işletim sistemi ve Samba bilgilerini göstermiştir:

```text
smb-os-discovery:
  OS: Unix (Samba 3.0.20-Debian)
  Computer name: metasploitable
  Domain name: localdomain
  FQDN: metasploitable.localdomain
```

Böylece:

* İşletim sistemi ailesi,
* Samba sürümü,
* Bilgisayar adı,
* Domain bilgisi,
* FQDN

gibi bilgiler elde edilmiştir.

---

# 12. SMB2 Durumu

NSE çıktısında ayrıca:

```text
smb2-time: Protocol negotiation failed (SMB2)
```

sonucu görülmüştür.

Bu sonuç, hedefle SMB2 protokol görüşmesinin başarılı olmadığını göstermektedir.

Bu durum doğrudan bir güvenlik açığı olarak değerlendirilmemelidir. Daha çok kullanılan SMB protokolü ve servis yapılandırması hakkında teknik bilgi sağlamaktadır.

---

# 13. MySQL Analizi

3306/tcp üzerinde MySQL servisi bulunmaktadır.

NSE:

```text
mysql-info:
  Protocol: 10
  Version: 5.0.51a-3ubuntu5
```

sonucunu vermiştir.

Buna göre:

* MySQL protokolü: `10`
* MySQL sürümü: `5.0.51a-3ubuntu5`

olarak tespit edilmiştir.

NSE ayrıca bağlantının desteklediği bazı özellikleri de göstermiştir:

```text
Support41Auth
SwitchToSSLAfterHandshake
ConnectWithDatabase
Speaks41ProtocolNew
SupportsCompression
SupportsTransactions
```

Bu bilgiler servis hakkında daha ayrıntılı teknik bilgi sağlamaktadır.

Sürüm bilgisi daha sonra CVE ve güvenlik açığı araştırmalarında kullanılabilir.

---

# 14. PostgreSQL Analizi

5432/tcp üzerinde PostgreSQL servisi bulunmaktadır.

NSE çıktısında SSL sertifikası hakkında bilgiler elde edilmiştir.

Sertifikanın geçerlilik tarihleri 2010 yılına aittir:

```text
Not valid before: 2010-03-17
Not valid after: 2010-04-16
```

Bu durum laboratuvar sisteminin eski bir yazılım ve sertifika altyapısına sahip olduğunu göstermektedir.

Buradaki sertifika bilgisi tek başına PostgreSQL servisinin güvenlik açığı bulunduğunu kanıtlamaz.

---

# 15. VNC Analizi

5900/tcp üzerinde VNC servisi çalışmaktadır.

NSE:

```text
vnc-info:
  Protocol version: 3.3
  Security types:
    VNC Authentication (2)
```

sonucunu vermiştir.

Bu sonuç:

* VNC protokol sürümünün `3.3` olduğunu,
* VNC Authentication mekanizmasının kullanıldığını

göstermektedir.

VNC, grafiksel uzaktan erişim sağladığı için erişim kontrolü ve ağ segmentasyonu açısından ayrıca değerlendirilmelidir.

---

# 16. AJP ve Apache Tomcat Analizi

8009/tcp üzerinde AJP servisi bulunmaktadır.

```text
8009/tcp open ajp13
|_ajp-methods: Failed to get a valid response for the OPTION request
```

NSE burada AJP servisinin açık olduğunu göstermiş ancak `OPTION` isteğine geçerli bir yanıt alınamamıştır.

Bu sonuç tek başına bir güvenlik açığı anlamına gelmez.

8180/tcp üzerinde ise:

```text
8180/tcp open unknown
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/5.5
```

çıktısı alınmıştır.

Bu nedenle 8180/tcp üzerinde **Apache Tomcat 5.5** çalıştığı anlaşılmaktadır.

Tomcat gibi web uygulama sunucularında sürüm bilgisi, sonraki güvenlik ve CVE araştırmaları açısından önemlidir.

---

# 17. Tarama Sonuçlarının Güvenlik Açısından Değerlendirilmesi

NSE taraması sonucunda yalnızca açık portların değil, servislerin çeşitli yapılandırma ve özelliklerinin de görülebildiği anlaşılmıştır.

Önemli gözlemler:

### FTP

* Anonymous FTP erişimi açık.
* Kontrol ve veri bağlantıları açık metin olarak belirtiliyor.
* `vsFTPd 2.3.4` kullanılıyor.

### SMTP

* SSLv2 desteği tespit edildi.
* Eski sertifika bilgileri görüldü.
* Birden fazla SMTP komutu destekleniyor.

### DNS

* BIND `9.4.2` sürümü tespit edildi.

### SMB

* Samba `3.0.20-Debian` bilgisi elde edildi.
* SMB message signing devre dışı.
* İşletim sistemi ve sistem adı bilgileri elde edildi.

### MySQL

* MySQL `5.0.51a-3ubuntu5` sürümü tespit edildi.

### VNC

* VNC protokol sürümü `3.3` olarak tespit edildi.

### Tomcat

* Apache Tomcat `5.5` tespit edildi.

Bu bulgular, sonraki aşamada yapılacak **zafiyet araştırması ve risk değerlendirmesi** için temel veri oluşturmaktadır.

---

# 18. Nmap, -sV ve -sC Arasındaki Fark

Bu çalışmada kullanılan üç farklı yaklaşımın temel farkı aşağıdaki gibidir:

| Komut                    | Temel Amaç                                               |
| ------------------------ | -------------------------------------------------------- |
| `nmap 192.168.56.20`     | Açık portları belirlemek                                 |
| `nmap -sV 192.168.56.20` | Servis ve sürümlerini belirlemek                         |
| `nmap -sC 192.168.56.20` | Varsayılan NSE betikleriyle ek servis bilgileri toplamak |

Bunu çalışma sürecinde şu şekilde düşünebiliriz:

```text
Nmap
  ↓
Açık portlar
  ↓
-sV
  ↓
Servis + sürüm
  ↓
-sC
  ↓
Servis hakkında ek teknik bilgiler
  ↓
Araştırma
  ↓
CVE / yapılandırma / risk değerlendirmesi
```

Bu nedenle NSE, tek başına son değerlendirme aracı değil; bilgi toplama ve analiz sürecini zenginleştiren bir mekanizmadır.

---

# 19. Güvenlik Açığı ile NSE Bulgusu Arasındaki Fark

Bu aşamada özellikle dikkat edilmesi gereken konu, NSE çıktılarının doğru yorumlanmasıdır.

Örneğin:

```text
message signing: disabled
```

çıktısı bir **güvenlik yapılandırması bulgusudur**.

Ancak bundan doğrudan:

> "Sistem kesin olarak ele geçirilebilir."

sonucu çıkarılamaz.

Benzer şekilde:

```text
vsFTPd 2.3.4
```

görülmesi de yalnızca ilgili sürümün kullanıldığını gösterir.

Bir CVE'nin gerçekten uygulanabilir olup olmadığını değerlendirmek için:

1. Etkilenen ürün ve sürüm doğrulanmalı,
2. CVE'nin etkilediği sürüm aralığı incelenmeli,
3. Hedef sistemin yapılandırması değerlendirilmelidir,
4. Güvenlik açığının koşulları kontrol edilmelidir,
5. Elde edilen sonuçlar risk açısından değerlendirilmelidir.

Bu nedenle:

> **NSE bulgusu ≠ kesin zafiyet**

ve

> **Sürüm eşleşmesi ≠ kesin istismar edilebilirlik**

olarak değerlendirilmelidir.

---

# 20. Saldırı Yüzeyine Katkısı

NSE taraması, daha önce oluşturulan saldırı yüzeyi görünümünü genişletmiştir.

```text
Hedef Sistem
│
├── FTP
│   ├── Anonymous access
│   └── Plain-text communication
│
├── SMTP
│   └── SSLv2 support
│
├── DNS
│   └── BIND 9.4.2
│
├── HTTP
│   └── Apache
│
├── SMB
│   ├── Samba 3.0.20
│   └── Message signing disabled
│
├── MySQL
│   └── MySQL 5.0.51a
│
├── PostgreSQL
│   └── SSL information
│
├── VNC
│   └── VNC 3.3
│
└── Tomcat
    └── Apache Tomcat 5.5
```

Burada amaç servislerin tamamını "açık" veya "güvensiz" olarak etiketlemek değil, **hangi servislerin daha ileri araştırmaya ihtiyaç duyduğunu belirlemektir.**

---

# 21. Savunma Perspektifi

NSE ile elde edilen bilgiler savunma tarafında da kullanılabilir.

Bir sistem yöneticisi açısından aşağıdaki sorular sorulabilir:

* Anonymous FTP gerçekten gerekli mi?
* FTP yerine güvenli bir aktarım protokolü kullanılabilir mi?
* Eski servis sürümleri güncellenmeli mi?
* SMB message signing gerekli şekilde yapılandırılmış mı?
* SSLv2 gibi eski protokoller devre dışı mı?
* İnternete açık olması gerekmeyen servisler kapatılmış mı?
* Veritabanı servislerine yalnızca gerekli istemciler erişebiliyor mu?
* Tomcat ve diğer uygulama sunucuları güncel mi?
* VNC erişimi ağ üzerinden sınırlandırılmış mı?
* NFS paylaşımlarının erişim izinleri doğru yapılandırılmış mı?

Bu yaklaşım, saldırı yüzeyinin yalnızca saldırgan perspektifinden değil, **savunma ve sistem güvenliği perspektifinden de değerlendirilmesini sağlar.**

---

# 22. Kullanılan Komut

Bu bölümde kullanılan temel komut:

```bash
nmap -sC 192.168.56.20
```

Belirli bir servis için daha hedefli NSE betikleri de kullanılabilir.

Örneğin SMB için:

```bash
nmap -p 139,445 --script smb-os-discovery 192.168.56.20
```

FTP için:

```bash
nmap -p 21 --script ftp-anon 192.168.56.20
```

HTTP için:

```bash
nmap -p 80 --script http-title,http-headers 192.168.56.20
```

Bu yaklaşım, genel NSE taramasından sonra belirli servislerin daha ayrıntılı incelenmesini sağlar.

---

# 23. Elde Edilen Temel Öğrenimler

Bu çalışma sonucunda:

* Nmap NSE'nin amacı öğrenildi.
* `-sC` seçeneğinin varsayılan NSE betiklerini çalıştırdığı öğrenildi.
* Temel Nmap taraması ile NSE taraması arasındaki fark görüldü.
* FTP anonymous erişimi tespit edildi.
* FTP bağlantılarının açık metin olarak raporlandığı görüldü.
* SMTP servisinde SSLv2 desteği tespit edildi.
* DNS servis sürümü belirlendi.
* RPC üzerinden NFS ve diğer servisler hakkında bilgi elde edildi.
* SMB güvenlik yapılandırması incelendi.
* SMB message signing'in devre dışı olduğu görüldü.
* MySQL sürümü tespit edildi.
* VNC protokol bilgisi elde edildi.
* Apache Tomcat 5.5 tespit edildi.
* NSE çıktısının doğrudan "kesin zafiyet" olarak yorumlanmaması gerektiği öğrenildi.
* Servis → sürüm → yapılandırma → CVE → risk şeklindeki analiz zinciri daha net hale getirildi.

---

# 24. Genel Değerlendirme

Nmap NSE, yalnızca açık portları listelemek yerine hedef sistemde çalışan servisler hakkında daha ayrıntılı teknik bilgiler elde edilmesini sağlamaktadır.

Bu laboratuvar çalışmasında NSE kullanılarak Metasploitable 2 üzerinde:

* servis özellikleri,
* sürüm bilgileri,
* erişim yapılandırmaları,
* protokol özellikleri,
* SMB güvenlik ayarları,
* bazı sistem ve ağ bilgileri

incelenmiştir.

Elde edilen veriler bir sonraki aşamada yapılacak **zafiyet araştırması, CVE incelemesi ve risk değerlendirmesi** için kullanılacaktır.

Bu çalışmanın temel çıkarımı:

> **Bir güvenlik değerlendirmesinde amaç yalnızca açık portları bulmak değil, o portların arkasındaki servisleri, sürümleri, yapılandırmaları ve bunların oluşturabileceği riskleri birlikte değerlendirmektir.**

---

## 25. Kanıt / Ekran Görüntüsü

Nmap NSE taramasının terminal çıktısı:

<img width="657" height="536" alt="image" src="https://github.com/user-attachments/assets/d003e609-bf16-42b6-9605-e89d27d18d1f" />
<img width="657" height="560" alt="image" src="https://github.com/user-attachments/assets/1fdc3a3a-7dee-4256-ae34-94a748b36427" />
<img width="658" height="556" alt="image" src="https://github.com/user-attachments/assets/26649ff4-908e-4527-9d2e-45ace3fe9999" />


## 26. Kullanılan Kaynaklar

* Nmap Documentation — Nmap Scripting Engine (NSE)
* Nmap Reference Guide
* Nmap NSE Documentation
* Metasploitable 2 Documentation
* Samba Documentation
* MITRE CVE
* National Vulnerability Database (NVD)

---

## Sonuç

Nmap NSE analizi ile Metasploitable 2'nin saldırı yüzeyi hakkında temel port taramasından daha ayrıntılı bilgiler elde edilmiştir.

Bu aşamada elde edilen sonuçlar doğrudan istismar gerçekleştirmek amacıyla değil, **güvenlik değerlendirmesinin sonraki aşamalarına veri sağlamak** amacıyla kullanılmıştır.

Bir sonraki aşamada bu servislerden seçilen örnekler için daha ayrıntılı **zafiyet ve CVE araştırması** yapılacaktır.
