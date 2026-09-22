# 🔎 08 — Nmap'i Tanıma ve Port Taraması

---

## 🎯 Bölümün Amacı

Bu bölümde **Nmap'in ne olduğunu, ne amaçla kullanıldığını ve temel port taraması mantığını** öğreniyorum.

Nmap, siber güvenlik çalışmalarında ağ üzerindeki sistemleri keşfetmek ve sistemlerde bulunan ağ servisleri hakkında bilgi edinmek için kullanılan önemli araçlardan biridir.

> ⚠️ Nmap yalnızca izin verilen sistemlerde kullanılmalıdır. Bu çalışmadaki taramalar kendi Kali Linux sistemim üzerinde gerçekleştirilmiştir.

---

# 🛠️ Nmap Nedir?

**Nmap (Network Mapper)**, ağ keşfi ve port taraması için kullanılan açık kaynaklı bir güvenlik aracıdır.

Siber güvenlikte aşağıdaki amaçlarla kullanılabilir:

* Ağdaki sistemleri keşfetmek
* Açık portları belirlemek
* Çalışan servisleri tespit etmek
* Servis sürümleri hakkında bilgi edinmek
* Ağ yapısını anlamak
* Yetkili güvenlik testleri gerçekleştirmek

Basit olarak Nmap'in çalışma mantığını şöyle düşünebilirim:

```text
Hedef sistem
     ↓
IP adresi
     ↓
Portlar
     ↓
Servisler
     ↓
Nmap ile keşif
```

---

# 🚪 Port Nedir?

Bir bilgisayar üzerinde ağ bağlantılarının gerçekleştiği iletişim noktalarına **port** denir.

Portları bir bilgisayarın ağ üzerindeki kapıları gibi düşünebilirim.

```text
              BİLGİSAYAR
        ┌────────────────────┐
        │                    │
        │ 🚪 22  → SSH       │
        │ 🚪 80  → HTTP      │
        │ 🚪 443 → HTTPS     │
        │ 🚪 3306 → MySQL    │
        │                    │
        └────────────────────┘
```

Burada:

| Kavram    | Açıklama                                          |
| --------- | ------------------------------------------------- |
| IP adresi | Hangi bilgisayara ulaşılacağını belirtir          |
| Port      | Ağ iletişiminin gerçekleştiği iletişim noktasıdır |
| Servis    | Port üzerinden çalışan ağ uygulamasıdır           |

Örneğin:

```text
22/tcp   → SSH
80/tcp   → HTTP
443/tcp  → HTTPS
```

---

# 🟢 Açık Port Nedir?

Bir port üzerinde bağlantıları kabul eden bir ağ servisi çalışıyorsa port **açık (`open`)** olarak görülebilir.

Örneğin:

```text
SSH servisi
     ↓
TCP 22 portunu dinliyor
     ↓
22/tcp → OPEN
```

Ancak:

> **Açık port tek başına güvenlik açığı anlamına gelmez.**

Güvenlik değerlendirmesinde portun yanında hangi servisin çalıştığı, hangi sürümün kullanıldığı, yapılandırmanın nasıl olduğu ve servisin dışarıya açık olup olmadığı gibi bilgiler de incelenir.

---

# 🔴 Kapalı Port Nedir?

Bir bilgisayara ulaşılabiliyor ancak belirli bir port üzerinde bağlantı kabul eden bir servis bulunmuyorsa port **closed** olarak görülebilir.

Örneğin:

```text
Bilgisayar → UP
Port 22 → CLOSED
```

Bu, bilgisayarın kapalı olduğu anlamına gelmez.

Sadece ilgili port üzerinde bağlantı kabul eden bir servis bulunmadığını gösterir.

---

# 🔍 Nmap'in Temel Sonuçları

Nmap taramalarında özellikle şu bilgiler önemlidir:

| Alan      | Anlamı                         |
| --------- | ------------------------------ |
| `PORT`    | Port numarası ve protokol      |
| `STATE`   | Portun durumu                  |
| `SERVICE` | Portla ilişkilendirilen servis |

Örneğin:

```text
PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
443/tcp  open     https
```

Burada:

* `22/tcp` → TCP 22 numaralı port
* `open` → Port bağlantı kabul ediyor
* `ssh` → İlişkili servis SSH

---

# 🧪 İlk Nmap Taraması

İlk olarak kendi Kali Linux bilgisayarımı taradım:

```bash
nmap localhost
```

Bu taramada hedef olarak `localhost` kullanıldı.

`localhost`, kendi bilgisayarımı ifade eden bir hostname'dir.

IPv4 loopback adresi:

```text
127.0.0.1
```

şeklindedir.

---

## 📸 Tarama Sonucum


<img width="568" height="169" alt="image" src="https://github.com/user-attachments/assets/80b80944-7be8-4e13-b44a-a219f8c8e30b" />


Çalıştırılan komut:

```bash
nmap localhost
```

Tarama sonucumda:

```text
Nmap scan report for localhost (127.0.0.1)
Host is up
```

ifadesini gördüm.

Bu, hedef bilgisayarın erişilebilir olduğunu gösteriyor.

Ayrıca:

```text
Not shown: 1000 closed tcp ports (reset)
```

sonucunu aldım.

Yani Nmap'in varsayılan taramasında kontrol ettiği **1000 TCP portunun kapalı** olduğunu gördüm.

### Sonuç

```text
Host → UP
Taranan TCP portları → 1000
Açık port → 0
Kapalı port → 1000
```

---

# 🛡️ Siber Güvenlik Açısından Önemi

Nmap, güvenlik uzmanlarının bir sistemin ağ üzerinden dışarıya sunduğu servisleri anlamasına yardımcı olur.

Örneğin bir sistemde:

```text
22/tcp   open   ssh
80/tcp   open   http
443/tcp  open   https
```

gibi sonuçlar görülürse, sonraki aşamada bu servislerin neden açık olduğu ve hangi sürümlerin çalıştığı incelenebilir.

Bu nedenle Nmap, güvenlik değerlendirmelerinde **keşif ve bilgi toplama aşamasında** önemli bir araçtır.

---

# 🧠 Bu Bölümde Öğrendiklerim

Bu bölümde:

* Nmap'in ne olduğunu,
* Nmap'in hangi amaçlarla kullanıldığını,
* Port kavramını,
* Açık portun ne olduğunu,
* Kapalı portun ne olduğunu,
* `PORT`, `STATE` ve `SERVICE` alanlarını,
* `localhost` kavramını,
* `nmap localhost` komutunun temel kullanımını

öğrendim.

Özellikle şu ilişkiyi öğrendim:

```text
IP adresi
    ↓
Bilgisayar
    ↓
Port
    ↓
Servis
    ↓
Nmap ile keşif
```

---

# 📝 Kendi Notum

> Nmap'i bir bilgisayarın ağ üzerindeki servislerini keşfetmeye yardımcı olan bir araç olarak düşünebilirim. Portları bilgisayarın ağ kapıları gibi düşünmek, `open` ve `closed` durumlarını anlamamı kolaylaştırdı. Ayrıca açık bir portun tek başına güvenlik açığı olmadığını öğrendim.

---

# ✅ Kontrol Listesi

* [x] Nmap'in ne olduğunu öğrendim.
* [x] Nmap'in kullanım amaçlarını öğrendim.
* [x] Port kavramını öğrendim.
* [x] Açık ve kapalı port kavramlarını öğrendim.
* [x] `PORT`, `STATE`, `SERVICE` alanlarını öğrendim.
* [x] `localhost` kavramını öğrendim.
* [x] `nmap localhost` komutunu çalıştırdım.
* [x] Tarama sonucunu yorumladım.

---

## ➡️ Sonraki Bölüm

Bir sonraki bölümde **İlk Nmap Taraması** uygulamasını gerçekleştireceğim.

Öncelikle:

```bash
nmap localhost
```

ardından:

```bash
nmap 127.0.0.1
```

komutlarını çalıştırarak iki hedefi karşılaştıracağım.

Ardından şu soruyu cevaplayacağım:

> **`localhost` ile `127.0.0.1` arasında ne fark vardır ve neden Nmap sonuçları aynı veya benzer olabilir?**
