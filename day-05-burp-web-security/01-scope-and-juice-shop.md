# 🛡️ Day 5 — Web Application Security Testing

## 1. Yetkilendirme ve Laboratuvar Kapsamı

Bugünkü çalışmada web uygulaması güvenlik testi metodolojisini uygulamalı olarak öğrenmeye başladım.

Test sırasında ilk dikkat etmem gereken konunun teknik bir araçtan önce **yetkilendirme ve kapsam (scope)** olduğunu öğrendim.

Bir sistemi güvenlik açısından test edebilmek için sistem sahibinden izin alınmış olması gerekir. Profesyonel bir güvenlik testinde hangi sistemlerin, uygulamaların, IP adreslerinin, API'lerin veya özelliklerin test edileceği önceden belirlenir.

Bu çalışmada yalnızca eğitim amacıyla kullanılan laboratuvar ortamını test ediyorum:

```text
Kali Linux
    ↓
Burp Suite
    ↓
OWASP Juice Shop
```

Gerçek web siteleri, şirket sistemleri, kamu sistemleri veya izinsiz başka uygulamalar test kapsamımın dışındadır.

---

## 2. OWASP Juice Shop

OWASP Juice Shop, web uygulama güvenliğini öğrenmek amacıyla hazırlanmış ve içerisinde kasıtlı olarak çeşitli güvenlik açıkları bulunan bir eğitim uygulamasıdır.

Gerçek bir uygulamadan farklı olarak burada güvenlik açıkları eğitim amacıyla oluşturulmuştur. Bu nedenle güvenlik testlerini güvenli ve kontrollü bir laboratuvar ortamında gerçekleştirebilirim.

Juice Shop üzerinden;

* Authentication
* Authorization
* Broken Access Control
* SQL Injection
* XSS
* Information Disclosure
* Security Misconfiguration
* API Security
* JWT ve Session

gibi web güvenliği konularını uygulamalı olarak inceleyebilirim.

---

## 3. Temel Kavramlar

### CTF

CTF (Capture The Flag), siber güvenlikte belirli güvenlik problemlerinin çözülmesine dayanan yarışma ve eğitim formatıdır.

Juice Shop içerisinde de challenge mantığıyla çeşitli güvenlik problemleri bulunmaktadır.

### Challenge

Challenge, çözülmesi beklenen güvenlik görevidir.

Challenge'ları sadece çözmek yerine, problemin neden oluştuğunu ve geliştirici tarafından nasıl düzeltilebileceğini anlamaya çalışıyorum.

### Vulnerability

Vulnerability, sistemde güvenliğin bozulmasına veya kötüye kullanılmasına neden olabilecek zayıflıktır.

### Exploit

Exploit, bir güvenlik açığından yararlanmak için kullanılan yöntem veya tekniktir.

### Proof of Concept (PoC)

PoC, tespit edilen güvenlik probleminin gerçekten mevcut olduğunu kontrollü bir şekilde göstermektir.

Amaç sisteme zarar vermek değil, bulguyu doğrulamaktır.

### Security Finding

Security Finding, güvenlik testi sırasında tespit edilen ve raporlanması gereken güvenlik bulgusudur.

Bir bulguda yalnızca "açık var" demek yerine problemin;

* nerede bulunduğunu,
* nasıl doğrulandığını,
* etkisini,
* riskini,
* nasıl düzeltilebileceğini

açıklamak gerekir.

---

## 4. Bugünkü Çalışmadan Çıkardığım Ana Fikir

Web güvenlik testini yalnızca araç kullanmak olarak görmemem gerektiğini öğrendim.

Temel yaklaşımım:

```text
Scope
  ↓
Keşif
  ↓
Uygulamayı Anlama
  ↓
Test
  ↓
Doğrulama
  ↓
Risk Analizi
  ↓
Raporlama
  ↓
Remediation
```

olmalı.

Özellikle profesyonel bir pentest çalışmasında amaç sadece açık bulmak değil, bulunan problemin teknik ve iş açısından ne anlama geldiğini anlayıp geliştiriciye düzeltilebilir bir güvenlik bulgusu sunmaktır.
