# 🔎 11. Service Detection (-sV)

## 🎯 Amaç

Bu bölümde Nmap'in açık portların arkasında çalışan servisleri ve mümkün olduğunda servis sürümlerini nasıl tespit ettiğini öğrenmek.

Bunun için `-sV` parametresini kullanarak kendi Kali Linux sistemimde kontrollü bir test gerçekleştirdim.

---

## 🧠 `-sV` Parametresi Nedir?

Nmap'te:

```bash
nmap -sV localhost
```

komutundaki `-sV` parametresi:

> **Service Version Detection**

anlamına gelir.

Temel port taraması bize bir portun açık veya kapalı olduğunu gösterebilir. `-sV` ise açık portların arkasında hangi servisin çalıştığını ve mümkün olduğunda bu servisin sürümünü belirlemeye çalışır.

Örneğin:

```text
PORT     STATE SERVICE VERSION
8000/tcp open  http    SimpleHTTPServer 0.6 (Python 3.13.7)
```

Bu sonuçta Nmap yalnızca 8000 numaralı portun açık olduğunu değil, bu portta HTTP servisi olarak çalışan Python tabanlı bir sunucu olduğunu da tespit etmiştir.

---

# 🧪 Uygulama

## 1. İlk Durum

Öncelikle kendi Kali Linux sistemimde:

```bash
nmap -sV localhost
```

komutunu çalıştırdım.

İlk taramada:

```text
Not shown: 1000 closed tcp ports (reset)
```

sonucunu aldım.

Bu durumda Nmap'in varsayılan olarak kontrol ettiği 1000 TCP portunun tamamı kapalıydı.

Dolayısıyla Nmap'in servis sürümü tespit edebileceği açık bir servis bulunmuyordu.

---

## 2. Yerel Web Sunucusu Başlatma

Port ve servis arasındaki ilişkiyi uygulamalı olarak görmek için Kali Linux üzerinde Python ile basit bir HTTP sunucusu başlattım:

```bash
python3 -m http.server 8000
```

Bu komut Python kullanarak **8000 numaralı port üzerinde basit bir HTTP sunucusu** başlatır.

Terminalde şu çıktı görüldü:

```text
Serving HTTP on 0.0.0.0 port 8000
```

Bu ifade, Python HTTP sunucusunun 8000 numaralı portta bağlantıları dinlemeye başladığını gösterir.

### 📸 Ekran Görüntüsü


<img width="667" height="305" alt="image" src="https://github.com/user-attachments/assets/f3aeb22b-0624-4d5b-a313-c07b0abf0811" />


Bu aşamada portu manuel olarak açmadım. Bir servis başlattım ve bu servis 8000 numaralı portu dinlemeye başladı.

---

## 3. Nmap ile Service Detection

Python HTTP sunucusu çalışırken yeni bir terminal açarak:

```bash
nmap -sV localhost
```

komutunu tekrar çalıştırdım.

### 📸 Ekran Görüntüsü


<img width="619" height="220" alt="image" src="https://github.com/user-attachments/assets/7f37a7f5-5a94-4a6d-82a5-24482aecd2bb" />


Nmap sonucunda:

```text
Starting Nmap 7.95 ( https://nmap.org ) at 2026-09-22 06:53 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0000030s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 999 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
8000/tcp open  http    SimpleHTTPServer 0.6 (Python 3.13.7)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.40 seconds
```

sonucunu elde ettim.

---

# 🔍 Nmap Sonucunun İncelenmesi

En önemli satır:

```text
8000/tcp open http SimpleHTTPServer 0.6 (Python 3.13.7)
```

Bu satırı parçalara ayırabiliriz:

| Alan    | Sonuç                                  | Açıklama                                         |
| ------- | -------------------------------------- | ------------------------------------------------ |
| Port    | `8000/tcp`                             | Kullanılan TCP portu                             |
| State   | `open`                                 | Port üzerinde bağlantı kabul eden bir servis var |
| Service | `http`                                 | Nmap servisi HTTP olarak tanımladı               |
| Version | `SimpleHTTPServer 0.6 (Python 3.13.7)` | Tespit edilen servis ve sürüm bilgisi            |

Ayrıca:

```text
Not shown: 999 closed tcp ports (reset)
```

ifadesi, diğer 999 taranan TCP portunun kapalı olduğunu gösterir.

Dolayısıyla bu taramada:

```text
1000 TCP port
     │
     ├── 999 kapalı
     │
     └── 1 açık
          │
          └── 8000/tcp
                │
                └── HTTP
                     │
                     └── SimpleHTTPServer
                          │
                          └── Python 3.13.7
```

sonucunu elde ettim.

---

# 🌐 4. Nmap Taraması Sırasında Oluşan HTTP İstekleri

Python HTTP sunucusunun çalıştığı terminalde Nmap taraması sırasında çeşitli HTTP istekleri görüldü.

Örneğin:

```text
127.0.0.1 - - [22/Sep/2026 06:53:50] "GET / HTTP/1.0" 200 -
127.0.0.1 - - [22/Sep/2026 06:53:50] code 501, message Unsupported method ('POST')
127.0.0.1 - - [22/Sep/2026 06:53:50] "POST /sdk HTTP/1.1" 501 -
127.0.0.1 - - [22/Sep/2026 06:53:50] "GET /nmaplowercheck1790074430 HTTP/1.1" 404 -
127.0.0.1 - - [22/Sep/2026 06:53:50] "GET /evox/about HTTP/1.1" 404 -
127.0.0.1 - - [22/Sep/2026 06:53:50] "GET /HNAP1 HTTP/1.1" 404 -
```



Bu istekler, Nmap'in servis tespiti sırasında hedef servisin davranışını anlamak için farklı HTTP istekleri gönderebildiğini göstermektedir.

---

## 📌 HTTP Yanıt Kodlarının Anlamı

Bu deney sırasında farklı HTTP yanıt kodları görüldü.

### `200 OK`

```text
GET / HTTP/1.0 → 200
```

Sunucunun isteği başarıyla karşıladığını gösterir.

### `404 Not Found`

```text
GET /HNAP1 → 404
```

İstenen kaynağın sunucuda bulunmadığını gösterir.

### `501 Not Implemented`

```text
POST /sdk → 501
```

Python'ın basit HTTP sunucusunun gönderilen HTTP metodunu desteklemediğini gösterir.

Bu yanıt kodlarının görülmesi tek başına bir güvenlik açığı olduğu anlamına gelmez.

---

# 🔄 5. Port ve Servis İlişkisi

Bu uygulama portların nasıl açık göründüğünü anlamamı sağladı.

Süreç şu şekilde gerçekleşti:

```text
Python HTTP sunucusu başlatıldı
          ↓
Sunucu 8000 portunu dinlemeye başladı
          ↓
Nmap 8000 portunu açık olarak tespit etti
          ↓
-sV ile servis tespiti yapıldı
          ↓
HTTP / SimpleHTTPServer / Python 3.13.7
bilgileri görüntülendi
```

Bu deneyden şu sonucu çıkarabiliriz:

> **Bir servisin belirli bir portu dinlemesi, o portun Nmap tarafından açık olarak görülmesini sağlayabilir.**

---

# 🛑 6. Servisi Durdurma

Test tamamlandıktan sonra Python sunucusunun çalıştığı terminalde:

```text
Ctrl + C
```

kombinasyonunu kullanarak sunucuyu durdurdum.

Sunucu durduğunda artık 8000 numaralı portu dinleyen bu servis çalışmıyor olacaktır.

Bu durum, port ve servis arasındaki ilişkiyi göstermektedir.

---

# 🛡️ Siber Güvenlik Açısından Önemi

Service detection, güvenlik testlerinde önemli bir adımdır.

Bir güvenlik uzmanı yalnızca:

> "Bu port açık mı?"

sorusunu sormaz.

Aynı zamanda:

> "Bu portta hangi servis çalışıyor ve hangi sürüm kullanılıyor?"

sorusunu da araştırır.

Örneğin:

```text
8000/tcp open http
```

bilgisi bize bir web hizmetinin erişilebilir olduğunu gösterebilir.

`-sV` ile elde edilen servis ve sürüm bilgileri ise sistem hakkında daha ayrıntılı bilgi sağlayabilir.

Ancak bir servisin veya sürümün tespit edilmesi **tek başına güvenlik açığı olduğu anlamına gelmez**.

Güvenlik değerlendirmesi için servis yapılandırması, erişim durumu, güncellik ve bilinen güvenlik sorunları gibi başka faktörlerin de incelenmesi gerekir.

---

# 🧠 Öğrendiklerim

Bu uygulamada benim için en önemli nokta port ile servis arasındaki ilişkiyi pratik olarak görmek oldu.

Önce sistemimde açık port bulunmuyordu.

Daha sonra:

```bash
python3 -m http.server 8000
```

komutu ile yerel bir HTTP servisi başlattım.

Ardından Nmap ile:

```bash
nmap -sV localhost
```

tarama yaptığımda:

```text
8000/tcp open http SimpleHTTPServer 0.6 (Python 3.13.7)
```

sonucunu gördüm.

Ayrıca Nmap taraması sırasında Python HTTP sunucusunun terminalinde çeşitli HTTP istekleri oluştuğunu gözlemledim.

Böylece:

> **Servis çalıştır → portu dinle → Nmap ile tespit et → servis ve sürümü belirle**

ilişkisini uygulamalı olarak öğrenmiş oldum.

---

## ✅ Kontrol Listesi

* [x] `-sV` parametresinin ne işe yaradığını öğrendim.
* [x] Service Version Detection kavramını öğrendim.
* [x] Yerel bir HTTP sunucusu çalıştırdım.
* [x] 8000 numaralı portun açık olduğunu Nmap ile tespit ettim.
* [x] Nmap'in HTTP servisini tespit ettiğini gördüm.
* [x] Python SimpleHTTPServer bilgisini gördüm.
* [x] Python sürüm bilgisini gördüm.
* [x] Nmap taraması sırasında HTTP isteklerini gözlemledim.
* [x] HTTP `200`, `404` ve `501` yanıtlarını gördüm.
* [x] Port ve servis arasındaki ilişkiyi uygulamalı olarak öğrendim.
* [x] Açık portun tek başına güvenlik açığı olmadığını tekrar gördüm.

---

## ➡️ Sonraki Bölüm

Bir sonraki bölümde Nmap'in **işletim sistemi tespiti** özelliğini inceleyeceğim:

```bash
nmap -O localhost
```

Bu komut ile Nmap'in hedef sistemin işletim sistemi hakkında nasıl tahminde bulunduğunu inceleyeceğim.
