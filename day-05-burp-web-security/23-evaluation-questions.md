# 🧠 23. Değerlendirme Soruları

Bu soruları çalışırken amacım cevapları ezberlemek değil, web güvenliği testlerinde kullandığım kavramların ne anlama geldiğini ve neden önemli olduklarını anlayabilmektir.

---

## 1. Burp Suite Proxy ne işe yarar?

**Cevap:**

Burp Suite Proxy, tarayıcı ile web sunucusu arasındaki HTTP/HTTPS trafiğini yakalamamı, incelememi ve gerektiğinde değiştirmemi sağlar.

Normalde:

```text
Browser → Web Server
```

şeklinde olan iletişim Burp Proxy kullanıldığında:

```text
Browser
   ↓
Burp Proxy
   ↓
Web Server
```

şeklinde ilerler.

Böylece tarayıcının gönderdiği:

* URL
* HTTP metodu
* Header
* Cookie
* Authorization bilgisi
* Request body

gibi verileri inceleyebilirim.

**Neden önemli?**

Bir web uygulamasını güvenlik açısından test ederken sunucuya gerçekten ne gönderildiğini görmek gerekir.

**Akılda tut:**

> **Proxy = Trafiği yakala ve gör.**

---

## 2. Repeater neden kullanılır?

**Cevap:**

Burp Suite Repeater, yakaladığım bir HTTP isteğini tekrar tekrar göndermemi ve her seferinde kontrollü değişiklikler yapmamı sağlar.

Örneğin:

```text
GET /rest/basket/1
```

isteğini alıp:

```text
GET /rest/basket/2
```

şeklinde değiştirebilirim.

Sonrasında iki response'u karşılaştırabilirim.

**Neden önemli?**

Bir güvenlik açığını araştırırken aynı isteği farklı parametrelerle tekrar göndermek ve sonucu karşılaştırmak gerekir.

Benim Juice Shop çalışmamda basket ID'sini değiştirerek başka bir basket verisine erişilip erişilemediğini Repeater ile test ettim.

**Akılda tut:**

> **Repeater = İsteği değiştir, tekrar gönder, sonucu karşılaştır.**

---

## 3. HTTP Request ile Response arasındaki fark nedir?

**Cevap:**

**Request**, istemcinin sunucuya gönderdiği istektir.

**Response**, sunucunun bu isteğe verdiği cevaptır.

Örneğin:

```text
Browser
   ↓
HTTP Request
   ↓
Server
   ↓
HTTP Response
   ↓
Browser
```

Request içerisinde:

* Method
* URL
* Headers
* Cookies
* Body

bulunabilir.

Response içerisinde:

* Status Code
* Response Headers
* Response Body

bulunabilir.

Örneğin:

```http
GET /rest/basket/1
```

bir requesttir.

Sunucunun:

```http
HTTP/1.1 200 OK
```

ile cevap vermesi ise response'tur.

**Akılda tut:**

> **Request = Ben sunucudan ne istiyorum?**
> **Response = Sunucu bana ne cevap verdi?**

---

## 4. Cookie ile JWT arasındaki temel farklar nelerdir?

**Cevap:**

Cookie ve JWT aynı şey değildir.

**Cookie**, tarayıcının sunucuya isteklerle birlikte gönderebildiği bir HTTP mekanizmasıdır.

**JWT** ise kimlik veya yetki bilgisini taşıyabilen, belirli bir formatta oluşturulmuş bir tokendır.

Örneğin JWT:

```text
Header.Payload.Signature
```

şeklindedir.

JWT bir cookie içerisinde de taşınabilir. Yani:

> Cookie ve JWT birbirinin alternatifi olmak zorunda değildir.

Bir uygulamada JWT:

```text
Authorization: Bearer <token>
```

şeklinde header üzerinden de gönderilebilir.

Cookie güvenliği açısından ise:

* `HttpOnly`
* `Secure`
* `SameSite`

gibi özellikler önemlidir.

**Akılda tut:**

> **Cookie = Tarayıcının taşıdığı HTTP mekanizması.**
> **JWT = Token formatı.**

---

## 5. Authentication ile Authorization neden birbirinden ayrılmalıdır?

**Cevap:**

Çünkü iki farklı soruya cevap verirler.

### Authentication

> **Sen kimsin?**

Örneğin login sırasında kullanıcı adı ve parola kontrol edilir.

### Authorization

> **Bunu yapmaya yetkin var mı?**

Kullanıcı sisteme giriş yapmış olsa bile her kaynağa erişme yetkisi olmayabilir.

Örneğin:

```text
Authentication
      ↓
Kullanıcı tanındı
      ↓
Authorization
      ↓
Bu kaynağa erişebilir mi?
```

Benim Juice Shop çalışmamda authentication sonrasında authorization kontrollerini ayrıca incelememin nedeni buydu.

**Akılda tut:**

> **Authentication = Kimsin?**
> **Authorization = Ne yapabilirsin?**

---

## 6. Broken Access Control nedir?

**Cevap:**

Broken Access Control, kullanıcının yetkisi olmaması gereken bir kaynağa veya işleve erişebilmesi durumudur.

Örneğin bir kullanıcının:

```text
GET /rest/basket/1
```

isteğinden sonra ID'yi:

```text
GET /rest/basket/2
```

olarak değiştirdiğinde başka kullanıcıya ait veriyi görebilmesi bir erişim kontrol problemi olabilir.

Buradaki temel problem:

> Sunucunun kullanıcının gerçekten bu kaynağa erişme yetkisi olup olmadığını kontrol etmemesidir.

**Akılda tut:**

> **Giriş yapmış olmak, her şeye erişebileceğin anlamına gelmez.**

---

## 7. IDOR nedir?

**Cevap:**

IDOR, **Insecure Direct Object Reference** ifadesinin kısaltmasıdır.

Bir uygulamanın bir kaynağı doğrudan bir ID ile belirtmesi ve sunucunun bu kaynağa erişim yetkisini doğru şekilde kontrol etmemesi durumunda ortaya çıkabilir.

Örneğin:

```text
/basket/1
/basket/2
/basket/3
```

gibi kaynaklar varsa saldırgan ID değerini değiştirebilir.

Eğer:

```text
/basket/1
```

kullanıcının kendi sepetiyken:

```text
/basket/2
```

başka kullanıcının sepetini döndürüyorsa erişim kontrolü problemi vardır.

**Önemli:**

Her ID değiştirme işlemi otomatik olarak IDOR değildir.

Önce:

1. Kaynak kime ait?
2. Kullanıcının erişim yetkisi var mı?
3. Sunucu bunu kontrol ediyor mu?

sorularına bakılmalıdır.

**Akılda tut:**

> **ID değişti → başka kullanıcı kaynağı geldi → ownership kontrolü yoksa IDOR adayı.**

---

## 8. SQL Injection neden oluşur?

**Cevap:**

SQL Injection, kullanıcı tarafından kontrol edilen verilerin güvenli şekilde ayrıştırılmadan SQL sorgusuna dahil edilmesi sonucunda ortaya çıkabilir.

Güvensiz yaklaşım:

```text
User Input
   ↓
SQL sorgusuna doğrudan ekleniyor
   ↓
Database
```

Güvenli yaklaşım:

```text
User Input
   ↓
Parameterized Query
   ↓
Database
```

Prepared statement ve parameterized query kullanılması SQL kodu ile kullanıcı verisinin birbirinden ayrılmasına yardımcı olur.

Benim çalışmamda login endpointine kontrollü bir input gönderdiğimde SQLite/Sequelize ile ilgili SQL hata bilgileri döndü.

Bu durum SQL Injection açısından güçlü bir göstergeydi ancak tek başına tam exploit kanıtı değildi.

**Akılda tut:**

> **Kullanıcı verisini SQL kodundan ayır.**

---

## 9. XSS ile SQL Injection arasındaki temel fark nedir?

**Cevap:**

İkisi de input validation problemleriyle ilişkili olabilir ancak hedefleri farklıdır.

### SQL Injection

Veritabanı sorgularını hedefler.

```text
User Input
    ↓
SQL
    ↓
Database
```

### XSS

Web sayfasında çalışan JavaScript bağlamını hedefler.

```text
User Input
    ↓
Web Application
    ↓
Browser
    ↓
Unexpected Script Execution
```

Yani:

> **SQL Injection → Database tarafı**

> **XSS → Browser tarafı**

XSS'in:

* Reflected XSS
* Stored XSS
* DOM-based XSS

gibi türleri vardır.

**Akılda tut:**

> **SQLi = Database**
> **XSS = Browser**

---

## 10. CSRF nedir?

**Cevap:**

CSRF, **Cross-Site Request Forgery** anlamına gelir.

Bir saldırganın, kullanıcının zaten authenticated olduğu bir web uygulamasına kullanıcının bilgisi dışında istek göndermesini sağlamaya çalışmasıdır.

Örneğin kullanıcı bir bankacılık uygulamasına giriş yapmış olsun.

Kötü niyetli bir sayfa, kullanıcının tarayıcısını bankaya istenmeyen bir request göndermeye yönlendirmeye çalışabilir.

Temel fikir:

```text
Kullanıcı giriş yapmış
       ↓
Tarayıcıda authentication bilgisi var
       ↓
Saldırgan isteği tetiklemeye çalışıyor
       ↓
Sunucu isteği gerçek kullanıcıdan geliyor sanabilir
```

CSRF'e karşı kullanılan mekanizmalardan biri **CSRF token** kontrolüdür.

Ayrıca `SameSite` cookie politikaları da CSRF riskinin azaltılmasına yardımcı olabilir.

**Akılda tut:**

> **CSRF = Kullanıcı farkında olmadan onun adına istek yaptırmaya çalışma.**

---

## 11. HttpOnly Cookie neden önemlidir?

**Cevap:**

`HttpOnly`, bir cookie'nin JavaScript tarafından okunmasını engellemek için kullanılan cookie özelliğidir.

Örneğin:

```http
Set-Cookie: session=...; HttpOnly
```

olduğunda JavaScript'in:

```javascript
document.cookie
```

ile bu cookie'ye erişmesi engellenir.

Bu özellikle XSS gibi durumlarda session cookie'sinin JavaScript tarafından okunabilmesi riskini azaltır.

Ancak:

> **HttpOnly XSS'i engellemez.**

Sadece cookie'nin JavaScript tarafından okunmasını sınırlar.

**Akılda tut:**

> **HttpOnly = JavaScript cookie'yi okuyamasın.**

---

## 12. Secure Cookie ne işe yarar?

**Cevap:**

`Secure` özelliği bulunan cookie'nin yalnızca HTTPS üzerinden gönderilmesini sağlar.

Örneğin:

```http
Set-Cookie: session=...; Secure
```

şeklindeki bir cookie HTTP yerine HTTPS üzerinden gönderilmek üzere tasarlanır.

Bu özellik, cookie'nin güvenli olmayan HTTP bağlantıları üzerinden taşınması riskini azaltır.

**Akılda tut:**

> **Secure = Cookie HTTPS üzerinden taşınsın.**

---

## 13. SameSite nedir?

**Cevap:**

`SameSite`, cookie'nin cross-site isteklerde ne zaman gönderileceğini kontrol eden bir cookie özelliğidir.

Temel seçenekleri:

```text
Strict
Lax
None
```

şeklindedir.

### Strict

Cross-site durumlarda cookie gönderimini daha sıkı sınırlar.

### Lax

Belirli cross-site navigasyonlarda cookie gönderilmesine izin verebilir.

### None

Cross-site kullanıma izin verir ancak modern tarayıcılarda `Secure` ile birlikte kullanılması gerekir.

SameSite politikası özellikle CSRF riskinin azaltılmasında önemlidir.

**Akılda tut:**

> **SameSite = Cookie başka siteler üzerinden gelen isteklerde ne zaman gönderilsin?**

---

## 14. API endpoint'leri neden saldırı yüzeyinin önemli bir parçasıdır?

**Cevap:**

Çünkü web uygulamasının birçok önemli işlemi frontend yerine API endpointleri üzerinden gerçekleştirilir.

Örneğin:

```text
Login
   ↓
API

Product
   ↓
API

Basket
   ↓
API

User Data
   ↓
API
```

Frontend'de bir butonun görünmemesi güvenlik kontrolü değildir.

Eğer ilgili API endpointi doğrudan erişilebilir durumdaysa güvenlik kontrollerinin server-side yapılması gerekir.

Bu yüzden test sırasında:

* Endpoint
* Method
* Parameter
* Authentication
* Authorization
* Response

incelenmelidir.

**Akılda tut:**

> **Frontend ne gösteriyor değil, backend neye izin veriyor?**

---

## 15. Bir endpoint'in gizli olması neden güvenlik kontrolü değildir?

**Cevap:**

Çünkü bir endpoint'in URL'sini bilmemek gerçek bir authorization mekanizması değildir.

Örneğin:

```text
/admin-secret-page
```

adresinin kullanıcı arayüzünde gösterilmemesi tek başına güvenlik sağlamaz.

Bir saldırgan endpointi:

* JavaScript dosyalarından
* HTTP trafiğinden
* API dokümantasyonundan
* Uygulama davranışından
* Endpoint tahminlerinden

öğrenebilir.

Gerçek güvenlik kontrolü sunucu tarafında yapılmalıdır.

Örneğin:

```text
Request
   ↓
Authentication
   ↓
Authorization
   ↓
Allowed / Denied
```

**Akılda tut:**

> **Gizlemek ≠ Yetkilendirmek.**

---

## 16. Bir HTTP 403 cevabı ne anlatabilir?

**Cevap:**

HTTP `403 Forbidden`, sunucunun isteği anladığını ancak erişime izin vermediğini gösterebilir.

Basit mantık:

```text
401 → Authentication problemi
403 → Authorization / erişim izni problemi
```

Örneğin kullanıcı sisteme giriş yapmıştır ancak yöneticiye özel bir kaynağa erişmeye çalışıyorsa sunucu `403 Forbidden` döndürebilir.

Ancak status code tek başına bütün güvenlik durumunu açıklamaz. Uygulamanın nasıl tasarlandığı da incelenmelidir.

**Akılda tut:**

> **403 = “İsteğini anladım ama buna izin vermiyorum.”**

---

## 17. Bir güvenlik açığının teknik etkisi ile iş etkisi arasındaki fark nedir?

**Cevap:**

**Teknik etki**, güvenlik açığının sistem üzerinde doğrudan oluşturabileceği etkidir.

Örneğin:

> Başka bir kullanıcının basket verisini okuyabilmek.

**İş etkisi** ise bunun kurum açısından ne anlama geldiğidir.

Örneğin:

* Kullanıcı gizliliğinin bozulması
* Güven kaybı
* Veri koruma yükümlülükleri
* Finansal zarar
* Operasyonel sorun

Teknik etki:

```text
Unauthorized Data Access
```

İş etkisi:

```text
Privacy / Compliance / Trust Impact
```

**Akılda tut:**

> **Teknik etki = Sistem üzerinde ne oldu?**
> **İş etkisi = Kurum açısından bunun sonucu ne olabilir?**

---

## 18. Proof of Concept neden önemlidir?

**Cevap:**

Proof of Concept yani **PoC**, bir güvenlik bulgusunun gerçekten çalıştığını kontrollü şekilde göstermeye yarayan kanıttır.

Örneğin sadece:

> “IDOR olabilir.”

demek yerine:

```text
GET /rest/basket/1
        ↓
GET /rest/basket/2
        ↓
Farklı kullanıcıya ait kaynak döndü
```

şeklinde kontrollü bir gösterim yapmak bulgunun doğrulanmasına yardımcı olur.

PoC'nin amacı zarar vermek değildir.

Amaç:

> **Bulgunun gerçekten mevcut olduğunu güvenli ve tekrarlanabilir şekilde göstermek.**

**Akılda tut:**

> **PoC = “Sadece tahmin etmiyorum, kontrollü olarak gösterdim.”**

---

## 19. Bir pentest raporunda remediation bölümünün amacı nedir?

**Cevap:**

Remediation bölümü, bulunan güvenlik problemlerinin **nasıl düzeltilebileceğini** açıklamak için kullanılır.

Örneğin SQL Injection için:

```text
Yanlış:
String Concatenation
       ↓
SQL Query
```

yerine:

```text
Doğru:
User Input
    ↓
Parameterized Query
    ↓
Database
```

önerilir.

IDOR için ise server-side ownership ve authorization kontrolü uygulanmalıdır.

Yani remediation bölümü:

> **“Sorun var.”**

demekle kalmaz.

Şunu da söyler:

> **“Sorunun temel nedeni bu ve şu şekilde düzeltilmeli.”**

---

## 20. Bir güvenlik uzmanı neden bulduğu açığın nasıl düzeltileceğini de bilmelidir?

**Cevap:**

Çünkü güvenlik testinin amacı yalnızca problem bulmak değil, sistemin güvenliğini geliştirmeye yardımcı olmaktır.

Bir güvenlik uzmanı:

```text
Zafiyeti bulur
      ↓
Teknik nedenini anlar
      ↓
Etkisini değerlendirir
      ↓
Riskini belirler
      ↓
Çözüm önerir
      ↓
Düzeltmeyi tekrar test eder
```

Bu nedenle saldırgan perspektifi kadar geliştirici perspektifini de anlamak önemlidir.

Örneğin:

### Saldırgan perspektifi

> “Basket ID'sini değiştirince başka kullanıcının verisini alabildim.”

### Geliştirici perspektifi

> “Sunucu tarafında resource ownership kontrolü yapmalıyım.”

### Güvenlik uzmanı perspektifi

> “Bu erişim kontrolü eksikliğini kanıtlamalı, etkisini değerlendirmeli ve uygulanabilir remediation önermeliyim.”

Böylece güvenlik testi yalnızca açık bulma işleminden çıkar ve **güvenlik iyileştirme sürecinin bir parçası** haline gelir.

---

# 🧠 20 Sorunun Hızlı Tekrarı

| #  | Konu                  | Akılda Tutulacak Cümle                                           |
| -- | --------------------- | ---------------------------------------------------------------- |
| 1  | Proxy                 | Trafiği yakala ve gör.                                           |
| 2  | Repeater              | Değiştir, tekrar gönder, karşılaştır.                            |
| 3  | Request / Response    | Ben ne istedim? Sunucu ne cevap verdi?                           |
| 4  | Cookie / JWT          | Cookie mekanizma, JWT token formatı.                             |
| 5  | AuthN / AuthZ         | Kimsin? / Ne yapabilirsin?                                       |
| 6  | Broken Access Control | Yetkisiz kaynağa erişim.                                         |
| 7  | IDOR                  | ID değiştirerek başka kaynağa erişim + eksik ownership kontrolü. |
| 8  | SQLi                  | Kullanıcı verisini SQL kodundan ayır.                            |
| 9  | XSS / SQLi            | XSS → Browser, SQLi → Database.                                  |
| 10 | CSRF                  | Kullanıcı adına istek yaptırmaya çalışma.                        |
| 11 | HttpOnly              | JavaScript cookie'yi okuyamasın.                                 |
| 12 | Secure                | Cookie HTTPS üzerinden gönderilsin.                              |
| 13 | SameSite              | Cross-site durumda cookie ne zaman gönderilsin?                  |
| 14 | API                   | Uygulamanın önemli işlemleri API üzerinden gerçekleşir.          |
| 15 | Gizli endpoint        | Gizlemek ≠ yetkilendirmek.                                       |
| 16 | 403                   | İstek anlaşıldı, erişime izin verilmedi.                         |
| 17 | Teknik / İş etkisi    | Sistemde ne oldu? / Kurum için sonucu ne?                        |
| 18 | PoC                   | Bulgunun kontrollü kanıtı.                                       |
| 19 | Remediation           | Problemi nasıl düzelteceğiz?                                     |
| 20 | Güvenlik uzmanı       | Bul → doğrula → değerlendir → düzelt → tekrar test et.           |

---

# 🎯 Kendimi Test Etmek İçin

Bu sorulara çalışırken cevabı doğrudan okumadan önce kendime şu soruları sormalıyım:

1. **Burp Proxy ile Repeater arasındaki fark ne?**
2. **Authentication başarılı olsa bile authorization neden başarısız olabilir?**
3. **ID değiştirmenin neden her zaman IDOR olmadığını açıklayabilir miyim?**
4. **SQL Injection ile XSS'in hedeflerini ayırabilir miyim?**
5. **401 ve 403 arasındaki farkı kendi cümlemle anlatabilir miyim?**
6. **HttpOnly, Secure ve SameSite'ın üç farklı amacını açıklayabilir miyim?**
7. **Bir endpointin gizli olmasının neden güvenlik kontrolü olmadığını açıklayabilir miyim?**
8. **Bir bulgunun teknik etkisi ile iş etkisini ayırabilir miyim?**
9. **PoC'nin neden gerekli olduğunu açıklayabilir miyim?**
10. **Bulduğum bir açığın geliştirici tarafından nasıl düzeltileceğini anlatabilir miyim?**

Bu soruların cevaplarını kendi cümlelerimle anlatabiliyorsam, sadece terimleri ezberlemek yerine web güvenliği mantığını anlamaya başlamışım demektir.
