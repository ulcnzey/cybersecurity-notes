# 🛡️ Cybersecurity Notes

> **Siber güvenlik alanında temel bilgilerden profesyonel güvenlik çalışmalarına doğru ilerleyen kişisel öğrenme ve araştırma arşivim.**

Bu repository, **Adli Bilişim Mühendisliği** eğitimim ve siber güvenlik alanındaki kariyer hedeflerim doğrultusunda oluşturduğum kişisel çalışma alanıdır.

Burada siber güvenliğin temel kavramlarını yalnızca tanımlamak yerine, kavramların birbirleriyle olan ilişkilerini anlamaya ve öğrendiklerimi kendi cümlelerimle dokümante etmeye çalışıyorum.

Amacım zaman içerisinde bu repository'yi;

* 📚 Öğrenme notlarım
* 🔐 Siber güvenlik araştırmalarım
* 🌐 Ağ ve web güvenliği çalışmalarım
* 🛡️ SOC / Blue Team öğrenme sürecim
* 🔎 Güvenlik analizi çalışmalarım
* 🧪 İleride gerçekleştireceğim laboratuvar ve projeler

için düzenli ve sürdürülebilir bir teknik arşive dönüştürmek.

---

## 🎯 Bu Repository'nin Amacı

Siber güvenlik çok geniş bir alan olduğu için öğrenme sürecimi belirli bir sıraya göre ilerletiyorum.

Öncelikle temel kavramları öğreniyor, ardından ağ, web, güvenlik mekanizmaları, SOC ve güvenlik operasyonları gibi alanlara geçiyorum.

Öğrenme yaklaşımım:

```text
Temel Kavramlar
      ↓
Ağ Temelleri
      ↓
Siber Saldırılar
      ↓
Güvenlik Mekanizmaları
      ↓
Web Güvenliği
      ↓
SOC & Blue Team
      ↓
Güvenlik Ekipleri
      ↓
Uzmanlık Alanları
      ↓
Zafiyetler
      ↓
Etik ve Yetkilendirme
      ↓
Uygulama / Laboratuvar / Projeler
```

Bu repository'nin temel amacı yalnızca bilgi biriktirmek değil, **öğrenilen bilgiyi düzenli şekilde dokümante etmek ve zaman içerisindeki gelişimi takip edebilmek.**

---

# 📚 İçerik

## 01 — Temel Kavramlar

Siber güvenliğin temelini oluşturan kavramlar.

* [Siber Güvenlik Nedir?](01-fundamentals/what-is-cybersecurity.md)
* [CIA Triad](01-fundamentals/cia-triad.md)
* [Threat, Vulnerability ve Risk](01-fundamentals/threat-vulnerability-risk.md)

### Öğrenilen temel kavramlar

* Cybersecurity
* Information Security
* Network Security
* Confidentiality
* Integrity
* Availability
* Threat
* Vulnerability
* Risk
* Attack
* Exploit
* Impact

---

## 02 — Saldırı Türleri ve Malware

Yaygın siber saldırı yöntemleri ve zararlı yazılım kavramları.

* [Siber Saldırı Türleri](02-attacks/attack-types.md)
* [Network Attacks](02-attacks/network-attacks.md)
* [Malware Türleri](02-attacks/malware.md)

### Çalışılan konular

* DoS / DDoS
* Brute Force
* Password Spraying
* MITM
* Sniffing
* Spoofing
* SQL Injection
* XSS
* CSRF
* Directory Traversal
* File Inclusion
* Phishing
* Spear Phishing
* Whaling
* Social Engineering
* BEC
* Virus
* Worm
* Trojan
* Ransomware
* Spyware
* Keylogger
* Rootkit
* Botnet

> Bu bölümde saldırıların yalnızca nasıl çalıştığı değil, temel belirtileri ve korunma yöntemleri de incelenmektedir.

---

## 03 — Ağ Temelleri

Siber güvenliğin temelini oluşturan ağ kavramları.

* [Ağ Temelleri](03-networking/network-basics.md)
* [Portlar ve Protokoller](03-networking/ports-protocols.md)

### Çalışılan konular

* IP Address
* MAC Address
* IPv4 / IPv6
* TCP / UDP
* Port
* Protocol
* Router
* Switch
* Firewall
* DNS
* DHCP
* NAT
* Gateway
* HTTP / HTTPS
* SSH

Ayrıca bir kullanıcının tarayıcıya:

```text
https://example.com
```

yazmasından web sayfasının görüntülenmesine kadar gerçekleşen temel ağ süreci incelenmektedir.

---

## 04 — Güvenlik Temelleri

Sistem ve kullanıcı güvenliğinde kullanılan temel mekanizmalar.

* [Firewall](04-security/firewall.md)
* [Authentication ve Authorization](04-security/authentication-authorization.md)
* [Cryptography Basics](04-security/cryptography-basics.md)

### Çalışılan konular

* Firewall
* Authentication
* Authorization
* MFA
* 2FA
* Password Policy
* Least Privilege
* Encryption
* Decryption
* Hash
* Salt
* Symmetric Encryption
* Asymmetric Encryption
* Digital Signature
* Certificate

---

## 05 — Web Güvenliği

Web uygulamalarının çalışma mantığı ve güvenlik açısından önemli temel kavramlar.

* [Web Güvenliğine Giriş](05-web-security/web-basics.md)

### Çalışılan konular

* Client
* Server
* Request
* Response
* Cookie
* Session
* Token
* API
* Endpoint
* HTTP Headers
* HTTP Status Codes

### HTTP Status Codes

```text
200 → OK
201 → Created
301 → Moved Permanently
302 → Found
400 → Bad Request
401 → Unauthorized
403 → Forbidden
404 → Not Found
500 → Internal Server Error
```

Özellikle **401 Authentication** ile **403 Authorization** arasındaki fark incelenmektedir.

---

## 06 — SOC ve Güvenlik Operasyonları

Siber güvenlik operasyonlarının temel çalışma mantığı.

* [SOC Nedir?](06-soc/soc.md)

### Çalışılan konular

* SOC
* SOC Analyst
* SIEM
* Log
* Alert
* Incident
* Incident Response
* Threat Intelligence

Ayrıca şüpheli giriş senaryoları üzerinden bir SOC analistinin hangi log ve güvenlik verilerini inceleyebileceği değerlendirilmektedir.

---

## 07 — Security Teams

Saldırı ve savunma ekiplerinin görevleri.

* [Red Team, Blue Team ve Purple Team](07-security-teams/red-blue-purple-team.md)

### İncelenen konular

* Red Team
* Blue Team
* Purple Team
* Attack Simulation
* Detection
* Defense
* Incident Response

Amaç, saldırı perspektifi ile savunma perspektifinin nasıl birlikte çalıştığını anlamaktır.

---

## 08 — Siber Güvenlikte Uzmanlık Alanları

Siber güvenliğin tek bir meslekten oluşmadığını ve farklı uzmanlık alanlarının bulunduğunu inceliyorum.

* [Siber Güvenlikte Uzmanlık Alanları](08-specializations/cybersecurity-domains.md)

### İncelenen alanlar

* Network Security
* SOC / Blue Team
* Penetration Testing
* Web Security
* Mobile Security
* Digital Forensics
* Malware Analysis
* Cloud Security
* Application Security
* Incident Response
* Threat Intelligence
* Security Engineering

Bu bölüm aynı zamanda hangi alanların hangi teknik altyapıya ihtiyaç duyduğunu anlamama yardımcı oluyor.

---

## 09 — Vulnerabilities

Güvenlik açıklarının tanımlanması ve teknik olarak değerlendirilmesi.

* [CVE ve CVSS](09-vulnerabilities/cve-cvss.md)

### Çalışılan konular

* CVE
* CVE ID
* CVSS
* Vulnerability Severity
* Exploitability
* Confidentiality
* Integrity
* Availability

Önemli ayrım:

```text
CVE  → Güvenlik açığının kimliği
CVSS → Teknik önem/severity değerlendirmesi
Risk → Ortama ve kuruma göre gerçek risk
```

---

## 10 — Etik ve Yetkilendirme

Siber güvenlik çalışmalarında izin, kapsam ve sorumlu davranış.

* [Siber Güvenlikte Etik ve Yetki](10-ethics/authorization.md)

### Çalışılan konular

* Authorized Testing
* Unauthorized Access
* Responsible Disclosure
* Bug Bounty
* Scope
* Rules of Engagement

Temel prensip:

> **Teknik olarak erişebilmek, o sistemi test etme yetkisine sahip olmak anlamına gelmez.**

---

# 🧭 Öğrenme Yaklaşımım

Bu repository'deki çalışmalarımı mümkün olduğunca şu sırayla ilerletiyorum:

### 1. Kavramı öğren

Önce kavramın ne olduğunu anlamaya çalışıyorum.

### 2. Kavramlar arasındaki ilişkiyi kur

Örneğin:

```text
Threat
  +
Vulnerability
  ↓
Risk
  ↓
Attack
  ↓
Impact
```

gibi kavramlar arasındaki bağlantıyı anlamaya çalışıyorum.

### 3. Gerçek senaryolarla düşün

Öğrendiğim kavramları şirket, web uygulaması, kullanıcı hesabı, ağ veya SOC senaryoları üzerinden değerlendirmeye çalışıyorum.

### 4. Dokümante et

Öğrendiklerimi kendi cümlelerimle Markdown dosyalarına aktarıyorum.

### 5. Uygulamaya geç

Temel bilgiler oturduktan sonra laboratuvarlar, güvenlik araçları ve kontrollü uygulamalarla pratiğe geçmeyi hedefliyorum.

---

# 🛠️ Şu Anki Teknik Odak Alanlarım

Siber güvenlik öğrenme sürecimde özellikle şu alanlara temel oluşturuyorum:

```text
Networking
    ↓
Linux / Windows
    ↓
Web Technologies
    ↓
Cybersecurity Fundamentals
    ↓
SOC / Blue Team
    ↓
Web & Application Security
    ↓
Cloud Security
    ↓
Digital Forensics
```

İlerleyen aşamalarda bu alanları uygulamalı çalışmalar ve projelerle desteklemeyi planlıyorum.

---

# 📈 Öğrenme Yol Haritası

Bu repository zaman içerisinde gelişen bir çalışma alanıdır.

### ✅ Aşama 1 — Temel Bilgiler

* [x] Cybersecurity Fundamentals
* [x] CIA Triad
* [x] Threat / Vulnerability / Risk
* [x] Attack Concepts
* [x] Malware
* [x] Networking Fundamentals
* [x] Ports & Protocols
* [x] Firewall
* [x] Authentication & Authorization
* [x] Cryptography Basics
* [x] Web Security Basics
* [x] SOC Fundamentals
* [x] Red / Blue / Purple Team
* [x] Cybersecurity Domains
* [x] CVE / CVSS
* [x] Ethics & Authorization
* [x] General Review

### 🔄 Aşama 2 — Teknik Temeller

* [ ] Linux Fundamentals
* [ ] Windows Security Fundamentals
* [ ] Advanced Networking
* [ ] TCP/IP Deep Dive
* [ ] DNS Security
* [ ] HTTP/HTTPS Deep Dive
* [ ] Active Directory Fundamentals
* [ ] Authentication Protocols
* [ ] Security Monitoring

### 🔄 Aşama 3 — Uygulamalı Siber Güvenlik

* [ ] Security Labs
* [ ] Log Analysis
* [ ] SIEM Practice
* [ ] Network Traffic Analysis
* [ ] Web Security Labs
* [ ] Vulnerability Analysis
* [ ] Incident Response Scenarios
* [ ] Threat Intelligence Exercises

### 🔄 Aşama 4 — Projeler

* [ ] Security Monitoring Project
* [ ] Log Analysis Project
* [ ] Vulnerability Analysis Project
* [ ] Security Automation Project
* [ ] Digital Forensics Project

---

# 📁 Repository Structure

```text
cybersecurity-notes/
│
├── README.md
│
├── 01-fundamentals/
│   ├── what-is-cybersecurity.md
│   ├── cia-triad.md
│   └── threat-vulnerability-risk.md
│
├── 02-attacks/
│   ├── attack-types.md
│   ├── network-attacks.md
│   └── malware.md
│
├── 03-networking/
│   ├── network-basics.md
│   └── ports-protocols.md
│
├── 04-security/
│   ├── firewall.md
│   ├── authentication-authorization.md
│   └── cryptography-basics.md
│
├── 05-web-security/
│   └── web-basics.md
│
├── 06-soc/
│   └── soc.md
│
├── 07-security-teams/
│   └── red-blue-purple-team.md
│
├── 08-specializations/
│   └── cybersecurity-domains.md
│
├── 09-vulnerabilities/
│   └── cve-cvss.md
│
└── 10-ethics/
    └── authorization.md
```

---

# 📚 Kaynak Yaklaşımı

Araştırmalarımda mümkün olduğunca güvenilir ve teknik kaynaklardan yararlanmaya çalışıyorum.

Özellikle:

* NIST
* CISA
* OWASP
* MITRE
* Microsoft Security
* Google Cloud Security
* Cisco Security
* Üniversitelerin ve güvenlik kuruluşlarının teknik dokümantasyonları

gibi kaynakları temel alıyorum.

Her konu için yalnızca tanım ezberlemek yerine, kavramın **neden önemli olduğunu ve diğer güvenlik kavramlarıyla nasıl ilişkili olduğunu** anlamaya çalışıyorum.

---

# ⚠️ Etik ve Güvenlik

Bu repository'deki çalışmaların temel amacı **eğitim ve savunma amaçlı siber güvenlik öğrenimidir.**

Uygulamalı çalışmalar başladığında testlerin yalnızca:

* Yetkili sistemlerde
* Belirlenmiş kapsam içerisinde
* Kontrollü ortamlarda
* İzin verilen yöntemlerle

gerçekleştirilmesi gerektiğini esas alıyorum.

> **Bilgi sahibi olmak ile bu bilgiyi nerede ve nasıl kullanmaya yetkili olmak farklı şeylerdir.**

---

# 👩‍💻 Hakkımda

Ben **Zeynep Ulucan**, Fırat Üniversitesi **Adli Bilişim Mühendisliği** öğrencisiyim.

Siber güvenlik alanında kendimi geliştirirken özellikle:

* 🔐 Cybersecurity
* 🛡️ SOC / Blue Team
* 🌐 Network Security
* 🔎 Digital Forensics
* ☁️ Cloud Security
* 📱 Mobile Security

alanlarını keşfediyor ve bu alanların kesişim noktalarını anlamaya çalışıyorum.

Bu repository, siber güvenlik alanındaki öğrenme sürecimin teknik bir günlüğü ve zaman içerisinde gelişen portföy çalışmalarımın bir parçasıdır.

---

# 🚀 Hedefim

Bu repository'nin zaman içerisinde yalnızca teorik notlardan oluşan bir arşiv olarak kalmasını istemiyorum.

Hedefim;

```text
Öğren
  ↓
Araştır
  ↓
Dokümante Et
  ↓
Uygula
  ↓
Analiz Et
  ↓
Proje Geliştir
  ↓
Teknik Portföy Oluştur
```

şeklinde ilerleyerek teorik bilgiyi gerçek teknik becerilere dönüştürmek.

---

<p align="center">
  <b>🔐 Learn. Analyze. Secure. Repeat.</b>
</p>
