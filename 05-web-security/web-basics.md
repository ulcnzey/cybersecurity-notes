# 🌐 Web Güvenliğine Giriş

Web uygulamalarının güvenliğini anlayabilmek için öncelikle bir web uygulamasının temel olarak nasıl çalıştığını bilmek gerekir.

Bir kullanıcı tarayıcı üzerinden bir web sitesine girdiğinde, tarayıcı ile sunucu arasında **HTTP istekleri (request)** ve **HTTP cevapları (response)** gerçekleşir.

Temel akış şu şekildedir:

```text
Kullanıcı
   ↓
Tarayıcı (Client)
   ↓ HTTP Request
Sunucu (Server)
   ↓ HTTP Response
Tarayıcı
   ↓
Web Sayfası
```

---

## 1. Client (Müşteri / İstemci)

**Client**, web uygulamasından hizmet isteyen taraftır.

En yaygın client örneği web tarayıcısıdır.

Örneğin:

* Google Chrome
* Microsoft Edge
* Firefox
* Safari
* Mobil uygulamalar

Bir kullanıcı `https://example.com` adresine girdiğinde tarayıcı sunucuya bir HTTP isteği gönderir.

### Kısaca

> **Client = Hizmeti isteyen taraf.**

---

## 2. Server (Sunucu)

**Server**, client tarafından gönderilen istekleri karşılayan ve uygun cevabı gönderen sistemdir.

Örneğin bir web sunucusu:

* Kullanıcı girişini kontrol edebilir.
* Veritabanından bilgi getirebilir.
* Bir web sayfası gönderebilir.
* API isteğine veri döndürebilir.

### Kısaca

> **Server = İsteği karşılayan ve cevap veren taraf.**

---

## 3. Request (İstek)

**Request**, client'ın sunucuya gönderdiği istektir.

Örneğin tarayıcı bir web sayfasını istediğinde sunucuya HTTP request gönderir.

Basitleştirilmiş bir request:

```text
GET /index.html HTTP/1.1
Host: example.com
```

Burada:

* `GET` → HTTP metodu
* `/index.html` → İstenen kaynak
* `Host` → Hangi alan adına istek gönderildiği

### Kısaca

> **Request = Client'ın sunucudan istediği şey.**

---

## 4. Response (Cevap)

**Response**, sunucunun client'a gönderdiği cevaptır.

Örneğin:

```text
HTTP/1.1 200 OK
Content-Type: text/html
```

Sunucu başarılı bir şekilde cevap verdiyse HTML, JSON veya başka bir veri gönderebilir.

### Kısaca

> **Response = Sunucunun client'a verdiği cevap.**

---

# 🍪 5. Cookie (Çerez)

**Cookie**, web sitesinin tarayıcıda küçük miktarda veri saklamasını sağlayan mekanizmadır.

Örneğin bir web sitesi:

* Kullanıcı tercihlerini,
* Oturumla ilişkili bilgileri,
* Bazı takip bilgilerini

cookie aracılığıyla saklayabilir.

Örneğin:

```text
theme=dark
```

Bir cookie her zaman hassas bilgi içermez. Ancak özellikle **oturum bilgileri içeren cookie'lerin korunması** önemlidir.

Güvenlik açısından `Secure`, `HttpOnly` ve uygun `SameSite` ayarları önemlidir.

### Kısaca

> **Cookie = Tarayıcıda saklanan ve web sitesiyle ilişkilendirilebilen küçük veri.**

---

# 🔐 6. Session (Oturum)

**Session**, kullanıcının bir web uygulamasıyla gerçekleştirdiği oturum durumunu ifade eder.

Örneğin kullanıcı bir alışveriş sitesine giriş yaptıktan sonra farklı sayfalara geçtiğinde sistem kullanıcının giriş yaptığını hatırlamalıdır.

Bunun için session mekanizmaları kullanılabilir.

Basit bir örnek:

```text
Kullanıcı giriş yapar
        ↓
Sunucu kullanıcıyı doğrular
        ↓
Oturum oluşturulur
        ↓
Kullanıcı diğer sayfalara erişebilir
```

Session genellikle bir **session ID** ile ilişkilendirilir.

### Kısaca

> **Session = Kullanıcının uygulamadaki oturum durumunun yönetilmesi.**

---

# 🎫 7. Token

**Token**, kullanıcının veya istemcinin belirli bir kimlik doğrulama/erişim durumunu temsil etmek için kullanılan bir veri parçasıdır.

Örneğin bir kullanıcı giriş yaptıktan sonra sunucu bir token verebilir.

Client daha sonraki isteklerde bu tokenı gönderebilir.

Basitleştirilmiş akış:

```text
Kullanıcı
   ↓
Giriş bilgileri
   ↓
Sunucu
   ↓
Token
   ↓
Client
   ↓
Sonraki isteklerde token
```

Tokenlar özellikle API'lerde ve modern web uygulamalarında yaygın olarak kullanılır.

### Kısaca

> **Token = Kimlik doğrulama veya erişim durumunu temsil etmek için kullanılan veri.**

---

# 🔌 8. API

**API (Application Programming Interface)**, farklı yazılım bileşenlerinin birbiriyle iletişim kurmasını sağlayan arayüzdür.

Örneğin bir mobil uygulama hava durumu bilgisini kendi içinde üretmek yerine bir hava durumu API'sinden veri alabilir.

Örnek:

```text
Mobil Uygulama
      ↓
GET /weather
      ↓
API
      ↓
Sunucu
      ↓
JSON verisi
      ↓
Mobil Uygulama
```

API'ler web uygulamalarında önemli bir güvenlik alanıdır.

Çünkü API'lerde:

* Authentication
* Authorization
* Input validation
* Rate limiting
* Veri erişim kontrolü

gibi güvenlik mekanizmaları önemlidir.

### Kısaca

> **API = Yazılımların birbiriyle iletişim kurmasını sağlayan arayüz.**

---

# 🎯 9. Endpoint (Uç Nokta)

**Endpoint**, bir API veya web uygulamasında belirli bir işleve veya kaynağa erişilen adrestir.

Örneğin:

```text
/api/users
/api/products
/api/login
```

Bir API'de farklı endpointler farklı işlemler yapabilir.

Örneğin:

```text
GET /api/users
```

kullanıcı bilgilerini almak için kullanılabilir.

```text
POST /api/login
```

giriş işlemini gerçekleştirmek için kullanılabilir.

### Kısaca

> **Endpoint = Bir API veya web servisinde belirli bir işleve erişilen adres.**

---

# 📋 10. HTTP Header (HTTP Başlığı)

**HTTP Header**, request veya response hakkında ek bilgiler taşıyan alanlardır.

Örneğin:

```text
Content-Type: application/json
```

veya:

```text
Authorization: Bearer <token>
```

gibi headerlar kullanılabilir.

Bazı önemli header örnekleri:

| Header          | Amaç                                 |
| --------------- | ------------------------------------ |
| `Content-Type`  | Gönderilen verinin türünü belirtir   |
| `Authorization` | Kimlik doğrulama bilgisi taşıyabilir |
| `User-Agent`    | Client hakkında bilgi verir          |
| `Cookie`        | Cookie bilgilerini taşıyabilir       |
| `Host`          | İstenen alan adını belirtir          |

Güvenlik açısından bazı HTTP headerları da önemlidir.

Örneğin:

```text
Content-Security-Policy
Strict-Transport-Security
X-Content-Type-Options
```

gibi headerlar web uygulamasının güvenliğini artırmaya yardımcı olabilir.

### Kısaca

> **HTTP Header = Request veya response hakkında ek bilgi taşıyan alan.**

---

# 🔢 11. HTTP Status Code (Durum Kodu)

**HTTP Status Code**, sunucunun gönderilen request'in sonucunu client'a bildirmek için kullandığı sayısal koddur.

Örneğin:

```text
200 OK
```

sunucunun isteği başarıyla işlediğini gösterir.

Status kodları genel olarak beş gruba ayrılır:

| Aralık | Anlam                |
| ------ | -------------------- |
| 1xx    | Bilgilendirme        |
| 2xx    | Başarılı             |
| 3xx    | Yönlendirme          |
| 4xx    | Client kaynaklı hata |
| 5xx    | Server kaynaklı hata |

---

# 📌 HTTP Durum Kodları

## 200 — OK

İstek başarıyla işlenmiştir.

Örnek:

```text
GET /profile
→ 200 OK
```

Kullanıcı profil bilgilerini başarıyla almıştır.

> **200 = Başarılı**

---

## 201 — Created

İstek başarıyla gerçekleştirilmiş ve yeni bir kaynak oluşturulmuştur.

Örneğin yeni kullanıcı oluşturma:

```text
POST /users
→ 201 Created
```

> **201 = Oluşturuldu**

---

## 301 — Moved Permanently

İstenen kaynağın kalıcı olarak başka bir adrese taşındığını belirtir.

Örneğin:

```text
http://example.com
        ↓
https://example.com
```

Tarayıcı yeni adrese yönlendirilebilir.

> **301 = Kalıcı olarak taşındı**

---

## 302 — Found

Kaynağın geçici olarak başka bir adreste bulunduğunu belirtmek için kullanılan yönlendirme kodudur.

> **302 = Geçici yönlendirme**

---

# ❌ 400 — Bad Request

Sunucu, client tarafından gönderilen isteği geçersiz veya hatalı bulmuştur.

Örneğin request'in formatı beklenen yapıya uygun olmayabilir.

> **400 = İstek hatalı**

---

# 🔑 401 — Unauthorized

İstek için geçerli bir kimlik doğrulama bilgisi bulunmadığında kullanılan durum kodudur.

Örneğin kullanıcı giriş yapmadan korumalı bir kaynağa erişmeye çalışabilir.

```text
Kullanıcı
   ↓
/profile
   ↓
Giriş yapılmamış
   ↓
401 Unauthorized
```

> **401 = Kimlik doğrulama gerekli veya geçersiz**

Buradaki önemli nokta:

**401 doğrudan "yetkin yok" anlamına gelmez.**

Daha çok:

> **"Senin kim olduğunu doğrulayamadım."**

şeklinde düşünülebilir.

---

# 🚫 403 — Forbidden

Sunucu isteği anlıyor ancak kullanıcının bu kaynağa erişmesine izin vermiyor.

Örneğin:

```text
Kullanıcı giriş yaptı
       ↓
Authentication başarılı
       ↓
Admin paneline erişmeye çalışıyor
       ↓
Kullanıcının admin yetkisi yok
       ↓
403 Forbidden
```

> **403 = Kimliğin biliniyor ancak bu işlemi yapmaya yetkin yok.**

---

# 🔎 401 ile 403 Arasındaki Fark

Bu iki durum kodu özellikle birbirine karıştırılmamalıdır.

| Kod     | Temel anlam             | Basit soru                    |
| ------- | ----------------------- | ----------------------------- |
| **401** | Authentication problemi | "Sen kimsin?"                 |
| **403** | Authorization problemi  | "Bunu yapmaya yetkin var mı?" |

### Örnek

Bir şirket sisteminde:

```text
Normal kullanıcı
```

admin paneline girmeye çalışıyor.

Eğer kullanıcı henüz giriş yapmamışsa:

```text
401 Unauthorized
```

Kullanıcı giriş yaptıysa fakat admin yetkisi yoksa:

```text
403 Forbidden
```

Bu nedenle:

> **401 → Kimlik doğrulama problemi**

> **403 → Yetkilendirme problemi**

Bu ayrım, daha önce öğrendiğimiz **Authentication ve Authorization** konusuyla doğrudan bağlantılıdır.

---

# ❌ 404 — Not Found

İstenen kaynak bulunamadığında kullanılır.

Örneğin:

```text
GET /olmayan-sayfa
→ 404 Not Found
```

> **404 = Kaynak bulunamadı**

---

# 💥 500 — Internal Server Error

Sunucu tarafında beklenmeyen bir hata oluştuğunu belirtir.

Örneğin uygulamanın sunucu tarafındaki bir işleminde hata meydana gelebilir.

> **500 = Sunucu tarafında hata**

---

# 🧠 401 ve 403'ü Akılda Tutma Yöntemi

Şöyle düşünebilirim:

```text
401
↓
"KİMSİN?"
↓
Authentication
```

```text
403
↓
"Kim olduğunu biliyorum ama buna iznin yok."
↓
Authorization
```

---

# 🌐 Web İsteğinin Genel Akışı

Önceki ağ bilgileriyle birlikte düşündüğümüzde:

```text
Kullanıcı
   ↓
Tarayıcı
   ↓
HTTP Request
   ↓
Sunucu
   ↓
Authentication / Authorization
   ↓
Uygulama işlemi
   ↓
HTTP Response
   ↓
Status Code
   ↓
Tarayıcı
```

Örneğin bir kullanıcı profil sayfasına erişmek istediğinde:

```text
GET /profile
        ↓
Sunucu
        ↓
Kullanıcının kimliği kontrol edilir
        ↓
Yetkisi kontrol edilir
        ↓
200 OK
        ↓
Profil bilgileri
```

Yetkisi yoksa:

```text
GET /admin
        ↓
Sunucu
        ↓
Kullanıcı giriş yapmış mı?
        ↓
Evet
        ↓
Admin yetkisi var mı?
        ↓
Hayır
        ↓
403 Forbidden
```

---

# 🔐 Web Güvenliği Açısından Neden Önemli?

Bu kavramlar siber güvenlik açısından önemlidir çünkü web uygulamalarında saldırıların önemli bir kısmı:

* Kimlik doğrulama hataları
* Yetkilendirme hataları
* Güvenli olmayan session yönetimi
* Güvenli olmayan token kullanımı
* Hatalı API erişim kontrolleri
* Kullanıcıdan gelen verilerin yeterince doğrulanmaması

gibi problemlerle ilişkili olabilir.

Örneğin bir kullanıcı normalde yalnızca kendi profilini görebilmesi gerekirken başka kullanıcıların bilgilerine erişebiliyorsa burada ciddi bir **authorization / access control** problemi olabilir.

---

# 📝 Kendi Öğrenme Notlarım

Bu bölümden öğrendiğim temel kavramları şöyle özetleyebilirim:

* **Client** → İsteği yapan taraf.
* **Server** → İsteği karşılayan taraf.
* **Request** → Client'ın gönderdiği istek.
* **Response** → Server'ın verdiği cevap.
* **Cookie** → Tarayıcıda saklanabilen küçük veri.
* **Session** → Kullanıcının uygulamadaki oturum durumunun yönetilmesi.
* **Token** → Kimlik doğrulama veya erişim durumunu temsil edebilen veri.
* **API** → Yazılımların iletişim kurmasını sağlayan arayüz.
* **Endpoint** → API veya web servisindeki belirli erişim noktası.
* **HTTP Header** → Request/response hakkında ek bilgi.
* **Status Code** → HTTP işleminin sonucunu belirten kod.

En önemli öğrendiğim ayrım:

> **401 = Authentication problemi**

> **403 = Authorization problemi**

---

# 📌 Kısa Özet

```text
Client
   ↓
Request
   ↓
Server
   ↓
Authentication
   ↓
Authorization
   ↓
Response
   ↓
Status Code
```

HTTP durum kodları:

```text
200 → Başarılı
201 → Kaynak oluşturuldu
301 → Kalıcı yönlendirme
302 → Geçici yönlendirme
400 → Hatalı istek
401 → Kimlik doğrulama gerekli/geçersiz
403 → Erişim izni yok
404 → Kaynak bulunamadı
500 → Sunucu hatası
```

### 🎯 En önemli cümle

> **401 "Sen kimsin?" problemiyle, 403 ise "Bunu yapmaya yetkin var mı?" problemiyle ilgilidir.**

---

# 📚 Kaynaklar

* NIST — Cybersecurity and Information Security
* MDN Web Docs — HTTP
* MDN Web Docs — HTTP Status Codes
* OWASP — Web Application Security
* OWASP — Authentication and Authorization
