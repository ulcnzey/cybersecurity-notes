# 08 - JWT İncelemesi

## Amaç

Bu bölümde OWASP Juice Shop'un login işlemi sonucunda oluşturulan JWT'yi inceleyerek JWT'nin yapısını ve authentication sürecindeki kullanımını anlamaya çalıştım.

İncelediğim token kendi laboratuvar hesabıma ait olup, token'ın tamamını veya içerisindeki hassas bilgileri GitHub'a eklemedim.

---

## JWT'nin Genel Yapısı

JWT (JSON Web Token) genel olarak üç bölümden oluşur:

```text
Header.Payload.Signature
```

Bu üç bölüm `.` karakteri ile birbirinden ayrılır.

İncelediğim Juice Shop token'ında da üç bölüm bulunduğunu gördüm.

---

## 1. Header

JWT'nin ilk bölümüdür.

İncelediğim token'ın Header bölümünde:

```json
{
  "typ": "JWT",
  "alg": "RS256"
}
```

bilgileri bulundu.

Burada:

* `typ` → Token tipini belirtir. Bu örnekte `JWT`.
* `alg` → JWT'nin imzalanmasında kullanılan algoritmayı belirtir. Bu örnekte `RS256`.

`RS256`, RSA tabanlı bir imzalama algoritmasıdır.

---

## 2. Payload

JWT'nin ikinci bölümüdür.

Payload içerisinde kullanıcı ve token hakkında çeşitli claim'ler bulunabilir.

İncelediğim Juice Shop token'ında örneğin:

```text
id
username
email
role
profileImage
isActive
createdAt
updatedAt
iat
```

gibi alanların bulunduğunu gördüm.

Payload içerisinde ayrıca laboratuvar hesabının rol bilgisinin de yer aldığını gözlemledim.

### Payload şifreleme değildir

JWT payload'ının okunabilir olması, JWT'nin şifrelenmiş olduğu anlamına gelmez.

JWT'nin yaygın kullanımında Header ve Payload bölümleri Base64URL ile kodlanır.

Bu nedenle:

```text
Encoding ≠ Encryption
```

Token'a erişebilen bir kişi payload'ı decode ederek içindeki bilgileri okuyabilir.

Bu nedenle parola, gizli anahtar veya başka hassas bilgilerin JWT payload'ında tutulması uygun değildir.

---

## 3. Signature

JWT'nin üçüncü bölümüdür.

Signature, token'ın bütünlüğünü ve imzasının doğrulanmasını sağlamak için kullanılır.

İncelediğim JWT'de Header içerisinde:

```text
alg: RS256
```

bulunduğu için token'ın RS256 algoritması kullanılarak imzalandığını gördüm.

Basitleştirilmiş olarak:

```text
Header + Payload
       ↓
   İmzalama
       ↓
   Signature
```

şeklinde düşünülebilir.

---

## JWT Neden Kullanılır?

JWT, authentication sonrasında kullanıcının kimliğini ve bazı bilgileri sonraki HTTP isteklerinde taşımak için kullanılabilir.

Juice Shop üzerinde gözlemlediğim akış:

```text
Kullanıcı
   ↓
Email + Password
   ↓
POST /rest/user/login
   ↓
200 OK
   ↓
JWT oluşturulur
   ↓
Authorization: Bearer <JWT>
   ↓
Sonraki API istekleri
```

şeklindedir.

---

## JWT Şifreleme midir?

Hayır.

JWT bir şifreleme yöntemi değildir.

JWT'nin Header ve Payload bölümleri genellikle Base64URL ile kodlanır. Kodlama ile şifreleme birbirinden farklıdır.

Bir JWT'nin payload'ının okunabilmesi, token'ın bozuk veya geçersiz olduğu anlamına gelmez.

---

## Payload Neden Gizli Veri Saklamak İçin Uygun Değildir?

Payload genellikle gizli tutulmadığı için token'a erişebilen bir kişi payload içerisindeki bilgileri okuyabilir.

Bu nedenle JWT payload'ında:

* Parola
* Gizli anahtar
* API secret
* Gereksiz hassas kişisel bilgiler

gibi verilerin tutulmaması gerekir.

İncelediğim Juice Shop JWT'sinde kullanıcıya ait çeşitli bilgilerin payload içerisinde bulunduğunu gördüm. Bu durum JWT payload'ına hangi bilgilerin konulması gerektiğinin güvenlik açısından önemli olduğunu gösterdi.

---

## Signature Ne İşe Yarar?

Signature, JWT'nin değiştirilmediğini ve geçerli bir imzaya sahip olduğunu kontrol etmeye yardımcı olur.

Örneğin payload değiştirildiğinde:

```text
Eski Header + Eski Payload
          ↓
     Eski Signature
```

ile yeni içerik arasında uyuşmazlık oluşabilir.

Sunucu token'ın signature değerini doğruladığında token'ın değiştirilmiş olduğunu tespit edebilir.

---

## Token'ın Değiştirilmesi Neden İmza Doğrulamasını Etkiler?

JWT'nin signature değeri token'ın Header ve Payload içeriğiyle ilişkilidir.

Örneğin payload içerisindeki:

```text
role: user
```

değerinin değiştirilerek:

```text
role: admin
```

yapılması token'ın içeriğini değiştirir.

Mevcut signature artık yeni içerikle eşleşmeyebileceği için sunucu imza doğrulamasında token'ı geçersiz kabul edebilir.

Bu nedenle JWT içindeki bir değeri değiştirmek tek başına yetki elde edildiği anlamına gelmez.

---

## Önemli Güvenlik Notu

JWT ile ilgili testlerde başka kullanıcıların tokenlarını ele geçirmeye veya gerçek kullanıcı hesaplarına erişmeye çalışmadım.

İnceleme yalnızca kendi OWASP Juice Shop laboratuvar ortamım ve kendi test hesabım üzerinden gerçekleştirildi.

JWT'nin yapısını anlamak için token'ın Header, Payload ve Signature bölümlerini inceledim.

---

## Bu Bölümde Öğrendiklerim

Bu çalışmada JWT'nin:

```text
Header.Payload.Signature
```

şeklinde üç temel bölümden oluştuğunu öğrendim.

İncelediğim Juice Shop token'ında `typ: JWT` ve `alg: RS256` bilgilerini gördüm.

Payload içerisinde kullanıcıya ait çeşitli claim'lerin bulunduğunu ve bu bölümün şifrelenmiş olmak zorunda olmadığını öğrendim.

Signature bölümünün token'ın bütünlüğünün ve imzasının doğrulanmasında kullanıldığını gördüm.

Ayrıca JWT, Access Token ve Bearer Token kavramlarının birbirinden farklı olduğunu daha net anladım:

* **JWT:** Token formatı
* **Access Token:** Erişim amacıyla kullanılan token
* **Bearer:** Token'ın HTTP Authorization header'ında gönderilme şekli

Bu bölüm sayesinde login sonrasında oluşan token'ın sadece rastgele bir değer olmadığını, belirli bir yapıya ve doğrulama mekanizmasına sahip olduğunu uygulamalı olarak görmüş oldum.
