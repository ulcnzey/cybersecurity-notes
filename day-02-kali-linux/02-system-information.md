# 💻 02 — Kali Linux Sistem Bilgilerini Çıkarma

## 📌 Amaç

Bu bölümde Kali Linux üzerinde temel sistem ve ağ bilgilerini terminal kullanarak çıkardım.

Amaç; çalıştığım sistemin:

* Hangi kullanıcıyla çalıştığını,
* Hostname bilgisini,
* Bulunduğum dizini,
* Linux kernel sürümünü,
* İşletim sistemi bilgisini,
* Ağ arayüzlerini,
* IP adreslerini,
* Varsayılan ağ geçidini

tespit etmek ve bu bilgilerin siber güvenlik açısından neden önemli olduğunu anlamaktır.

---

# 1. 👤 `whoami`

```bash
whoami
```

### Ne işe yarar?

`whoami` komutu, terminalde hangi kullanıcı hesabıyla işlem yaptığımı gösterir.

### Çıktım

```text
zeynep
```

### Öğrendiğim

Bu komut sayesinde sistem üzerinde hangi kullanıcı hesabının aktif olduğunu hızlı bir şekilde kontrol edebilirim.

Siber güvenlik açısından kullanıcı bilgisinin önemli olmasının nedeni, Linux sistemlerinde kullanıcıların farklı yetkilere sahip olabilmesidir.

### 📸 Ekran Görseli

<img width="152" height="50" alt="Adsız tasarım (8)" src="https://github.com/user-attachments/assets/a0234be9-99c7-4591-9994-adc2ec9202b7" />


---

# 2. 🖥️ `hostname`

```bash
hostname
```

### Ne işe yarar?

Bilgisayarın veya sanal makinenin ağ üzerindeki **host adını** gösterir.

### Çıktım

```text
vbox
```

### Öğrendiğim

Hostname, bir sistemi ağ içerisinde tanımlamak için kullanılan isimlerden biridir.

Benim Kali Linux ortamımın hostname değeri `vbox` olarak görünmektedir.

### 📸 Ekran Görseli

<img width="148" height="51" alt="Adsız tasarım (9)" src="https://github.com/user-attachments/assets/a545dda5-27b2-4ed6-92c4-5d29228e23b5" />


---

# 3. 📂 `pwd`

```bash
pwd
```

### Ne işe yarar?

`pwd` (**Print Working Directory**) komutu, terminalde bulunduğum mevcut dizinin tam yolunu gösterir.

### Çıktım

```text
# Buraya kendi terminal çıktımı ekleyeceğim.
```

### Siber Güvenlik Açısından Önemi

Dosya ve klasörlerle çalışırken hangi dizinde olduğumu bilmek önemlidir.

Özellikle sistem dosyalarıyla çalışırken yanlış dizinde işlem yapmak istenmeyen sonuçlara yol açabileceğinden, işlem öncesinde `pwd` ile bulunduğum konumu kontrol etmek iyi bir alışkanlıktır.

### 📸 Ekran Görseli

<img width="170" height="57" alt="Adsız tasarım (10)" src="https://github.com/user-attachments/assets/3ab8c0c4-91c3-4e07-995a-b5694c478fda" />


---

# 4. 🧠 `uname -a`

```bash
uname -a
```

### Ne işe yarar?

`uname -a`, Linux kernel'i hakkında ayrıntılı sistem bilgilerini gösterir.

Çıktıda işletim sistemi türü, hostname, kernel sürümü ve sistem mimarisi gibi bilgiler bulunabilir.

### Çıktım

```text
# Buraya kendi terminal çıktımı ekleyeceğim.
```

### Siber Güvenlik Açısından Önemi

Kernel sürümü ve sistem mimarisi gibi bilgiler, sistemin teknik yapısını anlamaya yardımcı olur.

Güvenlik değerlendirmelerinde sistem bileşenlerinin sürümlerini bilmek önemlidir.

### 📸 Ekran Görseli

<img width="434" height="55" alt="Adsız tasarım (11)" src="https://github.com/user-attachments/assets/e02aa2de-83a5-45a5-a03b-c894b0e4c271" />


---

# 5. 🐧 `/etc/os-release`

```bash
cat /etc/os-release
```

### Ne işe yarar?

Bu dosya, kullanılan Linux dağıtımı hakkında temel bilgileri içerir.

Kali Linux'un sürümü ve dağıtım adı gibi bilgiler buradan görülebilir.

### Çıktım

```text
# Buraya kendi terminal çıktımı ekleyeceğim.
```

### Öğrendiğim

Linux sistemlerinde `/etc` dizini sistem ve uygulama yapılandırmaları açısından önemli bir dizindir.

`/etc/os-release` dosyası ise işletim sistemi hakkında kimlik bilgileri sağlayan dosyalardan biridir.

### 📸 Ekran Görseli

<img width="207" height="141" alt="Adsız tasarım (12)" src="https://github.com/user-attachments/assets/c7a2874e-f7e7-4c6a-92c3-859ec8bd8401" />


---

# 6. 🌐 `ip addr`

```bash
ip addr
```

### Ne işe yarar?

`ip addr` komutu sistemde bulunan ağ arayüzlerini ve bu arayüzlere atanmış IP adreslerini gösterir.

Çıktıda:

* Network interface
* IPv4 adresi
* IPv6 adresi
* MAC adresi
* Interface durumu

gibi bilgiler görülebilir.

### Çıktım

```text
# Buraya kendi terminal çıktımı ekleyeceğim.
```

### Siber Güvenlik Açısından Önemi

Bir bilgisayarın ağ üzerindeki iletişimini anlayabilmek için öncelikle hangi ağ arayüzlerinin bulunduğunu ve hangi IP adreslerinin atanmış olduğunu bilmek gerekir.

### 🔒 Güvenlik Notu

Bu ekran görüntüsünü GitHub'a yüklemeden önce IP ve MAC adresleri gibi bilgilerin public olarak paylaşılmasının uygun olup olmadığını kontrol edeceğim.

### 📸 Ekran Görseli

<img width="265" height="152" alt="Adsız tasarım (13)" src="https://github.com/user-attachments/assets/3e568a51-63c4-4fd1-afef-7eeae9d66d25" />


---

# 7. 🛣️ `ip route`

```bash
ip route
```

### Ne işe yarar?

`ip route` komutu Linux sisteminin ağ trafiğini hangi yollar üzerinden yönlendireceğini gösterir.

Özellikle **default gateway** bilgisini görmek için kullanılır.

### Çıktım

```text
# Buraya kendi terminal çıktımı ekleyeceğim.
```

### `ip addr` ile Farkı

| Komut      | Gösterdiği bilgi                  |
| ---------- | --------------------------------- |
| `ip addr`  | Ağ arayüzleri ve IP adresleri     |
| `ip route` | Ağ trafiğinin yönlendirme yolları |

Basitçe:

**`ip addr` → "Benim ağ bilgilerim ne?"**

**`ip route` → "Başka ağlara giderken hangi yolu kullanıyorum?"**

### 📸 Ekran Görseli

<img width="443" height="61" alt="Adsız tasarım (14)" src="https://github.com/user-attachments/assets/0b86bdbd-fb66-47d7-b710-fc3104fd53b7" />


---

# 🧪 Sistem Bilgileri Özeti

| Bilgi           | Komut                 | Sonuç            |
| --------------- | --------------------- | ---------------- |
| Kullanıcı       | `whoami`              | `zeynep`         |
| Hostname        | `hostname`            | `vbox`           |
| Mevcut dizin    | `pwd`                 | Terminal çıktısı |
| Kernel          | `uname -a`            | Terminal çıktısı |
| İşletim sistemi | `cat /etc/os-release` | Kali Linux       |
| Ağ arayüzleri   | `ip addr`             | Terminal çıktısı |
| Routing         | `ip route`            | Terminal çıktısı |

---

# 🧠 Bu Bölümde Ne Öğrendim?

Bu bölümde Kali Linux sistemimin temel özelliklerini terminal üzerinden incelemeyi öğrendim.

Özellikle:

* Aktif kullanıcıyı,
* Hostname bilgisini,
* Dosya sistemi üzerindeki konumumu,
* Kernel bilgilerini,
* İşletim sistemi bilgilerini,
* Ağ arayüzlerini,
* IP adreslerini,
* Routing ve default gateway bilgisini

terminal üzerinden kontrol edebileceğimi öğrendim.

Bu bilgiler ileride yapılacak sistem ve ağ güvenliği analizlerinin temelini oluşturur.

### 🎯 Sonraki Adım

Bir sonraki bölümde Linux dosya sistemini inceleyeceğim ve özellikle `/etc`, `/home`, `/var`, `/var/log`, `/tmp`, `/usr`, `/opt` ve `/root` dizinlerinin amaçlarını araştıracağım.
