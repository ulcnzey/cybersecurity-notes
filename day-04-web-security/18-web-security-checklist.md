# Web Güvenlik Kontrol Listesi

## 1. Giriş

Şimdiye kadar web güvenliği kapsamında authentication, authorization, session, HTTP/HTTPS, input validation, SQL Injection, XSS, CSRF, Security Misconfiguration ve logging gibi birçok konuyu ayrı ayrı inceledim.

Bu bölümde ise öğrendiğim konuları tek bir değerlendirme mantığında birleştirdim.

Bir web uygulamasını güvenlik açısından değerlendirirken sadece:

> "Uygulamada bir açık var mı?"

diye bakmak yerine, uygulamanın farklı katmanlarını sistematik olarak kontrol etmek gerekir.

Benim için temel değerlendirme şu soruya dayanıyor:

> **Kullanıcı uygulamaya girdiğinde, veri gönderdiğinde, oturum açtığında, bir kaynağa eriştiğinde ve işlem yaptığında uygulama bunu güvenli şekilde kontrol ediyor mu?**

Bu amaçla kontrol listesini 6 ana başlık altında ele aldım:

```text
Web Application
      │
      ├── Authentication
      ├── Authorization
      ├── Input
      ├── Session
      ├── Configuration
      └── Logging
```

---

# 2. Authentication

Authentication, kullanıcının kimliğini doğrulama işlemidir.

Temel soru:

> **"Bu kullanıcı gerçekten söylediği kişi mi?"**

Bir kullanıcı kullanıcı adı ve parola ile giriş yaptığında uygulama bu bilgileri doğrular ve başarılı olması durumunda bir oturum oluşturur.

Ancak güvenli authentication yalnızca kullanıcı adı ve parola kontrolünden ibaret değildir.

---

## 2.1 Güçlü Parola Politikası

Uygulama kullanıcıların kolay tahmin edilebilir parolalar oluşturmasına izin vermemelidir.

Örneğin:

```text
123456
password
qwerty
12345678
```

gibi parolalar saldırgan tarafından kolayca tahmin edilebilir.

Kontrol ederken şunlara bakılabilir:

* Minimum parola uzunluğu var mı?
* Yaygın veya ele geçirilmiş parolalar engelleniyor mu?
* Parola sıfırlama mekanizması güvenli mi?
* Yönetici hesaplarında güçlü parola kullanılıyor mu?

Buradaki amaç, parola tahmin saldırılarının zorlaştırılmasıdır.

---

## 2.2 MFA

MFA, **Multi-Factor Authentication** anlamına gelir.

Kullanıcının yalnızca parolasını bilmesi yerine ek bir doğrulama faktörü kullanılır.

Örneğin:

```text
Şifre
  +
İkinci doğrulama
  ↓
Giriş
```

MFA'nın amacı, parola ele geçirilse bile hesabın doğrudan ele geçirilmesini zorlaştırmaktır.

Özellikle yönetici ve hassas hesaplarda önemli bir güvenlik katmanıdır.

---

## 2.3 Brute-Force Koruması

Saldırgan çok sayıda parola deneyerek hesabın parolasını bulmaya çalışabilir.

Örneğin:

```text
admin / 123456
admin / password
admin / 12345678
admin / qwerty
...
```

Uygulama sınırsız sayıda giriş denemesine izin veriyorsa brute-force riski artabilir.

Bu nedenle:

* Rate limiting
* Geçici bekleme
* Uygun hesap koruma mekanizmaları
* MFA
* Başarısız girişlerin izlenmesi

gibi kontroller kullanılabilir.

### Kontrol sorusu:

> **Bir kullanıcı veya IP adresi çok kısa sürede çok fazla giriş denemesi yapabiliyor mu?**

---

## 2.4 Session Yönetimi

Authentication başarılı olduktan sonra kullanıcının oturumu güvenli şekilde yönetilmelidir.

Temel akış:

```text
Login
  ↓
Authentication
  ↓
Session oluşturulması
  ↓
Session ID / Token
  ↓
Korumalı kaynaklara erişim
```

Kontrol ederken:

* Session ID tahmin edilebilir mi?
* Session güvenli şekilde oluşturuluyor mu?
* Session'ın süresi var mı?
* Logout sonrasında session geçersiz oluyor mu?
* Kritik işlemlerde tekrar authentication gerekiyor mu?

gibi sorular sorulmalıdır.

---

# 3. Authorization

Authentication:

> **"Sen kimsin?"**

Authorization:

> **"Neye erişmeye yetkin var?"**

sorusuna cevap verir.

Bir kullanıcının başarılı şekilde giriş yapmış olması, uygulamadaki her kaynağa erişebileceği anlamına gelmez.

Örneğin:

```text
User
 ├── /profile       → İzinli
 ├── /orders        → İzinli
 └── /admin         → İzinli değil
```

Bu kontroller sunucu tarafında uygulanmalıdır.

---

## 3.1 Rol Kontrolü

Uygulamalarda farklı roller bulunabilir:

```text
User
Admin
Moderator
Manager
```

Her rolün farklı yetkileri olabilir.

Kontrol edilmesi gereken:

> **Kullanıcının rolü ile yapmak istediği işlem gerçekten uyumlu mu?**

Sadece frontend'de admin butonunu gizlemek güvenlik kontrolü değildir.

Kullanıcı doğrudan HTTP isteği oluşturabileceği için yetki kontrolünün sunucu tarafında yapılması gerekir.

---

## 3.2 Object-Level Authorization

Bu konu daha önce incelediğim IDOR ile doğrudan bağlantılıdır.

Örneğin:

```text
/profile?id=100
```

kullanıcının kendi profilini gösteriyor olabilir.

Ancak kullanıcı:

```text
/profile?id=101
```

gönderdiğinde başka bir kullanıcının bilgilerine erişebiliyorsa object-level authorization kontrolü eksiktir.

Burada sadece:

```text
"Bu kullanıcı giriş yapmış mı?"
```

sorusunu sormak yeterli değildir.

Ayrıca:

```text
"Bu kullanıcı 101 numaralı kaynağa erişmeye yetkili mi?"
```

sorusu da kontrol edilmelidir.

---

## 3.3 Admin Kontrolleri

Yönetim alanları normal kullanıcılardan daha yüksek yetkilere sahip olabilir.

Örneğin:

```text
/admin
/admin/users
/admin/settings
```

gibi alanlar bulunabilir.

Kontrol edilmesi gereken:

* Normal kullanıcı admin paneline erişebiliyor mu?
* Admin işlemleri için rol kontrolü yapılıyor mu?
* Yönetim işlemleri ayrıca korunuyor mu?
* Kullanılmayan admin panelleri kapatılmış mı?

---

# 4. Input

Web uygulamaları sürekli olarak dışarıdan veri alır.

Örneğin:

```text
Login
Search
Comment
Profile
File Upload
URL Parameters
JSON
Cookies
Headers
```

Bu nedenle güvenlik açısından temel prensiplerden biri:

> **Kullanıcıdan gelen veriye güvenilmez.**

Örneğin:

```http
GET /search?q=apple
```

isteğinde `apple` değeri kullanıcı tarafından kontrol edilebilir.

Uygulama bu veriyi güvenilir kabul etmeden önce uygun şekilde işlemelidir.

---

## 4.1 Input Validation

Input validation, gelen verinin beklenen format ve kurallara uygun olup olmadığını kontrol etmektir.

Örneğin yaş alanında:

```text
age=22
```

beklenirken:

```text
age=hello
```

uygun bir değer değildir.

Validation yapılırken:

* Veri tipi
* Uzunluk
* Format
* Beklenen değerler
* İzin verilen karakterler

gibi özellikler kontrol edilebilir.

Ancak input validation tek başına bütün saldırılara karşı yeterli değildir.

---

## 4.2 Output Encoding

Kullanıcıdan alınan veri daha sonra web sayfasında gösterilebilir.

Burada uygulamanın bu veriyi güvenli şekilde sunması gerekir.

Output encoding'in amacı, verinin bulunduğu bağlama uygun şekilde encode edilerek tarayıcı tarafından kod olarak yorumlanma riskinin azaltılmasıdır.

Input validation ile output encoding aynı şey değildir.

```text
Input Validation
→ Gelen veri uygun mu?

Output Encoding
→ Bu veriyi kullanıcıya gösterirken güvenli şekilde nasıl sunmalıyım?
```

Bu konu özellikle XSS açısından önemlidir.

---

## 4.3 SQL Injection

Kullanıcı girdisinin güvenli olmayan şekilde SQL sorgusuna dahil edilmesi SQL Injection riskine yol açabilir.

Temel akış:

```text
Kullanıcı girdisi
      ↓
Web uygulaması
      ↓
SQL Query
      ↓
Database
```

Bu nedenle:

* Prepared Statements
* Parameterized Queries
* Güvenli ORM kullanımı

gibi yöntemler tercih edilmelidir.

Kontrol sorusu:

> **Kullanıcıdan gelen veri SQL sorgusunun yapısını etkileyebiliyor mu?**

---

## 4.4 XSS

Kullanıcı tarafından gönderilen verinin web sayfasında güvenli şekilde işlenmemesi XSS riskine yol açabilir.

Temel akış:

```text
Kullanıcı girdisi
      ↓
Web uygulaması
      ↓
HTML / DOM
      ↓
Tarayıcı
```

Kontrol sırasında:

* Input validation
* Output encoding
* Güvenli DOM kullanımı
* Content Security Policy

gibi savunmalar değerlendirilmelidir.

---

# 5. Session

Session, kullanıcının giriş yaptıktan sonraki oturumunun yönetilmesini sağlar.

Session güvenliği authentication'dan sonra devam eden bir süreçtir.

```text
Login
  ↓
Session
  ↓
Cookie / Token
  ↓
Korumalı kaynak
```

---

## 5.1 Secure Cookie

Session cookie'sinin `Secure` özelliği bulunması, cookie'nin yalnızca HTTPS üzerinden gönderilmesini sağlar.

```http
Set-Cookie: session=...; Secure
```

Bu özellik özellikle session bilgilerinin HTTP üzerinden gönderilmesini engellemeye yardımcı olur.

Ancak:

> `Secure` özelliği tek başına session güvenliğini sağlamaz.

---

## 5.2 HttpOnly

`HttpOnly`, cookie'nin JavaScript tarafından okunmasını sınırlar.

```http
Set-Cookie: session=...; HttpOnly
```

Bu özellik XSS gibi durumlarda session cookie'sinin JavaScript tarafından okunması riskini azaltmaya yardımcı olur.

Ancak HttpOnly:

> **XSS'i tamamen engellemez.**

---

## 5.3 SameSite

`SameSite`, cookie'nin cross-site isteklerde gönderilmesini kontrol etmeye yardımcı olur.

Örneğin:

```http
SameSite=Strict
```

veya:

```http
SameSite=Lax
```

kullanılabilir.

SameSite özellikle CSRF riskinin azaltılmasında önemlidir.

---

## 5.4 Session Expiration

Session'ların sonsuza kadar geçerli olmaması gerekir.

Kontrol edilmesi gereken:

* Session'ın süresi var mı?
* Uzun süre kullanılmayan session sona eriyor mu?
* Logout sonrasında session geçersiz oluyor mu?
* Kritik işlemlerde yeniden authentication gerekiyor mu?

Temel amaç:

> Kullanılmayan veya artık geçerli olmaması gereken session'ların uzun süre kullanılabilmesini engellemek.

---

# 6. Configuration

Bu bölüm uygulamanın ve sunucunun nasıl yapılandırıldığıyla ilgilidir.

Daha önce öğrendiğim **Security Misconfiguration** konusu burada doğrudan karşımıza çıkar.

---

## 6.1 HTTPS

Web uygulamasında hassas verilerin HTTPS üzerinden taşınması gerekir.

Özellikle:

* Login bilgileri
* Session cookie'leri
* Kişisel veriler
* API istekleri

korunmalıdır.

Kontrol:

> **Uygulama HTTP yerine HTTPS kullanıyor mu?**

Ancak HTTPS'in sınırını da bilmek gerekir.

HTTPS:

* SQL Injection'ı engellemez.
* XSS'i engellemez.
* IDOR'u engellemez.
* Authorization hatalarını düzeltmez.

HTTPS temel olarak iletişim kanalının güvenliğini sağlar.

---

## 6.2 Debug Mode

Production ortamında debug mode açık bırakılmamalıdır.

Kontrol:

```text
Production
   ↓
DEBUG = false
```

olmalıdır.

Debug açık olduğunda uygulama hata mesajlarında:

* Dosya yolları
* Stack trace
* Framework bilgileri
* Yapılandırma bilgileri

gibi hassas teknik bilgiler gösterebilir.

---

## 6.3 Security Headers

HTTP response'larında güvenliği artırmaya yardımcı olan header'lar kontrol edilmelidir.

Örneğin:

```http
Content-Security-Policy
X-Content-Type-Options
X-Frame-Options
Strict-Transport-Security
```

gibi header'lar belirli saldırı risklerini azaltmaya yardımcı olabilir.

Kontrol sorusu:

> **Uygulama ve web sunucusu gerekli güvenlik politikalarını tarayıcıya doğru şekilde bildiriyor mu?**

---

## 6.4 Gereksiz Servisler

Sunucuda uygulamanın ihtiyacı olmayan servisler açık mı?

Örneğin:

```text
Web Server
Database
SSH
Gereksiz servis
Gereksiz yönetim servisi
```

Her gereksiz servis potansiyel olarak saldırı yüzeyini artırabilir.

Bu nedenle:

> **Gerçekten ihtiyacım olmayan hangi servisler açık?**

sorusu sorulmalıdır.

---

# 7. Logging

Güvenlik yalnızca saldırıyı önlemekten ibaret değildir.

Bir güvenlik olayının gerçekleştiğini **tespit edebilmek** de önemlidir.

Bu nedenle uygulamanın önemli olayları kayıt altına alması gerekir.

---

## 7.1 Login Olayları

Başarılı girişler kaydedilebilir.

Örneğin:

```text
User: zeynep
Time: 10:32
Action: Login
Result: Success
```

Bu kayıtlar olay incelemesinde kullanılabilir.

---

## 7.2 Başarısız Girişler

Başarısız girişler de izlenmelidir.

Örneğin:

```text
10:30 → Login failed
10:30 → Login failed
10:30 → Login failed
10:31 → Login failed
```

gibi çok sayıda başarısız deneme şüpheli bir davranış olabilir.

Bu nedenle başarısız girişlerin kayıt altına alınması önemlidir.

---

## 7.3 Yetkisiz Erişim

Örneğin normal bir kullanıcı:

```http
GET /admin/users
```

isteği gönderiyor ve:

```http
HTTP/1.1 403 Forbidden
```

cevabını alıyor.

Bu olayın kaydedilmesi güvenlik izleme açısından değerli olabilir.

Özellikle aynı kullanıcının tekrar tekrar korumalı alanlara erişmeye çalışması incelenebilir.

---

## 7.4 Şüpheli İstekler

Uygulama aşağıdaki davranışları izleyebilir:

* Çok fazla login denemesi
* Çok sayıda `403` cevabı
* Beklenmeyen endpoint erişimleri
* Anormal istek yoğunluğu
* Şüpheli input değerleri
* Olağandışı kullanıcı davranışları

Buradaki amaç yalnızca saldırıyı engellemek değil:

> **Saldırı veya şüpheli davranış gerçekleştiğinde bunu fark edebilmek ve inceleyebilmektir.**

---

# 8. Genel Web Güvenlik Checklist

Öğrendiğim konuları tek bir checklist halinde topladığımda:

## Authentication

* [ ] Güçlü parola politikası var mı?
* [ ] MFA kullanılıyor mu?
* [ ] Brute-force koruması var mı?
* [ ] Rate limiting uygulanıyor mu?
* [ ] Session güvenli şekilde oluşturuluyor mu?
* [ ] Logout sonrası session geçersiz oluyor mu?

## Authorization

* [ ] Roller kontrol ediliyor mu?
* [ ] Object-level authorization uygulanıyor mu?
* [ ] Admin alanları korunuyor mu?
* [ ] Normal kullanıcı yönetici işlemlerine erişemiyor mu?
* [ ] Kullanıcı başka kullanıcıların kaynaklarına erişemiyor mu?

## Input

* [ ] Kullanıcı girdileri doğrulanıyor mu?
* [ ] Input validation uygulanıyor mu?
* [ ] Output encoding uygulanıyor mu?
* [ ] SQL Injection'a karşı parameterized query kullanılıyor mu?
* [ ] XSS'e karşı uygun kontroller var mı?
* [ ] Kullanıcı girdisine güvenilmiyor mu?

## Session

* [ ] Cookie üzerinde `Secure` kullanılıyor mu?
* [ ] Cookie üzerinde `HttpOnly` kullanılıyor mu?
* [ ] `SameSite` uygun şekilde yapılandırılmış mı?
* [ ] Session expiration uygulanıyor mu?
* [ ] Logout sonrası session geçersiz oluyor mu?
* [ ] Kritik işlemlerde yeniden doğrulama gerekiyor mu?

## Configuration

* [ ] HTTPS kullanılıyor mu?
* [ ] Production ortamında debug mode kapalı mı?
* [ ] Security header'ları uygun mu?
* [ ] Gereksiz servisler kapalı mı?
* [ ] Gereksiz portlar kapalı mı?
* [ ] Yazılımlar güncel mi?
* [ ] Gereğinden fazla izin verilmiş mi?
* [ ] Directory listing kapalı mı?

## Logging

* [ ] Başarılı login olayları kaydediliyor mu?
* [ ] Başarısız loginler kaydediliyor mu?
* [ ] Yetkisiz erişim denemeleri kaydediliyor mu?
* [ ] Şüpheli istekler izleniyor mu?
* [ ] Loglar güvenli şekilde tutuluyor mu?
* [ ] Güvenlik olayları incelenebiliyor mu?

---

# 9. Güvenlik Değerlendirmesinde Kullanacağım Soru Seti

Bir web uygulamasını incelerken artık kendime şu soruları sorabilirim:

### 🔑 Authentication

> Bu kullanıcı gerçekten kim olduğunu kanıtlıyor mu?

> Güçlü parola politikası var mı?

> MFA var mı?

> Brute-force saldırılarına karşı koruma var mı?

### 🛂 Authorization

> Bu kullanıcı bu kaynağa erişebilir mi?

> Rolü bu işlemi yapmaya uygun mu?

> Başka bir kullanıcının verisine erişebilir mi?

> Admin işlemleri doğru şekilde korunuyor mu?

### 📝 Input

> Kullanıcıdan gelen veriye güveniliyor mu?

> Input validation yapılıyor mu?

> Output encoding uygulanıyor mu?

> SQL Injection riski var mı?

> XSS riski var mı?

### 🍪 Session

> Session nasıl oluşturuluyor?

> Cookie `Secure` mı?

> `HttpOnly` mı?

> `SameSite` ayarlanmış mı?

> Session'ın süresi var mı?

### ⚙️ Configuration

> HTTPS kullanılıyor mu?

> Debug mode açık mı?

> Güvenlik header'ları doğru mu?

> Gereksiz servisler açık mı?

> Yazılımlar güncel mi?

> Gereğinden fazla izin verilmiş mi?

### 📋 Logging

> Başarılı girişler kayıt altına alınıyor mu?

> Başarısız girişler izleniyor mu?

> Yetkisiz erişimler kaydediliyor mu?

> Şüpheli istekler tespit edilebiliyor mu?

---

# 10. Öğrendiğim Temel Noktalar

Bu bölümde benim için en önemli nokta, web güvenliğinin tek bir kontrolden oluşmadığını görmek oldu.

Örneğin:

```text
Authentication = OK
```

olması:

```text
Authorization = OK
```

anlamına gelmez.

Aynı şekilde:

```text
HTTPS = OK
```

olması:

```text
SQL Injection = Yok
XSS = Yok
IDOR = Yok
```

anlamına gelmez.

Bu nedenle web güvenliği **katmanlı bir yaklaşım** olarak değerlendirilmelidir.

```text
Authentication
      ↓
Authorization
      ↓
Input Security
      ↓
Session Security
      ↓
Configuration
      ↓
Logging & Monitoring
```

Bu katmanların her biri farklı bir güvenlik problemini ele alır.

---

# 11. Sonuç

Web Security Checklist çalışması sayesinde bir web uygulamasına yalnızca tek tek zafiyetler açısından değil, daha sistematik bir şekilde bakmayı öğrendim.

Bir uygulamayı değerlendirirken artık sadece:

> "Burada açık var mı?"

diye düşünmek yerine:

> **"Kullanıcı kimliğini nasıl doğruluyor, neye erişmesine izin veriyor, gönderdiği veriyi nasıl işliyor, oturumunu nasıl koruyor, sistem nasıl yapılandırılmış ve güvenlik olaylarını nasıl takip ediyor?"**

sorularını birlikte değerlendirmem gerektiğini biliyorum.

Bu yaklaşım, daha sonraki güvenlik analizlerinde kullanacağım temel kontrol mantığını oluşturuyor.

> **Not:** Bu checklist eğitim ve güvenlik değerlendirmesi amacıyla hazırlanmıştır. Gerçek sistemlerde test yapılmadan önce gerekli yetki ve kapsam belirlenmelidir.
