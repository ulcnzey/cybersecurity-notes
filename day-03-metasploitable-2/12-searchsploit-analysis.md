# SearchSploit ile Exploit Araştırması

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde Nmap ve önceki zafiyet araştırmaları sonucunda belirlenen servis ve sürümlerin **SearchSploit** kullanılarak Exploit Database kayıtları açısından araştırılmasıdır.

Bu aşamada herhangi bir exploit çalıştırılmamış, sistem üzerinde yetkisiz erişim veya komut çalıştırma denenmemiştir.

Çalışmanın temel amacı:

* SearchSploit aracının kullanımını öğrenmek,
* servis ve sürüm bilgilerini exploit kayıtlarıyla karşılaştırmak,
* EDB-ID kavramını öğrenmek,
* CVE ile Exploit Database kayıtları arasındaki ilişkiyi anlamak,
* exploit kaydı ile doğrulanmış zafiyet arasındaki farkı kavramaktır.

---

## 2. SearchSploit Nedir?

**SearchSploit**, Exploit Database içerisinde bulunan exploit ve ilgili güvenlik araştırmalarını Kali Linux üzerinden aramaya yarayan komut satırı aracıdır.

Örneğin bir servisin adı ve sürümü biliniyorsa, bu bilgi SearchSploit ile araştırılarak ilgili exploit kayıtları incelenebilir.

Temel kullanım:

```bash
searchsploit <ürün> <sürüm>
```

Örnek:

```bash
searchsploit vsftpd 2.3.4
```

SearchSploit sonuçlarında bulunan kayıtların yanında genellikle bir **EDB-ID** bulunur.

---

## 3. EDB-ID Nedir?

**EDB-ID (Exploit Database ID)**, Exploit Database içerisinde bulunan bir exploit kaydını benzersiz şekilde tanımlayan numaradır.

Örneğin:

```text
vsftpd 2.3.4 - Backdoor Command Execution | 49757
```

Burada:

```text
49757
```

değeri EDB-ID'dir.

EDB-ID, belirli bir exploit kaydının Exploit Database içerisindeki kimliğini belirtir.

Bu nedenle raporlama sırasında exploit başlığının yanında EDB-ID bilgisinin tutulması, araştırılan kaydın daha kolay takip edilmesini sağlar.

---

# 4. SearchSploit Kurulum ve Kullanım Kontrolü

Öncelikle SearchSploit'in sistemde kullanılabilir olduğu kontrol edilmiştir.

Kullanılan komut:

```bash
searchsploit -h
```

Komut sonucunda SearchSploit yardım ekranı görüntülenmiş ve aracın kullanılabilir olduğu doğrulanmıştır.

SearchSploit sürümünü kontrol etmek amacıyla:

```bash
searchsploit --version
```

komutu da denenmiştir.

Ancak kullanılan SearchSploit sürümünde `--version` seçeneğinin desteklenmediği görülmüştür.

Bu nedenle aracın seçenekleri ve kullanım şekli `-h` yardım ekranı üzerinden incelenmiştir.

---

# 5. SearchSploit Temel Seçenekleri

Araştırma sırasında aşağıdaki seçenekler incelenmiştir:

| Seçenek | Açıklama                                 |
| ------- | ---------------------------------------- |
| `-c`    | Büyük/küçük harf duyarlı arama           |
| `-e`    | Tam eşleşme araması                      |
| `-s`    | Daha katı sürüm eşleştirmesi             |
| `-t`    | Yalnızca exploit başlıklarında arama     |
| `--cve` | CVE numarasına göre arama                |
| `--id`  | Sonuçların EDB-ID bilgisini gösterme     |
| `-w`    | Exploit Database bağlantılarını gösterme |
| `-x`    | Bir exploit kaydını inceleme             |
| `-p`    | Exploit dosyasının yolunu gösterme       |
| `-j`    | Sonuçları JSON formatında gösterme       |

Bu çalışmada özellikle:

```bash
searchsploit <ürün> <sürüm>
```

ve:

```bash
searchsploit --id <ürün> <sürüm>
```

komutları kullanılmıştır.

---

# 6. vsftpd 2.3.4 Araştırması

Nmap taraması sonucunda Metasploitable 2 üzerinde aşağıdaki servis belirlenmiştir:

```text
21/tcp open ftp vsftpd 2.3.4
```

Daha önce yapılan CVE araştırmasında **CVE-2011-2523** ile vsftpd 2.3.4 arasında ilişki tespit edilmiştir.

Bu nedenle SearchSploit üzerinden sürüm araştırması yapılmıştır.

Kullanılan komut:

```bash
searchsploit vsftpd 2.3.4
```

Sonuç:

```text
vsftpd 2.3.4 - Backdoor Command Execution     | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit) | unix/remote/17491.rb
Shellcodes: No Results
```

Bu sonuç, SearchSploit veritabanında vsftpd 2.3.4 ile ilişkilendirilen iki exploit kaydı bulunduğunu göstermektedir.

---

## 6.1 EDB-ID Bilgilerinin Görüntülenmesi

Exploit kayıtlarının EDB-ID değerlerini görmek için:

```bash
searchsploit --id vsftpd 2.3.4
```

komutu kullanılmıştır.

Sonuç:

```text
vsftpd 2.3.4 - Backdoor Command Execution     | 49757
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit) | 17491
```

Sonuçlar:

| Ürün         | Exploit kaydı                           | EDB-ID |
| ------------ | --------------------------------------- | -----: |
| vsftpd 2.3.4 | Backdoor Command Execution              |  49757 |
| vsftpd 2.3.4 | Backdoor Command Execution (Metasploit) |  17491 |

---

## 6.2 CVE ile SearchSploit İlişkisi

Daha önce araştırılan:

```text
CVE-2011-2523
```

için SearchSploit üzerinde doğrudan arama yapılmıştır.

Kullanılan komut:

```bash
searchsploit --cve 2011-2523
```

Sonuç:

```text
vsftpd 2.3.4 - Backdoor Command Execution | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit) | unix/remote/17491.rb
```

Bu sonuç, CVE araştırması ile Exploit Database kayıtları arasında bağlantı kurulabileceğini göstermektedir.

Araştırma zinciri şu şekilde özetlenebilir:

```text
Nmap
  ↓
vsftpd 2.3.4
  ↓
CVE-2011-2523 araştırması
  ↓
SearchSploit
  ↓
EDB-ID 49757
EDB-ID 17491
```

Bu zincir, bir servis hakkında yapılan güvenlik araştırmasının farklı kaynaklar kullanılarak nasıl derinleştirilebileceğini göstermektedir.

---

# 7. ProFTPD 1.3.1 Araştırması

Nmap taramasında:

```text
2121/tcp open ftp ProFTPD 1.3.1
```

sonucu elde edilmiştir.

Bu servis için SearchSploit araştırması yapılmıştır.

Kullanılan komut:

```bash
searchsploit proftpd 1.3.1
```

Sonuç:

```text
Exploits: No Results
Shellcodes: No Results
```

Bu sonuç, yapılan sorgu kapsamında SearchSploit yerel veritabanında eşleşen bir kayıt bulunmadığını göstermektedir.

Ancak:

> SearchSploit'te sonuç bulunmaması, ProFTPD 1.3.1 sürümünün güvenli olduğu anlamına gelmez.

Bunun nedeni SearchSploit sonuçlarının kullanılan sorguya, veritabanının içeriğine ve eşleştirme yöntemine bağlı olmasıdır.

Bu nedenle güvenlik değerlendirmesinde yalnızca SearchSploit sonucuna dayanılmamalıdır.

---

# 8. Samba 3.0.20 Araştırması

Nmap NSE çalışmaları sırasında Metasploitable 2 üzerinde Samba ile ilgili bilgiler elde edilmiştir.

Özellikle:

```text
Samba 3.0.20-Debian
```

bilgisi tespit edilmiştir.

SearchSploit araştırması:

```bash
searchsploit samba 3.0.20
```

Sonucunda:

```text
Samba 3.0.10 < 3.3.5 - Format String / Security | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC) | linux_x86/dos/36741.py
Shellcodes: No Results
```

sonuçları elde edilmiştir.

Burada SearchSploit'in sürüm aralıklarını dikkate alan eşleştirme yapabildiği görülmektedir.

Özellikle:

```text
Samba 3.0.20 < 3.0.25rc3
```

kaydı, araştırılan Samba sürümüyle doğrudan ilişkilendirilebilecek bir sonuç olarak ayrıca incelenebilir.

Ancak bu kayıt yalnızca bir **araştırma göstergesidir**.

SearchSploit sonucunun bulunması tek başına:

* hedefin kesin olarak zafiyetli olduğunu,
* exploit'in kesin olarak çalışacağını,
* sistem üzerinde yetki elde edilebileceğini

kanıtlamaz.

---

# 9. Apache 2.2.8 Araştırması

Nmap sonucunda HTTP servisi:

```text
80/tcp open http Apache httpd 2.2.8 ((Ubuntu) DAV/2)
```

olarak tespit edilmiştir.

SearchSploit ile Apache 2.2.8 araştırılmıştır:

```bash
searchsploit apache 2.2.8
```

Arama sonucunda Apache ve Apache ekosistemindeki farklı bileşenlere ait çok sayıda kayıt elde edilmiştir.

EDB-ID değerlerini görmek için:

```bash
searchsploit --id apache 2.2.8
```

komutu kullanılmıştır.

Önemli sonuçlardan bazıları:

| Exploit başlığı                                                 | EDB-ID |
| --------------------------------------------------------------- | -----: |
| Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution |  29290 |
| Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution         |  29316 |
| Apache < 2.0.64 / < 2.2.21 mod_setenvif - Integer Overflow      |  41769 |
| Apache < 2.2.34 / < 2.4.27 - OPTIONS Memory Leak                |  42745 |
| Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service             |  26710 |
| Apache mod_ssl < 2.8.7 OpenSSL - OpenFuck                       |  21671 |
| Apache Tomcat < 5.5.17 - Remote Directory Listing               |   2061 |
| Apache Tomcat < 6.0.18 - UTF8 Directory Traversal               |   6229 |
| Apache Tomcat < 6.0.18 - UTF8 Directory Traversal               |  14489 |
| Apache Xerces-C XML Parser < 3.1.2 - Denial of Service          |  36906 |

SearchSploit çıktısının geniş olmasının temel nedeni, `apache` ifadesinin Apache HTTP Server dışında Apache ekosistemindeki farklı ürün ve bileşenlerle de eşleşebilmesidir.

Örneğin:

```text
Apache Struts
Apache Tomcat
Apache CXF
Apache OpenMeetings
Apache Xerces
```

gibi ürünler ayrı bileşenlerdir.

Bu nedenle SearchSploit'te bir sonuç görmek, ilgili ürünün Metasploitable 2 üzerinde çalıştığı anlamına gelmez.

---

# 10. Strict Search ile Karşılaştırma

Apache için daha katı bir arama yapılmıştır:

```bash
searchsploit -s "Apache 2.2.8"
```

Sonuç:

```text
Exploits: No Results
Shellcodes: No Results
```

Burada önemli olan nokta, normal aramada sonuç bulunmasına rağmen strict aramada sonuç bulunmamasıdır.

Bu durum SearchSploit'in arama yöntemlerinin sonuçları etkileyebileceğini göstermektedir.

Dolayısıyla:

```text
Strict search → sonuç yok
```

ifadesi:

```text
Apache 2.2.8 güvenlidir.
```

anlamına gelmez.

Aynı şekilde:

```text
Normal search → sonuç var
```

ifadesi de:

```text
Apache 2.2.8 kesin olarak zafiyetlidir.
```

anlamına gelmez.

Sonuçların ilgili ürün, sürüm, bileşen ve zafiyet koşulları açısından ayrıca doğrulanması gerekir.

---

# 11. SearchSploit Sonuçlarının Güvenlik Açısından Değerlendirilmesi

SearchSploit araştırması sırasında aşağıdaki önemli ayrım yapılmıştır:

### Exploit kaydı bulunması

Bir exploit kaydının bulunması, ilgili ürün veya sürüm için daha önce bir exploit araştırması yapıldığını gösterebilir.

Ancak bu durum tek başına hedef sistemde zafiyetin doğrulandığını göstermez.

### Sürüm eşleşmesi

Bir ürünün sürümünün exploit kaydındaki sürüm aralığıyla eşleşmesi, araştırma açısından önemli bir göstergedir.

Fakat gerçek sistemde:

* yapılandırma,
* patch durumu,
* kullanılan modüller,
* işletim sistemi,
* erişim kontrolleri,
* servis konfigürasyonu

gibi faktörler sonucu değiştirebilir.

### Exploit edilebilirlik

Bir zafiyetin teorik olarak mevcut olması ile belirli bir hedef sistem üzerinde başarılı şekilde istismar edilebilmesi aynı şey değildir.

Bu nedenle güvenlik değerlendirmesi şu şekilde ilerlemelidir:

```text
Servis tespiti
      ↓
Sürüm tespiti
      ↓
CVE / zafiyet araştırması
      ↓
SearchSploit araştırması
      ↓
Teknik doğrulama
      ↓
Risk değerlendirmesi
```

---

# 12. Bu Çalışmada Kullanılan Komutlar

### SearchSploit yardım ekranı

```bash
searchsploit -h
```

### Servis ve sürüm araştırması

```bash
searchsploit vsftpd 2.3.4
searchsploit proftpd 1.3.1
searchsploit samba 3.0.20
searchsploit apache 2.2.8
```

### EDB-ID görüntüleme

```bash
searchsploit --id vsftpd 2.3.4
searchsploit --id apache 2.2.8
```

### CVE araştırması

```bash
searchsploit --cve 2011-2523
```

### Strict arama

```bash
searchsploit -s "Apache 2.2.8"
```

---

# 13. Güvenlik ve Etik Sınırlar

Bu çalışmada SearchSploit yalnızca **araştırma ve analiz amacıyla** kullanılmıştır.

Exploit kayıtları incelenmiş ancak herhangi bir exploit:

* gerçek sistemlerde çalıştırılmamış,
* internete açık sistemlere karşı denenmemiş,
* yetkisiz erişim amacıyla kullanılmamış,
* Metasploitable 2 üzerinde dahi bu aşamada çalıştırılmamıştır.

Laboratuvar ortamı:

```text
Kali Linux
      │
      │  cyber-lab
      │
      ▼
Metasploitable 2
```

şeklinde izole bir sanal ağ üzerinde kurulmuştur.

Bu yaklaşım, güvenlik araştırmalarının kontrollü ve yetkili bir ortamda gerçekleştirilmesini sağlar.

---

# 14. Önemli Kavramsal Ayrımlar

Bu çalışmada aşağıdaki kavramların birbirinden ayrılması gerektiği görülmüştür:

| Kavram              | Anlamı                                                                   |
| ------------------- | ------------------------------------------------------------------------ |
| SearchSploit sonucu | İlgili sorguyla eşleşen exploit araştırma kaydı                          |
| EDB-ID              | Exploit Database kaydının benzersiz kimliği                              |
| CVE                 | Güvenlik zafiyetlerini tanımlamak için kullanılan kimliklendirme sistemi |
| Sürüm eşleşmesi     | Ürün sürümünün belirli bir kayıtla ilişkilendirilebilmesi                |
| Zafiyet doğrulaması | Belirli sistemde güvenlik açığının teknik olarak doğrulanması            |
| Exploit             | Bir zafiyetten yararlanmak için kullanılan yöntem/kod                    |
| Risk                | Zafiyetin gerçekleşme olasılığı ve etkisinin birlikte değerlendirilmesi  |

Özellikle şu ayrım önemlidir:

> **Exploit kaydı bulunması ≠ doğrulanmış zafiyet**

ve:

> **SearchSploit'te sonuç bulunmaması ≠ sistem güvenli**

Bu nedenle SearchSploit, güvenlik değerlendirmesinin tek başına yeterli bir aracı olarak değil, diğer analiz yöntemlerini destekleyen bir araştırma aracı olarak değerlendirilmelidir.

---

# 15. Genel Değerlendirme

SearchSploit çalışması sonucunda Metasploitable 2 üzerinde daha önce Nmap ile belirlenen servislerin Exploit Database kayıtları açısından araştırılabileceği görülmüştür.

Özellikle **vsftpd 2.3.4** için yapılan araştırmada:

```text
vsftpd 2.3.4
      ↓
CVE-2011-2523
      ↓
SearchSploit
      ↓
EDB-ID 49757
EDB-ID 17491
```

şeklinde açık bir araştırma zinciri oluşturulmuştur.

Samba ve Apache araştırmalarında ise SearchSploit'in sürüm aralıklarını ve ilişkili ürünleri de sonuçlara dahil edebildiği görülmüştür.

Bu nedenle SearchSploit çıktılarının doğrudan "zafiyet listesi" olarak değerlendirilmemesi; servis, sürüm, yapılandırma, CVE bilgisi ve diğer teknik bulgularla birlikte analiz edilmesi gerektiği sonucuna varılmıştır.

---

## 16. Öğrenilenler

Bu çalışma sonucunda:

* SearchSploit'in temel kullanımını,
* Exploit Database kavramını,
* EDB-ID'nin ne olduğunu,
* CVE ile EDB kayıtları arasındaki ilişkiyi,
* SearchSploit'te sürüm eşleştirmesinin nasıl çalışabildiğini,
* normal ve strict arama arasındaki farkı,
* exploit kaydı ile doğrulanmış zafiyet arasındaki farkı,
* Apache gibi geniş ekosistemlerde sonuçların neden dikkatli yorumlanması gerektiğini

öğrenmiş oldum.

Bu aşamadaki temel kazanım, bir güvenlik aracının ürettiği sonucu doğrudan doğru kabul etmek yerine, sonucu **bağlamı içerisinde değerlendirmek ve teknik olarak doğrulamak** gerektiğini anlamaktır.

---

## 17. Sonraki Aşama

SearchSploit araştırmasının tamamlanmasının ardından bir sonraki aşamada elde edilen bulgular:

* etkilenen servis,
* port,
* sürüm,
* potansiyel zafiyet,
* etki,
* risk gerekçesi,
* önerilen çözüm

başlıkları altında değerlendirilerek bir **risk değerlendirme tablosuna** dönüştürülecektir.

Bu aşamada amaç, bulunan bilgileri yalnızca teknik açıdan listelemek değil, güvenlik açısından anlamlandırmaktır.
