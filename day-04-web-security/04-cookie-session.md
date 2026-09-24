# 🍪 Cookie ve Session Güvenliği

Web uygulamalarında kullanıcı giriş yaptıktan sonra sunucunun kullanıcının kim olduğunu ve oturumunun devam edip etmediğini takip etmesi gerekir.

HTTP temel olarak **stateless (durumsuz)** çalıştığı için her request birbirinden bağımsızdır.

Bu nedenle web uygulamalarında kullanıcı oturumlarını takip etmek için **cookie** ve **session** gibi mekanizmalar kullanılır.

---

# 1. Cookie Nedir?

Cookie, web sunucusu tarafından tarayıcıya gönderilebilen ve tarayıcı tarafından saklanabilen küçük veri parçalarıdır.

Sunucu response içerisinde:

```http
Set-Cookie: session=abc123
```

gibi bir header gönderebilir.

Tarayıcı daha sonra uygun request'lerde:

```http
Cookie: session=abc123
```

şeklinde bu bilgiyi sunucuya geri gönderebilir.

Basitleştirilmiş akış:

```text
Browser ────────→ Server
        Login

Browser ←──────── Server
        Set-Cookie:
        session=abc123

Browser ────────→ Server
        Cookie:
        session=abc123
```

---

# 2. Session Nedir?

Session, sunucunun bir kullanıcının oturum durumunu takip etmesini sağlayan mekanizmadır.

Örneğin sunucu tarafında:

```text
Session ID    User
-------------------------
abc123        User 42
xyz789        User 15
```

gibi bir ilişki bulunabilir.

Tarayıcı:

```text
session=abc123
```

gönderdiğinde sunucu bu session ID üzerinden ilgili oturumu bulabilir.

---

# 3. Cookie ve Session Aynı Şey Değildir

Bu iki kavram birbirinden farklıdır.

### Cookie

Tarayıcı tarafında saklanan veya taşınan bilgidir.

### Session

Sunucunun kullanıcı oturumunu takip etmek için kullandığı mekanizmadır.

Basitleştirilmiş yapı:

```text
Browser
   │
   │ Cookie: session=abc123
   ↓
Server
   │
   │ abc123
   ↓
Session
   │
   ↓
User ID = 42
```

---

# 4. Login Sonrasında Session Nasıl Oluşabilir?

Bir kullanıcının login olması sonrasında süreç kabaca şöyle olabilir:

```text
1. Kullanıcı login bilgilerini gönderir.
             ↓
2. Sunucu kullanıcı bilgilerini doğrular.
             ↓
3. Sunucu bir session oluşturur.
             ↓
4. Session ID oluşturulur.
             ↓
5. Session ID cookie üzerinden tarayıcıya gönderilir.
             ↓
6. Tarayıcı sonraki request'lerde cookie'yi gönderir.
```

Örneğin:

```http
POST /login
```

isteği başarılı olduktan sonra sunucu:

```http
Set-Cookie: session=abc123
```

gönderebilir.

Sonraki request:

```http
GET /profile
Cookie: session=abc123
```

şeklinde olabilir.

---

# 5. Session ID Neden Önemlidir?

Session ID, uygulamanın kullanıcının aktif oturumunu tanımasına yardımcı olabilir.

Örneğin:

```text
session=abc123
```

değeri:

```text
abc123 → User 42
```

şeklinde bir oturuma karşılık gelebilir.

Bu nedenle session ID'nin ele geçirilmesi durumunda, uygulamanın tasarımına bağlı olarak saldırgan başka bir kullanıcının aktif oturumunu kullanmaya çalışabilir.

---

# 6. Session Hijacking

**Session hijacking**, saldırganın başka bir kullanıcının session bilgisini ele geçirerek o kullanıcının aktif oturumunu kullanmaya çalışmasıdır.

Basitleştirilmiş senaryo:

```text
Normal kullanıcı
      ↓
session=abc123
      ↓
Web Application

Saldırgan
      ↓
session=abc123
      ↓
Web Application
```

Bu nedenle session bilgilerinin güvenli şekilde yönetilmesi gerekir.

---

# 7. HttpOnly

Cookie'lerde kullanılabilen güvenlik özelliklerinden biri:

```text
HttpOnly
```

Örneğin:

```http
Set-Cookie: session=abc123; HttpOnly
```

HttpOnly, cookie'nin JavaScript tarafından doğrudan okunmasını engellemeye yardımcı olur.

Örneğin JavaScript:

```javascript
document.cookie
```

kullanarak normal cookie'lere erişebilir.

HttpOnly olarak işaretlenmiş cookie'lere ise JavaScript üzerinden doğrudan erişilemez.

Bu özellik özellikle bazı XSS kaynaklı cookie hırsızlığı senaryolarının etkisini azaltmaya yardımcı olabilir.

Ancak:

> HttpOnly, XSS açığını tamamen ortadan kaldırmaz.

---

# 8. Secure

Bir diğer önemli cookie özelliği:

```text
Secure
```

Örneğin:

```http
Set-Cookie: session=abc123; Secure
```

Secure özelliği, cookie'nin yalnızca HTTPS bağlantıları üzerinden gönderilmesini sağlar.

Bu nedenle session cookie'lerinde Secure kullanılması önemlidir.

---

# 9. SameSite

Bir diğer önemli cookie özelliği:

```text
SameSite
```

Yaygın değerleri:

```text
SameSite=Strict
SameSite=Lax
SameSite=None
```

SameSite, cookie'nin farklı site bağlamlarında yapılan request'lerde ne zaman gönderileceğini kontrol etmeye yardımcı olur.

Özellikle **CSRF** gibi saldırılara karşı korunmada önemli bir mekanizmadır.

---

# 10. Cookie Güvenlik Özellikleri

Örneğin:

```http
Set-Cookie: session=abc123; Secure; HttpOnly; SameSite=Lax
```

şeklinde bir cookie gördüğümde:

```text
Secure
↓
Cookie'nin HTTPS üzerinden gönderilmesini sağlar.

HttpOnly
↓
JavaScript'in cookie'ye doğrudan erişmesini sınırlar.

SameSite
↓
Cross-site request'lerde cookie gönderimini kontrol etmeye yardımcı olur.
```

---

# 11. Session Güvenliği İçin Önemli Noktalar

Güvenli bir session mekanizmasında:

* Session ID tahmin edilemeyecek şekilde oluşturulmalıdır.
* HTTPS kullanılmalıdır.
* Session cookie'lerinde `Secure` kullanılmalıdır.
* Uygun durumlarda `HttpOnly` kullanılmalıdır.
* `SameSite` politikası uygun şekilde yapılandırılmalıdır.
* Session süreleri kontrol edilmelidir.
* Logout sonrasında session geçersiz hale getirilmelidir.
* Kimlik doğrulama sonrasında session yönetimi güvenli şekilde yapılmalıdır.

---

# 🔐 Siber Güvenlik Açısından Önemi

Web güvenliği analizlerinde request'leri incelerken cookie ve session bilgileri sık sık karşıma çıkar.

Örneğin:

```http
GET /profile HTTP/1.1
Host: lab.local
Cookie: session=abc123
```

gibi bir request gördüğümde bunun kullanıcının oturumuyla ilişkili olabileceğini anlayabilirim.

Burp Suite ile analiz yaparken özellikle:

* Cookie var mı?
* Session nasıl tutuluyor?
* `HttpOnly` var mı?
* `Secure` var mı?
* `SameSite` nasıl ayarlanmış?
* Login sonrasında session değişiyor mu?
* Logout sonrasında session geçersiz oluyor mu?

gibi noktalar incelenebilir.

---

# 🧠 Kısa Özet

```text
Cookie
↓
Tarayıcıda saklanan/taşınan bilgi

Session
↓
Sunucunun kullanıcı oturumunu takip etme mekanizması

Session ID
↓
Bir oturumu tanımlayan değer
```

Cookie güvenlik özellikleri:

```text
HttpOnly → JavaScript erişimini sınırlar
Secure   → HTTPS üzerinden gönderimi sağlar
SameSite → Cross-site cookie gönderimini kontrol eder
```

En önemli nokta:

> Kullanıcının login olması tek başına yeterli değildir. Kullanıcının oturumunun güvenli şekilde yönetilmesi de web uygulaması güvenliğinin önemli bir parçasıdır.
