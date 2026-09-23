# 16. Saldırı Zinciri Mantığı

## 16.1. Genel Bakış

Siber güvenlikte bir saldırı veya penetrasyon testi süreci tek bir adımdan oluşmaz. Hedef hakkında bilgi toplama, servisleri belirleme, ayrıntılı bilgi edinme, zafiyetleri araştırma ve uygun durumlarda zafiyetlerin kontrollü şekilde doğrulanması gibi birden fazla aşamadan oluşur.

Bu süreç genel olarak aşağıdaki şekilde modellenebilir:

```text
Reconnaissance
      ↓
Scanning
      ↓
Enumeration
      ↓
Vulnerability Identification
      ↓
Exploitation
      ↓
Privilege Escalation
      ↓
Post-Exploitation
```

---

## 16.2. Reconnaissance

Reconnaissance, hedef hakkında bilgi toplama aşamasıdır.

Toplanabilecek bilgilere örnek olarak:

* IP adresleri
* Domain bilgileri
* DNS kayıtları
* Kullanılan teknolojiler
* İnternete açık sistemler
* Kamuya açık bilgiler

verilebilir.

Bu aşamanın temel amacı hedefin genel yapısını anlamaktır.

---

## 16.3. Scanning

Scanning, hedef sistemde erişilebilir portların ve servislerin belirlenmesine yönelik tarama aşamasıdır.

Day 3 laboratuvarında aşağıdaki komut kullanılmıştır:

```bash
nmap 192.168.56.20
```

Tarama sonucunda FTP, SSH, Telnet, HTTP, SMB, MySQL ve PostgreSQL gibi çeşitli servislerin erişilebilir olduğu görülmüştür.

Scanning aşamasının temel sorusu:

> Hedef üzerinde hangi servisler erişilebilir durumda?

---

## 16.4. Enumeration

Enumeration, tespit edilen servisler hakkında daha ayrıntılı bilgi toplama aşamasıdır.

Örneğin:

```bash
nmap -sV 192.168.56.20
```

komutu ile servis ve sürüm bilgileri araştırılmıştır.

SMB servisi için ise:

```bash
nmap -p 139,445 --script smb-enum-shares 192.168.56.20
```

gibi NSE komutları kullanılarak paylaşım bilgileri incelenmiştir.

Bu aşamanın temel amacı:

> Tespit edilen servislerin nasıl çalıştığını ve hangi bilgileri sunduğunu anlamaktır.

---

## 16.5. Vulnerability Identification

Bu aşamada elde edilen servis, sürüm ve yapılandırma bilgileri kullanılarak olası güvenlik açıkları araştırılır.

Örneğin:

```text
vsftpd 2.3.4
       ↓
CVE araştırması
       ↓
SearchSploit araştırması
       ↓
Potansiyel güvenlik açığı
```

Ancak bir CVE veya exploit kaydının bulunması, hedef sistemin kesin olarak istismar edilebilir olduğunu tek başına kanıtlamaz.

Zafiyetin gerçekten mevcut olup olmadığı ve uygulanabilirliği ayrıca değerlendirilmelidir.

---

## 16.6. Exploitation

Exploitation, belirlenen bir zafiyetin kontrollü şekilde istismar edilerek etkisinin doğrulanması aşamasıdır.

Bu aşamanın amacı, teorik olarak belirlenen bir zafiyetin gerçekten kullanılabilir olup olmadığını ve ne tür bir etki oluşturabileceğini değerlendirmektir.

Day 3 çalışmasında exploitation gerçekleştirilmemiştir. Çalışma; keşif, servis analizi, zafiyet araştırması ve risk değerlendirmesi kapsamında tutulmuştur.

---

## 16.7. Privilege Escalation

Privilege Escalation, elde edilen mevcut yetkilerin daha yüksek seviyeye çıkarılmasıdır.

Örneğin Linux sistemlerinde düşük yetkili bir kullanıcı hesabından daha yüksek yetkili bir hesaba geçiş bu kavrama örnek olarak verilebilir.

Basit şekilde:

```text
Düşük yetkili kullanıcı
        ↓
Daha yüksek yetki
        ↓
Root / Yönetici
```

Yetki yükseltme, güvenlik değerlendirmesinde bir saldırının sistem üzerindeki potansiyel etkisini anlamak açısından önemlidir.

---

## 16.8. Post-Exploitation

Post-Exploitation, sistem üzerinde erişim elde edildikten sonraki değerlendirme aşamasıdır.

Yetkili bir penetrasyon testi kapsamında:

* Erişimin kapsamı
* Kullanılabilen yetkiler
* Erişilebilen kaynaklar
* Etkilenen sistemler
* Güvenlik etkisi

gibi konular incelenebilir.

Bu aşamada gerçekleştirilen faaliyetler testin kapsamına ve kurallarına uygun olmalıdır.

---

## 16.9. Neden Doğrudan Exploitation Aşamasına Geçilmez?

Reconnaissance ve Enumeration aşamalarının amacı hedef hakkında yeterli teknik bilgi edinmektir.

Hedef hakkında bilgi edinmeden doğrudan exploitation aşamasına geçmek:

* Yanlış servisin hedeflenmesine,
* Yanlış zafiyetin denenmesine,
* Gereksiz denemelere,
* Sistemlerin etkilenmesine,
* Güvenlik değerlendirmesinin eksik kalmasına

neden olabilir.

Bu nedenle süreç genellikle bilgi toplama ile başlar.

Day 3 laboratuvarında bu süreç şu şekilde ilerlemiştir:

```text
192.168.56.20
      ↓
Nmap
      ↓
Açık portların belirlenmesi
      ↓
Servis ve versiyon tespiti
      ↓
NSE ile ayrıntılı enumeration
      ↓
CVE araştırması
      ↓
SearchSploit araştırması
      ↓
Risk değerlendirmesi
```

Bu yaklaşım, hedef hakkında rastgele işlem yapmak yerine elde edilen teknik bilgiler doğrultusunda sistematik bir güvenlik değerlendirmesi yapılmasını sağlar.

---

## 16.10. Saldırı Zincirinin Özeti

| Aşama                        | Temel Amaç                                  |
| ---------------------------- | ------------------------------------------- |
| Reconnaissance               | Hedef hakkında genel bilgi toplamak         |
| Scanning                     | Açık port ve servisleri belirlemek          |
| Enumeration                  | Servisler hakkında ayrıntılı bilgi toplamak |
| Vulnerability Identification | Olası güvenlik açıklarını belirlemek        |
| Exploitation                 | Zafiyeti kontrollü şekilde doğrulamak       |
| Privilege Escalation         | Yetki seviyesini artırmayı değerlendirmek   |
| Post-Exploitation            | Erişim sonrasındaki etkiyi değerlendirmek   |

## 16.11. Sonuç

Saldırı zinciri, bir hedefin rastgele şekilde test edilmesi yerine sistematik olarak değerlendirilmesini açıklar.

Reconnaissance ve Scanning hedefin genel yapısını ortaya çıkarırken, Enumeration daha ayrıntılı teknik bilgiler sağlar. Bu bilgiler kullanılarak olası zafiyetler araştırılır.

Exploitation aşaması ise ancak uygun yetki ve kapsam dahilinde, kontrollü bir şekilde değerlendirilmelidir.

Bu nedenle güvenlik değerlendirmesinin temel yaklaşımı:

> **Önce hedefi tanı, ardından saldırı yüzeyini belirle, ayrıntıları öğren, zafiyetleri araştır ve uygun kapsamda kontrollü doğrulama yap.**
