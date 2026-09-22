# 🐧 03 — Linux Dosya Sistemi

## 📌 Amaç

Bu bölümde Linux dosya sisteminin temel yapısını inceleyerek önemli dizinlerin ne amaçla kullanıldığını öğrendim.

Linux'ta dosya sistemi Windows'taki gibi `C:\` veya `D:\` sürücülerinden başlamaz.

Linux dosya sistemi **`/` (root directory)** adı verilen kök dizinden başlar.

---

# 🌳 Linux Dosya Sisteminin Temel Yapısı

Basitleştirilmiş şekilde:

```text
/
├── etc
├── home
├── var
│   └── log
├── tmp
├── usr
├── opt
└── root
```

Her dizinin sistem içerisinde farklı bir görevi vardır.

---

# 1. 📍 `pwd`

```bash
pwd
```

### Ne işe yarar?

`pwd` komutu mevcut çalışma dizinimi gösterir.

Herhangi bir işlem yapmadan önce bulunduğum konumu kontrol etmek için kullanılabilir.

### 📸 Ekran Görseli

<img width="180" height="56" alt="image" src="https://github.com/user-attachments/assets/d705e6da-f415-41e8-9e78-e22ddd6cf77d" />


---

# 2. 📋 `ls`

```bash
ls
```

### Ne işe yarar?

Bulunduğum dizinin içerisindeki dosya ve klasörleri listeler.

### 📸 Ekran Görseli

<img width="394" height="78" alt="image" src="https://github.com/user-attachments/assets/b5b5cacf-f85f-4fd6-b32a-17b75e95b161" />


---

# 3. 🔎 `ls -la`

```bash
ls -la
```

Bu komutta:

* `-l` → Ayrıntılı listeleme
* `-a` → Gizli dosya ve klasörleri de gösterme

anlamına gelir.

Bu nedenle `ls -la`, normal `ls` komutuna göre daha fazla bilgi sağlar.

### 📸 Ekran Görseli

<img width="542" height="359" alt="image" src="https://github.com/user-attachments/assets/93d20610-37ce-407b-aff3-be6ae4bcff5d" />


---

# 4. 🌳 Kök Dizin `/`

```bash
cd /
ls
```

Linux dosya sisteminin en üst seviyesidir.

Sistemdeki temel dizinlerin büyük bölümü bu noktadan itibaren bulunur.

### 📸 Ekran Görseli

<img width="538" height="148" alt="image" src="https://github.com/user-attachments/assets/5ba40c16-3658-4e6c-ab00-9977d1ca8fd9" />


---

# 5. ⚙️ `/etc`

```bash
cd /etc
ls
```

`/etc`, sistem ve birçok uygulama için kullanılan **yapılandırma dosyalarının** bulunduğu önemli bir dizindir.

### 🔐 Siber Güvenlik Açısından Önemi

Bir sistemin yapılandırmasını anlamak güvenlik analizi açısından önemlidir.

Yanlış veya güvensiz yapılandırmalar sistemin güvenliğini etkileyebilir.

### 📸 Ekran Görseli

<img width="538" height="328" alt="image" src="https://github.com/user-attachments/assets/5ccf85ba-a9be-4708-a126-2e50b87d1303" />


---

# 6. 👤 `/home`

```bash
cd /home
ls
```

`/home`, normal kullanıcıların kişisel dosyalarının bulunduğu ana dizindir.

Örneğin:

* Belgeler
* İndirilenler
* Kişisel dosyalar
* Kullanıcı ayarları

bu yapı altında bulunabilir.

### 📸 Ekran Görseli

<img width="204" height="116" alt="image" src="https://github.com/user-attachments/assets/aa4c21b8-4f45-4f67-b4d5-90bdc076b3f7" />


---

# 7. 📊 `/var`

```bash
cd /var
ls
```

`/var`, sistem çalışırken değişebilen verilerin tutulduğu dizindir.

Örneğin:

* Loglar
* Önbellekler
* Kuyruklar
* Bazı servis verileri

bu yapı altında bulunabilir.

---

# 8. 📝 `/var/log`

```bash
cd /var/log
ls
```

`/var/log`, Linux sistemlerindeki önemli log dosyalarının bulunduğu dizinlerden biridir.

### 🔐 Siber Güvenlik Açısından Önemi

Loglar, sistem üzerinde gerçekleşen olayların incelenmesinde önemli bir kaynaktır.

Güvenlik analizlerinde:

* Kullanıcı girişleri
* Servis olayları
* Sistem hataları
* Yetkilendirme olayları

gibi bilgiler incelenebilir.

Bu nedenle `/var/log`, **olay inceleme ve güvenlik analizi açısından önemli bir dizindir.**

### 📸 Ekran Görseli

<img width="540" height="214" alt="image" src="https://github.com/user-attachments/assets/633e3606-917e-4b4b-b514-aa1baa374306" />


---

# 9. 🗑️ `/tmp`

```bash
cd /tmp
ls
```

`/tmp`, geçici dosyaların ve geçici çalışma verilerinin tutulabildiği dizindir.

Geçici olması nedeniyle burada bulunan dosyaların kalıcı olması garanti edilmez.

### 🔐 Siber Güvenlik Açısından

Geçici dizinler güvenlik incelemelerinde göz ardı edilmemelidir.

### 📸 Ekran Görseli

<img width="592" height="128" alt="image" src="https://github.com/user-attachments/assets/9696d94f-98e0-455c-a060-682c5871a88a" />


---

# 10. 📦 `/usr`

```bash
cd /usr
ls
```

`/usr`, kullanıcı alanındaki birçok program, kütüphane ve ilgili dosyanın bulunduğu önemli bir dizindir.

### 📸 Ekran Görseli

<img width="595" height="119" alt="image" src="https://github.com/user-attachments/assets/4b1cea7c-4686-4698-a6df-d39a073529b4" />


---

# 11. 🧰 `/opt`

```bash
cd /opt
ls
```

`/opt`, özellikle sistemin standart paket yönetimi dışında kurulabilen bazı isteğe bağlı veya üçüncü taraf yazılımlar için kullanılabilen bir dizindir.

Her sistemde aynı içerikte olması beklenmez.

### 📸 Ekran Görseli

<img width="201" height="98" alt="image" src="https://github.com/user-attachments/assets/8f980478-b8e5-4d74-ae80-f74416e98275" />


---

# 12. 👑 `/root`

```bash
cd /root
ls
```

`/root`, Linux sistemindeki **root kullanıcısının home dizinidir.**

Burada önemli bir ayrım vardır:

> `/root` bir kullanıcı değildir. Root kullanıcısının home dizinidir.

Normal kullanıcıların home dizinleri genellikle:

```text
/home/kullanici_adi
```

şeklindeyken root kullanıcısının home dizini:

```text
/root
```

şeklindedir.

Normal bir kullanıcının `/root` dizinine erişim izni olmayabilir.

### 📸 Ekran Görseli

<img width="248" height="57" alt="image" src="https://github.com/user-attachments/assets/2afdc1d1-6e4c-4249-aaf9-d11608cd0a30" />


---

# 📚 Dizinlerin Özeti

| Dizin      | Temel Amacı                     | Siber Güvenlik Açısından           |
| ---------- | ------------------------------- | ---------------------------------- |
| `/`        | Kök dizin                       | Tüm dosya sisteminin başlangıcı    |
| `/etc`     | Yapılandırma dosyaları          | Sistem yapılandırmalarını inceleme |
| `/home`    | Kullanıcı dosyaları             | Kullanıcı verileri ve izinler      |
| `/var`     | Değişken veriler                | Servis ve sistem verileri          |
| `/var/log` | Loglar                          | Olay ve güvenlik incelemesi        |
| `/tmp`     | Geçici dosyalar                 | Geçici verilerin incelenmesi       |
| `/usr`     | Programlar ve kütüphaneler      | Yazılım bileşenleri                |
| `/opt`     | İsteğe bağlı yazılımlar         | Üçüncü taraf uygulamalar           |
| `/root`    | Root kullanıcısının home dizini | Yetki ve erişim açısından önemli   |

---

# 🔐 `/etc` ve `/var/log` Neden Önemli?

Bu iki dizin özellikle dikkatimi çekti.

### `/etc`

Sistemin nasıl yapılandırıldığını anlamama yardımcı olur.

Servislerin ve sistem bileşenlerinin çeşitli yapılandırmaları burada bulunabilir.

### `/var/log`

Sistemde gerçekleşen olayların kayıtlarını incelememe yardımcı olur.

Bir güvenlik olayının araştırılması sırasında loglar önemli kaynaklardan biri olabilir.

Bu nedenle Linux dosya sistemini bilmek, yalnızca sistem yönetimi açısından değil, **olay inceleme ve güvenlik analizi açısından da önemlidir.**

---

# 🧠 Bu Bölümde Ne Öğrendim?

Bu bölümde Linux dosya sisteminin temel yapısını öğrendim.

Özellikle:

* Linux dosya sisteminin `/` kök dizininden başladığını,
* `/etc` dizininin yapılandırma dosyaları için önemli olduğunu,
* `/home` dizininin kullanıcı dosyalarını içerdiğini,
* `/var` dizininin değişken sistem verilerini tuttuğunu,
* `/var/log` dizininin log analizi açısından önemli olduğunu,
* `/tmp` dizininin geçici dosyalar için kullanıldığını,
* `/usr` dizininde birçok program ve kütüphane bulunduğunu,
* `/opt` dizininin isteğe bağlı yazılımlar için kullanılabildiğini,
* `/root` dizininin root kullanıcısının home dizini olduğunu

öğrendim.

### 🎯 Sonraki Adım

Bir sonraki bölümde Linux üzerinde temel dosya ve dizin işlemlerini uygulayacağım:

```bash
mkdir
touch
cp
mv
rm
cat
less
head
tail
grep
find
```

Ardından kendi `cyber-lab` çalışma alanımı oluşturacağım.
