# 🔐 Şifreleme Temelleri

Siber güvenlikte verilerin korunması için çeşitli kriptografik yöntemler kullanılır.

Bu çalışmada:

* Encryption
* Decryption
* Hash
* Salt
* Symmetric Encryption
* Asymmetric Encryption
* Digital Signature
* Certificate

kavramlarını inceledim.

Bu kavramların bazıları birbirine benzese de kullanım amaçları farklıdır.

En önemli ayrımlardan biri:

> **Encryption = Veriyi gizlemek ve gerektiğinde geri çözebilmek**
>
> **Hash = Veriden tek yönlü bir özet üretmek**

---

# 1. Encryption Nedir?

**Encryption (Şifreleme)**, okunabilir bir verinin belirli bir algoritma ve anahtar kullanılarak okunamaz bir forma dönüştürülmesidir.

Amaç, verinin yetkisiz kişiler tarafından okunmasını zorlaştırmaktır.

Örneğin:

```text
Açık veri
   ↓
Encryption
   ↓
Şifreli veri
```

Basitleştirilmiş örnek:

```text
"Merhaba"
    ↓
  Şifreleme
    ↓
"X7kP2..."
```

Şifrelenmiş verinin tekrar okunabilir hale getirilebilmesi için uygun anahtar ve algoritma gerekir.

> **Kısaca:** Encryption = Veriyi gizlemek için şifreleme.

---

# 2. Decryption Nedir?

**Decryption (Şifre Çözme)**, şifrelenmiş verinin uygun anahtar kullanılarak tekrar okunabilir hale getirilmesidir.

Akış:

```text
Açık veri
   ↓
Encryption
   ↓
Şifreli veri
   ↓
Decryption
   ↓
Açık veri
```

Örneğin:

```text
"Merhaba"
    ↓
Encryption
    ↓
Şifreli veri
    ↓
Decryption
    ↓
"Merhaba"
```

Encryption ile Decryption birbirinin tamamlayıcısıdır.

> **Kısaca:** Decryption = Şifrelenmiş veriyi tekrar çözmek.

---

# 3. Hash Nedir?

**Hash**, bir veriden belirli bir algoritma kullanılarak sabit uzunlukta bir çıktı elde edilmesidir.

Hash işlemi normalde **tek yönlü** olarak düşünülür.

Örneğin:

```text
"Merhaba"
    ↓
  Hash
    ↓
"8b1a9953..."
```

Aynı veri aynı hash algoritması ve koşullarıyla tekrar işlendiğinde aynı hash değeri elde edilir.

Ancak hash işlemi encryption gibi:

```text
Veri
 ↓
Hash
 ↓
Hash değeri
 ↓
???
 ↓
Orijinal veri
```

şeklinde geri çevrilecek bir işlem değildir.

Bu nedenle hash ile encryption aynı amaçla kullanılmaz.

Hash fonksiyonları:

* Veri bütünlüğünü kontrol etme
* Parola saklama
* Dosya doğrulama
* Dijital sistemlerde çeşitli doğrulama mekanizmaları

gibi alanlarda kullanılabilir.

> **Kısaca:** Hash = Veriden tek yönlü bir özet üretme.

---

# 4. Salt Nedir?

**Salt**, özellikle parola hashleme işlemlerinde kullanılan rastgele bir veridir.

Amaç, aynı parolayı kullanan farklı kullanıcıların aynı hash değerine sahip olmasını önlemek ve önceden hazırlanmış hash tablolarının etkisini azaltmaya yardımcı olmaktır.

Örneğin iki kullanıcı aynı parolayı kullansın:

```text
Kullanıcı 1 → "123456"
Kullanıcı 2 → "123456"
```

Salt kullanılmadan basit bir hash yaklaşımında:

```text
123456 → Aynı hash
```

elde edilebilir.

Salt kullanıldığında ise her parola için farklı rastgele değer kullanılabilir:

```text
Parola + Salt
     ↓
Password Hash
```

Örneğin:

```text
Kullanıcı 1:
123456 + Salt_A → Hash_A

Kullanıcı 2:
123456 + Salt_B → Hash_B
```

Böylece aynı parolaların aynı hash değerini üretmesi engellenmiş olur.

### Önemli nokta

Salt'ın gizli tutulması şart değildir.

Salt genellikle parola hash'i ile birlikte saklanabilir. Güvenlik açısından önemli olan salt'ın **benzersiz ve uygun şekilde rastgele üretilmesi** ve modern parola hashleme yöntemleriyle kullanılmasıdır.

> **Kısaca:** Salt = Parola hashleme işlemine eklenen benzersiz rastgele veri.

---

# 5. Symmetric Encryption Nedir?

**Symmetric Encryption (Simetrik Şifreleme)**, şifreleme ve şifre çözme işlemlerinde aynı gizli anahtarın kullanıldığı şifreleme türüdür.

Basitleştirilmiş olarak:

```text
             Aynı anahtar
                  ↓
Açık veri → Encryption → Şifreli veri
                            ↓
                         Decryption
                            ↓
                         Açık veri
```

Örneğin:

```text
Gizli Anahtar
     ↓
Veri → Şifreleme → Şifreli veri
                         ↓
                     Şifre çözme
                         ↓
                        Veri
```

Simetrik şifrelemede tarafların anahtarı güvenli şekilde paylaşması önemli bir konudur.

Yaygın simetrik şifreleme algoritmalarından biri **AES**'tir.

> **Kısaca:** Symmetric = Aynı gizli anahtar.

---

# 6. Asymmetric Encryption Nedir?

**Asymmetric Encryption (Asimetrik Şifreleme)**, birbiriyle ilişkili iki anahtarın kullanıldığı kriptografik sistemdir:

* Public Key
* Private Key

Basitleştirilmiş olarak:

```text
Public Key
     ↓
Şifreleme
     ↓
Şifreli veri
     ↓
Private Key
     ↓
Şifre çözme
```

**Public key** başkalarıyla paylaşılabilir.

**Private key** ise gizli tutulmalıdır.

Asimetrik kriptografi:

* Güvenli iletişim
* Anahtar değişimi
* Dijital imza
* Kimlik doğrulama

gibi alanlarda kullanılabilir.

Örneğin RSA ve ECC, asimetrik kriptografiyle ilişkili yaygın algoritma aileleridir.

> **Kısaca:** Asymmetric = Public key + Private key.

---

# 7. Symmetric ve Asymmetric Arasındaki Fark

| Symmetric                                       | Asymmetric                                                         |
| ----------------------------------------------- | ------------------------------------------------------------------ |
| Aynı gizli anahtar kullanılır                   | Public + Private key kullanılır                                    |
| Genellikle daha hızlıdır                        | Genellikle daha fazla hesaplama maliyetine sahiptir                |
| Büyük miktarda veriyi şifrelemede kullanışlıdır | Kimlik doğrulama ve anahtar değişimi gibi işlemlerde kullanışlıdır |
| Anahtar paylaşımı önemli bir problemdir         | Public key paylaşılabilir                                          |
| Örnek: AES                                      | Örnek: RSA, ECC                                                    |

Gerçek sistemlerde bu iki yaklaşım birlikte de kullanılabilir.

Örneğin güvenli iletişim protokollerinde asimetrik kriptografi, oturum anahtarlarının güvenli şekilde oluşturulmasına yardımcı olurken büyük veri trafiği simetrik şifreleme ile korunabilir.

---

# 8. Digital Signature Nedir?

**Digital Signature (Dijital İmza)**, dijital bir verinin veya mesajın kim tarafından imzalandığını doğrulamaya ve verinin imzalandıktan sonra değiştirilip değiştirilmediğini kontrol etmeye yardımcı olan kriptografik mekanizmadır.

Dijital imzanın temel olarak iki önemli amacı vardır:

### 1. Bütünlük

Mesajın imzalandıktan sonra değiştirilip değiştirilmediğinin kontrol edilmesine yardımcı olur.

### 2. Kimlik doğrulama

İmzanın ilgili private key'in sahibi tarafından oluşturulduğunun doğrulanmasına yardımcı olur.

Basitleştirilmiş olarak:

```text
Mesaj
  ↓
Hash
  ↓
Hash değeri
  ↓
Private Key ile imzalama
  ↓
Digital Signature
```

Alıcı tarafında ise imza doğrulanır.

```text
Mesaj + Digital Signature
          ↓
      Verification
          ↓
      Geçerli mi?
```

> **Kısaca:** Digital Signature = Kimlik doğrulama + bütünlük kontrolüne yardımcı olan kriptografik imza.

### Önemli ayrım

Dijital imza temel olarak:

> **"Bu veriyi kim imzaladı ve veri sonradan değişti mi?"**

sorusuna yardımcı olur.

Şifreleme ise:

> **"Bu veriyi yetkisiz kişiler okuyamasın."**

amacına yöneliktir.

Yani dijital imza ile encryption aynı şey değildir.

---

# 9. Certificate Nedir?

**Certificate (Dijital Sertifika)**, bir açık anahtarın belirli bir kimlik veya alan adıyla ilişkisini doğrulamaya yardımcı olan dijital belgedir.

Özellikle HTTPS bağlantılarında kullanılır.

Örneğin tarayıcı:

```text
https://example.com
```

adresine bağlandığında sunucu kendisini bir dijital sertifika ile tanıtabilir.

Sertifika içerisinde çeşitli bilgiler bulunabilir.

Örneğin:

* Alan adı
* Public key
* Sertifikayı düzenleyen kuruluş
* Geçerlilik bilgileri
* Dijital imza

Tarayıcı sertifikayı doğrulayarak sunucunun kimliği hakkında güven oluşturur.

Basitleştirilmiş yapı:

```text
Web Sunucusu
     ↓
Digital Certificate
     ↓
Domain + Public Key
     ↓
Tarayıcı doğrulaması
     ↓
HTTPS bağlantısı
```

> **Kısaca:** Certificate = Bir public key'in belirli bir kimlik/alan adıyla ilişkilendirilmesini doğrulamaya yardımcı olan dijital belge.

---

# 🔥 Hash ile Encryption Arasındaki Temel Fark Nedir?

Bu konunun en önemli sorularından biridir.

## Encryption

Encryption'ın amacı veriyi gizlemektir.

```text
Açık veri
   ↓
Encryption
   ↓
Şifreli veri
   ↓
Decryption
   ↓
Açık veri
```

Uygun anahtar mevcutsa veri tekrar çözülebilir.

---

## Hash

Hash'in amacı verinin kendisini geri elde etmek değil, veriden bir özet oluşturmaktır.

```text
Veri
 ↓
Hash
 ↓
Hash değeri
```

Hash çıktısından orijinal veriyi normal bir "decrypt" işlemiyle geri elde etmek mümkün değildir.

Bu nedenle:

> **Encryption geri çözülebilen bir dönüşümken, hash tek yönlü bir özetleme işlemidir.**

---

# 🆚 Hash ve Encryption Karşılaştırması

| Özellik                       | Encryption                | Hash                                  |
| ----------------------------- | ------------------------- | ------------------------------------- |
| Temel amaç                    | Gizlilik                  | Özetleme / doğrulama                  |
| Geri çevrilebilir mi?         | Evet, uygun anahtarla     | Normalde hayır                        |
| Anahtar kullanımı             | Evet                      | Normal hash fonksiyonlarında hayır    |
| Kullanım alanı                | Gizli veri saklama/iletme | Parola saklama, bütünlük kontrolü vb. |
| Örnek                         | AES                       | SHA-256                               |
| Orijinal veri elde edilir mi? | Uygun anahtarla evet      | Normalde hayır                        |

---

# 🔑 Parolalar Neden Düz Metin Olarak Saklanmamalıdır?

Bir sistemin kullanıcı parolalarını doğrudan veritabanında saklaması ciddi bir güvenlik problemidir.

Örneğin kötü bir tasarım:

```text
Kullanıcı
   ↓
Parola
   ↓
Veritabanı

zeynep → 123456
ahmet  → qwerty
```

Veritabanına yetkisiz erişim gerçekleşirse saldırgan kullanıcıların parolalarını doğrudan görebilir.

Bu parolalar başka sistemlerde de kullanılıyorsa saldırgan başka hesaplara erişmeyi deneyebilir.

---

# ❌ Düz Metin Parola Saklamanın Problemi

Bir saldırgan veritabanını ele geçirirse:

```text
Veritabanı
     ↓
Kullanıcı adı
     +
Düz metin parola
     ↓
Saldırgan parolayı doğrudan öğrenebilir
```

Bu nedenle parolaların düz metin olarak saklanmaması gerekir.

---

# 🔐 Parolalar Nasıl Saklanmalıdır?

Parolalar için uygun bir **password hashing** yöntemi kullanılmalıdır.

Genel mantık:

```text
Kullanıcının parolası
        ↓
      Salt
        ↓
Password Hashing
        ↓
Password Hash
        ↓
Veritabanı
```

Kullanıcı daha sonra giriş yaptığında:

```text
Girilen parola
      ↓
Aynı password hashing işlemi
      ↓
Oluşan değer
      ↓
Veritabanındaki değerle karşılaştırma
```

yapılır.

Sistem doğru parolayı saklanan düz metinden okumak yerine doğrulama işlemi gerçekleştirir.

---

# ⚠️ Sadece "SHA-256 ile Hashledim" Yeterli mi?

Burada önemli bir ayrıntı vardır.

Parolalar için yalnızca hızlı bir genel amaçlı hash fonksiyonunu kullanmak iyi bir parola saklama yaklaşımı değildir.

Örneğin:

```text
SHA-256(password)
```

gibi basit bir yapı, parola saklamak için modern password hashing yöntemlerinin yerini tutmaz.

Çünkü parola hashleme işlemlerinin saldırganlar açısından maliyetli olması istenir.

Bu amaçla:

* Argon2
* bcrypt
* scrypt
* PBKDF2

gibi parola hashleme algoritmaları kullanılabilir.

Bu yöntemler parola doğrulamasını saldırgan açısından daha maliyetli hale getirecek şekilde tasarlanmıştır.

> **Önemli:** Parolalar için uygun password hashing + benzersiz salt kullanılmalıdır.

---

# 🧂 Salt Neden Önemli?

Örneğin iki kullanıcının aynı parolayı kullandığını düşünelim:

```text
Kullanıcı A → "123456"
Kullanıcı B → "123456"
```

Salt kullanılmadan basit bir hashleme yapılırsa:

```text
A → Hash X
B → Hash X
```

olabilir.

Salt kullanıldığında:

```text
A:
123456 + Salt A → Hash A

B:
123456 + Salt B → Hash B
```

şeklinde farklı sonuçlar oluşur.

Bu, aynı parolaların aynı hash değerine sahip olmasını engellemeye yardımcı olur.

Ayrıca saldırganların önceden hesaplanmış hash tablolarını kullanmasını zorlaştırır.

---

# 🔐 Encryption ve Password Hashing Neden Farklı?

Bu ayrım özellikle önemlidir.

Bir mesajın tekrar okunması gerekiyorsa:

```text
Encryption
    ↓
Şifreli veri
    ↓
Decryption
    ↓
Orijinal veri
```

kullanılabilir.

Ama parola için sistemin normalde:

> "Kullanıcının eski parolasını geri getir."

gibi bir ihtiyacı yoktur.

Sistem sadece:

> "Kullanıcının girdiği parola doğru mu?"

sorusunu cevaplamalıdır.

Bu nedenle parola saklama için password hashing kullanılır.

---

# 🌐 Gerçek Hayattan Örnek: HTTPS

HTTPS kullanırken encryption ve sertifikalar birlikte karşımıza çıkabilir.

Basitleştirilmiş şekilde:

```text
Tarayıcı
   ↓
HTTPS bağlantısı
   ↓
Sertifika doğrulaması
   ↓
Güvenli bağlantının kurulması
   ↓
Şifreli veri iletişimi
```

Burada:

* **Certificate** → Sunucunun kimliğini doğrulamaya yardımcı olur.
* **Asymmetric cryptography** → Güvenli bağlantının kurulmasında rol oynayabilir.
* **Symmetric encryption** → Veri iletişiminin korunmasında kullanılabilir.
* **Hash** → Bütünlük ve çeşitli kriptografik işlemlerde kullanılabilir.
* **Digital signature** → Kimlik ve bütünlük doğrulamasında rol oynayabilir.

Bu nedenle bu kavramların hepsi birbirinden bağımsız değil, gerçek güvenlik sistemlerinde birlikte kullanılabilir.

---

# 🧠 Kavramları Birbirinden Ayırmak

```text
Encryption
→ Veriyi gizle
→ Geri çözülebilir

Decryption
→ Şifreli veriyi çöz

Hash
→ Veriden tek yönlü özet üret

Salt
→ Password hashing işlemine benzersiz rastgele veri ekle

Symmetric
→ Aynı gizli anahtar

Asymmetric
→ Public Key + Private Key

Digital Signature
→ İmza / bütünlük / kimlik doğrulama

Certificate
→ Public Key'i bir kimlik/alan adıyla ilişkilendirmeye yardımcı olur
```

---

# ✍️ Kendi Öğrenme Notlarım

Bu konuda benim için en önemli ayrım:

> **Encryption ile Hash aynı şey değildir.**

Encryption'da amaç veriyi gizlemektir ve uygun anahtarla veri tekrar çözülebilir.

Hash işleminde ise veriden tek yönlü bir özet oluşturulur.

Parolalar düz metin olarak saklanmamalıdır. Çünkü veritabanı ele geçirilirse kullanıcıların gerçek parolaları doğrudan ortaya çıkabilir.

Parola saklamak için uygun password hashing algoritmaları ve benzersiz salt kullanılmalıdır.

Ayrıca:

```text
Authentication
      ↓
"Bu kullanıcı kim?"
      ↓
Password Verification
      ↓
Password Hash + Salt
```

şeklinde bir bağlantı kurabileceğimi öğrendim.

---

# 🎯 Kısa Özet

| Kavram            | Temel anlamı                                                       |
| ----------------- | ------------------------------------------------------------------ |
| Encryption        | Veriyi gizlemek için şifreleme                                     |
| Decryption        | Şifreli veriyi çözme                                               |
| Hash              | Veriden tek yönlü özet üretme                                      |
| Salt              | Password hashing işlemine eklenen benzersiz rastgele veri          |
| Symmetric         | Aynı gizli anahtar kullanılır                                      |
| Asymmetric        | Public + Private key kullanılır                                    |
| Digital Signature | Kimlik ve bütünlük doğrulamasına yardımcı olan dijital imza        |
| Certificate       | Public key'i kimlik/alan adıyla ilişkilendiren dijital belge       |
| Password Hashing  | Parolaları güvenli şekilde doğrulamak için özel hashleme yaklaşımı |

### En önemli iki cümle:

> 🔐 **Encryption veriyi gizler ve uygun anahtarla geri çözülebilir.**

> #️⃣ **Hash veriden tek yönlü bir özet üretir ve parola saklama gibi durumlarda uygun password hashing yöntemleri kullanılır.**

---

# 📚 Kaynaklar

* NIST — National Institute of Standards and Technology
* NIST Cryptographic Standards and Guidelines
* OWASP — Password Storage Cheat Sheet
* OWASP — Cryptographic Storage Cheat Sheet
* IETF — Internet Engineering Task Force
* MDN Web Docs — Web Security
