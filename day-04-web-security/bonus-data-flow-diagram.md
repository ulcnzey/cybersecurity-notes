# Bonus: Data Flow Diagram

## 1. Amaç

Bu çalışmada, kendi laboratuvar ortamımda kullandığım web uygulamasında bir kullanıcının tarayıcısından sunucuya gönderdiği bilgilerin hangi aşamalardan geçtiğini inceledim.

Bu akışı anlamak benim için özellikle Burp Suite kullanımını anlamak açısından önemli. Çünkü Burp Suite, tarayıcı ile web uygulaması arasındaki HTTP trafiğini görmemi ve incelememi sağlıyor.

Temel veri akışı şu şekilde:

```text
┌──────────────┐
│   Browser    │
└──────┬───────┘
       │
       │ HTTP Request
       ↓
┌──────────────┐
│ Burp Proxy   │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Web Server   │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Application  │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│   Database   │
└──────┬───────┘
       │
       │ Data
       ↓
┌──────────────┐
│ Application  │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Web Server   │
└──────┬───────┘
       │
       │ HTTP Response
       ↓
┌──────────────┐
│ Burp Proxy   │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│   Browser    │
└──────────────┘
```

---

## 2. Browser

İlk aşama kullanıcının tarayıcısıdır.

Kullanıcı bir web sitesini açtığında, giriş yaptığında, arama yaptığında veya bir butona bastığında tarayıcı web uygulamasına bir HTTP request gönderir.

Örneğin kullanıcı bir arama kutusuna:

```text
apple
```

yazıp arama yaptığında tarayıcı aşağıdaki gibi bir istek oluşturabilir:

```http
GET /rest/products/search?q=apple HTTP/1.1
Host: localhost:3000
Accept: application/json
User-Agent: Mozilla/5.0
```

Bu istekte kullanıcı tarafından gönderilen bilgiler arasında URL, HTTP methodu, query parameter, header bilgileri ve gerekiyorsa cookie veya authorization bilgileri bulunabilir.

Bu nedenle browser tarafında gönderilen verilerin güvenlik açısından güvenilmez kabul edilmesi gerekir.

---

## 3. Burp Proxy

Tarayıcı ile web uygulaması arasına Burp Suite yerleştirildiğinde HTTP trafiğini gözlemleyebilirim.

Akış:

```text
Browser
   ↓
Burp Proxy
   ↓
Web Application
```

Burp Proxy'nin temel amacı burada HTTP request ve response trafiğini görmemi sağlamaktır.

Burp Suite üzerinden örneğin:

* HTTP methodunu
* URL'yi
* Query parameter'ları
* Request header'larını
* Cookie'leri
* Request body'yi
* Response status code'u
* Response header'larını
* Response body'yi

inceleyebilirim.

Bu nedenle Burp Suite benim için web uygulamasının içerisindeki işlemleri doğrudan gösteren bir araç değil, **istemci ile uygulama arasındaki HTTP iletişimini incelememi sağlayan bir aracı katmandır.**

---

## 4. Web Server

Burp Proxy'den geçen HTTP request web sunucusuna ulaşır.

Web server'ın görevi gelen HTTP isteklerini karşılamak ve uygun şekilde uygulamaya yönlendirmektir.

Örneğin:

```text
GET /rest/products/search?q=apple
```

isteği web sunucusuna ulaştığında, ilgili endpoint'in işlenmesi için uygulama katmanına aktarılabilir.

Burada önemli nokta, web server'ın her zaman verinin asıl işlendiği yer olmadığıdır. Web uygulamasının mimarisine göre request daha sonra application katmanına gönderilebilir.

---

## 5. Application

Application katmanı web uygulamasının asıl iş mantığının bulunduğu bölümdür.

Örneğin kullanıcı:

```text
/rest/products/search?q=apple
```

isteğini gönderdiğinde application:

1. İsteği alır.
2. `q` parametresini okur.
3. Gerekli kontrolleri yapar.
4. Gerekirse database'e sorgu gönderir.
5. Database'den gelen sonucu işler.
6. Kullanıcıya gönderilecek response'u oluşturur.

Örneğin application database'den ürün bilgilerini aldıktan sonra aşağıdaki gibi JSON oluşturabilir:

```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "name": "Apple Juice"
    }
  ]
}
```

Bu katman web güvenliği açısından oldukça önemlidir. Authentication, authorization, input validation ve diğer güvenlik kontrollerinin önemli bir bölümü burada uygulanır.

---

## 6. Database

Application ihtiyaç duyduğu verileri database üzerinden alabilir.

Örneğin ürün araması yapıldığında application database'e uygun bir sorgu gönderebilir.

Basitleştirilmiş akış:

```text
User Input
    ↓
Application
    ↓
Database Query
    ↓
Database
    ↓
Result
    ↓
Application
```

Database doğrudan kullanıcının tarayıcısıyla iletişim kurmak zorunda değildir.

Genellikle kullanıcı:

```text
Browser → Web Server → Application → Database
```

şeklinde ilerleyen bir yapı üzerinden işlem yapar.

Bu ayrım güvenlik açısından önemlidir. Kullanıcının doğrudan database'e erişmesine izin verilmesi yerine database erişiminin application tarafından kontrol edilmesi beklenir.

---

## 7. HTTP Response

Database'den gerekli bilgiler alındıktan sonra application kullanıcıya gönderilecek response'u oluşturur.

Response tekrar web server üzerinden istemciye gönderilir.

Örneğin:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

ve response body içerisinde:

```json
{
  "status": "success",
  "data": [...]
}
```

bulunabilir.

Response içerisinde ayrıca cookie, cache bilgileri ve çeşitli security header'ları da bulunabilir.

---

## 8. Response'un Burp Proxy'den Geçmesi

Response kullanıcıya ulaşmadan önce Burp Proxy üzerinden geçebilir.

Bu noktada Burp Suite sayesinde response'u da inceleyebilirim.

Örneğin:

```text
Application
     ↓
Web Server
     ↓
HTTP Response
     ↓
Burp Proxy
     ↓
Browser
```

Burp üzerinden response'un:

* Status code
* Headers
* Cookies
* Content-Type
* Response body

gibi bölümlerini inceleyebilirim.

Bu sayede sadece kullanıcının gönderdiği request'i değil, sunucunun kullanıcıya gönderdiği response'u da analiz edebilirim.

---

## 9. Browser'a Dönüş

Son aşamada HTTP response tekrar tarayıcıya ulaşır.

Tarayıcı response'u işler ve kullanıcıya web sayfasını, ürünleri, mesajları veya API'den gelen diğer verileri gösterir.

Böylece temel iletişim tamamlanmış olur:

```text
Browser
   ↓
HTTP Request
   ↓
Burp Proxy
   ↓
Web Server
   ↓
Application
   ↓
Database
   ↓
Application
   ↓
Web Server
   ↓
HTTP Response
   ↓
Burp Proxy
   ↓
Browser
```

---

## 10. Burp Suite Bu Akışta Nerede?

Burp Suite'in konumunu özellikle ayırmak benim için önemli.

Burp Suite:

```text
Browser ←→ Burp Proxy ←→ Web Application
```

arasında bulunur.

Burp Suite database'e doğrudan bağlanmaz.

Ayrıca application'ın içerisinde gerçekleşen SQL sorgusunu da doğrudan göstermez.

Örneğin Burp üzerinde:

```http
GET /rest/products/search?q=apple
```

isteğini görebilirim.

Fakat application'ın database'e gönderdiği gerçek SQL sorgusu Burp'un gördüğü HTTP trafiğinin bir parçası değildir.

Bu nedenle:

```text
Burp → HTTP katmanını görür
Application → İş mantığını yürütür
Database → Veriyi saklar ve sorgular
```

şeklinde düşünebilirim.

---

## 11. Güvenlik Açısından Veri Akışının Önemi

Bu veri akışını anlamak, web güvenliği konularını birbirinden bağımsız düşünmek yerine bağlantılı şekilde anlamamı sağlıyor.

Örneğin:

### Authentication

Kullanıcının kimliğinin doğrulanması application tarafında kontrol edilebilir.

```text
Browser → Login Request → Application
```

### Authorization

Kullanıcının istediği kaynağa erişme yetkisinin olup olmadığı kontrol edilir.

```text
Browser → Request → Application
                  ↓
          Authorization Check
```

### SQL Injection

Kullanıcı girdisi application tarafından database sorgusunda güvenli şekilde işlenmezse database katmanında problem oluşabilir.

```text
Browser
   ↓
User Input
   ↓
Application
   ↓
Database
```

### XSS

Kullanıcıdan gelen verinin güvenli şekilde işlenmeden browser'a geri gönderilmesi durumunda browser tarafında risk oluşabilir.

```text
Browser
   ↓
User Input
   ↓
Application
   ↓
HTTP Response
   ↓
Browser
```

### Broken Access Control / IDOR

Kullanıcı geçerli bir request gönderse bile application'ın istenen kaynağa erişim yetkisini kontrol etmemesi güvenlik problemi oluşturabilir.

Bu nedenle web güvenliğinde sadece request'in gönderilip gönderilmediğine değil, **request'in application tarafından nasıl işlendiğine** de bakmak gerekir.

---

## 12. Genel Akış

Bu çalışmadan çıkardığım temel veri akışı:

```text
┌────────────┐
│  Browser   │
└─────┬──────┘
      │
      │ HTTP Request
      ↓
┌────────────┐
│ Burp Proxy │
└─────┬──────┘
      │
      ↓
┌────────────┐
│ Web Server │
└─────┬──────┘
      │
      ↓
┌────────────┐
│ Application │
└─────┬──────┘
      │
      │ Database Query
      ↓
┌────────────┐
│  Database  │
└─────┬──────┘
      │
      │ Data
      ↓
┌────────────┐
│ Application │
└─────┬──────┘
      │
      ↓
┌────────────┐
│ Web Server │
└─────┬──────┘
      │
      │ HTTP Response
      ↓
┌────────────┐
│ Burp Proxy │
└─────┬──────┘
      │
      ↓
┌────────────┐
│  Browser   │
└────────────┘
```

Bu akışı öğrendiğimde Burp Suite üzerinde gördüğüm bir HTTP request'in aslında web uygulamasının daha büyük bir veri akışının yalnızca bir parçası olduğunu daha net anlayabiliyorum.

### Kısa Özet

| Aşama         | Görevi                                         |
| ------------- | ---------------------------------------------- |
| Browser       | Kullanıcının isteğini oluşturur                |
| Burp Proxy    | HTTP trafiğini yakalar ve incelememi sağlar    |
| Web Server    | HTTP isteklerini karşılar ve yönlendirir       |
| Application   | İş mantığını ve güvenlik kontrollerini yürütür |
| Database      | Verileri saklar ve sorgular                    |
| HTTP Response | Sonucu istemciye taşır                         |
| Browser       | Response'u işler ve kullanıcıya gösterir       |

> **Not:** Bu çalışma eğitim amaçlı, kendi yerel laboratuvar ortamımdaki web uygulaması üzerinden veri akışını anlamak için hazırlanmıştır.
