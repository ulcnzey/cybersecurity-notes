# Risk Assessment — Risk Değerlendirmesi

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde gerçekleştirilen ağ keşfi, servis ve sürüm tespiti, Nmap NSE, FTP, HTTP, SMB/Samba, Nikto, CVE ve SearchSploit araştırmaları sonucunda elde edilen güvenlik bulgularını risk açısından değerlendirmektir.

Değerlendirme sırasında yalnızca tespit edilen teknik bulgular dikkate alınmış; bir bulgunun doğrudan doğrulanmış bir zafiyet veya başarılı exploit sonucu olmadığı durumlarda bu ayrım özellikle korunmuştur.

Temel değerlendirme yaklaşımı:

```text
Bulgu
  ↓
Teknik kanıt
  ↓
Olası güvenlik etkisi
  ↓
Risk gerekçesi
  ↓
Önerilen güvenlik önlemi
```

---

# 2. Risk Kavramı

Siber güvenlikte risk, bir güvenlik olayının gerçekleşme olasılığı ile gerçekleşmesi durumunda oluşturabileceği etkinin birlikte değerlendirilmesiyle ele alınır.

Basitleştirilmiş olarak:

```text
Risk ≈ Olasılık × Etki
```

### Olasılık

Bir güvenlik probleminin kötüye kullanılma ihtimalini ifade eder.

Olasılığı etkileyebilecek bazı faktörler:

* Servisin ağ üzerinden erişilebilir olması
* Kimlik doğrulama gerektirmemesi
* Eski veya desteklenmeyen yazılım sürümleri
* Gereksiz servislerin açık olması
* Hatalı erişim izinleri
* Bilgi ifşası
* Bilinen güvenlik sorunlarının bulunması

### Etki

Bir güvenlik probleminin gerçekleşmesi durumunda oluşabilecek sonucu ifade eder.

Örnek etkiler:

* Bilgi ifşası
* Yetkisiz erişim
* Veri değiştirme
* Veri kaybı
* Hizmet kesintisi
* Kimlik bilgilerinin açığa çıkması
* Sistem bütünlüğünün bozulması

---

# 3. Risk Seviyesi Yaklaşımı

Bu laboratuvar çalışmasında bulguların önemini ifade etmek amacıyla üç seviyeli bir değerlendirme kullanılmıştır:

| Risk Seviyesi | Genel Açıklama                                                                                                           |
| ------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Düşük         | Güvenlik açısından sınırlı etkisi olan veya öncelikli olmayan bulgular                                                   |
| Orta          | Bilgi ifşası, yanlış yapılandırma veya belirli koşullarda kötüye kullanılabilecek bulgular                               |
| Yüksek        | Yetkisiz erişim, veri bütünlüğü/gizliliği üzerinde ciddi etki veya bilinen ciddi güvenlik sorunlarıyla ilişkili bulgular |

Bu seviyeler laboratuvar ortamındaki teknik değerlendirmeyi ifade etmektedir. Gerçek üretim ortamlarında risk seviyesi; varlığın kritiklik seviyesi, tehdit modeli, erişilebilirlik, mevcut güvenlik kontrolleri ve kurumsal risk metodolojisi gibi ek faktörlerle birlikte değerlendirilmelidir.

---

# 4. Tespit Edilen Ana Bulgular

Çalışma boyunca aşağıdaki önemli güvenlik bulguları elde edilmiştir:

1. FTP servisinde anonymous login erişiminin açık olması
2. vsftpd 2.3.4 sürümünün bilinen exploit kayıtlarıyla ilişkilendirilmesi
3. `phpinfo.php` dosyasının web üzerinden erişilebilir olması
4. SMB paylaşımlarında anonymous READ/WRITE erişiminin bulunması
5. Web dizinlerinde directory indexing tespit edilmesi
6. Apache 2.2.8 ve PHP 5.2.4 gibi eski yazılım sürümlerinin kullanılması

---

# 5. Bulgu 1 — Anonymous FTP Erişimi

## 5.1 Teknik Bilgi

Nmap ve manuel FTP testleri sırasında Metasploitable 2 üzerindeki FTP servisinin anonymous login kabul ettiği tespit edilmiştir.

Servis:

```text
21/tcp
vsftpd 2.3.4
```

FTP bağlantısı:

```bash
ftp 192.168.56.20
```

ile gerçekleştirilmiş ve kullanıcı adı olarak:

```text
anonymous
```

kullanılarak bağlantı kurulabilmiştir.

---

## 5.2 Kanıt

Nmap NSE sonucunda anonymous FTP erişiminin aktif olduğu görülmüştür.

Manuel bağlantı sırasında da anonymous kullanıcı ile FTP servisine erişim sağlanmıştır.

Ancak test sırasında tüm sistem dosyalarına erişim sağlandığı veya sistem üzerinde yetki elde edildiği gözlemlenmemiştir.

---

## 5.3 Olası Etki

Anonymous FTP erişimi yanlış yapılandırıldığında yetkisiz kullanıcıların belirli FTP kaynaklarına erişmesine neden olabilir.

Olası etkiler:

* Hassas dosyaların okunması
* Bilgi ifşası
* Dosya yükleme/değiştirme ihtimali
* Saldırganın sonraki saldırılar için bilgi toplaması

Gerçek etki, FTP servisinin yapılandırmasına ve erişim izinlerine bağlıdır.

---

## 5.4 Risk Değerlendirmesi

**Risk Seviyesi: Orta**

Anonymous erişimin kimlik doğrulaması gerektirmemesi saldırı yüzeyini artırmaktadır.

Ancak bu laboratuvar çalışmasında anonymous kullanıcının sistemdeki tüm kaynaklara erişebildiği doğrulanmamıştır.

---

## 5.5 Önerilen Önlemler

* Anonymous FTP erişimi gerekmiyorsa devre dışı bırakılmalıdır.
* FTP erişim izinleri en az yetki prensibine göre yapılandırılmalıdır.
* Hassas dosyaların FTP üzerinden erişilebilir olması engellenmelidir.
* Mümkünse güvenli dosya aktarım protokolleri tercih edilmelidir.
* FTP erişimleri loglanmalı ve izlenmelidir.

---

# 6. Bulgu 2 — vsftpd 2.3.4 ve Exploit Kayıtları

## 6.1 Teknik Bilgi

Nmap servis ve sürüm taramasında:

```text
21/tcp open ftp vsftpd 2.3.4
```

tespit edilmiştir.

Daha sonra CVE araştırmasında **CVE-2011-2523** ile vsftpd 2.3.4 arasında ilişki tespit edilmiştir.

SearchSploit araştırmasında ise:

```text
EDB-ID 49757
EDB-ID 17491
```

numaralı exploit kayıtları bulunmuştur.

---

## 6.2 Araştırma Zinciri

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

---

## 6.3 Önemli Değerlendirme

SearchSploit'te exploit kaydının bulunması, hedef sistemde exploit'in kesin olarak çalışacağı anlamına gelmez.

Aynı şekilde bir ürün sürümünün belirli bir CVE ile eşleşmesi de tek başına hedef sistemde zafiyetin teknik olarak doğrulandığını göstermez.

Bu nedenle bu bulgu:

> "Kesin olarak exploit edilebilir sistem"

şeklinde değil,

> "Bilinen güvenlik araştırmaları ve exploit kayıtlarıyla ilişkili eski bir servis sürümü"

şeklinde değerlendirilmiştir.

---

## 6.4 Olası Etki

İlgili güvenlik sorununun hedef sistemde gerçekten mevcut olması ve uygun koşulların sağlanması durumunda:

* Yetkisiz erişim
* Sistem bütünlüğünün bozulması
* Hizmet üzerinde kontrol kaybı

gibi ciddi etkiler ortaya çıkabilir.

---

## 6.5 Risk Değerlendirmesi

**Risk Seviyesi: Yüksek**

Bu değerlendirme, servisin eski sürümü, ilgili CVE araştırması ve SearchSploit kayıtları birlikte değerlendirilerek yapılmıştır.

Ancak bu çalışma kapsamında exploit çalıştırılmadığından gerçek hedef üzerindeki exploit edilebilirlik doğrulanmamıştır.

---

## 6.6 Önerilen Önlemler

* vsftpd güncel ve desteklenen bir sürüme yükseltilmelidir.
* Gereksiz FTP servisi kapatılmalıdır.
* Servis erişimi ağ seviyesinde sınırlandırılmalıdır.
* Güvenlik güncellemeleri düzenli uygulanmalıdır.
* Servis hesapları ve erişim izinleri kontrol edilmelidir.

---

# 7. Bulgu 3 — phpinfo.php Bilgi İfşası

## 7.1 Teknik Bilgi

Nikto taraması sırasında:

```text
/phpinfo.php
```

dosyasının web sunucusu üzerinden erişilebilir olduğu tespit edilmiştir.

Manuel doğrulama:

```bash
curl -I http://192.168.56.20/phpinfo.php
```

ile gerçekleştirilmiştir.

Sonuçta:

```text
HTTP/1.1 200 OK
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
```

gibi HTTP header bilgileri görülmüştür.

---

## 7.2 Olası Etki

PHP bilgi sayfaları yapılandırmaya bağlı olarak:

* PHP sürümü
* Web sunucusu bilgileri
* Yüklü modüller
* Yapılandırma seçenekleri
* Ortam bilgileri

gibi teknik bilgileri açığa çıkarabilir.

Bu bilgiler saldırganların hedef sistem hakkında daha ayrıntılı keşif yapmasına yardımcı olabilir.

---

## 7.3 Risk Değerlendirmesi

**Risk Seviyesi: Orta**

Bu bulgunun temel riski doğrudan sistem ele geçirilmesinden ziyade bilgi ifşası ve saldırı yüzeyinin daha ayrıntılı şekilde öğrenilmesidir.

---

## 7.4 Önerilen Önlemler

* Production ortamlarında `phpinfo.php` gibi tanılama dosyaları kaldırılmalıdır.
* Gereksiz teknik bilgilerin web üzerinden gösterilmesi engellenmelidir.
* Web sunucusu hata ve bilgi mesajları sınırlandırılmalıdır.
* PHP ve web sunucusu sürümleri güncel tutulmalıdır.

---

# 8. Bulgu 4 — SMB Anonymous READ/WRITE Erişimi

## 8.1 Teknik Bilgi

SMB araştırması sırasında Samba servisinde anonymous kullanıcı erişiminin bazı paylaşımlarda açık olduğu görülmüştür.

Nmap NSE sonucunda:

```text
IPC$ → Anonymous READ/WRITE
tmp   → Anonymous READ/WRITE
```

bilgisi elde edilmiştir.

SMB servisleri:

```text
139/tcp
445/tcp
```

üzerinde çalışmaktadır.

---

## 8.2 Olası Etki

Anonymous READ/WRITE erişimi, yapılandırmaya bağlı olarak yetkisiz kullanıcıların paylaşılan kaynaklara erişmesine neden olabilir.

Olası etkiler:

* Dosya okunması
* Dosya oluşturulması
* Dosya değiştirilmesi
* Hassas bilgilerin elde edilmesi
* Veri bütünlüğünün bozulması

Özellikle yazma yetkisinin bulunması, yalnızca bilgi ifşasından daha geniş bir saldırı yüzeyi oluşturabilir.

---

## 8.3 Risk Değerlendirmesi

**Risk Seviyesi: Yüksek**

Kimlik doğrulaması olmadan READ/WRITE erişiminin bulunması, veri gizliliği ve bütünlüğü açısından önemli bir yanlış yapılandırma olarak değerlendirilmiştir.

Ancak bu çalışmada bu erişimin kullanılarak sistem üzerinde yetki elde edildiği gösterilmemiştir.

---

## 8.4 Önerilen Önlemler

* Anonymous SMB erişimi kapatılmalıdır.
* Paylaşım izinleri yeniden düzenlenmelidir.
* Kullanıcılara yalnızca ihtiyaç duydukları izinler verilmelidir.
* READ ve WRITE yetkileri ayrı ayrı kontrol edilmelidir.
* Hassas paylaşımlar erişim kontrolü ile sınırlandırılmalıdır.
* SMB erişimi güvenilir ağlarla sınırlandırılmalıdır.
* SMB erişimleri loglanmalı ve izlenmelidir.

---

# 9. Bulgu 5 — Directory Indexing

## 9.1 Teknik Bilgi

Nikto taraması sırasında çeşitli web dizinlerinde directory indexing bulguları tespit edilmiştir.

Örnekler:

```text
/doc/
/test/
/icons/
```

Nikto özellikle `/doc/` ve `/test/` gibi dizinlerin listelenebilir olduğunu bildirmiştir.

`/doc/` adresinin HTTP üzerinden erişilebilirliği:

```bash
curl -I http://192.168.56.20/doc/
```

komutu ile kontrol edilmiştir.

Sonuç:

```text
HTTP/1.1 200 OK
```

olarak alınmıştır.

---

## 9.2 Teknik Not

`curl -I` yalnızca HTTP header bilgilerini görüntüler.

Bu nedenle:

```text
HTTP 200 OK
```

sonucu tek başına directory listing içeriğini kanıtlamaz.

Directory indexing bulgusu Nikto çıktısına dayanmaktadır; curl testi ise ilgili URL'nin HTTP üzerinden erişilebilir olduğunu doğrulamaktadır.

---

## 9.3 Olası Etki

Directory indexing aktif olduğunda saldırganlar:

* Dosya ve dizin isimlerini görebilir,
* Uygulamanın yapısı hakkında bilgi toplayabilir,
* Eski veya unutulmuş dosyaları keşfedebilir,
* Sonraki keşif faaliyetlerini kolaylaştırabilir.

---

## 9.4 Risk Değerlendirmesi

**Risk Seviyesi: Orta**

Directory indexing çoğunlukla doğrudan sistem ele geçirme sağlamaz; ancak saldırı yüzeyi hakkında ek bilgi sağlayarak diğer saldırıların keşif aşamasını kolaylaştırabilir.

---

## 9.5 Önerilen Önlemler

* Gereksiz directory indexing devre dışı bırakılmalıdır.
* Web kök dizinindeki gereksiz dosyalar kaldırılmalıdır.
* Test ve dokümantasyon dizinleri production ortamından çıkarılmalıdır.
* Hassas dosyaların web kök dizini altında bulunması engellenmelidir.

---

# 10. Bulgu 6 — Eski Apache ve PHP Sürümleri

## 10.1 Teknik Bilgi

Nmap ve HTTP header analizlerinde:

```text
Apache 2.2.8
PHP 5.2.4
```

sürümleri tespit edilmiştir.

Nmap:

```text
80/tcp open http Apache httpd 2.2.8 ((Ubuntu) DAV/2)
```

Curl:

```text
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
```

bilgilerini göstermiştir.

Nikto da Apache sürümünün eski olduğunu bildirmiştir.

---

## 10.2 Olası Etki

Güncel olmayan yazılım sürümleri:

* Bilinen güvenlik sorunlarına maruz kalma,
* Bilgi ifşası,
* Yetkisiz erişim,
* Hizmet kesintisi,
* Saldırı yüzeyinin genişlemesi

gibi riskler oluşturabilir.

Ancak yalnızca sürüm bilgisinin eski olması, belirli bir zafiyetin hedef sistemde kesin olarak bulunduğunu kanıtlamaz.

---

## 10.3 Risk Değerlendirmesi

**Risk Seviyesi: Yüksek**

Apache ve PHP gibi doğrudan web uygulamasına hizmet veren bileşenlerin eski sürümlerinin kullanılması, güvenlik güncellemelerinin uygulanmadığını gösterebilecek önemli bir güvenlik göstergesidir.

Kesin risk seviyesi üretim ortamında kullanılan uygulamalar, yapılandırma, mevcut güvenlik kontrolleri ve ilgili CVE'ler ile birlikte yeniden değerlendirilmelidir.

---

## 10.4 Önerilen Önlemler

* Apache güncel ve desteklenen bir sürüme yükseltilmelidir.
* PHP güncel ve desteklenen bir sürüme yükseltilmelidir.
* İşletim sistemi güvenlik güncellemeleri uygulanmalıdır.
* Kullanılmayan Apache modülleri devre dışı bırakılmalıdır.
* Web sunucusu düzenli olarak güvenlik taramalarından geçirilmelidir.
* Patch management süreci uygulanmalıdır.

---

# 11. Genel Risk Özeti

| # | Bulgu                                      | Servis     | Port    | Risk Seviyesi |
| - | ------------------------------------------ | ---------- | ------- | ------------- |
| 1 | Anonymous FTP erişimi                      | vsftpd     | 21/tcp  | Orta          |
| 2 | vsftpd 2.3.4 ve ilişkili exploit kayıtları | vsftpd     | 21/tcp  | Yüksek        |
| 3 | `phpinfo.php` erişilebilirliği             | Apache/PHP | 80/tcp  | Orta          |
| 4 | Anonymous SMB READ/WRITE                   | Samba      | 139/445 | Yüksek        |
| 5 | Directory indexing                         | Apache     | 80/tcp  | Orta          |
| 6 | Eski Apache/PHP sürümleri                  | Apache/PHP | 80/tcp  | Yüksek        |

---

# 12. Bulguların Önceliklendirilmesi

Bulguların önemini belirleyen temel faktörler:

### 1. Yetkisiz erişim ihtimali

Kimlik doğrulaması olmadan erişim sağlayan servis ve paylaşımlar daha dikkatli değerlendirilmelidir.

### 2. Veri gizliliği

Bir bulgunun hassas bilgilerin açığa çıkmasına neden olup olmadığı değerlendirilmelidir.

### 3. Veri bütünlüğü

Bir saldırganın verileri değiştirme veya silme ihtimali ayrıca değerlendirilmelidir.

### 4. Saldırı yüzeyi

Gereksiz servisler, açık portlar ve bilgi ifşaları saldırganın keşif faaliyetlerini kolaylaştırabilir.

### 5. Yazılım sürümü

Eski yazılım sürümleri bilinen güvenlik sorunları açısından araştırılmalıdır.

---

# 13. Savunma Perspektifinden Öneriler

Bu laboratuvar sonucunda tespit edilen riskleri azaltmak için aşağıdaki güvenlik önlemleri önerilmektedir:

## Servis Yönetimi

* Kullanılmayan servisler kapatılmalıdır.
* Gereksiz portlar firewall ile sınırlandırılmalıdır.
* Servislerin yalnızca gerekli ağlardan erişilebilir olması sağlanmalıdır.

## Güncelleme ve Patch Management

* İşletim sistemi düzenli olarak güncellenmelidir.
* Web sunucusu ve uygulama bileşenleri desteklenen sürümlerde tutulmalıdır.
* Güvenlik güncellemeleri düzenli takip edilmelidir.

## Erişim Kontrolü

* Anonymous erişimler mümkün olduğunca devre dışı bırakılmalıdır.
* Kullanıcılara yalnızca ihtiyaç duydukları yetkiler verilmelidir.
* Least privilege prensibi uygulanmalıdır.

## Web Güvenliği

* Directory indexing kapatılmalıdır.
* `phpinfo.php` gibi tanılama dosyaları production ortamından kaldırılmalıdır.
* Gereksiz HTTP metodları devre dışı bırakılmalıdır.
* Güvenlik HTTP header'ları uygun şekilde yapılandırılmalıdır.

## Ağ Güvenliği

* SMB erişimi güvenilir ağlarla sınırlandırılmalıdır.
* Ağ segmentasyonu uygulanmalıdır.
* Gereksiz servislerin internete açık olması engellenmelidir.

## İzleme

* Sistem ve servis logları düzenli olarak incelenmelidir.
* Şüpheli bağlantılar izlenmelidir.
* IDS/IPS çözümleri uygun ortamlarda kullanılmalıdır.

---

# 14. Risk ile Zafiyet Arasındaki Fark

Bu çalışmanın önemli öğrenme noktalarından biri, zafiyet ile risk kavramlarının aynı şey olmadığıdır.

### Zafiyet

Sistemin güvenliğini olumsuz etkileyebilecek zayıflık veya yanlış yapılandırmadır.

Örnek:

```text
SMB paylaşımında anonymous READ/WRITE
```

### Risk

Bu zayıflığın gerçekleşme ihtimali ve gerçekleşmesi durumunda oluşturabileceği etkinin değerlendirilmesidir.

Örnek:

```text
Anonymous READ/WRITE
        ↓
Yetkisiz erişim ihtimali
        ↓
Dosya okuma/değiştirme ihtimali
        ↓
Veri gizliliği ve bütünlüğü riski
```

---

# 15. Threat, Vulnerability, Risk ve Attack İlişkisi

Bu çalışmada daha önce öğrenilen kavramlarla birlikte değerlendirme yapılabilir:

```text
Threat
  ↓
Bir tehdit aktörü veya tehdit olayı
  ↓
Vulnerability
  ↓
Sistemdeki zayıflık
  ↓
Attack
  ↓
Zayıflığın kötüye kullanılmaya çalışılması
  ↓
Impact
  ↓
Ortaya çıkan sonuç
  ↓
Risk
  ↓
Olasılık + Etkinin değerlendirilmesi
```

Örneğin SMB açısından:

```text
Threat:
Yetkisiz kullanıcı

        ↓

Vulnerability:
Anonymous READ/WRITE SMB erişimi

        ↓

Attack:
Paylaşıma yetkisiz erişim girişimi

        ↓

Potential Impact:
Veri okunması veya değiştirilmesi

        ↓

Risk:
Veri gizliliği ve bütünlüğünün etkilenme ihtimali
```

Bu ilişki, güvenlik değerlendirmesinin yalnızca araç çıktılarından ibaret olmadığını göstermektedir.

---

# 16. Genel Sonuç

Metasploitable 2 üzerinde gerçekleştirilen çalışmalar sonucunda birden fazla güvenlik bulgusu tespit edilmiştir.

Öne çıkan bulgular:

* Anonymous FTP erişimi
* vsftpd 2.3.4 sürümü ve ilişkili exploit kayıtları
* `phpinfo.php` bilgi ifşası
* SMB anonymous READ/WRITE erişimi
* Directory indexing
* Eski Apache ve PHP sürümleri

Bu bulguların değerlendirilmesi sırasında özellikle **tespit, potansiyel zafiyet ve doğrulanmış exploit edilebilirlik** kavramları birbirinden ayrılmıştır.

Bir güvenlik aracının çıktı üretmesi tek başına sistemin kesin olarak zafiyetli olduğunu kanıtlamaz. Güvenlik değerlendirmesinde araç çıktılarının manuel doğrulama, servis yapılandırması, sürüm bilgileri, CVE araştırması ve ortam koşullarıyla birlikte değerlendirilmesi gerekir.

Bu çalışma sonucunda güvenlik değerlendirmesinin temel yaklaşımı:

```text
Keşfet
  ↓
Tanımla
  ↓
Araştır
  ↓
Doğrula
  ↓
Risk değerlendir
  ↓
Önlem öner
```

şeklinde özetlenebilir.

Bu yaklaşım, sonraki aşamada hazırlanacak mini penetrasyon testi raporunun temelini oluşturacaktır.
