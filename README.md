# 🛡️ Cybersecurity Notes

> Siber güvenlik alanında temel kavramlardan uygulamalı güvenlik analizlerine doğru ilerleyen kişisel öğrenme, araştırma ve laboratuvar arşivim.

Bu repository, **Fırat Üniversitesi Adli Bilişim Mühendisliği** eğitimim ve siber güvenlik alanında kendimi geliştirme sürecim boyunca öğrendiğim konuları, yaptığım araştırmaları ve uygulamalı laboratuvar çalışmalarını düzenli şekilde dokümante ettiğim teknik çalışma alanıdır.

Amacım yalnızca teorik bilgileri biriktirmek değil; öğrendiğim kavramları **araştırmak, uygulamak, analiz etmek ve dokümante ederek** zaman içerisinde sürdürülebilir bir teknik portföye dönüştürmek.

---

## 🎯 Repository'nin Amacı

Siber güvenlik çok geniş bir alan olduğu için öğrenme sürecimi temel kavramlardan başlayarak teknik ve uygulamalı çalışmalara doğru ilerletiyorum.

Temel yaklaşımım:

```text
Temel Kavramlar
      ↓
Networking
      ↓
Linux / Windows
      ↓
Web Technologies
      ↓
Cybersecurity Fundamentals
      ↓
Security Analysis
      ↓
Vulnerability Assessment
      ↓
SOC / Blue Team
      ↓
Web & Application Security
      ↓
Cloud Security
      ↓
Digital Forensics
      ↓
Security Projects
```

Bu repository'de özellikle şu konulara odaklanıyorum:

* 🔐 Cybersecurity Fundamentals
* 🌐 Network Security
* 🐧 Linux Security
* 🕸️ Web Security
* 🔎 Vulnerability Assessment
* 🛡️ SOC / Blue Team
* ☁️ Cloud Security
* 📱 Mobile Security
* 🧪 Security Labs
* 🔬 Digital Forensics
* ⚙️ Security Automation

---

# 📚 İçerik

## 01 — Cybersecurity Fundamentals

Siber güvenliğin temelini oluşturan kavramlar.

* Cybersecurity
* Information Security
* Network Security
* CIA Triad
* Threat
* Vulnerability
* Risk
* Attack
* Exploit
* Impact

📁 [`01-fundamentals/`](./01-fundamentals/)

---

## 02 — Attacks & Malware

Yaygın saldırı türleri ve zararlı yazılım kavramları.

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
* Social Engineering
* Malware Types

📁 [`02-attacks/`](./02-attacks/)

---

## 03 — Networking

Siber güvenlik için gerekli ağ temelleri.

* IP Address
* MAC Address
* IPv4 / IPv6
* TCP / UDP
* Ports
* Protocols
* Router
* Switch
* Firewall
* DNS
* DHCP
* NAT
* Gateway
* HTTP / HTTPS
* SSH

📁 [`03-networking/`](./03-networking/)

---

## 04 — Security Fundamentals

Sistem ve kullanıcı güvenliğinde kullanılan temel mekanizmalar.

* Firewall
* Authentication
* Authorization
* MFA / 2FA
* Password Security
* Least Privilege
* Encryption
* Hashing
* Salt
* Symmetric Encryption
* Asymmetric Encryption
* Digital Signature
* Certificates

📁 [`04-security/`](./04-security/)

---

## 05 — Web Security

Web uygulamalarının çalışma mantığı ve güvenlik açısından önemli temel kavramlar.

* Client / Server
* HTTP Request / Response
* Cookies
* Sessions
* Tokens
* APIs
* Endpoints
* HTTP Headers
* HTTP Status Codes
* Authentication / Authorization

📁 [`05-web-security/`](./05-web-security/)

---

## 06 — SOC & Security Operations

Güvenlik operasyonlarının temel çalışma mantığı.

* SOC
* SOC Analyst
* SIEM
* Logs
* Alerts
* Incidents
* Incident Response
* Threat Intelligence

📁 [`06-soc/`](./06-soc/)

---

## 07 — Security Teams

Siber güvenlik ekiplerinin görevleri ve çalışma modelleri.

* Red Team
* Blue Team
* Purple Team
* Attack Simulation
* Detection
* Defense
* Incident Response

📁 [`07-security-teams/`](./07-security-teams/)

---

## 08 — Cybersecurity Specializations

Siber güvenlik içerisindeki farklı uzmanlık alanlarını inceliyorum.

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

📁 [`08-specializations/`](./08-specializations/)

---

## 09 — Vulnerabilities

Güvenlik açıklarının tanımlanması ve teknik olarak değerlendirilmesi.

* CVE
* CVSS
* Vulnerability Severity
* Exploitability
* Confidentiality
* Integrity
* Availability

Temel ayrım:

```text
CVE
↓
Güvenlik açığının kimliği

CVSS
↓
Teknik önem / severity değerlendirmesi

Risk
↓
Ortam ve sistem bağlamındaki gerçek risk
```

📁 [`09-vulnerabilities/`](./09-vulnerabilities/)

---

## 10 — Ethics & Authorization

Siber güvenlik çalışmalarında yetki, kapsam ve sorumlu davranış.

* Authorized Testing
* Unauthorized Access
* Responsible Disclosure
* Bug Bounty
* Scope
* Rules of Engagement

> Teknik olarak erişebilmek, o sistemi test etme yetkisine sahip olmak anlamına gelmez.

📁 [`10-ethics/`](./10-ethics/)

---

# 🧪 Uygulamalı Çalışmalar

Teorik temellerin ardından kontrollü laboratuvar ortamlarında uygulamalı çalışmalar yapmaya başladım.

---

## Day 2 — Kali Linux & Local Network Security

Kali Linux kurulumu ve izole bir yerel ağ üzerinde temel ağ keşfi ve güvenlik taraması çalışmaları.

### Çalışılan konular

* Kali Linux temel kullanımı
* Linux ağ komutları
* IP adresi ve interface kontrolü
* Local network discovery
* Port scanning
* Service discovery
* Temel güvenlik değerlendirmesi

### Proje

🔗 **Local Network Security Scanner**

📁 [`day-02-kali-linux/`](./day-02-kali-linux/)

---

# 🔬 Day 3 — Metasploitable 2 Security Assessment

Bu çalışma kapsamında kasıtlı olarak zayıf bırakılmış **Metasploitable 2** sanal makinesi üzerinde kontrollü bir güvenlik değerlendirmesi gerçekleştirilmiştir.

Laboratuvar tamamen izole bir VirtualBox **Internal Network** üzerinde kurulmuştur.

### Lab Network

```text
Kali Linux
192.168.56.10
      │
      │  cyber-lab
      │
      ▼
Metasploitable 2
192.168.56.20
```

### Kullanılan temel araçlar

* Nmap
* Nmap NSE
* Nikto
* SearchSploit
* curl
* ping
* Linux networking tools

### Çalışılan güvenlik değerlendirme süreci

```text
Reconnaissance
      ↓
Scanning
      ↓
Enumeration
      ↓
Service & Version Analysis
      ↓
Vulnerability Research
      ↓
Risk Assessment
      ↓
Security Recommendations
      ↓
Reporting
```

### İncelenen başlıklar

* Metasploitable 2
* Attack Surface
* Network Discovery
* Service & Version Detection
* FTP Analysis
* HTTP Analysis
* SMB / Samba Analysis
* Nmap NSE
* Nikto
* SearchSploit
* CVE Research
* Risk Assessment
* Defense Perspective
* Attack Chain
* Mini Penetration Test Report

📁 [`day-03-metasploitable-2/`](./day-03-metasploitable-2/)

### Önemli değerlendirme

Bu laboratuvarda özellikle şu ayrımlar üzerinde durulmuştur:

```text
Open Port
    ≠
Vulnerability

Vulnerability
    ≠
Confirmed Exploitability

CVE
    ≠
Exploit

CVSS
    ≠
Real-World Risk
```

Bir güvenlik bulgusunun gerçek önemini değerlendirmek için servis, sürüm, yapılandırma, erişim durumu, sistemin kritiklik seviyesi ve olası etki birlikte ele alınmıştır.

> Tüm uygulamalı çalışmalar yalnızca yetkili ve izole laboratuvar ortamında gerçekleştirilmiştir.

---

# 🧭 Öğrenme Yaklaşımım

Çalışmalarımı mümkün olduğunca şu yöntemle ilerletiyorum:

### 1. Kavramı öğren

Önce konunun ne olduğunu ve neden önemli olduğunu anlamaya çalışıyorum.

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

gibi kavramların birbirleriyle ilişkisini anlamaya çalışıyorum.

### 3. Teknik olarak uygula

Öğrendiğim kavramları kontrollü ve yetkili laboratuvar ortamlarında uyguluyorum.

### 4. Sonuçları analiz et

Bir aracın çıktısını yalnızca almak yerine, çıktının ne anlama geldiğini değerlendirmeye çalışıyorum.

### 5. Dokümante et

Öğrendiklerimi ve yaptığım çalışmaları kendi cümlelerimle Markdown dosyalarına aktarıyorum.

### 6. Savunma perspektifinden değerlendir

Bir güvenlik probleminin nasıl oluştuğunun yanında, nasıl önlenebileceğini ve tespit edilebileceğini de incelemeye çalışıyorum.

---

# 🛠️ Teknik Odak Alanlarım

Şu aşamada özellikle aşağıdaki alanlarda temel oluşturmaya çalışıyorum:

```text
Networking
     ↓
Linux / Windows
     ↓
Web Technologies
     ↓
Cybersecurity Fundamentals
     ↓
Security Analysis
     ↓
Vulnerability Assessment
     ↓
SOC / Blue Team
     ↓
Web & Application Security
     ↓
Cloud Security
     ↓
Digital Forensics
```

İlerleyen çalışmalarla bu alanları uygulamalı laboratuvarlar ve teknik projelerle desteklemeyi hedefliyorum.

---

# 📈 Öğrenme Yol Haritası

### ✅ Aşama 1 — Temel Bilgiler

* Cybersecurity Fundamentals
* CIA Triad
* Threat / Vulnerability / Risk
* Attack Concepts
* Malware
* Networking Fundamentals
* Ports & Protocols
* Firewall
* Authentication & Authorization
* Cryptography Basics
* Web Security Basics
* SOC Fundamentals
* Red / Blue / Purple Team
* Cybersecurity Domains
* CVE / CVSS
* Ethics & Authorization

### ✅ Aşama 2 — İlk Uygulamalı Çalışmalar

* Kali Linux
* Linux Networking
* Local Network Discovery
* Nmap
* Service Enumeration
* Metasploitable 2
* Nmap NSE
* Nikto
* SearchSploit
* CVE Research
* Vulnerability Assessment
* Risk Assessment
* Security Reporting

### 🔄 Aşama 3 — Teknik Temeller

* Linux Security
* Windows Security
* Advanced Networking
* TCP/IP Deep Dive
* DNS Security
* HTTP / HTTPS Deep Dive
* Active Directory Fundamentals
* Authentication Protocols
* Security Monitoring

### 🔄 Aşama 4 — Uygulamalı Siber Güvenlik

* Security Labs
* Log Analysis
* SIEM Practice
* Network Traffic Analysis
* Web Security Labs
* Vulnerability Analysis
* Incident Response
* Threat Intelligence
* Detection Engineering

### 🔄 Aşama 5 — Teknik Projeler

* Security Monitoring Project
* Log Analysis Project
* Vulnerability Analysis Project
* Security Automation Project
* Digital Forensics Project
* Security-focused Application Projects

---

# 📁 Repository Structure

```text
cybersecurity-notes/
│
├── README.md
│
├── 01-fundamentals/
├── 02-attacks/
├── 03-networking/
├── 04-security/
├── 05-web-security/
├── 06-soc/
├── 07-security-teams/
├── 08-specializations/
├── 09-vulnerabilities/
├── 10-ethics/
│
├── 11-general-review/
│
├── day-02-kali-linux/
│
└── day-03-metasploitable-2/
    ├── 01-metasploitable-2.md
    ├── 02-lab-network-setup.md
    ├── 03-network-discovery.md
    ├── 04-attack-surface-analysis.md
    ├── 05-service-version-research.md
    ├── 06-vulnerability-research.md
    ├── 07-ftp-analysis.md
    ├── 08-http-analysis.md
    ├── 09-smb-samba-analysis.md
    ├── 10-nmap-nse-analysis.md
    ├── 11-tools-research.md
    ├── 12-searchsploit-analysis.md
    ├── 13-risk-assessment.md
    ├── 14-mini-pentest-report.md
    ├── 15-defense-perspective.md
    ├── 16-attack-chain.md
    ├── 17-review-questions.md
    ├── 18-review-questions.md
    │
    └── screenshots/
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
* Üniversitelerin teknik kaynakları
* Güvenlik araçlarının resmi dokümantasyonları

gibi kaynakları temel alıyorum.

Amacım yalnızca bir kavramın tanımını öğrenmek değil; kavramın **neden önemli olduğunu, nasıl çalıştığını ve diğer güvenlik konularıyla nasıl ilişkilendiğini** anlamaktır.

---

# ⚠️ Etik ve Güvenlik

Bu repository'deki çalışmaların temel amacı eğitim, araştırma ve savunma odaklı siber güvenlik öğrenimidir.

Uygulamalı çalışmalar yalnızca:

* Yetkili sistemlerde
* Belirlenmiş kapsam içerisinde
* Kontrollü laboratuvar ortamlarında
* İzin verilen yöntemlerle

gerçekleştirilmelidir.

Özellikle Metasploitable 2 çalışması **izole bir VirtualBox Internal Network** üzerinde gerçekleştirilmiştir.

> Bilgi sahibi olmak ile bu bilgiyi nerede ve nasıl kullanmaya yetkili olmak farklı şeylerdir.

---

# 👩‍💻 Hakkımda

Ben **Zeynep Ulucan**, Fırat Üniversitesi Adli Bilişim Mühendisliği öğrencisiyim.

Siber güvenlik alanında kendimi geliştirirken özellikle:

* 🔐 Cybersecurity
* 🛡️ SOC / Blue Team
* 🌐 Network Security
* 🔎 Digital Forensics
* ☁️ Cloud Security
* 📱 Mobile Security

alanlarını öğreniyor ve bu alanların birbirleriyle olan ilişkilerini anlamaya çalışıyorum.

Bu repository, siber güvenlik alanındaki öğrenme sürecimin teknik bir günlüğü ve zaman içerisinde gelişen portföy çalışmalarımın bir parçasıdır.

---

# 🚀 Hedefim

Bu repository'nin yalnızca teorik notlardan oluşan bir arşiv olarak kalmasını istemiyorum.

Hedeflediğim çalışma modeli:

```text
Learn
  ↓
Research
  ↓
Document
  ↓
Practice
  ↓
Analyze
  ↓
Build
  ↓
Improve
  ↓
Technical Portfolio
```

Öğrendiğim teorik bilgileri kontrollü laboratuvarlar ve teknik projelerle destekleyerek zaman içerisinde gerçek dünyadaki güvenlik problemlerini analiz edebilecek bir teknik altyapı oluşturmaya çalışıyorum.

---

## 🔐 Learn. Analyze. Secure. Repeat.
