# 🔐 10. Portlar

## 🎯 Amaç

Bu bölümde bilgisayar ağlarında kullanılan **portların ne olduğunu**, port numaralarının ne anlama geldiğini ve siber güvenlik açısından neden önemli olduklarını öğrenmek.

---

## 🚪 Port Nedir?

Bir bilgisayar ağ üzerinden farklı hizmetler sunabilir. Bu hizmetlere ulaşabilmek için **port numaraları** kullanılır.

Basit bir şekilde:

> **IP adresi = Hangi bilgisayar?**
> **Port = Bilgisayardaki hangi iletişim noktası?**
> **Servis = Bu port üzerinden hangi hizmet çalışıyor?**

Örneğin:

```text
192.168.1.10:80
```

Burada:

* `192.168.1.10` → Bilgisayarın IPv4 adresi
* `80` → Port numarası
* `HTTP` → Bu portla ilişkilendirilen servis/protokol

---

## 🔢 Port Numaraları

TCP ve UDP iletişiminde port numaraları:

```text
0 - 65535
```

aralığında olabilir.

Portlar farklı hizmetlerin birbirinden ayrılmasını sağlar.

Örneğin aynı bilgisayarda hem web servisi hem de SSH servisi çalışabilir:

```text
192.168.1.10:22   → SSH
192.168.1.10:80   → HTTP
192.168.1.10:443  → HTTPS
```

Bu durumda aynı bilgisayar üzerinde farklı hizmetler farklı portlar üzerinden iletişim kurar.

---

## 📌 Sık Kullanılan Portlar

|     Port | Yaygın Servis | Kullanım Alanı                   |
| -------: | ------------- | -------------------------------- |
|   **21** | FTP           | Dosya aktarımı                   |
|   **22** | SSH           | Uzak sistem yönetimi             |
|   **23** | Telnet        | Uzak bağlantı                    |
|   **25** | SMTP          | E-posta gönderimi                |
|   **53** | DNS           | Alan adı çözümleme               |
|   **80** | HTTP          | Web iletişimi                    |
|  **110** | POP3          | E-posta alma                     |
|  **139** | NetBIOS       | Windows ağ iletişimi             |
|  **443** | HTTPS         | Şifreli web iletişimi            |
|  **445** | SMB           | Dosya ve yazıcı paylaşımı        |
| **3389** | RDP           | Windows uzak masaüstü bağlantısı |

---

## 🔓 Açık Port Nedir?

Bir port üzerinde bir servis bağlantıları kabul edecek şekilde çalışıyorsa Nmap tarafından genellikle **open (açık)** olarak görülebilir.

Örneğin:

```text
PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
443/tcp  open     https
```

Bu çıktı:

* `22` numaralı portta SSH servisinin,
* `80` numaralı portta HTTP servisinin,
* `443` numaralı portta HTTPS servisinin

erişilebilir durumda olduğunu gösterebilir.

---

## 🔒 Kapalı Port Nedir?

Bir port bilgisayarda erişilebilir olsa da o port üzerinde bağlantı kabul eden bir servis bulunmuyorsa Nmap tarafından **closed (kapalı)** olarak görülebilir.

Örneğin önceki taramamızda:

```bash
nmap localhost
```

sonucunda:

```text
Not shown: 1000 closed tcp ports (reset)
```

ifadesini gördük.

Bu, Nmap'in varsayılan olarak kontrol ettiği 1000 TCP portunun kapalı olduğunu gösteriyordu.

---

## 🛡️ Port Açık Olması Güvenlik Açığı Mıdır?

**Hayır.**

Bir portun açık olması tek başına güvenlik açığı olduğu anlamına gelmez.

Örneğin:

```text
443/tcp → HTTPS
```

portunun açık olması normal olabilir.

Burada daha önemli sorular şunlardır:

* Hangi servis çalışıyor?
* Hangi yazılım kullanılıyor?
* Yazılımın hangi sürümü çalışıyor?
* Servis doğru yapılandırılmış mı?
* Bu porta dış ağlardan erişilebiliyor mu?
* Portun açık olması gerçekten gerekli mi?

Bu nedenle siber güvenlikte yalnızca portun açık olup olmadığına bakmak yeterli değildir.

---

## 🔎 Nmap ve Portlar

Nmap'in temel kullanım amaçlarından biri hedef sistemdeki portların durumunu belirlemektir.

Örneğin:

```bash
nmap localhost
```

komutu ile kendi Kali Linux sistemimizde temel bir port taraması gerçekleştirdik.

Nmap sonucunda:

```text
Host is up
```

ifadesi hedef sistemin erişilebilir olduğunu,

```text
1000 closed tcp ports
```

ifadesi ise taranan 1000 TCP portunun kapalı olduğunu gösterdi.

---

## 🧠 Siber Güvenlik Açısından Önemi

Açık portlar bir sistemin **saldırı yüzeyinin** parçalarından biri olabilir.

Bu nedenle güvenlik uzmanları bir sistem üzerinde:

1. Hangi portların açık olduğunu,
2. Bu portlarda hangi servislerin çalıştığını,
3. Servislerin hangi sürümleri kullandığını,
4. Bu servislerin gerekli olup olmadığını,
5. Güvenlik yapılandırmalarının uygun olup olmadığını

inceler.

Örneğin kullanılmayan bir servisin dışarıya açık olması gereksiz bir erişim noktası oluşturabilir.

Ancak:

> **Açık port = güvenlik açığı**

şeklinde doğrudan bir sonuç çıkarılmamalıdır.

Açık port, daha ayrıntılı güvenlik incelemesinin başlangıç noktalarından biridir.

---

## 📝 Kendi Notum

Bu bölümde portların, bir bilgisayardaki farklı ağ hizmetlerinin iletişim noktalarını belirlemek için kullanıldığını öğrendim.

Özellikle şu ayrımı anlamak benim için önemliydi:

```text
IP adresi → Hangi bilgisayar?
Port       → Hangi iletişim noktası?
Servis     → Bu portta hangi hizmet çalışıyor?
```

Ayrıca açık bir portun doğrudan güvenlik açığı anlamına gelmediğini, portun arkasındaki servis ve yapılandırmanın da incelenmesi gerektiğini öğrendim.

---

## ✅ Kontrol Listesi

* [x] Port kavramını öğrendim.
* [x] Port numaralarının `0-65535` aralığında olduğunu öğrendim.
* [x] Açık ve kapalı port kavramlarını öğrendim.
* [x] Yaygın portları ve servislerini inceledim.
* [x] Portların siber güvenlik açısından neden önemli olduğunu öğrendim.
* [x] Açık portun tek başına güvenlik açığı olmadığını öğrendim.
* [x] Nmap'in port taramasındaki rolünü öğrendim.

---

## ➡️ Sonraki Bölüm

Bir sonraki bölümde yaygın portları daha yakından inceleyecek ve **Nmap ile portların nasıl kontrol edildiğini** uygulamalı olarak göreceğim.
