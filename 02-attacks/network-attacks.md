# 🌐 Ağ Saldırıları

Bu bölümde ağlar ve ağ üzerinden gerçekleşebilecek temel saldırı türlerini araştırdım.

Saldırı türlerini ezberlemek yerine, saldırının **neyi hedeflediğini ve nasıl bir mantığa sahip olduğunu** anlamaya çalıştım.

---
<img width="678" height="452" alt="image" src="https://github.com/user-attachments/assets/af2e9e84-9473-414c-b653-aeba27b1ac64" />

## 1. DoS — Denial of Service

**DoS (Denial of Service)**, bir sistemin veya hizmetin normal kullanıcılar tarafından kullanılamaz hale gelmesini amaçlayan saldırıdır.

Saldırgan sistemi aşırı istek veya işlemlerle meşgul etmeye çalışır.

### Basitçe:

> **DoS = Sistemi kullanılamaz hale getirmeye çalışma.**

En çok etkilediği CIA bileşeni:

**Availability (Erişilebilirlik)**

---

## 2. DDoS — Distributed Denial of Service

**DDoS**, DoS saldırısının birçok farklı kaynaktan gerçekleştirilmesidir.

Saldırı çok sayıda cihazdan geldiği için saldırıyı tek bir kaynağa bağlamak daha zor olabilir.

Bu cihazlar bazen saldırganın kontrol ettiği bir **botnet** içerisinde bulunabilir.

### DoS ve DDoS farkı:

* **DoS:** Daha sınırlı/tekil kaynaklardan gelen saldırı
* **DDoS:** Dağıtılmış birçok kaynaktan gelen saldırı

### Akılda tut:

> **DDoS = Dağıtılmış saldırıyla hizmeti kullanılamaz hale getirme.**

En çok etkilediği CIA bileşeni:

**Availability**

---

## 3. Brute Force

**Brute Force**, bir hesabın parolasını bulmak amacıyla çok sayıda parola kombinasyonunun denenmesidir.

Örneğin saldırgan tek bir hesap üzerinde birçok farklı parola deneyebilir.

```text
Hesap
  ↓
123456
password
qwerty
...
```

### Akılda tut:

> **Brute Force = Bir hesapta çok sayıda parola denemek.**

---

## 4. Password Spraying

**Password Spraying**, az sayıda yaygın parolanın çok sayıda kullanıcı hesabında denenmesidir.

Örneğin:

```text
Kullanıcı 1 → yaygın parola
Kullanıcı 2 → yaygın parola
Kullanıcı 3 → yaygın parola
Kullanıcı 4 → yaygın parola
```

### Brute Force ile farkı:

**Brute Force:**

> 1 hesap + çok parola

**Password Spraying:**

> Çok hesap + az sayıda yaygın parola

### Akılda tut:

> **Password Spraying = Aynı parolayı birçok hesaba yaymak.**

---

## 5. MITM — Man-in-the-Middle

**MITM (Man-in-the-Middle)** saldırısında saldırgan, iki taraf arasındaki iletişimin arasına girmeye çalışır.

Normal iletişim:

```text
Kullanıcı ↔ Sunucu
```

MITM durumunda:

```text
Kullanıcı ↔ Saldırgan ↔ Sunucu
```

Saldırgan iletişimi izlemeye veya değiştirmeye çalışabilir.

Bu nedenle:

* Confidentiality (Gizlilik)
* Integrity (Bütünlük)

etkilenebilir.

### Akılda tut:

> **MITM = İletişimin arasına gir.**

---

## 6. Sniffing

**Sniffing**, ağ üzerinden geçen veri trafiğinin yakalanması ve incelenmesidir.

Saldırgan ağ trafiğini izlemeye çalışabilir.

Özellikle yeterince korunmayan iletişimlerde hassas bilgilerin açığa çıkması riski oluşabilir.

### MITM ile farkı:

**Sniffing:**

> Trafiği yakalama/izleme

**MITM:**

> İletişimin arasına girme

MITM sırasında sniffing yapılabilir ancak iki kavram aynı değildir.

### Akılda tut:

> **Sniffing = Trafiği izle.**

---

## 7. Spoofing

**Spoofing**, saldırganın kendisini başka bir kaynak veya kimlik gibi göstermeye çalışmasıdır.

Farklı türleri olabilir:

* IP Spoofing
* DNS Spoofing
* Email Spoofing
* MAC Spoofing

Örneğin saldırgan sahte bir e-posta adresi kullanarak güvenilir bir kişiden mesaj geliyormuş gibi görünmeye çalışabilir.

### Akılda tut:

> **Spoofing = Taklit et.**

---

# 🧠 Saldırıları Kolay Hatırlama

Ağ saldırılarını ilk öğrenirken şu kısa kelimeler benim için daha anlaşılır oldu:

| Saldırı           | Kısa çağrışım     |
| ----------------- | ----------------- |
| DoS               | 💥 Düşür          |
| DDoS              | 🌐 Dağıt          |
| Brute Force       | 🔑 Çok dene       |
| Password Spraying | 🔑 Çok hesaba yay |
| MITM              | 🧍 Araya gir      |
| Sniffing          | 👀 İzle           |
| Spoofing          | 🎭 Taklit et      |

---

# 🔐 CIA Triad ile İlişkisi

Saldırıların güvenlik açısından hangi alanı etkileyebileceğini düşünmek de önemlidir.

| Saldırı           | Etkilenebilecek alan        |
| ----------------- | --------------------------- |
| DoS               | Availability                |
| DDoS              | Availability                |
| Brute Force       | Confidentiality             |
| Password Spraying | Confidentiality             |
| MITM              | Confidentiality / Integrity |
| Sniffing          | Confidentiality             |
| Spoofing          | Confidentiality / Integrity |

Bu tablo saldırıların yalnızca tek bir CIA bileşenini etkileyebileceği anlamına gelmez. Etki, saldırının nasıl gerçekleştirildiğine ve sonucuna göre değişebilir.

---

# 🧠 Kendi Öğrenme Notlarım

Bu bölümde benim için en önemli ayrım **Brute Force ve Password Spraying** arasındaki fark oldu.

* Brute Force → bir hesap üzerinde çok sayıda parola denenmesi
* Password Spraying → az sayıda yaygın parolanın birçok hesapta denenmesi

Ayrıca:

* Sniffing → trafiği izlemek
* MITM → iletişimin arasına girmek
* Spoofing → başka bir kaynakmış gibi görünmek

şeklinde düşünebilirim.

DoS ve DDoS ise temel olarak hizmetin erişilebilirliğini etkilemeye yönelik saldırılardır.

---

# 🎯 Kısa Özet

```text
AĞ SALDIRILARI

DoS
→ Sistemi kullanılamaz hale getirmeye çalışma

DDoS
→ Dağıtılmış kaynaklarla sistemi kullanılamaz hale getirme

Brute Force
→ Bir hesapta çok sayıda parola deneme

Password Spraying
→ Aynı/yaygın parolaları birçok hesapta deneme

MITM
→ İletişimin arasına girme

Sniffing
→ Ağ trafiğini izleme

Spoofing
→ Kimlik/kaynak taklidi yapma
```

---

# 📚 Kaynaklar

* NIST — Cybersecurity Glossary
* CISA — Cybersecurity Resources
* NIST — Computer Security Resource Center

Bu notlar, temel kavramları öğrenmek amacıyla kendi ifadelerimle hazırlanmıştır.
