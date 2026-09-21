# 🔌 Portlar ve Protokoller

Ağ iletişiminde cihazların hangi hizmetlerle iletişim kurduğunu anlamak için portlar ve protokoller önemli bir yere sahiptir.

Bir **port**, aynı cihaz üzerindeki farklı ağ hizmetlerinin birbirinden ayırt edilmesine yardımcı olan mantıksal bir numaradır.

Örneğin bir sunucuda aynı anda:

```text
22   → SSH
80   → HTTP
443  → HTTPS
```

gibi farklı servisler çalışabilir.

Burada IP adresi cihazı/ağ arayüzünü, port ise ilgili hizmeti ayırt etmeye yardımcı olur.

> **IP = Hangi cihaz/ağ?**
> **Port = Hangi hizmet?**

---

# 📋 Yaygın Portlar ve Protokoller

|     Port | Protokol / Servis | Ne amaçla kullanılır?                                |
| -------: | ----------------- | ---------------------------------------------------- |
|   **21** | FTP               | Dosya transferi                                      |
|   **22** | SSH               | Güvenli uzak erişim ve sistem yönetimi               |
|   **23** | Telnet            | Uzak terminal erişimi                                |
|   **25** | SMTP              | E-posta gönderimi/posta sunucuları arası aktarım     |
|   **53** | DNS               | Alan adlarının IP adresleriyle eşleştirilmesi        |
|   **80** | HTTP              | Web iletişimi                                        |
|  **110** | POP3              | E-postaların sunucudan alınması                      |
|  **143** | IMAP              | E-postaların sunucu üzerinde yönetilmesi ve alınması |
|  **443** | HTTPS             | TLS ile korunan web iletişimi                        |
| **3389** | RDP               | Windows sistemlere uzak masaüstü erişimi             |

---

# 1. Port 21 — FTP

**FTP (File Transfer Protocol)**, dosya transferi için kullanılan bir protokoldür.

Port:

```text
21
```

FTP ile dosyalar bir sistemden başka bir sisteme aktarılabilir.

Ancak klasik FTP, iletişimi kendi başına şifrelemez.

Bu nedenle kullanıcı adı, parola ve dosyaların güvenli şekilde aktarılması gerektiğinde güvenli alternatifler tercih edilir.

> **21 → FTP → Dosya transferi**

---

# 2. Port 22 — SSH

**SSH (Secure Shell)**, uzak sistemlere güvenli şekilde bağlanmak ve yönetmek için kullanılan protokoldür.

Port:

```text
22
```

Özellikle Linux sunucularının uzaktan yönetiminde yaygın olarak kullanılır.

Örneğin:

```text
Yönetici bilgisayarı
       ↓
      SSH
       ↓
Linux Sunucu
```

SSH şifreli iletişim sağlar.

Ancak SSH portunun açık olması tek başına güvenlik açığı değildir. Güçlü kimlik doğrulama, güncel yazılım ve uygun erişim kısıtlamaları önemlidir.

> **22 → SSH → Güvenli uzak erişim**

---

# 3. Port 23 — Telnet

**Telnet**, uzak sistemlere terminal üzerinden bağlanmak için kullanılan eski bir protokoldür.

Port:

```text
23
```

Telnet'in önemli güvenlik problemi, iletişimi varsayılan olarak şifrelememesidir.

Bu nedenle modern sistemlerde güvenli uzak erişim için genellikle SSH tercih edilir.

> **23 → Telnet → Uzak terminal erişimi**

---

# 4. Port 25 — SMTP

**SMTP (Simple Mail Transfer Protocol)**, e-posta gönderiminde kullanılan protokollerden biridir.

Port:

```text
25
```

SMTP özellikle posta sunucularının birbirleriyle e-posta aktarımı gerçekleştirmesinde kullanılır.

E-posta istemcilerinin gönderim yapılandırmalarında farklı SMTP portları da kullanılabilir.

> **25 → SMTP → E-posta gönderimi/aktarımı**

---

# 5. Port 53 — DNS

**DNS (Domain Name System)**, alan adlarının IP adresleriyle eşleştirilmesine yardımcı olur.

Port:

```text
53
```

DNS sorguları yaygın olarak **UDP 53** üzerinden gerçekleştirilir.

Bazı durumlarda DNS için **TCP 53** de kullanılabilir.

Örneğin:

```text
example.com
     ↓
    DNS
     ↓
IP adresi
```

> **53 → DNS → Alan adı çözümleme**

---

# 6. Port 80 — HTTP

**HTTP (Hypertext Transfer Protocol)**, web istemcileri ile web sunucuları arasındaki iletişimde kullanılan protokoldür.

Port:

```text
80
```

Örneğin:

```text
Tarayıcı
   ↓
HTTP
   ↓
Web Sunucusu
```

HTTP kendi başına iletişimi şifrelemez.

Güvenli web iletişiminde HTTPS kullanılır.

> **80 → HTTP → Web iletişimi**

---

# 7. Port 110 — POP3

**POP3 (Post Office Protocol version 3)**, e-postaların posta sunucusundan alınması için kullanılan bir protokoldür.

Port:

```text
110
```

POP3 kullanımında e-postaların istemciye indirilmesi temel yaklaşımlardan biridir.

Günümüzde güvenli e-posta iletişimi için şifreli POP3 seçenekleri de kullanılabilir.

> **110 → POP3 → E-posta alma**

---

# 8. Port 143 — IMAP

**IMAP (Internet Message Access Protocol)**, e-postalara sunucu üzerinde erişmek ve e-postaları yönetmek için kullanılan bir protokoldür.

Port:

```text
143
```

POP3 ile karşılaştırıldığında IMAP, e-postaların sunucu üzerinde tutulması ve farklı cihazlardan senkronize şekilde yönetilmesi açısından farklı bir yaklaşım sunar.

Örneğin aynı e-posta hesabına:

```text
Telefon
   ↕
Sunucu
   ↕
Bilgisayar
```

üzerinden erişilebilir.

> **143 → IMAP → E-postaları sunucu üzerinden yönetme**

---

# 9. Port 443 — HTTPS

**HTTPS (HTTP Secure)**, HTTP iletişiminin TLS ile korunmuş halidir.

Port:

```text
443
```

Günümüzde web sitelerinde yaygın olarak kullanılan güvenli web iletişim yöntemidir.

HTTPS temel olarak:

* Gizlilik
* Bütünlük
* Sunucu kimlik doğrulaması

sağlanmasına yardımcı olur.

Örneğin:

```text
Tarayıcı
   ↓
HTTPS : 443
   ↓
Web Sunucusu
```

> **443 → HTTPS → Güvenli web iletişimi**

---

# 10. Port 3389 — RDP

**RDP (Remote Desktop Protocol)**, özellikle Windows sistemlere uzak masaüstü bağlantısı sağlamak için kullanılan Microsoft protokolüdür.

Port:

```text
3389
```

Örneğin sistem yöneticisi uzaktaki bir Windows bilgisayara grafiksel masaüstü üzerinden bağlanabilir.

RDP'nin internet üzerinden gereksiz şekilde doğrudan erişilebilir olması güvenlik açısından dikkat edilmesi gereken bir durumdur.

Bu nedenle erişim kontrolü, güçlü kimlik doğrulama, MFA, ağ segmentasyonu ve uygun güvenlik politikaları önemlidir.

> **3389 → RDP → Uzak masaüstü erişimi**

---

# 🔐 Portların Kısa Özeti

| Port | Servis | Temel kullanım             |
| ---: | ------ | -------------------------- |
|   21 | FTP    | Dosya transferi            |
|   22 | SSH    | Güvenli uzak erişim        |
|   23 | Telnet | Uzak terminal              |
|   25 | SMTP   | E-posta gönderimi/aktarımı |
|   53 | DNS    | Alan adı çözümleme         |
|   80 | HTTP   | Web                        |
|  110 | POP3   | E-posta alma               |
|  143 | IMAP   | E-posta yönetimi           |
|  443 | HTTPS  | Güvenli web                |
| 3389 | RDP    | Uzak masaüstü              |

---

# ❓ Araştırma Sorusu

## Bir portun açık olması otomatik olarak güvenlik açığı olduğu anlamına gelir mi?

**Hayır.**

Bir portun açık olması, o port üzerinden bir servisin bağlantı kabul ettiğini gösterir. Bu durum tek başına güvenlik açığı olduğunu göstermez.

Örneğin bir web sunucusunda:

```text
443 → HTTPS
```

portunun açık olması normal ve beklenen bir durum olabilir.

Çünkü web sunucusunun internet üzerinden HTTPS bağlantılarını kabul edebilmesi gerekir.

Ancak güvenlik açısından asıl önemli olan portun arkasındaki servisin nasıl çalıştığıdır.

Şu sorular değerlendirilmelidir:

### 1. Hangi servis çalışıyor?

Örneğin:

```text
22 → SSH
443 → HTTPS
3389 → RDP
```

Açık portun hangi servise ait olduğu bilinmelidir.

### 2. Servis güncel mi?

Servisin kullandığı yazılım eski veya bilinen güvenlik zafiyetlerine sahip olabilir.

Bu durumda açık port üzerinden erişilebilen servis güvenlik riski oluşturabilir.

### 3. Servis internete açık olmak zorunda mı?

Bir servisin yalnızca şirket içerisinden erişilmesi gerekiyorsa internete açık olması gereksiz bir saldırı yüzeyi oluşturabilir.

### 4. Erişim kontrolü var mı?

Servise herkesin erişmesine izin vermek yerine yalnızca gerekli kullanıcıların veya ağların erişmesine izin verilebilir.

### 5. Kimlik doğrulama güvenli mi?

Özellikle SSH ve RDP gibi uzaktan erişim servislerinde güçlü kimlik doğrulama önemlidir.

### 6. Güvenlik yapılandırması doğru mu?

Servis güvenli şekilde yapılandırılmamışsa açık port risk oluşturabilir.

---

# 🧠 Açık Port ile Güvenlik Açığı Arasındaki Fark

Şöyle düşünmek benim için daha kolay:

```text
Açık Port
    ↓
Bir servis erişilebilir
    ↓
Servisin yapılandırmasını ve güvenliğini incele
    ↓
Zafiyet / yanlış yapılandırma / gereksiz erişim var mı?
    ↓
Güvenlik riski oluşabilir
```

Bu nedenle:

> **Açık port = saldırı yüzeyinin bir parçası olabilir.**

Ama:

> **Açık port = otomatik olarak güvenlik açığı değildir.**

Örneğin:

```text
443 açık
HTTPS çalışıyor
Güncel yazılım
Doğru TLS yapılandırması
Uygun firewall kuralları
```

şeklindeki bir sistemde 443 numaralı portun açık olması normal olabilir.

Buna karşılık:

```text
3389 açık
RDP internete doğrudan açık
Zayıf kimlik doğrulama
Eski/güncel olmayan sistem
```

gibi bir yapı daha dikkatli incelenmesi gereken bir saldırı yüzeyi oluşturabilir.

---

# 🛡️ Siber Güvenlik Açısından Neden Önemli?

Bir güvenlik uzmanı bir sistemin ağını incelerken sadece:

> "Hangi portlar açık?"

sorusunu sormaz.

Aynı zamanda:

> "Bu portun arkasında hangi servis var?"

> "Bu servis gerekli mi?"

> "Kimler erişebiliyor?"

> "Servis güncel mi?"

> "Bilinen bir zafiyeti var mı?"

> "Güvenli yapılandırılmış mı?"

gibi soruları da değerlendirir.

Bu yüzden port bilgisi, **saldırı yüzeyini anlamanın önemli parçalarından biridir.**

---

# ✍️ Kendi Öğrenme Notlarım

Bu çalışmadan sonra portları sadece numara olarak ezberlemek yerine hangi hizmetle ilişkili olduklarını anlamaya çalışıyorum.

Özellikle şu portları bilmem gerektiğini düşünüyorum:

```text
21   → FTP
22   → SSH
23   → Telnet
25   → SMTP
53   → DNS
80   → HTTP
110  → POP3
143  → IMAP
443  → HTTPS
3389 → RDP
```

Benim için en önemli çıkarım:

> **Bir portun açık olması tek başına güvenlik açığı değildir. Açık portun arkasındaki servis, yapılandırma, erişim kontrolü, güncellik ve güvenlik durumu birlikte değerlendirilmelidir.**

---

# 🎯 Kısa Özet

```text
Port = Hizmete ulaşmak için kullanılan mantıksal numara

21   → FTP
22   → SSH
23   → Telnet
25   → SMTP
53   → DNS
80   → HTTP
110  → POP3
143  → IMAP
443  → HTTPS
3389 → RDP
```

Ve en önemli cümle:

> **Açık port, doğrudan güvenlik açığı değildir; ancak erişilebilir bir servis sunduğu için saldırı yüzeyinin bir parçası olabilir. Güvenlik değerlendirmesi servis, yapılandırma, erişim kontrolü, güncellik ve zafiyetler birlikte incelenerek yapılır.**

---

# 📚 Kaynaklar

* NIST — National Institute of Standards and Technology
* IETF — Internet Engineering Task Force
* Cisco Networking Academy
* Microsoft Security
* Cloudflare Learning Center
