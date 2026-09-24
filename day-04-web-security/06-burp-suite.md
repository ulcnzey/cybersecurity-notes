# Burp Suite ile İlk HTTP Trafik Analizi

## 📌 Çalışmanın Amacı

Bu çalışmada Burp Suite kullanarak bir web uygulamasının browser ile server arasındaki HTTP trafiğini incelemeyi öğrendim.

Çalışmayı kendi lokal laboratuvar ortamımda **OWASP Juice Shop** üzerinde gerçekleştirdim.

Bu aşamadaki amacım herhangi bir saldırı gerçekleştirmek değil, web uygulamasında kullanıcı tarafından yapılan işlemlerin HTTP request ve response'lara nasıl dönüştüğünü anlamaktı.

---

## 🛠️ Kullandığım Ortam

* Kali Linux
* Burp Suite
* OWASP Juice Shop
* Docker
* Burp Browser

Juice Shop'u Docker üzerinde lokal olarak çalıştırdım ve uygulamaya:

```text
http://localhost:3000
```

üzerinden eriştim.

Tüm incelemeler kendi lab ortamım üzerinde gerçekleştirildi.

---

# 1. Burp Suite ile HTTP Trafiğini Görmek

Burp Suite'i bir proxy olarak kullanarak browser ile web uygulaması arasındaki trafiği gözlemledim.

Temel iletişim akışı:

```text
Browser
   ↓
Burp Proxy
   ↓
Web Application
   ↓
Burp Proxy
   ↓
Browser
```

Burp Suite'in **Proxy → HTTP history** bölümünden browser tarafından gönderilen request'leri ve server tarafından döndürülen response'ları inceleyebildim.

Bu sayede browser'da sadece bir sayfanın açıldığını görmek yerine, arka planda hangi HTTP iletişiminin gerçekleştiğini görebildim.

---

# 2. İlk HTTP Request İncelemesi

Juice Shop ana sayfasını açtığımda Burp HTTP History içerisinde aşağıdaki request'e benzer bir istek gördüm:

```http
GET / HTTP/1.1
Host: localhost:3000
User-Agent: Mozilla/5.0 ...
Accept: text/html,application/xhtml+xml,...
```

### Request'in anlamı

```text
GET
```

Server'dan bir kaynak istediğimi gösterir.

```text
/
```

Uygulamanın ana sayfasını istediğimi gösterir.

```text
Host: localhost:3000
```

İsteğin kendi bilgisayarımda çalışan Juice Shop uygulamasına gönderildiğini gösterir.

```text
User-Agent
```

Browser ve işletim sistemi hakkında bilgi taşır.

```text
Accept
```

Browser'ın hangi içerik türlerini kabul edebileceğini belirtir.

---

# 3. İlk HTTP Response İncelemesi

Server'ın bu request'e verdiği response'u Burp üzerinden inceledim.

Örneğin:

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: ...
ETag: ...
```

### `200 OK`

İsteğin başarılı şekilde işlendiğini gösterir.

### `Content-Type`

Server'ın gönderdiği içeriğin türünü belirtir.

Örneğin:

```text
text/html
```

HTML içerik gönderildiğini gösterir.

### `Content-Length`

Response body'sinin boyutunu belirtir.

### `ETag`

Kaynağın belirli bir versiyonunu tanımlamak ve cache mekanizmalarında kullanılmak için kullanılan bir değerdir.

---

# 4. Login Request'ini İncelemek

Daha sonra Juice Shop'un login bölümünü kullandım ve Burp HTTP History üzerinden login işlemi sırasında oluşan request/response trafiğini inceledim.

Login işlemi sırasında request'in `POST` yöntemiyle gönderildiğini ve kullanıcı bilgilerinin request içerisinde taşındığını gözlemledim.

Temel yapı:

```text
Browser
   ↓
POST /rest/user/login
   ↓
Burp
   ↓
Juice Shop
```

Login request'inde e-posta ve parola gibi kullanıcı tarafından gönderilen bilgiler request body içerisinde taşınabilir.

Bu çalışma sayesinde daha önce teorik olarak öğrendiğim **HTTP Request Body** kavramını gerçek bir uygulama üzerinde görmüş oldum.

---

# 5. Login Response ve JWT

Login işleminden sonra server tarafından:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

şeklinde bir response döndüğünü gözlemledim.

Response içerisinde authentication bilgileri ve bir JWT token bulunuyordu.

Örnek yapı:

```json
{
  "authentication": {
    "token": "...",
    "bid": 1,
    "umail": "admin@juice-sh.op"
  }
}
```

Buradaki `token` alanının bir **JWT (JSON Web Token)** olduğunu öğrendim.

JWT, kullanıcı oturumunun/kimliğinin uygulama tarafından taşınmasında kullanılabilen bir token yapısıdır.

Ayrıca token içerisinde kullanıcıyla ilgili bazı bilgilerin bulunduğunu gördüm.

Burada önemli bir nokta öğrendim:

> JWT içerisinde verinin bulunması, verinin şifrelenmiş olduğu anlamına gelmez. JWT'nin payload bölümü genellikle Base64URL ile kodlanır.

Bu nedenle token içerisindeki bilgilerin gizli kabul edilmemesi gerektiğini öğrendim.

---

# 6. Authentication ve Authorization ile Bağlantısı

Login işlemini incelerken authentication ve authorization kavramlarının HTTP trafiğiyle bağlantısını daha iyi anladım.

### Authentication

```text
Kullanıcı kim?
```

Login işlemi kullanıcının kimliğinin doğrulanmasıyla ilgilidir.

### Authorization

```text
Bu kullanıcı ne yapabilir?
```

Kullanıcının rolü veya sahip olduğu yetkiler üzerinden hangi kaynaklara erişebileceği belirlenir.

İncelediğim JWT içerisinde:

```text
role: admin
```

gibi kullanıcıya ait rol bilgisinin bulunduğunu gördüm.

Bu, authentication sonrasında authorization kararlarının nasıl kullanıcı bilgileriyle ilişkilendirilebildiğini anlamama yardımcı oldu.

---

# 7. Arama İşlemini HTTP Üzerinden İncelemek

Burp Suite'in sadece login işlemlerini değil, normal kullanıcı işlemlerini de gözlemleyebildiğini görmek için Juice Shop içerisindeki arama özelliğini kullandım.

Arama kutusuna:

```text
ZEYNEP
```

yazdım.

Daha sonra Burp:

```text
Proxy → HTTP history
```

bölümünden oluşan request'i inceledim.

Arama işlemlerinde kullanıcı tarafından girilen değerin HTTP request içerisinde, uygulamanın kullandığı yapıya bağlı olarak URL içerisindeki bir **query parameter** veya request body içerisinde taşınabileceğini gözlemledim.

Örneğin URL tabanlı bir arama isteğinde yapı şu şekilde olabilir:

```text
/rest/products/search?q=ZEYNEP
```

Burada:

```text
q
```

arama parametresini,

```text
ZEYNEP
```

ise kullanıcı tarafından gönderilen arama değerini ifade eder.

Bu çalışma sayesinde kullanıcı tarafından girilen bir verinin browser'dan server'a giderken HTTP request içerisinde nasıl taşındığını görmüş oldum.

---

# 8. Bu Çalışmada Öğrendiklerim

Bu çalışmanın sonunda:

* Burp Suite'in proxy mantığını,
* HTTP History bölümünü,
* Request ve Response arasındaki farkı,
* GET ve POST request'lerini,
* HTTP headers kavramını,
* Request body'nin kullanımını,
* Query parameter mantığını,
* `200 OK` response'unu,
* `Content-Type` header'ını,
* Login işleminin HTTP trafiğini,
* JWT token'ın authentication sürecindeki kullanımını,
* Authentication ve Authorization arasındaki farkı,
* Kullanıcı tarafından girilen verilerin HTTP trafiğinde nasıl taşınabildiğini

uygulamalı olarak görmüş oldum.

---

## 🔐 Güvenlik Notu

Bu çalışma yalnızca kendi oluşturduğum **lokal OWASP Juice Shop laboratuvar ortamında** gerçekleştirilmiştir.

Bu aşamada herhangi bir gerçek sisteme yönelik saldırı veya yetkisiz test gerçekleştirmedim.

Amacım öncelikle HTTP trafiğini okuyabilmek, request/response yapısını anlayabilmek ve web uygulamalarının browser ile server arasında nasıl iletişim kurduğunu öğrenmekti.

---

## 🎯 Sonuç

Burp Suite'i kullanmadan önce web uygulamasında yalnızca browser üzerinde gördüğüm işlemleri biliyordum.

Bu çalışma sayesinde bir kullanıcının yaptığı işlemin arka planda HTTP request'lerine dönüştüğünü ve server'ın bu request'lere response gönderdiğini uygulamalı olarak gördüm.

Özellikle login ve arama işlemlerini incelemek, **kullanıcı girdilerinin web uygulamasına nasıl ulaştığını** anlamam açısından faydalı oldu.

Bu temel, ilerleyen çalışmalarda SQL Injection, XSS, IDOR, CSRF ve diğer web güvenliği konularını anlamak için önemli bir başlangıç oluşturuyor.
