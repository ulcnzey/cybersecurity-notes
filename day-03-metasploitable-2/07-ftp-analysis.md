# FTP Service Analysis

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde çalışan FTP servisinin incelenmesi, servis sürümünün belirlenmesi, anonymous FTP erişiminin kontrol edilmesi ve elde edilen bulguların güvenlik açısından değerlendirilmesidir.

Bu çalışma yalnızca izole edilmiş Metasploitable 2 laboratuvar ortamında gerçekleştirilmiştir.

**Hedef IP:** `192.168.56.20`

---

## 2. FTP Nedir?

FTP (File Transfer Protocol), istemci ile sunucu arasında dosya aktarımı gerçekleştirmek için kullanılan bir uygulama katmanı protokolüdür.

FTP geleneksel olarak TCP üzerinden çalışır ve kontrol bağlantısı için **21/tcp** portunu kullanır.

FTP servislerinde kimlik doğrulama için kullanıcı adı ve parola kullanılabilir. Bazı FTP sunucularında **anonymous authentication** özelliği de bulunabilir.

Anonymous FTP, kullanıcıların kişisel bir hesap oluşturmadan veya özel bir kullanıcı hesabı kullanmadan FTP sunucusuna erişmesine izin veren bir yapılandırmadır.

Güvenlik açısından anonymous erişimin hangi kaynaklara izin verdiği önemlidir.

---

## 3. Nmap ile FTP Servisinin Tespit Edilmesi

Daha önce gerçekleştirilen servis keşfi sonucunda Metasploitable 2 üzerinde 21/tcp portunun açık olduğu ve FTP servisinin çalıştığı tespit edilmiştir.

Kullanılan komut:

```bash
nmap -sV 192.168.56.20
```

İlgili sonuç:

```text
21/tcp   open  ftp   vsftpd 2.3.4
```

Bu sonuçlara göre:

| Özellik | Sonuç    |
| ------- | -------- |
| Port    | `21/tcp` |
| Servis  | FTP      |
| Yazılım | vsftpd   |
| Sürüm   | 2.3.4    |
| Durum   | Open     |

---

## 4. Manuel FTP Bağlantısı

FTP servisine manuel olarak bağlanmak için aşağıdaki komut kullanılmıştır:

```bash
ftp 192.168.56.20
```

Bağlantı sırasında sunucu aşağıdaki bilgiyi vermiştir:

```text
Connected to 192.168.56.20.
220 (vsFTPd 2.3.4)
```

Bu çıktı, FTP servisinin erişilebilir olduğunu ve sunucunun `vsFTPd 2.3.4` kullandığını doğrulamaktadır.

---

## 5. Anonymous Authentication Testi

FTP bağlantısı sırasında kullanıcı adı olarak:

```text
anonymous
```

kullanılmıştır.

Sunucu parola istemiş ve anonymous FTP için kullanılan parola girildikten sonra aşağıdaki cevap alınmıştır:

```text
230 Login successful.
```

Bu sonuç, Metasploitable 2 üzerindeki FTP sunucusunda **anonymous authentication özelliğinin etkin olduğunu** göstermektedir.

Bağlantı sonrasında FTP istemcisi aşağıdaki bilgiyi de göstermiştir:

```text
Remote system type is UNIX.
Using binary mode to transfer files.
```

---

## 6. Anonymous Kullanıcının Dizin Erişimi

Başarılı authentication sonrasında mevcut uzak dizin kontrol edilmiştir:

```bash
pwd
```

Sonuç:

```text
Remote directory: /
```

Anonymous kullanıcı FTP sunucusunun `/` dizininde başlatılmıştır.

Dizin içeriğini incelemek için:

```bash
ls
```

ve:

```bash
ls -la
```

komutları kullanılmıştır.

`ls -la` sonucunda:

```text
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 .
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 ..
```

görülmüştür.

Bu sonuç, başlangıç dizininde görünür bir dosya veya alt dizin bulunmadığını göstermektedir.

---

## 7. Dizin Erişim Kontrolleri

Anonymous kullanıcının farklı dizinlere erişim durumu kontrol edilmiştir.

### `/home`

Aşağıdaki komut çalıştırılmıştır:

```bash
cd /home
```

Sunucu tarafından `550` hata kodu döndürülmüştür.

Bu durum anonymous kullanıcının `/home` dizinine erişemediğini göstermektedir.

### `/var/ftp`

Aşağıdaki komut çalıştırılmıştır:

```bash
cd /var/ftp
```

Bu işlemde de `550` hata kodu alınmıştır.

Bu sonuç, anonymous kullanıcının `/var/ftp` dizinine erişiminin de olmadığını göstermektedir.

Bu testler sonucunda anonymous kullanıcının erişiminin sınırsız olmadığı görülmüştür.

---

## 8. Nmap NSE ile Anonymous FTP Doğrulaması

Anonymous FTP erişimini otomatik olarak kontrol etmek amacıyla Nmap'in `ftp-anon` NSE script'i kullanılmıştır.

Kullanılan komut:

```bash
nmap -p 21 --script ftp-anon 192.168.56.20
```

Nmap sonucu:

```text
PORT   STATE SERVICE
21/tcp open  ftp
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

Bu sonuç, manuel olarak gerçekleştirilen anonymous login testini bağımsız olarak doğrulamaktadır.

Özellikle:

```text
FTP code 230
```

ifadesi, FTP authentication işleminin başarılı olduğunu göstermektedir.

---

## 9. Bulguların Özeti

Yapılan testler sonucunda aşağıdaki bulgular elde edilmiştir:

| Test                     | Sonuç                   |
| ------------------------ | ----------------------- |
| FTP portu                | `21/tcp` açık           |
| FTP servisi              | vsftpd                  |
| Sürüm                    | 2.3.4                   |
| Anonymous authentication | Etkin                   |
| Anonymous login          | Başarılı                |
| Başlangıç dizini         | `/`                     |
| `/home` erişimi          | Başarısız               |
| `/var/ftp` erişimi       | Başarısız               |
| Görünür dosya/klasör     | Tespit edilmedi         |
| Nmap `ftp-anon` sonucu   | Anonymous login allowed |

---

## 10. Güvenlik Değerlendirmesi

Anonymous FTP erişiminin etkin olması bir **güvenlik yapılandırma bulgusu** olarak değerlendirilmelidir.

Anonymous authentication etkin olduğunda, FTP servisine erişen bir istemcinin kişisel bir kullanıcı hesabına sahip olmadan authentication gerçekleştirmesine izin verilir.

Bunun güvenlik açısından oluşturduğu risk, anonymous hesabın sahip olduğu yetkilere ve erişebildiği kaynaklara bağlıdır.

Bu laboratuvar testinde anonymous kullanıcı:

* FTP sunucusuna başarılı şekilde giriş yapabilmiştir.
* `/` dizinine erişebilmiştir.
* `/home` dizinine erişememiştir.
* `/var/ftp` dizinine erişememiştir.
* Başlangıç dizininde görünür dosya veya alt dizin görüntüleyememiştir.

Dolayısıyla mevcut test sonuçları, anonymous FTP erişiminin etkin olduğunu doğrulamaktadır ancak hassas dosyalara erişim veya yazma yetkisi bulunduğunu göstermemektedir.

---

## 11. vsftpd 2.3.4 ve CVE Araştırması

Nmap servis tespiti sonucunda FTP sunucusunun:

```text
vsftpd 2.3.4
```

sürümünü kullandığı belirlenmiştir.

Bu sürüm, **CVE-2011-2523** ile ilişkilendirilen bilinen bir güvenlik açığı nedeniyle ayrıca incelenmiştir.

CVE araştırması sırasında önemli bir ayrım yapılmalıdır:

> Bir yazılım sürümünün bilinen bir CVE ile eşleşmesi, sistemin ilgili açığa karşı kesin olarak savunmasız olduğunu tek başına kanıtlamaz.

Bu nedenle bu çalışmada CVE bilgisi, **zafiyet araştırması ve risk değerlendirmesi** amacıyla kullanılmıştır. CVE'nin istismar edildiği sonucuna varılmamıştır.

Detaylı CVE araştırması:

`06-vulnerability-research.md`

dosyasında ele alınmıştır.

---

## 12. Önerilen Güvenlik Önlemleri

Anonymous FTP erişiminin gerekli olmadığı sistemlerde bu özellik devre dışı bırakılmalıdır.

Önerilen önlemler:

1. Anonymous FTP erişimini gereksizse devre dışı bırakmak.
2. FTP sunucusunu güncel ve desteklenen bir sürümde çalıştırmak.
3. Gereksiz FTP servislerini kapatmak.
4. FTP erişimini yalnızca gerekli istemci veya ağlarla sınırlandırmak.
5. Güvenlik duvarı kurallarıyla 21/tcp erişimini kontrol etmek.
6. Dosya ve dizin izinlerini en az ayrıcalık prensibine göre düzenlemek.
7. FTP bağlantılarını ve authentication olaylarını loglamak.
8. Mümkün olduğunda güvenli dosya aktarım protokollerini tercih etmek.

---

## 13. Saldırı Yüzeyine Etkisi

Açık FTP servisi, sistemin saldırı yüzeyindeki servislerden biridir.

Bu değerlendirmede zincir şu şekilde ele alınabilir:

```text
21/tcp
   ↓
FTP
   ↓
vsftpd 2.3.4
   ↓
Anonymous Authentication
   ↓
Erişim Yetkilerinin Değerlendirilmesi
   ↓
CVE / Zafiyet Araştırması
   ↓
Risk Değerlendirmesi
```

Bu yaklaşım, yalnızca açık portları listelemek yerine port → servis → yazılım → sürüm → yapılandırma → zafiyet → etki ilişkisini değerlendirmeyi sağlar.

---

## 14. Sonuç

Metasploitable 2 üzerinde çalışan FTP servisi incelenmiştir.

Analiz sonucunda:

* `21/tcp` portunun açık olduğu,
* `vsftpd 2.3.4` kullanıldığı,
* anonymous FTP authentication özelliğinin etkin olduğu,
* anonymous kullanıcı ile başarılı authentication gerçekleştirilebildiği,
* anonymous kullanıcının `/` dizinine erişebildiği,
* `/home` ve `/var/ftp` dizinlerine erişemediği,
* anonymous kullanıcının başlangıç dizininde görünür dosya veya alt dizin bulunmadığı,
* `ftp-anon` NSE script'i ile anonymous erişimin doğrulandığı

tespit edilmiştir.

Bu bulgu, FTP servisinin yapılandırmasının ve kullanılan yazılım sürümünün ayrıca güvenlik açısından değerlendirilmesi gerektiğini göstermektedir.

---

## 15. Kullanılan Komutlar

```bash
ftp 192.168.56.20
```

```text
anonymous
```

FTP oturumu içerisinde:

```bash
pwd
ls
ls -la
cd /home
cd /var/ftp
bye
```

Nmap NSE kontrolü:

```bash
nmap -p 21 --script ftp-anon 192.168.56.20
```

---

## 16. Ekran Görüntüleri

### FTP bağlantısı ve Anonymous Login

FTP servisine bağlantı ve anonymous authentication işlemi.

<img width="321" height="169" alt="image" src="https://github.com/user-attachments/assets/ddb26220-d057-4ff6-a506-bf49717edd4c" />


### FTP Dizin Kontrolleri

Anonymous kullanıcının FTP dizinindeki erişim kontrolleri.

<img width="476" height="300" alt="image" src="https://github.com/user-attachments/assets/1e70b806-6d9f-46a8-80b5-f39ed12025d5" />


### Nmap FTP Anonymous Script

Nmap NSE `ftp-anon` script'i ile anonymous FTP erişiminin doğrulanması.

<img width="648" height="203" alt="image" src="https://github.com/user-attachments/assets/cef07007-c05d-4072-8b1d-23b79447c88e" />

