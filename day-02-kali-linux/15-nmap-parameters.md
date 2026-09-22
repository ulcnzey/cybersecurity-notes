# 15 - Nmap Parametreleri

## 🎯 Görevin Amacı

Bu bölümde Nmap'in sık kullanılan bazı parametrelerini öğrenmek ve hangi amaçlarla kullanıldıklarını anlamak amaçlanmıştır.

Nmap'i etkili kullanabilmek için yalnızca komutun çalıştırılması değil, kullanılan parametrenin taramayı nasıl değiştirdiğinin anlaşılması önemlidir.

Bu bölümde aşağıdaki parametreler incelenmiştir:

```text
-sV
-O
-p
-sT
-sn
```

---

# 1. `-sV` — Service Version Detection

```bash
nmap -sV localhost
```

`-sV` parametresi, açık portlarda çalışan servisleri ve mümkün olduğunda bu servislerin sürüm bilgilerini belirlemeye çalışır.

Örneğin:

```text
PORT     STATE SERVICE VERSION
8000/tcp open  http    SimpleHTTPServer 0.6 (Python 3.13.7)
```

şeklinde bir sonuç elde edilebilir.

Burada:

* `8000/tcp` → port
* `open` → port açık
* `http` → tespit edilen servis
* `SimpleHTTPServer 0.6` → servis bilgisi
* `Python 3.13.7` → tespit edilen teknoloji/sürüm bilgisi

### 🔐 Güvenlik açısından neden önemli?

Bir servisin yalnızca çalıştığını bilmek yeterli olmayabilir.

Kullanılan sürüm bilgisi, güvenlik değerlendirmesinde hangi yazılımın ve hangi sürümün bulunduğunu anlamaya yardımcı olabilir.

> **Not:** Bir sürümün tespit edilmesi tek başına güvenlik açığı olduğu anlamına gelmez.

---

# 2. `-O` — Operating System Detection

```bash
nmap -O localhost
```

`-O` parametresi işletim sistemi tespiti için kullanılır.

Nmap işletim sistemini doğrudan bilgisayardan okumaz. Ağ üzerinden alınan yanıtları inceleyerek işletim sistemi hakkında bir tahminde bulunmaya çalışır.

Önceki çalışmamda:

```text
Too many fingerprints match this host to give specific OS details
```

sonucunu almıştım.

Bu sonuç Nmap'in gözlemlediği davranışların birden fazla işletim sistemi parmak iziyle eşleştiğini ve belirli bir işletim sistemi için yeterli kesinlik bulunmadığını gösteriyordu.

### 🔐 Güvenlik açısından neden önemli?

İşletim sistemi bilgisi, hedefin teknik yapısı hakkında bağlam sağlayabilir.

Örneğin:

```text
İşletim sistemi
      ↓
Muhtemel servisler
      ↓
Teknoloji altyapısı
      ↓
Güvenlik değerlendirmesi
```

şeklinde bir ilişki kurulabilir.

---

# 3. `-p` — Belirli Portları Tarama

Nmap varsayılan olarak belirli bir port kümesini tarayabilir.

Ancak yalnızca belirli bir portu veya portları kontrol etmek istediğimizde `-p` parametresini kullanabiliriz.

Örneğin:

```bash
nmap -p 80 localhost
```

Bu komut yalnızca **80 numaralı TCP portunu** tarar.

Birden fazla port için:

```bash
nmap -p 22,80,443 localhost
```

kullanılabilir.

Bir port aralığı için:

```bash
nmap -p 20-100 localhost
```

kullanılabilir.

### 🧠 Neden kullanılır?

Her zaman bütün portları taramak gerekmeyebilir.

Belirli bir servisin çalışıp çalışmadığını kontrol etmek istediğimizde `-p` ile hedef portu belirleyebiliriz.

Örneğin:

```text
80  → HTTP
443 → HTTPS
22  → SSH
```

gibi belirli servisleri kontrol etmek mümkün olur.

---

# 4. `-sT` — TCP Connect Scan

```bash
nmap -sT localhost
```

`-sT`, TCP bağlantı taraması gerçekleştirmek için kullanılır.

Bu yöntemde Nmap, hedef port ile normal bir TCP bağlantısı kurmaya çalışır.

Basitleştirilmiş şekilde:

```text
Nmap
  ↓
TCP bağlantı isteği
  ↓
Hedef port
  ↓
Bağlantı kurulabiliyor mu?
```

Port bağlantıyı kabul ederse portun açık olduğu anlaşılabilir.

### 🧠 Neden önemlidir?

TCP Connect Scan, TCP bağlantısının kurulup kurulamadığını kontrol ederek portun durumunu anlamaya yardımcı olur.

Bu tarama yöntemi özellikle Nmap'in diğer tarama yöntemlerini anlamak açısından önemlidir.

---

# 5. `-sn` — Host Discovery

```bash
nmap -sn 192.168.1.0/24
```

`-sn` parametresi port taraması yapmak yerine **host discovery** amacıyla kullanılır.

Yani temel soru:

> "Bu ağda hangi cihazlar aktif?"

olur.

Örneğin bir ağda:

```text
192.168.1.1
192.168.1.5
192.168.1.10
192.168.1.20
```

gibi aktif cihazlar bulunabilir.

`-sn` ile amaç öncelikle hangi hedeflerin erişilebilir olduğunu belirlemektir.

### ⚠️ Önemli

Bu tür ağ taramaları yalnızca **izin verilen ağlarda ve sistemlerde** gerçekleştirilmelidir.

Kendi bilgisayarım, kendi sanal makinelerim veya yetkili laboratuvar ortamları güvenli çalışma alanlarıdır.

---

# 📊 Parametreleri Karşılaştıralım

| Parametre | Temel amacı             | Örnek                     |
| --------- | ----------------------- | ------------------------- |
| `-sV`     | Servis ve sürüm tespiti | `nmap -sV localhost`      |
| `-O`      | İşletim sistemi tespiti | `nmap -O localhost`       |
| `-p`      | Belirli portları tarama | `nmap -p 80 localhost`    |
| `-sT`     | TCP Connect Scan        | `nmap -sT localhost`      |
| `-sn`     | Aktif host keşfi        | `nmap -sn 192.168.1.0/24` |

---

# 🧠 Hangi Durumda Hangisini Kullanırım?

Bunu ezberlemek yerine soruya göre düşünmek daha kolaydır.

### "Hangi servis çalışıyor?"

```bash
nmap -sV hedef
```

### "İşletim sistemi hakkında bilgi edinmek istiyorum."

```bash
nmap -O hedef
```

### "Sadece 80 numaralı porta bakmak istiyorum."

```bash
nmap -p 80 hedef
```

### "TCP bağlantısıyla portları kontrol etmek istiyorum."

```bash
nmap -sT hedef
```

### "Ağda hangi cihazlar aktif?"

```bash
nmap -sn hedef
```

---

# 🔗 Parametreleri Birlikte Kullanmak

Nmap parametreleri bazı durumlarda birlikte kullanılabilir.

Örneğin:

```bash
nmap -p 80,443 -sV localhost
```

Bu komut:

1. Yalnızca 80 ve 443 numaralı portlara bakar.
2. Açık portlarda çalışan servisleri ve mümkün olduğunda sürümlerini belirlemeye çalışır.

Burada komutun mantığı:

```text
-p 80,443
     ↓
Hangi portlar?
     +
-sV
     ↓
Bu portlarda hangi servis çalışıyor?
```

şeklindedir.

---

# 🛡️ Savunma Perspektifi

Nmap parametrelerini öğrenmek yalnızca tarama yapmak için önemli değildir.

Savunma açısından da bir sistem yöneticisi kendi sisteminin dışarıdan nasıl göründüğünü kontrol edebilir.

Örneğin yetkili bir sistem yöneticisi:

```bash
nmap -sV kendi-sistemim
```

ile dışarıdan erişilebilir servisler hakkında bilgi edinebilir.

Böylece:

* Gereksiz servisler çalışıyor mu?
* Beklenmeyen portlar açık mı?
* Hangi servisler dışarıya açık?
* Sistem dışarıdan nasıl görünüyor?

gibi sorular değerlendirilebilir.

Bu yaklaşım **savunmacı güvenlik (defensive security)** açısından önemlidir.

---

# ⚠️ Yetkilendirme

Nmap güçlü bir ağ keşif aracıdır.

Bu nedenle yalnızca:

* Kendi bilgisayarım
* Kendi sanal makinelerim
* Kendi laboratuvar ortamım
* Tarama yapma iznim bulunan sistemler

üzerinde kullanılmalıdır.

İzin olmadan başka kişilerin veya kurumların sistemlerini taramak doğru değildir.

---

# 📝 Kendi Öğrendiklerim

Bu bölümde Nmap'in farklı parametrelerinin farklı amaçlara hizmet ettiğini öğrendim.

Özellikle:

```text
-sV → Servis / sürüm
-O  → İşletim sistemi
-p  → Belirli port
-sT → TCP bağlantı taraması
-sn → Aktif host keşfi
```

şeklinde temel bir ayrım yapabiliyorum.

Ayrıca Nmap kullanırken sadece komutu ezberlemek yerine **"Ben şu anda neyi öğrenmek veya kontrol etmek istiyorum?"** sorusunu sormam gerektiğini öğrendim.

---

## ✅ Kontrol Listesi

* [x] `-sV` parametresini öğrendim.
* [x] `-O` parametresini öğrendim.
* [x] `-p` parametresini öğrendim.
* [x] `-sT` parametresini öğrendim.
* [x] `-sn` parametresini öğrendim.
* [x] Parametrelerin amaçlarını birbirinden ayırabiliyorum.
* [x] Birden fazla parametrenin birlikte kullanılabileceğini öğrendim.
* [x] Nmap kullanımında yetkilendirmenin önemini öğrendim.

---

## 🚀 Sonraki Adım

Bir sonraki bölümde Nmap'i yalnızca saldırı perspektifinden değil, **savunma perspektifinden** ele alacağım.

Kendi sistemimin dışarıdan görünen ağ yüzeyini anlamanın neden önemli olduğunu inceleyeceğim.
