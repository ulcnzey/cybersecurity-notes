# 🔐 Authentication ve Authorization

Bir sistemin güvenli olabilmesi için yalnızca kullanıcıların sisteme giriş yapabilmesi yeterli değildir.

Sistem aynı zamanda:

1. Kullanıcının kim olduğunu doğrulamalı,
2. Kullanıcının hangi kaynaklara erişebileceğini belirlemelidir.

Bu iki aşama **Authentication** ve **Authorization** kavramlarıyla açıklanır.

En kolay şekilde:

> **Authentication = Sen kimsin?**
>
> **Authorization = Neye erişebilirsin?**

---

# 1. Authentication Nedir?

**Authentication (Kimlik Doğrulama)**, bir kullanıcının gerçekten iddia ettiği kişi veya hesap olduğunu doğrulama işlemidir.

Sistem kullanıcının kimliğini doğrulamak için çeşitli yöntemler kullanabilir.

Örneğin:

```text id="2xq6v4"
Kullanıcı adı
     +
Şifre
     ↓
Authentication
     ↓
Kimlik doğrulandı
```

Başka kimlik doğrulama yöntemleri de olabilir:

* Şifre
* PIN
* Parmak izi
* Yüz tanıma
* Donanım güvenlik anahtarı
* Tek kullanımlık kod

Örneğin bir kullanıcı banka uygulamasına giriş yaptığında sistem önce:

> "Bu kişi gerçekten bu hesaba sahip olan kişi mi?"

sorusuna cevap arar.

> **Kısaca:** Authentication = Kim olduğunu doğrulama.

---

# 2. Authorization Nedir?

**Authorization (Yetkilendirme)**, kimliği doğrulanmış bir kullanıcının hangi kaynaklara ve işlemlere erişebileceğini belirleme sürecidir.

Örneğin bir şirkette:

```text id="6x3d6m"
Çalışan
  ↓
Kendi dosyaları → İzin var ✅
Yönetici paneli → İzin yok ❌
```

Kullanıcı sisteme giriş yapmış olabilir ancak bu onun sistemdeki her şeyi görebileceği anlamına gelmez.

Authorization şu soruya cevap verir:

> **"Bu kullanıcı ne yapabilir?"**

Örneğin:

* Dosyayı görüntüleyebilir mi?
* Dosyayı değiştirebilir mi?
* Kullanıcı silebilir mi?
* Yönetici paneline girebilir mi?
* Raporları görebilir mi?

> **Kısaca:** Authorization = Kullanıcının ne yapabileceğini belirleme.

---

# 3. Authentication ve Authorization Arasındaki Fark

En kolay şekilde şöyle düşünebilirim:

```text id="8v5y7p"
Authentication
      ↓
"Sen kimsin?"
      ↓
Kullanıcı doğrulandı
      ↓
Authorization
      ↓
"Neye erişebilirsin?"
```

Örneğin bir şirkete giriş yaptığımı düşünelim.

Girişte güvenlik görevlisine kimliğimi gösteriyorum.

```text id="qg5e6p"
Kimliğimi gösterdim
        ↓
"Bu kişi gerçekten Zeynep."
        ↓
Authentication
```

Daha sonra güvenlik görevlisi:

> "Zeynep'in sunucu odasına girme yetkisi var mı?"

diye kontrol ediyor.

```text id="9nqzj3"
Yetki kontrolü
      ↓
Authorization
```

Yani kimliğimin doğrulanması, her odaya girebileceğim anlamına gelmiyor.

---

# 4. Verilen Senaryonun Açıklaması

Senaryo:

> Bir kullanıcı sisteme kullanıcı adı ve şifresiyle giriş yapıyor. Daha sonra yalnızca yöneticilerin görebildiği bir sayfaya erişmeye çalışıyor.

Bu senaryoyu iki aşamada inceleyebiliriz.

## Aşama 1 — Authentication

Kullanıcı:

```text id="47skwd"
Kullanıcı adı
      +
Şifre
      ↓
Sistem
```

bilgilerini giriyor.

Sistem bilgileri kontrol ediyor.

Bilgiler doğruysa:

```text id="4cxm9j"
Kimlik doğrulandı
        ↓
Authentication başarılı ✅
```

Burada sistem:

> "Bu kullanıcı gerçekten bu hesapla ilişkili kişi mi?"

sorusunu cevaplıyor.

---

## Aşama 2 — Authorization

Kullanıcı sisteme başarıyla giriş yaptıktan sonra yönetici sayfasına gitmeye çalışıyor.

Sistem bu kez:

> "Bu kullanıcının yönetici sayfasına erişim yetkisi var mı?"

sorusunu soruyor.

Kullanıcı yönetici değilse:

```text id="7k1rjv"
Authentication → Başarılı ✅

Authorization → Başarısız ❌

Yönetici sayfası → Erişim reddedildi
```

Burada çok önemli bir nokta vardır:

> **Authentication başarılı olabilirken Authorization başarısız olabilir.**

Bu ikisini birbirine karıştırmamak gerekir.

---

# 5. Authentication ve Authorization Karşılaştırması

| Özellik          | Authentication            | Authorization              |
| ---------------- | ------------------------- | -------------------------- |
| Türkçesi         | Kimlik doğrulama          | Yetkilendirme              |
| Temel soru       | Sen kimsin?               | Neye erişebilirsin?        |
| Ne zaman?        | Genellikle erişimden önce | Kimlik doğrulamadan sonra  |
| Örnek            | Kullanıcı adı + şifre     | Yönetici sayfasına erişim  |
| Başarısız olursa | Sisteme giriş yapılamaz   | İstenen kaynağa erişilemez |

---

# 6. MFA Nedir?

**MFA (Multi-Factor Authentication)**, kullanıcının kimliğini doğrulamak için birden fazla bağımsız doğrulama faktörünün kullanılmasıdır.

Temel fikir:

> **Tek bir doğrulama yöntemine güvenmek yerine birden fazla faktör kullanmak.**

Kimlik doğrulama faktörleri genellikle üç ana grupta düşünülür:

### 1. Bildiğin bir şey

Örneğin:

* Şifre
* PIN

### 2. Sahip olduğun bir şey

Örneğin:

* Telefon
* Güvenlik anahtarı
* Doğrulama cihazı

### 3. Olduğun bir şey

Örneğin:

* Parmak izi
* Yüz tanıma

Örneğin:

```text id="9p2n7x"
Şifre
  +
Telefon üzerindeki doğrulama
  ↓
MFA
```

Burada saldırgan yalnızca şifreyi ele geçirse bile ikinci faktör olmadan hesabın kontrolünü ele geçirmesi zorlaşabilir.

> **Kısaca:** MFA = Birden fazla farklı doğrulama faktörü kullanmak.

---

# 7. 2FA Nedir?

**2FA (Two-Factor Authentication)**, iki farklı kimlik doğrulama faktörünün kullanılmasıdır.

Yani:

> **2FA, MFA'nın bir türüdür.**

Örneğin:

```text id="p9kgf0"
Şifre
  +
Güvenlik anahtarı
  ↓
2FA
```

Burada iki farklı faktör kullanılır.

### Önemli fark

```text id="8d2t5c"
MFA
↓
2 veya daha fazla faktör

2FA
↓
Tam olarak 2 faktör
```

Bu nedenle:

> **Her 2FA MFA'dır ancak her MFA 2FA olmak zorunda değildir.**

---

# 8. Password Policy Nedir?

**Password Policy (Parola Politikası)**, kullanıcıların oluşturduğu ve kullandığı parolalarla ilgili güvenlik kurallarını belirleyen politikalardır.

Bir kurum örneğin:

* Minimum parola uzunluğu
* Yaygın/parolası kolay tahmin edilen şifrelerin engellenmesi
* Eski veya ele geçirilmiş parolaların tekrar kullanılmasının engellenmesi
* Hesap kilitleme veya oturum açma denemelerine yönelik kontroller
* MFA kullanımı

gibi kurallar belirleyebilir.

Amaç kullanıcı hesaplarının zayıf parolalar nedeniyle kolayca ele geçirilmesini zorlaştırmaktır.

Örneğin:

```text id="zj5jpo"
123456
password
qwerty
```

gibi kolay tahmin edilebilecek parolalar güvenli bir parola politikası açısından uygun değildir.

> **Kısaca:** Password Policy = Güvenli parola kullanımını belirleyen kurallar.

---

# 9. Least Privilege Nedir?

**Least Privilege (En Az Ayrıcalık İlkesi)**, bir kullanıcının veya sistem bileşeninin görevini yerine getirebilmesi için ihtiyaç duyduğu **minimum yetkiye** sahip olması ilkesidir.

Örneğin bir çalışan sadece kendi görevini yapmak için gerekli dosyalara erişebiliyorsa, şirketin tüm dosyalarına erişim verilmesine gerek yoktur.

```text id="o0y4j6"
Gereken yetki
     ↓
Minimum yetki
     ↓
Least Privilege
```

Örneğin:

```text id="k4br4m"
Muhasebe çalışanı
       ↓
Muhasebe dosyaları → ✅
İK dosyaları       → ❌
Sunucu yönetimi    → ❌
```

Bu yaklaşım, bir hesabın ele geçirilmesi durumunda saldırganın erişebileceği alanı sınırlandırmaya yardımcı olur.

> **Kısaca:** Least Privilege = Gerektiği kadar yetki ver.

---

# 10. Dört Kavramı Birlikte Düşünmek

Bu kavramlar birbirinden bağımsız değildir.

Örneğin kurumsal bir sistemde:

```text id="a6p3j7"
                 Kullanıcı
                     ↓
             Authentication
                     ↓
              Kimlik doğrulama
                     ↓
              Authorization
                     ↓
              Yetki kontrolü
                     ↓
              Erişim sağlanır
```

Bu yapıya ek güvenlik katmanları da eklenebilir:

```text id="z7s6q1"
Authentication
      ↓
     MFA
      ↓
Authorization
      ↓
Least Privilege
      ↓
Kaynağa erişim
```

Password Policy ise özellikle authentication aşamasındaki hesap güvenliğini destekleyen kurallardan biridir.

---

# 🔐 Gerçek Hayattan Bir Örnek

Bir şirket sistemini düşünelim.

Zeynep sisteme giriş yapıyor:

```text id="p8a3h2"
Kullanıcı adı
      +
Şifre
      ↓
Authentication
```

Şirket ayrıca MFA kullanıyor:

```text id="w7i1cc"
Şifre
  +
Telefon / güvenlik anahtarı
  ↓
MFA
```

Kimliği doğrulandıktan sonra sistem kullanıcının rolünü kontrol ediyor:

```text id="3f7k7y"
Zeynep → Çalışan
```

Authorization sonucunda:

```text id="m5t9e1"
Kendi belgeleri      → ✅
Departman belgeleri  → ✅
Yönetici paneli      → ❌
Sunucu yönetimi      → ❌
```

Bu da **Least Privilege** ilkesine uygun bir yaklaşım olabilir.

---

# 🧠 Sık Karıştırılan Nokta

Şunu özellikle aklımda tutmalıyım:

```text id="h5x8fk"
Authentication
= Kim olduğunu doğrula

Authorization
= Ne yapabileceğini belirle
```

Örneğin:

> "Kullanıcı doğru şifreyle giriş yaptı."

Bu **Authentication** ile ilgilidir.

> "Kullanıcı yönetici paneline girebilir mi?"

Bu **Authorization** ile ilgilidir.

---

# ✍️ Kendi Öğrenme Notlarım

Bu konuda öğrendiğim en önemli ayrım:

> **Authentication ve Authorization aynı şey değildir.**

Authentication sırasında sistem kullanıcının kim olduğunu doğrular.

Authorization sırasında ise doğrulanmış kullanıcının hangi kaynaklara ve işlemlere erişebileceği belirlenir.

MFA ve 2FA kimlik doğrulama güvenliğini güçlendirmeye yardımcı olur.

Password Policy kullanıcı hesaplarının parola güvenliğini destekler.

Least Privilege ise kullanıcıların gereğinden fazla yetkiye sahip olmasını önlemeye yönelik bir güvenlik ilkesidir.

Benim için kısa hali:

```text id="7e8j4q"
Authentication → Kimsin?
MFA            → Bunu daha güçlü nasıl doğrularım?
Authorization  → Neye erişebilirsin?
Least Privilege→ Ne kadar yetkiye gerçekten ihtiyacın var?
Password Policy→ Hesap/parola güvenliğini nasıl destekleriz?
```

---

# 🎯 Kısa Özet

* **Authentication** → Kimlik doğrulama
* **Authorization** → Yetkilendirme
* **MFA** → Birden fazla doğrulama faktörü kullanılması
* **2FA** → Tam olarak iki farklı doğrulama faktörü
* **Password Policy** → Parola kullanımına ilişkin güvenlik kuralları
* **Least Privilege** → Gerektiği kadar yetki verme

En önemli cümle:

> 🔐 **Bir kullanıcının sisteme giriş yapabilmesi, sistemdeki her kaynağa erişebileceği anlamına gelmez.**

Örneğin:

```text id="0xwq6q"
Şifre doğru
     ↓
Authentication ✅
     ↓
Kullanıcı sisteme girdi
     ↓
Yönetici sayfası?
     ↓
Authorization ❌
     ↓
Erişim reddedildi
```

Bu ayrım ileride **web güvenliği, SOC, IAM (Identity and Access Management) ve hesap güvenliği** konularını öğrenirken sürekli karşına çıkacak.

