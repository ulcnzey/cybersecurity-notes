# CSRF (Cross-Site Request Forgery)

## 1. CSRF Nedir?

CSRF, **Cross-Site Request Forgery** kelimelerinin kısaltmasıdır. Türkçe olarak **Siteler Arası İstek Sahteciliği** şeklinde ifade edilebilir.

CSRF saldırısında temel amaç, kullanıcının zaten giriş yapmış olduğu bir web uygulamasına, kullanıcının bilgisi veya isteği dışında bir işlem yaptırmaya çalışmaktır.

Buradaki önemli nokta, saldırganın doğrudan kullanıcının şifresini bilmesine gerek olmamasıdır. Kullanıcının hedef uygulamadaki aktif oturumu saldırının temelini oluşturabilir.

Basit şekilde:

```text
Kullanıcı → Hedef uygulamada giriş yapmış
        ↓
Oturum cookie'si mevcut
        ↓
Kullanıcı başka bir web sitesini açıyor
        ↓
Bu site hedef uygulamaya istek oluşturmayı deniyor
        ↓
Uygulama isteği yeterince doğrulamazsa
        ↓
Kullanıcının oturumu üzerinden işlem gerçekleşebilir
```

CSRF özellikle **durum değiştiren işlemler** açısından önemlidir. Örneğin para transferi, e-posta değiştirme, şifre değiştirme veya hesap ayarlarını değiştirme gibi işlemler sadece kullanıcının aktif oturumuna güvenilerek gerçekleştirilmemelidir.

---

## 2. Kullanıcı Oturumu Neden Önemlidir?

CSRF'nin çalışabilmesi açısından kullanıcının hedef uygulamadaki oturumu önemli bir unsurdur.

Kullanıcı bir web uygulamasına giriş yaptığında sunucu, kullanıcının kimliğini takip etmek için bir session mekanizması kullanabilir. Bu oturum bilgisi çoğu durumda bir cookie aracılığıyla tarayıcıda tutulur.

Örneğin:

```http
Set-Cookie: session=abc123
```

Daha sonraki uygun isteklerde tarayıcı bu cookie'yi gönderebilir:

```http
Cookie: session=abc123
```

Sunucu da bu session bilgisine bakarak isteğin hangi kullanıcıya ait olduğunu anlayabilir.

CSRF açısından problem şu noktada ortaya çıkar:

> Tarayıcı, bazı durumlarda kullanıcı tarafından açıkça yazılmamış olsa bile bir istekte kimlik doğrulama bilgisini gönderebilir.

Bu nedenle uygulama yalnızca:

> "Bu istek geçerli bir oturumdan mı geliyor?"

sorusuna bakmamalıdır.

Aynı zamanda:

> "Bu isteği gerçekten kullanıcı bu uygulamadan yapmak istedi mi?"

sorusunu da güvenlik tasarımında dikkate almalıdır.

---

## 3. CSRF Token Nedir?

**CSRF Token**, sunucunun oluşturduğu ve isteğin gerçekten beklenen uygulama akışından geldiğini doğrulamaya yardımcı olan özel bir değerdir.

Örneğin bir form içerisinde:

```html
<input type="hidden" name="csrf_token" value="RANDOM_VALUE">
```

bulunabilir.

Kullanıcı formu gönderdiğinde token da sunucuya gönderilir.

Sunucu:

1. Token'ın mevcut olup olmadığını kontrol eder.
2. Token'ın beklenen değerle eşleşip eşleşmediğini kontrol eder.
3. Doğrulama başarılıysa işlemi gerçekleştirir.
4. Başarısızsa isteği reddeder.

Önemli nokta, CSRF token'ın tahmin edilmesi zor olması ve sunucu tarafından doğrulanmasıdır.

Örneğin:

```text
Kullanıcı formu
      ↓
CSRF Token
      ↓
Sunucu
      ↓
Token doğrulama
      ↓
Geçerliyse işlem
```

Böylece saldırganın başka bir siteden sadece isteği oluşturması yeterli olmayabilir. Çünkü gerekli CSRF token değerini bilmesi ve doğru şekilde göndermesi gerekir.

---

## 4. SameSite Cookie Nedir?

**SameSite**, cookie'lerin farklı site bağlamlarında gönderilmesini kontrol eden bir cookie özelliğidir.

CSRF açısından önemli bir güvenlik katmanıdır.

Temel değerleri:

### Strict

Cookie'nin cross-site isteklerde gönderilmesini daha sıkı şekilde sınırlar.

```http
SameSite=Strict
```

### Lax

Cross-site durumlarda daha kontrollü bir davranış sağlar ve modern tarayıcılarda yaygın olarak kullanılan seçeneklerden biridir.

```http
SameSite=Lax
```

### None

Cookie'nin cross-site kullanıma izin vermesini belirtir.

```http
SameSite=None; Secure
```

`SameSite=None` kullanıldığında `Secure` gerekliliği de önemlidir.

SameSite, CSRF riskini azaltabilir ancak tek başına bütün CSRF problemlerini çözmek için güvenilmemelidir. Uygulamanın genel güvenlik tasarımı ve gerekli diğer kontroller de uygulanmalıdır.

---

## 5. GET ve POST Metodlarının Güvenlik Açısından İlişkisi

HTTP metodlarının uygulamadaki amaçları farklıdır.

Genel olarak:

* `GET` → Veri almak/okumak
* `POST` → Veri göndermek veya işlem başlatmak
* `PUT` → Bir kaynağı güncellemek
* `PATCH` → Bir kaynağın belirli kısmını güncellemek
* `DELETE` → Bir kaynağı silmek

Özellikle **durum değiştiren işlemlerin GET ile yapılmaması** gerekir.

Örneğin:

```text
GET /profile
```

profil bilgisini görüntülemek için kullanılabilir.

Buna karşılık:

```text
POST /change-password
```

şifre değiştirme gibi durum değiştiren bir işlem için daha uygun bir tasarımdır.

Ancak önemli bir nokta vardır:

> POST kullanmak tek başına CSRF koruması değildir.

Çünkü saldırgan, bazı durumlarda kullanıcı tarayıcısını POST isteği oluşturmaya yönlendirebilir.

Bu nedenle durum değiştiren işlemlerde uygun CSRF korumaları da uygulanmalıdır.

---

# 6. Banka Hesabı Senaryosu

Bir kullanıcının banka uygulamasına giriş yaptığını düşünelim.

Kullanıcının tarayıcısında banka uygulamasına ait geçerli bir oturum bulunmaktadır:

```text
Kullanıcı
   ↓
Banka uygulamasına giriş
   ↓
Session Cookie
```

Daha sonra kullanıcı aynı tarayıcıda başka bir web sitesini açıyor.

Eğer bu kötü niyetli web sitesi, kullanıcının tarayıcısını banka uygulamasına istenmeyen bir istek göndermeye yönlendirebilirse ve banka uygulaması bu isteğin gerçekten kullanıcı tarafından başlatıldığını yeterince doğrulamıyorsa, CSRF riski ortaya çıkabilir.

Yüksek seviyede akış şu şekilde düşünülebilir:

```text
Kullanıcı
   ↓
Banka hesabında giriş yapılmış
   ↓
Geçerli session mevcut
   ↓
Kötü niyetli başka bir site açılıyor
   ↓
Hedef uygulamaya istek oluşturulmaya çalışılıyor
   ↓
Uygulama yeterli CSRF kontrolü yapmıyorsa
   ↓
İstek kullanıcının oturumu üzerinden değerlendirilebilir
```

Buradaki temel problem, kullanıcının kimliğinin doğrulanmış olması değil, **isteğin gerçekten kullanıcının amacıyla oluşturulup oluşturulmadığının yeterince doğrulanmamasıdır.**

---

# 7. CSRF'ye Karşı Temel Korunma Yöntemleri

CSRF riskini azaltmak için birden fazla güvenlik katmanı kullanılabilir.

### CSRF Token

Durum değiştiren isteklerde sunucu tarafından oluşturulan ve doğrulanan token kullanılabilir.

### SameSite Cookie

Cookie'lerin cross-site isteklerde gönderilmesini sınırlandırarak CSRF riskini azaltabilir.

### Secure Cookie

Cookie'nin HTTPS üzerinden gönderilmesini sağlar.

```http
Secure
```

Ancak `Secure` özelliği tek başına CSRF koruması değildir.

### HttpOnly

Cookie'nin JavaScript tarafından okunmasını sınırlar.

```http
HttpOnly
```

Bu özellik özellikle XSS sonrası cookie erişimi açısından önemlidir ancak tek başına CSRF koruması değildir.

### Doğru HTTP Metodu Kullanımı

Veri değiştiren işlemlerin GET yerine uygun durum değiştiren HTTP metodlarıyla tasarlanması gerekir.

### Sunucu Tarafında Kontrol

Asıl güvenlik kontrolü sunucu tarafında yapılmalıdır. Sadece frontend üzerinde yapılan kontroller güvenlik açısından yeterli değildir.

---

# 8. CSRF ve XSS Arasındaki Fark

CSRF ve XSS birbirinden farklı güvenlik problemleridir.

| Konu            | CSRF                                               | XSS                                                   |
| --------------- | -------------------------------------------------- | ----------------------------------------------------- |
| Temel problem   | Kullanıcının oturumuyla istenmeyen istek oluşturma | Güvenilmeyen içeriğin tarayıcıda kod olarak çalışması |
| Odak noktası    | İstek ve oturum                                    | Tarayıcı ve web içeriği                               |
| Oturum ilişkisi | Çok önemli                                         | Saldırının türüne göre değişebilir                    |
| Temel korunma   | CSRF Token, SameSite vb.                           | Output Encoding, Input Validation, CSP vb.            |

Kısaca:

> **CSRF, kullanıcının oturumunu kullanarak istenmeyen işlem yaptırmaya çalışır.**

> **XSS ise güvenilmeyen içeriğin tarayıcı tarafından çalıştırılmasına neden olmaya çalışır.**

---

# 9. Öğrendiğim Temel Noktalar

Bu bölümde CSRF konusunda özellikle şu noktaları öğrendim:

* CSRF, Cross-Site Request Forgery anlamına gelir.
* Saldırının temelinde kullanıcının mevcut oturumu bulunabilir.
* Tarayıcının authentication cookie'sini uygun koşullarda otomatik gönderebilmesi önemlidir.
* CSRF Token, isteğin beklenen uygulama akışından geldiğini doğrulamaya yardımcı olur.
* SameSite cookie özelliği cross-site cookie gönderimini sınırlandırabilir.
* GET metodu durum değiştiren işlemler için kullanılmamalıdır.
* POST kullanmak tek başına CSRF koruması değildir.
* `Secure` ve `HttpOnly` önemli cookie güvenlik özellikleridir ancak tek başlarına CSRF koruması sağlamazlar.
* Güvenlik kontrolleri mümkün olduğunca sunucu tarafında uygulanmalıdır.
* CSRF ve XSS aynı problem değildir ve farklı savunma yöntemleri gerektirir.

---

## 10. Kısa Özet

CSRF'nin temelinde şu problem vardır:

> **Kullanıcının zaten açık olan oturumunun, kullanıcının amacı dışında bir işlem için kullanılabilmesi.**

Bu nedenle web uygulamalarında yalnızca kullanıcının giriş yapmış olup olmadığına bakmak yeterli değildir.

Durum değiştiren işlemler için:

```text
Authentication
       +
Authorization
       +
CSRF Protection
       +
Secure Session Management
       ↓
Daha güvenli web uygulaması
```

şeklinde çok katmanlı bir güvenlik yaklaşımı uygulanmalıdır.

Bu çalışmada CSRF'nin çalışma mantığını teorik olarak inceledim. Gerçek sistemlerde test yapılmamış, herhangi bir gerçek kullanıcı hesabı veya uygulama üzerinde saldırı gerçekleştirilmemiştir.
