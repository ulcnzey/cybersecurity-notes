# 🌐 Web Uygulaması Nasıl Çalışır?

Bu bölümde bir web uygulamasının temel çalışma mantığını ve kullanıcı ile sunucu arasındaki iletişimi anlamaya çalıştım. Web güvenliği konularını öğrenmeden önce, normal bir web uygulamasının hangi parçalardan oluştuğunu ve bu parçaların nasıl iletişim kurduğunu anlamanın önemli olduğunu fark ettim.

---

## 1. Client Nedir?

**Client**, bir servisten veya sistemden veri ya da hizmet isteyen taraftır.

Web uygulamalarında kullanıcının bilgisayarı veya telefonu client olarak düşünülebilir. Web tarayıcısı da client tarafında çalışan ve web uygulamasıyla iletişim kuran yazılımdır.

Örneğin Chrome üzerinden bir web sitesine girdiğimde, Chrome sunucuya istek gönderen client görevi görür.

```text
Kullanıcı
   ↓
Web Browser
   ↓
Server
```

---

## 2. Web Browser Nedir?

Web browser, web uygulamalarına erişmemizi sağlayan istemci yazılımıdır.

Chrome, Firefox, Edge ve Safari gibi tarayıcılar buna örnektir.

Bir browser'ın temel görevleri arasında:

* HTTP/HTTPS istekleri göndermek,
* Sunucudan gelen response'ları almak,
* HTML, CSS ve JavaScript'i işlemek,
* Cookie'leri yönetmek,
* Web uygulamasının kullanıcı arayüzünü göstermek

bulunur.

Bu nedenle browser'ı sadece web sitelerini görüntüleyen bir program olarak değil, web uygulaması ile iletişim kuran bir **client** olarak düşünmek gerekir.

---

## 3. Server Nedir?

Server, client tarafından gönderilen istekleri karşılayan ve uygun cevapları sağlayan sistemdir.

Basit olarak:

```text
Client
   │
   │ Request
   ▼
Server
   │
   │ Response
   ▼
Client
```

şeklinde düşünebilirim.

Örneğin bir web sitesinde `/profile` adresini istediğimde browser bir HTTP request gönderir. Server bu isteği işler ve uygun response'u geri gönderir.

---
<img width="356" height="229" alt="image" src="https://github.com/user-attachments/assets/562d01fd-e965-4a42-b0ba-6d0ce215d6a7" />


## 4. Web Server Nedir?

Web server, HTTP/HTTPS üzerinden gelen istekleri karşılayan ve istemciye içerik veya cevap sağlayan sunucu yazılımıdır.

Yaygın web server yazılımlarına:

* Nginx
* Apache
* IIS

örnek verilebilir.

Web server ile web uygulamasını birbirinden ayırmak gerekir.

Web server gelen HTTP isteklerini karşılayabilirken, uygulamanın asıl iş mantığı backend/application tarafında bulunabilir.

---

## 5. Application / Backend Nedir?

Backend, web uygulamasının kullanıcı tarafından doğrudan görülmeyen ve uygulamanın iş mantığını gerçekleştiren bölümüdür.

Örneğin bir kullanıcı `/profile` adresine girdiğinde backend:

1. İsteği alabilir.
2. Kullanıcının kim olduğunu kontrol edebilir.
3. Kullanıcının bu kaynağa erişme yetkisini kontrol edebilir.
4. Veritabanından gerekli bilgileri isteyebilir.
5. Sonucu response olarak browser'a gönderebilir.

Bu nedenle backend, web güvenliği açısından önemli bir kontrol noktasıdır.

---

## 6. Database Nedir?

Database, uygulamanın ihtiyaç duyduğu verilerin saklandığı sistemdir.

Örneğin bir kullanıcı sistemi içerisinde:

```text
Users
----------------
id
username
email
password_hash
role
```

gibi bilgiler bulunabilir.

Bir web uygulamasında kullanıcı genellikle database'e doğrudan bağlanmaz.

Bunun yerine:

```text
Browser
   ↓
API / Backend
   ↓
Database
```

şeklinde bir iletişim kurulur.

Backend, gelen isteği kontrol ederek gerekli veriyi database'den alır ve kullanıcıya uygun response'u gönderir.

---

## 7. API Nedir?

API, farklı yazılımların belirli kurallar üzerinden birbiriyle iletişim kurmasını sağlayan bir arayüzdür.

Örneğin bir mobil uygulamanın doğrudan database'e bağlanması yerine:

```text
Mobile App
    ↓
   API
    ↓
 Backend
    ↓
 Database
```

şeklinde bir yapı kullanılabilir.

Bir API endpoint'i örneğin:

```text
GET /api/users/15
```

şeklinde olabilir.

Burada uygulama API üzerinden belirli bir işlemi veya veriyi istemektedir.

<img width="950" height="457" alt="image" src="https://github.com/user-attachments/assets/42912d85-7af5-4cc7-b311-b059f921b971" />

---

## 8. DNS Nedir?

Kullanıcılar web sitelerine genellikle alan adlarıyla erişir:

```text
example.com
```

Ancak ağ üzerindeki iletişimde IP adresleri kullanılır.

DNS (Domain Name System), alan adlarının ilgili IP adresleriyle eşleştirilmesine yardımcı olur.

Basitleştirilmiş olarak:

```text
example.com
     ↓
    DNS
     ↓
IP adresi
```

şeklinde düşünebilirim.
<img width="1200" height="600" alt="image" src="https://github.com/user-attachments/assets/c3b138ba-c999-4e5b-b588-431ff3d2df95" />

---

# 🔄 Bir Web Sitesine Girdiğimde Ne Olur?

Örneğin browser'a:

```text
https://example.com/profile
```

yazdığımı düşünelim.

Basitleştirilmiş süreç şu şekilde ilerler:

```text
Kullanıcı
   ↓
Browser
   ↓
DNS
   ↓
Web Server
   ↓
Backend / Application
   ↓
Database
   ↓
Backend / Application
   ↓
Web Server
   ↓
HTTP Response
   ↓
Browser
```

Browser önce ilgili alan adı için gerekli adres bilgisini öğrenir. Daha sonra server'a HTTP/HTTPS üzerinden bir request gönderir.

Server bu isteği ilgili uygulamaya iletebilir. Uygulama gerekiyorsa database'den veri alır, gerekli kontrolleri gerçekleştirir ve sonucu response olarak browser'a gönderir.

Browser da gelen response'u işleyerek kullanıcıya web sayfasını gösterir.

---

# 🔐 Browser Neden Doğrudan Database'e Bağlanmaz?

Web uygulamalarında browser'ın doğrudan database'e bağlanması yerine genellikle API/backend üzerinden iletişim kurulmasının önemli nedenlerinden biri **kontrol ve güvenliktir**.

Örneğin:

```text
Browser
   ↓
Database
```

şeklinde doğrudan bir bağlantı olsaydı database erişim bilgilerinin ve database'in doğrudan kullanıcı tarafına açılması gibi ciddi problemler ortaya çıkabilirdi.

Bunun yerine:

```text
Browser
   ↓
API / Backend
   ↓
Database
```

şeklinde bir yapı kullanılır.

Backend burada bir kontrol katmanı görevi görür.

Örneğin kullanıcı bir profile erişmek istediğinde backend:

```text
İstek kimden geliyor?
        ↓
Kullanıcı doğrulanmış mı?
        ↓
Bu veriye erişme yetkisi var mı?
        ↓
Gerekli veriyi database'den al
        ↓
Response oluştur
```

gibi kontroller gerçekleştirebilir.

Bu yapı aynı zamanda ileride inceleyeceğim **Authentication, Authorization, SQL Injection ve Broken Access Control** gibi web güvenliği konularıyla doğrudan bağlantılıdır.

---

# 🛡️ Siber Güvenlik Açısından Neden Önemli?

Bir web uygulamasını güvenlik açısından inceleyebilmek için öncelikle uygulamanın normal çalışma şeklini bilmek gerekir.

Örneğin:

```text
Browser → Server
```

arasındaki iletişimde HTTP request ve response'lar önemlidir.

```text
Backend → Database
```

arasındaki iletişimde ise kullanıcıdan gelen verilerin nasıl işlendiği önemlidir.

Bu nedenle ilerleyen bölümlerde inceleyeceğim:

* SQL Injection
* XSS
* IDOR
* CSRF
* Authentication
* Authorization
* Session Security

gibi konuların aslında web uygulamasının farklı noktalarında ortaya çıkan güvenlik problemleri olduğunu görebilirim.

---

## 🧠 Bu Bölümden Çıkardığım Temel Mantık

Bir web uygulamasını artık sadece:

> "Tarayıcıda açılan bir web sitesi"

olarak düşünmüyorum.

Arka planda birbirleriyle iletişim kuran farklı bileşenler bulunuyor:

```text
User
 ↓
Browser / Client
 ↓
DNS
 ↓
Web Server
 ↓
Backend / Application
 ↓
Database
```

Kullanıcı ile server arasındaki iletişim HTTP/HTTPS üzerinden gerçekleşirken, backend uygulamanın iş mantığını yürütüyor ve gerektiğinde database ile iletişim kuruyor.

Web güvenliğini anlamak için bu veri akışının hangi noktasında hangi bilginin işlendiğini ve hangi güvenlik kontrolünün yapılması gerektiğini bilmek gerekiyor.
