# 15. TLS Traffic Analysis

## 1. TLS Nedir?

TLS (Transport Layer Security), ağ üzerinden iletişim sırasında verilerin güvenli şekilde iletilmesini sağlayan bir kriptografik protokoldür.

Özellikle HTTPS bağlantılarında kullanılır. TLS sayesinde istemci ile sunucu arasındaki iletişimde gizlilik, bütünlük ve sunucu kimlik doğrulaması sağlanır.

TLS kullanılmadığında ağ üzerindeki trafik daha kolay okunabilirken, TLS kullanıldığında uygulama verileri şifrelenir.

---

## 2. TLS Handshake Nedir?

TLS Handshake, istemci ile sunucu arasında güvenli bağlantının kurulması için gerçekleştirilen iletişim sürecidir.

Handshake sırasında taraflar:

* TLS sürümünü belirler.
* Kullanılacak kriptografik algoritmaları belirler.
* Anahtar değişimi için gerekli bilgileri paylaşır.
* Sunucunun kimliğinin doğrulanmasına yardımcı olan sertifikayı iletir.
* Daha sonra kullanılacak güvenli iletişim için gerekli anahtar materyalini oluşturur.

Basit olarak:

```text
Client                         Server
  │                              │
  │──── Client Hello ──────────>│
  │                              │
  │<──── Server Hello ──────────│
  │                              │
  │<──── Certificate ───────────│
  │                              │
  │<──── Key Exchange ──────────│
  │                              │
  │──── Handshake devamı ──────>│
  │                              │
  │════ Şifreli iletişim ══════>│
```

TLS sürümüne göre handshake mesajlarının yapısı ve sırası değişebilir.

---

## 3. Client Hello

Wireshark'ta aşağıdaki TLS paketini inceledim:

```text
Frame 725
10.0.3.15 → 34.107.243.93
TLSv1.3
Client Hello
SNI = push.services.mozilla.com
```

Client Hello, istemcinin TLS bağlantısını başlatırken sunucuya gönderdiği ilk önemli handshake mesajlarından biridir.

Bu mesaj içerisinde istemcinin desteklediği TLS sürümleri, cipher suite'ler ve anahtar değişimi için gerekli bazı bilgiler bulunabilir.

İncelediğim pakette ayrıca:

```text
SNI = push.services.mozilla.com
```

bilgisini gördüm.

SNI (Server Name Indication), istemcinin bağlanmak istediği sunucu adını TLS handshake sırasında belirtmesini sağlar.

---

## 4. Server Hello

Client Hello paketinden sonra sunucudan Server Hello mesajını gözlemledim.

```text
Frame 727
34.107.243.93 → 10.0.3.15
TLSv1.3
Server Hello
```

Server Hello, sunucunun TLS bağlantısı için kullanılacak bazı parametreleri seçtiğini gösterir.

İncelediğim pakette:

```text
Cipher Suite:
TLS_AES_128_GCM_SHA256
```

bilgisini gördüm.

Ayrıca:

```text
Extension: key_share
x25519
```

ve:

```text
Extension: supported_versions
TLS 1.3
```

bilgileri bulunuyordu.

Burada Wireshark'ın Record Layer bölümünde:

```text
Version: TLS 1.2 (0x0303)
```

görünmesine rağmen `supported_versions` alanında gerçek seçilen sürümün:

```text
TLS 1.3
```

olduğunu gördüm.

Bu nedenle Record Layer'daki `0x0303` değerinin tek başına bağlantının TLS 1.2 olduğunu göstermediğini öğrendim.

---

## 5. Cipher Suite

Server Hello paketinde:

```text
TLS_AES_128_GCM_SHA256
```

cipher suite'ini gördüm.

Cipher suite, TLS bağlantısında kullanılacak kriptografik algoritma takımını ifade eder.

Burada:

* AES-128-GCM şifreleme ve bütünlük korumasında kullanılır.
* SHA-256 hash algoritmasıdır.

Cipher suite doğrudan şifreleme anahtarının kendisi değildir. Kullanılacak kriptografik yöntemleri belirtir.

---

## 6. Key Share

Server Hello içerisinde:

```text
Extension: key_share
x25519
```

bilgisini gördüm.

Key Share, TLS bağlantısında güvenli anahtar değişiminin gerçekleştirilmesine yardımcı olan bilgiyi taşır.

X25519, anahtar değişiminde kullanılan bir eliptik eğri Diffie-Hellman yöntemidir.

Burada önemli olan nokta, ortak gizli değerin ağ üzerinden açık şekilde gönderilmemesidir.

---

## 7. Certificate Nedir?

TLS bağlantılarında sunucunun kimliğinin doğrulanmasına yardımcı olmak için dijital sertifikalar kullanılır.

Wireshark'ta TLS 1.2 kullanan başka bir bağlantıyı da inceledim.

```text
Frame 470
34.149.226.178 → 10.0.3.15
TLSv1.2
Certificate
Server Key Exchange
Server Hello Done
```

Certificate mesajının içerisinde birden fazla sertifika bulunduğunu gördüm.

```text
Certificates Length: 4100
Certificate Length: 1317
Certificate Length: 1246
Certificate Length: 1528
```

Bu yapı bir certificate chain yani sertifika zinciridir.

---

## 8. İncelediğim Sertifika

İlk sertifikanın bilgilerinde:

```text
Subject:
content-signature-2.cdn.mozilla.net
```

bilgisini gördüm.

Sertifikanın issuer bilgisi:

```text
Common Name: YR2
Organization: Let's Encrypt
Country: US
```

şeklindeydi.

Sertifikanın geçerlilik bilgileri:

```text
Not Before:
2026-09-24 09:28:58 UTC

Not After:
2026-12-23 09:28:57 UTC
```

şeklindeydi.

Bu bilgilerden sertifikanın hangi sunucu adı için düzenlendiğini, kim tarafından imzalandığını ve geçerlilik tarihlerini inceleyebildim.

---

## 9. Public Key ve Private Key

Sertifika içerisinde:

```text
subjectPublicKeyInfo
algorithm: rsaEncryption
```

bilgisini gördüm.

Buradan sertifikada public key bilgisinin bulunabileceğini öğrendim.

Public key paylaşılabilirken private key gizli tutulur.

```text
Public Key
    ↓
Sertifikada bulunabilir

Private Key
    ↓
Sunucuda gizli tutulur
```

Private key ağ üzerinden istemciye gönderilmez.

---

## 10. Server Key Exchange

Frame 470 içerisinde:

```text
Handshake Type: Server Key Exchange
EC Diffie-Hellman Server Params
```

bilgisini gördüm.

Bu mesaj TLS 1.2 handshake'inde anahtar değişimi için kullanılan bilgileri içerir.

Burada Certificate ile Key Exchange'in aynı şey olmadığını öğrendim.

```text
Certificate
→ Sunucunun kimliğinin doğrulanmasına yardımcı olur.

Key Exchange
→ Güvenli oturum anahtarlarının oluşturulmasına yardımcı olur.
```

---

## 11. Server Hello Done

Frame 470 içerisinde:

```text
Handshake Type: Server Hello Done
Length: 0
```

mesajını da gördüm.

Bu mesaj TLS 1.2 handshake'inde sunucunun Server Hello aşamasını tamamladığını belirtir.

TLS 1.2 ve TLS 1.3 handshake yapılarının tamamen aynı olmadığını gerçek Wireshark trafiğinde gözlemledim.

---

## 12. TLS 1.2 ve TLS 1.3 Karşılaştırması

İncelediğim iki farklı bağlantıda farklı TLS handshake yapıları gördüm.

### TLS 1.3

```text
Client Hello
      ↓
Server Hello
      ↓
Key Share
      ↓
TLS handshake devamı
      ↓
Şifreli iletişim
```

### TLS 1.2

```text
Client Hello
      ↓
Server Hello
      ↓
Certificate
      ↓
Server Key Exchange
      ↓
Server Hello Done
      ↓
Handshake devamı
```

TLS 1.3'te handshake yapısının TLS 1.2'den farklı olduğunu gözlemledim.

---

## 13. HTTPS Trafiğinde Hangi Bilgiler Görülebilir?

HTTPS kullanıldığında bütün ağ trafiğinin görünmez olmadığını öğrendim.

Wireshark üzerinde aşağıdaki bilgiler görülebilir:

* Kaynak IP adresi
* Hedef IP adresi
* Kaynak port
* Hedef port
* TCP bağlantısı
* Paket boyutları
* Paket zamanları
* TLS sürümü
* Cipher suite gibi bazı TLS bilgileri
* TLS handshake bilgileri
* SNI gibi bazı bilgiler

SNI'nin her durumda görünür olmadığını da öğrendim. Örneğin Encrypted ClientHello (ECH) kullanılması durumunda SNI gizlenebilir.

---

## 14. Hangi Bilgiler Şifrelenmiş Kalır?

HTTPS üzerinden normal bir bağlantıda TLS kurulduktan sonra uygulama verilerinin içeriği şifrelenir.

Örneğin:

```text
GET /...
Cookie: ...
Authorization: ...
HTTP Body
```

gibi HTTP uygulama verileri normalde ağ üzerinde açık şekilde okunamaz.

Bu nedenle Wireshark ile IP adreslerini ve bağlantı özelliklerini görebilsem de HTTPS kullanıldığında HTTP içeriğini doğrudan okuyamam.

---

## 15. HTTPS Kullanılması Ağ Analizini Tamamen İmkânsız Hale Getirir mi?

Hayır.

TLS şifrelemesi uygulama verilerinin içeriğini korur fakat ağ üzerinde belirli miktarda metadata görünmeye devam eder.

Örneğin:

```text
IP
 ↓
TCP
 ↓
TLS
 ↓
Encrypted Application Data
```

şeklindeki iletişim yapısını Wireshark üzerinden inceleyebilirim.

Bu nedenle HTTPS kullanan bir bağlantıda da:

* Hangi IP'lerle iletişim kurulduğu
* Hangi portların kullanıldığı
* Trafiğin ne zaman gerçekleştiği
* Paketlerin yaklaşık boyutları
* TLS handshake bilgileri
* TLS sürümü
* Bazı sertifika bilgileri
* Bazı durumlarda SNI

gibi bilgiler analiz edilebilir.

Ancak uygulama katmanındaki hassas içerikler TLS tarafından şifrelenir.

---

## 16. Yaptığım Çalışmadan Çıkardığım Sonuç

Bu çalışmada Wireshark kullanarak TLS trafiğini inceledim.

Özellikle:

* Client Hello
* Server Hello
* TLS 1.3
* Cipher Suite
* Key Share
* Certificate
* Certificate Chain
* Server Key Exchange
* Server Hello Done

mesajlarını gerçek ağ trafiği üzerinde gözlemledim.

Ayrıca TLS 1.2 ve TLS 1.3 handshake yapılarının farklı olduğunu gördüm.

HTTPS'in ağ analizini tamamen ortadan kaldırmadığını, ancak uygulama verilerinin gizliliğini sağlamak için önemli bir koruma sunduğunu öğrendim.

En önemli olarak, **ağ analizi ile uygulama içeriğinin okunmasının aynı şey olmadığını** anladım. Wireshark ile bağlantının metadata'sını analiz etmek mümkünken, TLS tarafından şifrelenen uygulama verileri normal şartlarda doğrudan okunamaz.
