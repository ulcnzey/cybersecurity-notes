# Security Misconfiguration

## 1. Security Misconfiguration Nedir?

Security Misconfiguration, Türkçesiyle **Güvenlik Yanlış Yapılandırması**, bir web uygulamasının, sunucunun veya kullanılan altyapının güvenlik ayarlarının hatalı, eksik veya gereğinden fazla açık bırakılması durumudur.

Bu konuda öğrendiğim en önemli noktalardan biri, bir sistemin çalışıyor olmasının onun güvenli olduğu anlamına gelmemesidir.

Örneğin bir uygulamanın `/admin` panelinin bulunması tek başına güvenlik açığı değildir. Ancak bu panel yeterli authentication ve authorization kontrolleri olmadan erişilebilir durumdaysa güvenlik problemi oluşabilir.

Genel olarak şu şekilde düşünebiliriz:

```text
Sistem çalışıyor
      ↓
Güvenlik ayarları doğru yapılmamış
      ↓
Gereksiz bilgiler / servisler / izinler açık
      ↓
Saldırı yüzeyi artıyor
      ↓
Güvenlik riski oluşuyor
```

---

## 2. Default Password

Bazı uygulamalar, cihazlar veya servisler kurulum sonrasında varsayılan kullanıcı adı ve parola ile çalışabilir.

Örneğin:

```text
username: admin
password: admin
```

gibi tahmin edilmesi kolay veya üretici tarafından bilinen bilgiler kullanılabilir.

### Neden güvenlik problemi oluşturur?

Varsayılan bilgiler değiştirilmezse saldırgan bunları tahmin ederek veya bilinen varsayılan bilgileri deneyerek sisteme erişmeye çalışabilir.

Özellikle yönetici hesaplarında varsayılan parolanın değiştirilmemesi önemli bir güvenlik riski oluşturabilir.

### Korunma

* Varsayılan parolalar değiştirilmelidir.
* Güçlü ve benzersiz parolalar kullanılmalıdır.
* Mümkün olan yerlerde MFA kullanılmalıdır.
* İlk kurulum sırasında parola değiştirme zorunlu hale getirilebilir.

---

## 3. Debug Mode

Debug mode, geliştirme sırasında uygulamadaki hataları daha kolay bulabilmek için kullanılan bir özelliktir.

Ancak production ortamında açık bırakılması güvenlik açısından sorun oluşturabilir.

Bir hata meydana geldiğinde uygulama kullanıcıya normalde gösterilmemesi gereken teknik bilgileri gösterebilir.

Örneğin:

```text
Database connection failed

/app/config/database.py

SECRET_KEY=...
```

gibi bilgiler açığa çıkabilir.

### Neden güvenlik problemi oluşturur?

Saldırgan uygulamanın:

* dosya yapısı,
* kullanılan teknolojiler,
* hata noktaları,
* yapılandırma bilgileri,
* bazı hassas bilgiler

hakkında bilgi elde edebilir.

### Korunma

Production ortamında debug modu kapatılmalıdır.

Kullanıcıya ayrıntılı teknik hata bilgileri yerine genel bir hata mesajı gösterilmelidir. Ayrıntılı hata kayıtları ise güvenli şekilde sunucu tarafındaki loglarda tutulmalıdır.

---

## 4. Gereksiz Servisler

Bir sunucuda uygulamanın çalışması için gerekli olmayan servislerin açık bırakılması saldırı yüzeyini artırabilir.

Her çalışan servis potansiyel olarak:

* bir port,
* bir yazılım,
* bir yapılandırma,
* yeni bir erişim noktası

oluşturabilir.

Örneğin:

```text
Sunucu
 ├── Web Server
 ├── HTTPS
 ├── Gereksiz servis
 └── Gereksiz yönetim servisi
```

Uygulamanın ihtiyacı olmayan servislerin çalıştırılması, saldırganın değerlendirebileceği alanı genişletebilir.

### Korunma

* İhtiyaç olmayan servisler kapatılmalıdır.
* Gereksiz portlar kapatılmalıdır.
* Ağ erişimleri sınırlandırılmalıdır.
* Yalnızca gerekli servisler çalıştırılmalıdır.

Buradaki temel yaklaşım **attack surface'i mümkün olduğunca azaltmaktır.**

---

## 5. Gereksiz HTTP Header Bilgileri

HTTP response header'larında sunucu veya kullanılan teknoloji hakkında gereğinden fazla bilgi bulunabilir.

Örneğin:

```http
Server: Apache/2.4.x
```

gibi bir header kullanılan yazılım hakkında bilgi verebilir.

Bu bilgi tek başına bir güvenlik açığı değildir. Ancak saldırganın sistem hakkında bilgi toplama sürecinde kullanabileceği ek bir bilgi sağlayabilir.

### Neden problem oluşturabilir?

Saldırgan kullanılan:

* web server,
* framework,
* yazılım sürümü,
* teknoloji

hakkında daha fazla bilgi elde edebilir.

Bu bilgiler daha sonra bilinen güvenlik problemlerini araştırmak için kullanılabilir.

### Korunma

Gereksiz teknik bilgilerin dışarıya verilmesi mümkün olduğunca sınırlandırılmalıdır.

Ancak burada amaç bütün HTTP header'larını kaldırmak değildir. Uygulamanın ihtiyaçları ve güvenlik gereksinimleri birlikte değerlendirilmelidir.

---

## 6. Directory Listing

Directory Listing, web sunucusunun bir klasör içerisindeki dosyaları kullanıcıya listelemesi durumudur.

Örneğin:

```text
/uploads/

    image.jpg
    document.pdf
    backup.zip
    old-config.txt
```

gibi bir liste kullanıcı tarafından görüntülenebilir.

### Neden güvenlik problemi oluşturur?

Bu durum uygulamanın:

* dosya isimlerini,
* klasör yapısını,
* yedek dosyaları,
* yanlışlıkla bırakılmış hassas dosyaları

açığa çıkarabilir.

Özellikle backup veya configuration dosyalarının erişilebilir olması daha ciddi güvenlik problemlerine yol açabilir.

### Korunma

* Directory listing gerekmiyorsa kapatılmalıdır.
* Hassas dosyalar web root altında tutulmamalıdır.
* Dosya erişimleri uygun şekilde sınırlandırılmalıdır.
* Kullanıcıların yalnızca erişmesine izin verilen dosyalara ulaşabilmesi sağlanmalıdır.

---

## 7. Açık Admin Paneli

Bir web uygulamasında `/admin` veya benzeri bir yönetim panelinin bulunması normal olabilir.

Buradaki problem admin panelinin varlığı değil, **yeterli şekilde korunmaması**dır.

Örneğin:

```text
/admin
/admin/login
/management
```

gibi yönetim alanları bulunabilir.

Eğer bu alanlarda authentication veya authorization kontrolleri yetersizse yetkisiz kişiler yönetim işlemlerine erişebilir.

### Neden güvenlik problemi oluşturur?

Admin paneli genellikle normal kullanıcılara göre daha yüksek yetkilere sahiptir.

Bu nedenle:

```text
Normal kullanıcı
      ↓
Admin paneline erişim
      ↓
Yönetici işlemleri
```

gibi bir durum ciddi güvenlik sonuçları doğurabilir.

### Korunma

* Güçlü authentication uygulanmalıdır.
* Authorization kontrolleri yapılmalıdır.
* Admin paneline erişim sınırlandırılmalıdır.
* MFA kullanılabilir.
* Kullanılmayan yönetim panelleri kapatılmalıdır.

---

## 8. Eski Yazılım Versiyonu

Sunucuda veya web uygulamasında eski bir yazılım sürümünün kullanılması güvenlik problemi oluşturabilir.

Buradaki problem sadece yazılımın eski olması değildir.

Önemli olan, kullanılan sürümde daha önce tespit edilmiş ve düzeltilmiş güvenlik problemlerinin bulunabilmesidir.

Örneğin:

```text
Eski yazılım sürümü
        ↓
Bilinen güvenlik problemi
        ↓
Güncelleme mevcut
        ↓
Sistem güncellenmemiş
        ↓
Risk devam ediyor
```

### Korunma

* Yazılımlar güncel tutulmalıdır.
* Güvenlik güncellemeleri takip edilmelidir.
* Kullanılan bağımlılıklar düzenli olarak kontrol edilmelidir.
* Kullanılmayan yazılım ve bileşenler kaldırılmalıdır.

Burada düzenli **patch management** önemli bir güvenlik uygulamasıdır.

---

## 9. Gereğinden Fazla İzin

Bir kullanıcıya, uygulamaya veya servise ihtiyacı olandan daha fazla yetki verilmesi de yanlış yapılandırma örneğidir.

Örneğin bir web uygulamasının sadece belirli bir klasördeki dosyaları okuması gerekiyorsa bütün dosya sistemine erişim verilmesi gereksiz bir yetkilendirme olur.

```text
Gerekli:

read → /app/uploads/

Verilen:

read
write
execute
+
daha geniş sistem erişimi
```

### Neden güvenlik problemi oluşturur?

Uygulamada başka bir güvenlik açığı oluşursa saldırgan, uygulamanın sahip olduğu yetkilerden de yararlanabilir.

Uygulamanın gereğinden fazla yetkili olması, olası bir güvenlik ihlalinin etkisini büyütebilir.

### Korunma

Burada **Least Privilege (En Az Yetki)** prensibi uygulanmalıdır.

> Bir kullanıcı, uygulama veya servis yalnızca görevini gerçekleştirmek için ihtiyaç duyduğu yetkilere sahip olmalıdır.

---

## 10. Genel Değerlendirme

İncelediğim örnekleri karşılaştırdığımda Security Misconfiguration'ın tek bir güvenlik probleminden oluşmadığını gördüm.

Farklı yanlış yapılandırmalar farklı sonuçlara yol açabilir:

| Yanlış yapılandırma            | Temel risk                                  |
| ------------------------------ | ------------------------------------------- |
| Default Password               | Yetkisiz erişim                             |
| Debug Mode                     | Hassas teknik bilgi sızıntısı               |
| Gereksiz servisler             | Saldırı yüzeyinin büyümesi                  |
| Gereksiz HTTP header bilgileri | Sistem hakkında bilgi sızıntısı             |
| Directory Listing              | Dosya ve klasör bilgilerinin açığa çıkması  |
| Açık Admin Paneli              | Yetkisiz yönetim erişimi                    |
| Eski yazılım versiyonu         | Bilinen güvenlik problemleri                |
| Gereğinden fazla izin          | Güvenlik ihlali sonrasında etkinin büyümesi |

---

## 11. Öğrendiğim Temel Noktalar

Bu bölümde özellikle şunları öğrendim:

* Security Misconfiguration, sistemin güvenli yapılandırılmaması sonucu ortaya çıkabilir.
* Varsayılan parolalar değiştirilmelidir.
* Production ortamında debug mode açık bırakılmamalıdır.
* Gereksiz servisler ve portlar saldırı yüzeyini artırabilir.
* Gereksiz teknik bilgiler HTTP response'larında dışarıya verilmemelidir.
* Directory listing hassas dosya ve klasör bilgilerini açığa çıkarabilir.
* Admin paneli uygun authentication ve authorization kontrolleriyle korunmalıdır.
* Eski ve bilinen güvenlik problemleri bulunan yazılımlar güncellenmelidir.
* Kullanıcılara ve servislere ihtiyaçlarından fazla yetki verilmemelidir.
* Least Privilege prensibi güvenli yapılandırmanın önemli parçalarından biridir.

---

## 12. Kısa Özet

Security Misconfiguration konusunda benim için en önemli nokta şu oldu:

> **Bir sistemin çalışıyor olması, güvenli şekilde yapılandırıldığı anlamına gelmez.**

Güvenli bir web uygulamasında yalnızca kodun güvenliği değil, uygulamanın çalıştığı sunucunun ve altyapının yapılandırması da önemlidir.

Bu nedenle güvenlik değerlendirmesinde:

```text
Uygulama
   ↓
Sunucu
   ↓
Servisler
   ↓
HTTP yapılandırması
   ↓
Dosya erişimleri
   ↓
Yetkiler
   ↓
Yazılım sürümleri
```

gibi farklı katmanların birlikte değerlendirilmesi gerekir.

Security Misconfiguration'ın temelinde ise çoğu zaman gereğinden fazla açık bırakılmış, unutulmuş veya güvenli şekilde yapılandırılmamış bir sistem bileşeni bulunur.

> **Not:** Bu bölümde yalnızca kavramsal araştırma yapılmıştır. Herhangi bir gerçek sistem üzerinde test veya saldırı gerçekleştirilmemiştir.
