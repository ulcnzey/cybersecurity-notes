# 04 - Attack Surface Analysis

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde Nmap ile tespit edilen açık portları ve çalışan servisleri değerlendirerek sistemin saldırı yüzeyini belirlemektir.

Bu aşamada herhangi bir istismar işlemi gerçekleştirilmemiştir. Çalışma, yalnızca yetkilendirilmiş ve izole edilmiş laboratuvar ortamında saldırı yüzeyinin anlaşılması ve güvenlik açısından değerlendirilmesi amacıyla gerçekleştirilmiştir.

---

## 2. Saldırı Yüzeyi Nedir?

Saldırı yüzeyi (Attack Surface), bir sistemin dışarıdan erişilebilen veya sistemle etkileşim kurulmasını sağlayan potansiyel giriş noktalarının bütünüdür.

Bir sistem açısından saldırı yüzeyini oluşturan unsurlara örnek olarak:

* Açık ağ portları
* Çalışan ağ servisleri
* Web uygulamaları
* Veritabanı servisleri
* Uzak erişim servisleri
* Dosya paylaşım servisleri
* Kullanıcı hesapları
* API'ler
* Yazılım sürümleri
* Yapılandırmalar

verilebilir.

---

## 3. Port, Servis ve Saldırı Yüzeyi İlişkisi

Bir IP adresi ağ üzerindeki sistemi belirtirken, portlar sistem üzerindeki iletişim noktalarını temsil eder.

Örneğin:

```text
192.168.56.20:21
```

ifadesinde:

* `192.168.56.20` → hedef sistemin IP adresi
* `21` → hedef port
* `FTP` → port üzerinde çalışan servis
* `vsftpd 2.3.4` → tespit edilen yazılım ve sürüm

bulunmaktadır.

Bu nedenle güvenlik analizinde temel ilişki şu şekilde kurulabilir:

```text
IP Adresi
    ↓
Açık Port
    ↓
Servis
    ↓
Yazılım
    ↓
Sürüm
    ↓
Zafiyet Araştırması
    ↓
Etki
    ↓
Risk
```

---

## 4. Nmap Sonuçlarına Göre Saldırı Yüzeyi

Gerçekleştirilen Nmap taramasında Metasploitable 2 üzerinde 23 açık TCP portu tespit edilmiştir.

Tespit edilen servisler farklı kategoriler altında değerlendirilebilir.

### Uzak Erişim Servisleri

```text
22    SSH
23    Telnet
512   exec
513   login
514   shell
5900  VNC
```

Bu servislerin ortak özelliği uzak sistem erişimi veya uzak yönetim ile ilişkili olmalarıdır.

---

### Web Servisleri

```text
80    HTTP / Apache
8009  AJP
8180  HTTP / Apache Tomcat
```

Bu servisler web sunucuları veya Java tabanlı web uygulamalarıyla ilişkilidir.

Web servislerinde yalnızca sunucu yazılımı değil, uygulama kodu, yapılandırmalar ve erişim kontrolleri de saldırı yüzeyinin bir parçası olabilir.

---

### Dosya ve Ağ Paylaşım Servisleri

```text
139   NetBIOS / SMB
445   SMB / Samba
2049  NFS
```

Bu servisler ağ üzerinden dosya veya kaynak paylaşımıyla ilişkilidir.

Bu servisler değerlendirilirken erişim izinleri, paylaşım yapılandırmaları ve kullanılan yazılım sürümleri incelenebilir.

---

### Veritabanı Servisleri

```text
3306  MySQL
5432  PostgreSQL
```

Veritabanı servislerinin ağ üzerinden erişilebilir olması, sistemin saldırı yüzeyinin bir parçasını oluşturur.

Güvenlik değerlendirmesinde:

* Servisin ağ üzerinden erişilebilir olup olmadığı
* Erişim kontrolü
* Kimlik doğrulama
* Kullanılan yazılım sürümü
* Ağ erişiminin gerekli olup olmadığı

gibi konular incelenmelidir.

---

### Diğer Ağ Servisleri

```text
21    FTP
25    SMTP
53    DNS
111   RPC
1099  Java RMI
1524  Bindshell
6667  IRC
```

Bu servislerin her biri farklı bir işlev sağlamakta ve sistemin toplam saldırı yüzeyine katkıda bulunmaktadır.

---

## 5. Açık Port ile Zafiyet Arasındaki Fark

Açık bir portun bulunması doğrudan güvenlik zafiyeti olduğu anlamına gelmez.

Örneğin:

```text
80/tcp open http
```

sonucu yalnızca hedef sistemde HTTP servisinin çalıştığını gösterir.

Bu sonuçtan doğrudan:

```text
HTTP servisi zafiyetlidir.
```

sonucuna varılamaz.

Bir zafiyet değerlendirmesi için servis hakkında daha fazla bilgiye ihtiyaç vardır.

Örnek değerlendirme zinciri:

```text
80/tcp
   ↓
HTTP
   ↓
Apache
   ↓
Apache 2.2.8
   ↓
Bilinen güvenlik sorunlarının araştırılması
   ↓
Etkisinin değerlendirilmesi
```

Bu nedenle keşif aşaması ile zafiyet değerlendirmesi birbirinden ayrı tutulmalıdır.

---

## 6. Servis Sürümünün Önemi

Nmap'in `-sV` seçeneği ile bazı servislerin yazılım ve sürüm bilgileri tespit edilmiştir.

Örneğin:

```text
21/tcp open ftp vsftpd 2.3.4
```

Bu bilgi:

* Port: `21`
* Servis: `FTP`
* Yazılım: `vsftpd`
* Sürüm: `2.3.4`

şeklinde ayrıştırılabilir.

Benzer şekilde:

```text
3306/tcp open mysql MySQL 5.0.51a-3ubuntu5
```

sonucunda:

* Port: `3306`
* Servis: `MySQL`
* Yazılım: `MySQL`
* Sürüm: `5.0.51a-3ubuntu5`

bilgileri elde edilmiştir.

Sürüm bilgisi, sonraki aşamada bilinen güvenlik zafiyetlerinin araştırılması için kullanılacaktır.

---

## 7. Metasploitable 2 Üzerindeki Dikkat Çeken Saldırı Yüzeyleri

Tarama sonucunda aşağıdaki servisler sonraki güvenlik araştırmaları için incelenebilecek saldırı yüzeyleri olarak belirlenmiştir:

| Port | Servis     | Saldırı Yüzeyi Kategorisi |
| ---: | ---------- | ------------------------- |
|   21 | FTP        | Dosya aktarımı            |
|   22 | SSH        | Uzak erişim               |
|   23 | Telnet     | Uzak erişim               |
|   80 | HTTP       | Web                       |
|  139 | SMB        | Dosya paylaşımı           |
|  445 | SMB        | Dosya paylaşımı           |
| 1099 | Java RMI   | Uzak servis               |
| 1524 | Bindshell  | Uzak erişim               |
| 2049 | NFS        | Dosya paylaşımı           |
| 2121 | FTP        | Dosya aktarımı            |
| 3306 | MySQL      | Veritabanı                |
| 5432 | PostgreSQL | Veritabanı                |
| 5900 | VNC        | Uzak masaüstü             |
| 6667 | IRC        | Ağ servisi                |
| 8009 | AJP        | Web/Java                  |
| 8180 | Tomcat     | Web                       |

Bu liste, sonraki zafiyet araştırmalarının hangi servislerden başlanabileceğini belirlemek için kullanılacaktır.

---

## 8. Saldırı Yüzeyinin Azaltılması

Savunma açısından temel amaç, sistemin gereksiz saldırı yüzeyini mümkün olduğunca azaltmaktır.

Bunun için aşağıdaki güvenlik önlemleri uygulanabilir:

* Gereksiz servislerin kapatılması
* Kullanılmayan portların erişime kapatılması
* Güncel ve desteklenen yazılım sürümlerinin kullanılması
* Firewall kurallarının uygulanması
* Ağ segmentasyonu
* Erişim kontrol mekanizmalarının kullanılması
* Güçlü kimlik doğrulama
* En az ayrıcalık prensibinin uygulanması
* Servislerin yalnızca ihtiyaç duyan sistemlere açılması
* Loglama ve güvenlik izleme mekanizmalarının kullanılması

Temel prensip:

```text
Gereksiz servis
      ↓
Gereksiz açık port
      ↓
Daha geniş saldırı yüzeyi
```

Bu nedenle saldırı yüzeyini azaltmanın temel yollarından biri, sistemin gerçekten ihtiyaç duymadığı servisleri çalıştırmamak veya ağ üzerinden erişilebilir bırakmamaktır.

---

## 9. Güvenlik Analizinde Sorulması Gereken Sorular

Bir saldırı yüzeyi analiz edilirken aşağıdaki sorular kullanılabilir:

1. Bu servis neden çalışıyor?
2. Bu servise gerçekten ihtiyaç var mı?
3. Servisin hangi yazılımı kullanılıyor?
4. Kullanılan yazılımın sürümü nedir?
5. Bu sürüm için bilinen güvenlik zafiyetleri var mı?
6. Servise kimlerin erişmesi gerekiyor?
7. Servis ağ üzerinden erişilebilir olmak zorunda mı?
8. Erişim firewall ile sınırlandırılmış mı?
9. Servisin güvenli yapılandırıldığı doğrulanmış mı?
10. Servisin oluşturduğu risk kabul edilebilir seviyede mi?

Bu sorular, yalnızca açık portların listelenmesinden daha kapsamlı bir güvenlik değerlendirmesi yapılmasını sağlar.

---

## 10. Saldırı Yüzeyi Analizinin Güvenlik Sürecindeki Yeri

Saldırı yüzeyi analizi, güvenlik değerlendirmesinin erken aşamalarından biridir.

Genel süreç şu şekilde gösterilebilir:

```text
Keşif
  ↓
Port Tespiti
  ↓
Servis Tespiti
  ↓
Sürüm Tespiti
  ↓
Saldırı Yüzeyi Analizi
  ↓
Zafiyet Araştırması
  ↓
Risk Değerlendirmesi
  ↓
Gerekli Güvenlik Önlemleri
```

Bu çalışmada Nmap kullanılarak yapılan tarama, saldırı yüzeyinin belirlenmesi için temel veri sağlamıştır.

---

## 11. Sonuç

Metasploitable 2 üzerinde gerçekleştirilen ağ taraması sonucunda çok sayıda ağ servisinin erişilebilir olduğu görülmüştür.

Tespit edilen açık portlar; uzak erişim, web, dosya paylaşımı, veritabanı ve diğer ağ servisleri olmak üzere farklı saldırı yüzeyi kategorilerine ayrılmıştır.

Açık bir portun tek başına güvenlik zafiyeti anlamına gelmediği, ancak çalışan servisin ve kullanılan yazılım sürümünün güvenlik açısından araştırılması gereken bir giriş noktası oluşturduğu değerlendirilmiştir.

Bu çalışma sonucunda elde edilen servis ve sürüm bilgileri, sonraki aşamada gerçekleştirilecek **zafiyet ve CVE araştırmasının temelini oluşturmaktadır**.

---

## 12. Kullanılan Araç

* Nmap 7.95

## 13. Kullanılan Komutlar

### Temel port taraması

```bash
nmap 192.168.56.20
```

### Servis ve sürüm tespiti

```bash
nmap -sV 192.168.56.20
```

---

