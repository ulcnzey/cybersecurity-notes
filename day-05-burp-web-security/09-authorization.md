# Authorization (Yetkilendirme) Analizi

## 1. Authorization Nedir?

Authentication (kimlik doğrulama) ile authorization (yetkilendirme) birbirinden farklı kavramlardır.

* **Authentication:** Kullanıcının kim olduğunu doğrular.
* **Authorization:** Doğrulanmış kullanıcının hangi işlemleri yapabileceğini ve hangi kaynaklara erişebileceğini belirler.

Örneğin bir kullanıcı sisteme başarılı şekilde giriş yapmış olabilir. Ancak bu, uygulamadaki bütün API endpointlerine erişebileceği anlamına gelmez.

Yetkilendirme kontrollerinin sunucu tarafında yapılması gerekir.

---

## 2. Juice Shop'ta Yetkilendirme İncelemesi

Juice Shop üzerinde giriş yaptıktan sonra Burp Suite'in **HTTP History** bölümünden uygulamanın gönderdiği HTTP isteklerini inceledim.

Daha önce analiz ettiğim isteklerde `Authorization` header'ı içerisinde Bearer token kullanıldığını gördüm.

Örneğin isteklerde genel olarak şu yapı bulunuyordu:

```http
Authorization: Bearer <JWT>
```

Buradaki JWT, kullanıcının kimlik doğrulama bilgisini taşımaktadır.

JWT'nin payload bölümünü incelediğimde kullanıcıya ait `role` bilgisinin bulunduğunu gördüm.

Kendi kullandığım hesapta bu alanın:

```json
"role": "admin"
```

şeklinde olduğunu gözlemledim.

> Not: Gerçek JWT tokenını veya içindeki hassas bilgileri GitHub'a eklemedim.

---

## 3. Neden Role Göre Erişim Kontrolü Yapılmalı?

Bir kullanıcının sisteme giriş yapmış olması, uygulamadaki bütün işlemleri gerçekleştirebilmesi anlamına gelmez.

Örneğin:

```text
Kullanıcı giriş yaptı
        ↓
Authentication başarılı
        ↓
Kullanıcının rolü kontrol edilir
        ↓
İşlem için yetkisi var mı?
       ↙ ↘
     Evet  Hayır
      ↓      ↓
    İzin    Erişim reddedilir
```

Örneğin yöneticiye özel bir endpoint normal bir kullanıcı tarafından çağrıldığında uygulamanın yalnızca kullanıcının giriş yapmış olmasına bakmaması gerekir. Kullanıcının ilgili işlem için yetkili olup olmadığını da kontrol etmesi gerekir.

---

## 4. Authentication ve Authorization Farkı

| Kavram         | Kontrol ettiği şey      |
| -------------- | ----------------------- |
| Authentication | Kullanıcı kim?          |
| Authorization  | Kullanıcı ne yapabilir? |

Örneğin:

```text
Login
  ↓
Authentication
  ↓
"Bu kullanıcı gerçekten sisteme giriş yaptı mı?"
  ↓
Authorization
  ↓
"Bu kullanıcı bu kaynağa/işleme erişebilir mi?"
```

Bu nedenle giriş yapmış olmak, bütün API endpointlerine erişim hakkı anlamına gelmez.

---

## 5. İncelemeden Çıkardığım Sonuç

Bu çalışmada Burp Suite üzerinden Juice Shop'un HTTP isteklerini ve Authorization header'larını inceledim.

Authentication aşamasında oluşturulan JWT'nin sonraki isteklerde:

```http
Authorization: Bearer <JWT>
```

şeklinde gönderildiğini gördüm.

JWT içerisinde bulunan `role` bilgisinin kullanıcının rolü hakkında bilgi taşıdığını gözlemledim.

Buradan authorization kontrolünün yalnızca "kullanıcı giriş yaptı mı?" şeklinde düşünülmemesi gerektiğini öğrendim. Uygulamanın, kullanıcının **hangi kaynağa veya işlemlere erişmeye yetkili olduğunu** ayrıca kontrol etmesi gerekir.

Bu nedenle:

> **Login olmuş bir kullanıcının uygulamadaki bütün API endpointlerine erişebilmesi gerekmez.**

Özellikle yönetici işlemleri veya başka kullanıcıların kaynakları gibi alanlarda sunucu tarafında uygun yetkilendirme kontrollerinin bulunması gerekir.
