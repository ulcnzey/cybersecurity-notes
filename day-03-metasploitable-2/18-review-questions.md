# 18. Değerlendirme Soruları

Bu bölümde Metasploitable 2 güvenlik değerlendirmesi boyunca öğrenilen temel konular gözden geçirilmiştir. Cevaplar, çalışma sırasında edinilen bilgiler doğrultusunda kendi cümlelerimle hazırlanmıştır.

---

## 1. Metasploitable 2 neden kullanıldı?

Metasploitable 2, güvenlik testleri ve siber güvenlik eğitimi amacıyla hazırlanmış, kasıtlı olarak zayıf ve eski servisler barındıran bir sanal makinedir.

Bu çalışma kapsamında gerçek sistemlere zarar vermeden Nmap, NSE, Nikto ve SearchSploit gibi araçlarla güvenlik değerlendirmesi yapabilmek için kullanılmıştır.

Ayrıca açık port, servis, servis sürümü, yapılandırma ve potansiyel zafiyet arasındaki ilişkiyi uygulamalı olarak anlamamı sağladı.

---

## 2. Nmap bize hangi bilgileri sağlayabilir?

Nmap temel olarak ağ üzerindeki sistemleri ve sistemlerde erişilebilir olan servisleri keşfetmek için kullanılabilir.

Yapılan tarama türüne göre:

* Açık portlar
* Portların durumları
* Çalışan servisler
* Servis sürümleri
* İşletim sistemi hakkında bilgiler
* Bazı servis yapılandırmaları
* NSE scriptleri aracılığıyla ek servis bilgileri

elde edilebilir.

Bu çalışmada örneğin:

```bash
nmap 192.168.56.20
```

ile açık portlar,

```bash
nmap -sV 192.168.56.20
```

ile servis ve sürüm bilgileri incelenmiştir.

---

## 3. Port ile servis arasındaki ilişki nedir?

Port, bir sistem üzerinde ağ trafiğinin yönlendirildiği mantıksal bir iletişim noktasıdır.

Servis ise bu port üzerinden ağ bağlantılarını kabul eden uygulama veya uygulama bileşenidir.

Örneğin bu laboratuvarda:

```text
21/tcp → FTP
22/tcp → SSH
80/tcp → HTTP
445/tcp → SMB
3306/tcp → MySQL
```

şeklinde eşleşmeler görülmüştür.

Bu nedenle port ve servis aynı şey değildir. Port, iletişim noktası; servis ise o iletişim noktasını kullanan uygulamadır.

---

## 4. Servis versiyonunu öğrenmek neden önemlidir?

Servisin sürümünü öğrenmek, kullanılan yazılımın hangi güvenlik durumunda olduğunu değerlendirmek açısından önemlidir.

Bazı eski yazılım sürümleri için bilinen CVE kayıtları veya güvenlik problemleri bulunabilir.

Örneğin çalışmada:

```text
vsftpd 2.3.4
Apache 2.2.8
ProFTPD 1.3.1
```

gibi eski sürümler tespit edilmiştir.

Ancak bir sürümün eski olması tek başına kesin olarak zafiyet bulunduğu anlamına gelmez. Sürüm bilgisi, daha ayrıntılı güvenlik araştırması için başlangıç noktasıdır.

---

## 5. CVE nedir?

CVE, **Common Vulnerabilities and Exposures** ifadesinin kısaltmasıdır.

Yazılımlarda ve sistemlerde bulunan kamuya açık güvenlik açıklarının standart şekilde tanımlanmasını sağlayan bir kimliklendirme sistemidir.

Örneğin bu çalışmada:

```text
CVE-2011-2523
```

gibi bir kayıt incelenmiştir.

CVE numarası, belirli bir güvenlik problemini araştırırken ortak bir referans noktası sağlar.

---

## 6. CVE ile exploit arasındaki fark nedir?

CVE, bir güvenlik açığını tanımlayan standart kimliktir.

Exploit ise belirli bir güvenlik açığından yararlanmak amacıyla kullanılan yöntem, kod veya araçtır.

Kısaca:

```text
CVE → Güvenlik açığının tanımlanması
Exploit → Güvenlik açığından yararlanma yöntemi
```

Örneğin bir CVE kaydının bulunması, o güvenlik açığının tanımlandığını gösterir. Bu açığı kullanmaya yönelik bir exploit ise ayrı bir konudur.

Ayrıca bir exploit kaydının bulunması, her sistemde exploitin kesin olarak başarılı olacağı anlamına gelmez.

---

## 7. Vulnerability Assessment ile Penetration Testing arasındaki fark nedir?

**Vulnerability Assessment**, sistemdeki potansiyel güvenlik açıklarını belirlemeye ve değerlendirmeye odaklanır.

**Penetration Testing** ise belirlenen güvenlik açıklarının kontrollü ve yetkili bir test kapsamında gerçekten kullanılabilir olup olmadığını değerlendirmeyi de içerebilir.

Basit şekilde:

```text
Vulnerability Assessment
→ "Sistemde hangi güvenlik problemleri olabilir?"

Penetration Testing
→ "Bu güvenlik problemi kontrollü bir testte gerçekten kullanılabilir mi?"
```

Bu çalışmada ağırlıklı olarak keşif, enumeration, zafiyet araştırması ve risk değerlendirmesi yapılmıştır. Exploit ve post-exploitation aşamalarına geçilmemiştir.

---

## 8. NSE nedir?

NSE, **Nmap Scripting Engine** ifadesinin kısaltmasıdır.

Nmap'in scriptler kullanarak standart port taramasından daha ayrıntılı bilgiler toplamasını sağlar.

Örneğin:

```bash
nmap -sC 192.168.56.20
```

komutu Nmap'in varsayılan NSE scriptlerini çalıştırır.

Bu çalışmada NSE kullanılarak:

* FTP anonymous erişimi
* SMB paylaşım bilgileri
* SMB kullanıcı bilgileri
* SMB işletim sistemi bilgileri
* HTTP bilgileri
* SMTP bilgileri
* NFS ve RPC bilgileri

gibi çeşitli bilgiler incelenmiştir.

---

## 9. Enumeration neden önemlidir?

Enumeration, keşfedilen servisler hakkında daha ayrıntılı bilgi toplama aşamasıdır.

Sadece:

```text
445/tcp → SMB açık
```

bilgisini bilmek sınırlıdır.

Enumeration sonucunda:

```text
Samba sürümü
↓
SMB paylaşım isimleri
↓
Erişim izinleri
↓
Kullanıcı bilgileri
↓
Yapılandırma hakkında bilgiler
```

gibi daha ayrıntılı bilgiler elde edilebilir.

Bu nedenle enumeration, saldırı yüzeyini ve potansiyel güvenlik problemlerini daha iyi anlamaya yardımcı olur.

---

## 10. Bir portun açık olması neden tek başına güvenlik açığı değildir?

Açık bir port, o port üzerinden bir servisin erişilebilir olduğunu gösterir.

Ancak bir servisin erişilebilir olması otomatik olarak güvenlik açığı bulunduğu anlamına gelmez.

Güvenlik değerlendirmesinde ayrıca:

* Servisin ne olduğu
* Sürümü
* Yapılandırması
* Kimlik doğrulama mekanizması
* Erişim izinleri
* Bilinen güvenlik problemleri
* Ağdaki konumu

incelenmelidir.

Örneğin 80 numaralı portun açık olması normal bir web sunucusunun çalıştığını gösterebilir. Güvenlik açısından asıl önemli olan web servisinin nasıl yapılandırıldığı ve hangi güvenlik problemlerini barındırdığıdır.

---

## 11. Eski bir servis versiyonu neden risk oluşturabilir?

Eski bir servis sürümü zaman içerisinde keşfedilmiş ve yayımlanmış güvenlik açıklarına sahip olabilir.

Ayrıca eski sürümler:

* Güvenlik güncellemelerinden yoksun olabilir.
* Destek süresi bitmiş olabilir.
* Güncel güvenlik mekanizmalarını içermeyebilir.
* Bilinen saldırı tekniklerine karşı daha savunmasız olabilir.

Ancak eski sürüm görmek, tek başına belirli bir güvenlik açığının kesin olarak mevcut olduğunu kanıtlamaz. Sürümün hangi CVE'lerden etkilendiği ve sistemin gerçek yapılandırması ayrıca incelenmelidir.

---

## 12. Bir güvenlik açığının CVSS skoru yüksekse bu tek başına sistemin kesinlikle saldırıya uğrayacağı anlamına gelir mi?

Hayır.

CVSS, bir güvenlik açığının teknik önemini ve olası etkisini değerlendirmek için kullanılan bir puanlama sistemidir.

Yüksek CVSS skoru önemli bir güvenlik göstergesi olabilir ancak tek başına sistemin kesinlikle saldırıya uğrayacağını göstermez.

Gerçek risk değerlendirmesinde ayrıca:

* Sistemin internete veya ağa açık olup olmadığı
* Güvenlik açığının gerçekten mevcut olup olmadığı
* Yapılandırma
* Kimlik doğrulama mekanizmaları
* Firewall kuralları
* Ağ segmentasyonu
* Kullanılan güvenlik kontrolleri
* Varlığın kritikliği

gibi faktörler değerlendirilmelidir.

---

## 13. Saldırganın hedef sistem hakkında bilgi toplamasını engellemek neden önemlidir?

Bir saldırgan hedef hakkında ne kadar fazla bilgi elde ederse, saldırı yüzeyini anlaması ve potansiyel hedefleri belirlemesi o kadar kolaylaşabilir.

Örneğin:

```text
Servis
↓
Sürüm
↓
İşletim sistemi
↓
Kullanıcı bilgileri
↓
Paylaşımlar
↓
Web dizinleri
```

gibi bilgilerin gereksiz şekilde dışarıya açılması reconnaissance ve enumeration aşamalarını kolaylaştırabilir.

Bu nedenle gereksiz servislerin kapatılması, bilgi sızıntısının azaltılması ve erişimlerin sınırlandırılması önemlidir.

---

## 14. Network segmentation saldırının etkisini nasıl azaltabilir?

Network segmentation, ağı farklı bölümlere ayırarak sistemler arasındaki erişimi sınırlandırır.

Örneğin:

```text
Internet
   │
Firewall
   │
DMZ
   │
Application Network
   │
Database Network
```

şeklinde ayrıştırılmış bir ağ yapısında bir sistem ele geçirilse bile diğer sistemlere doğrudan erişim mümkün olmayabilir.

Bu durum saldırganın ağ içerisinde ilerlemesini zorlaştırabilir ve olası saldırının etkisini sınırlandırabilir.

Bu nedenle network segmentation yalnızca saldırıları önlemeye değil, bir güvenlik olayının yayılmasını sınırlamaya da yardımcı olur.

---

## 15. Bir sistemde 100 güvenlik açığı bulunması ile 1 kritik güvenlik açığı bulunması arasındaki fark nasıl değerlendirilmelidir?

Güvenlik açığı sayısı tek başına sistemin güvenlik durumunu belirlemek için yeterli değildir.

100 düşük etkili bulgu ile 1 kritik etkili bulgu aynı şekilde değerlendirilmemelidir.

Örneğin bir bulgunun:

* Etkisi
* Saldırılabilirliği
* Sisteme erişim gereksinimleri
* Varlığın önemi
* Veri üzerindeki etkisi
* Yetki seviyesine etkisi
* Ağ içerisindeki konumu

birlikte değerlendirilmelidir.

Bu nedenle güvenlik değerlendirmesinde yalnızca:

> "Kaç tane açık var?"

sorusu değil,

> "Hangi açıkların etkisi daha önemli ve hangileri öncelikli olarak ele alınmalı?"

sorusu da sorulmalıdır.

---

# Genel Değerlendirme

Bu soruların tamamı birlikte değerlendirildiğinde güvenlik değerlendirmesinin yalnızca araç çalıştırmaktan ibaret olmadığı görülmektedir.

Temel yaklaşım şu şekilde özetlenebilir:

```text
Keşif
  ↓
Tarama
  ↓
Enumeration
  ↓
Servis ve sürüm analizi
  ↓
Zafiyet araştırması
  ↓
Risk değerlendirmesi
  ↓
Önceliklendirme
  ↓
Güvenlik önlemleri
```

Bu çalışma sayesinde bir sistem değerlendirilirken **açık port, servis, sürüm, yapılandırma, zafiyet, tehdit, risk ve saldırı** kavramlarının birbirinden farklı olduğu ve birlikte değerlendirilmesi gerektiği anlaşılmıştır.

Ayrıca güvenlik testlerinde teknik bulgu ile kesin saldırı başarısının aynı şey olmadığı görülmüştür. Bir bulgunun gerçekten güvenlik riski oluşturup oluşturmadığını anlayabilmek için teknik verilerin, yapılandırmanın ve sistemin bağlamının birlikte değerlendirilmesi gerekir.
