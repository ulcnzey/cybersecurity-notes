# 07 - Authentication İncelemesi

## Amaç

Bu bölümde OWASP Juice Shop'un login mekanizmasını Burp Suite üzerinden inceleyerek kullanıcı doğrulama sürecinin nasıl gerçekleştiğini anlamaya çalıştım.

Özellikle kullanıcı bilgilerinin sunucuya nasıl gönderildiğini, login response'unu ve authentication sonrasında kullanılan token yapısını inceledim.

---

## Login İsteğinin İncelenmesi

Juice Shop üzerinde giriş işlemi gerçekleştirdikten sonra Burp Suite HTTP History üzerinden login isteğini inceledim.

### Login Request

```http
POST /rest/user/login HTTP/1.1
```

Login isteğinde:

* **HTTP Method:** POST
* **Endpoint:** `/rest/user/login`
* **Content-Type:** `application/json`

kullanıldığı görüldü.

Kullanıcı bilgileri request body içerisinde JSON formatında gönderildi.

Örneğin yapı şu şekildeydi:

```json
{
  "email": "kullanici@example.com",
  "password": "REDACTED"
}
```

Gerçek parola güvenlik nedeniyle notlara eklenmedi.

### Kullanıcı Bilgisi

Kullanıcının e-posta adresi JSON body içerisindeki `email` alanında gönderildi.

### Parola

Parola JSON body içerisindeki `password` alanında gönderildi.

---

## Login Response

Login isteğine sunucu tarafından:

```http
HTTP/1.1 200 OK
```

response'u döndürüldü.

Response'un:

```http
Content-Type: application/json
```

olduğu görüldü.

Response body içerisinde authentication için kullanılan bir **token** bulunduğu gözlemlendi.

Token'ın gerçek değeri güvenlik nedeniyle notlara eklenmedi.

---

## Token Kullanımı

Login sonrasında gerçekleştirilen API isteklerinde:

```http
Authorization: Bearer <token>
```

şeklinde Authorization header'ının kullanıldığı gözlemlendi.

Buna göre authentication akışı genel olarak:

```text
Kullanıcı
   ↓
Email + Password
   ↓
POST /rest/user/login
   ↓
200 OK
   ↓
Token oluşturulur
   ↓
Authorization: Bearer <token>
   ↓
Korunan API istekleri
```

şeklindedir.

Token'ın response içerisinde oluşturulduğu ve sonraki API isteklerinde kullanıldığı gözlemlendi.

---

# Authentication Kavramları

## JWT

JWT (JSON Web Token), taraflar arasında bilgi taşımak ve kimlik doğrulama süreçlerinde kullanılabilen, imzalanmış bir token formatıdır.

JWT genel olarak üç bölümden oluşur:

```text
Header.Payload.Signature
```

JWT'nin yapısı ve bu üç bölümün görevi bir sonraki bölümde ayrıca incelenecektir.

JWT'nin kodlanmış olması şifrelenmiş olduğu anlamına gelmez.

---

## Access Token

Access Token, kullanıcının kimliğini doğruladıktan sonra korunan kaynaklara erişmek için kullanılabilen token'dır.

Bizim incelememizde login sonrasında elde edilen token'ın sonraki API isteklerinde kullanıldığı görüldü.

---

## Refresh Token

Refresh Token, Access Token'ın süresi dolduğunda yeni bir Access Token almak için kullanılabilen token türüdür.

Bu çalışmada ayrı bir Refresh Token gözlemlenmedi.

---

## Session Cookie

Session Cookie, kullanıcının oturumunu sunucu tarafındaki bir session ile ilişkilendirmek için kullanılabilir.

HTTP isteklerinde Cookie header'ı içerisinde gönderilir.

Juice Shop trafiğinde Cookie bilgileri de gözlemlendi.

---

## Bearer Token

Bearer, bir token'ın HTTP Authorization header'ında gönderilme biçimlerinden biridir.

Örneğin:

```http
Authorization: Bearer <token>
```

Buradaki `Bearer`, token'ın gönderim yöntemini ifade eder.

---

## Önemli Ayrım

Bu kavramları birbirinden ayırmak önemlidir:

* **JWT:** Token'ın formatı
* **Access Token:** Token'ın kullanım amacı
* **Bearer Token:** Token'ın HTTP üzerinde gönderilme şekli
* **Session Cookie:** Oturum bilgisini cookie üzerinden taşıyan mekanizma
* **Refresh Token:** Yeni access token almak için kullanılabilen token

Dolayısıyla JWT ile Bearer aynı kavram değildir.

---

## Bu Bölümde Öğrendiklerim

Bu çalışmada Juice Shop'un login mekanizmasını Burp Suite üzerinden inceledim.

Kullanıcı bilgilerinin `POST /rest/user/login` endpoint'ine JSON formatında gönderildiğini gördüm.

Başarılı login sonrasında sunucunun `200 OK` response'u döndürdüğünü ve response içerisinde bir authentication token oluşturduğunu gözlemledim.

Sonraki API isteklerinde token'ın:

```http
Authorization: Bearer <token>
```

formatında kullanıldığını gördüm.

Ayrıca JWT, Access Token, Refresh Token, Session Cookie ve Bearer Token kavramlarının birbirinden farklı olduğunu öğrendim.

Bu inceleme sayesinde authentication sürecinin sadece login formundan ibaret olmadığını; login sonrasında oluşturulan kimlik bilgilerinin sonraki HTTP isteklerinde nasıl kullanıldığının da önemli olduğunu anladım.
