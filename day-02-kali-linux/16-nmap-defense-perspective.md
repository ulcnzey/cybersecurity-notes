# 16. Nmap – Savunma Perspektifi

## 🎯 Görevin Amacı

Nmap yalnızca saldırganların kullandığı bir araç değildir. Bir sistem yöneticisi veya siber güvenlik uzmanı da Nmap'i kendi sistemlerinin dışarıdan nasıl göründüğünü anlamak için kullanabilir.

Bu bölümde Nmap sonuçlarına **savunma (defensive security)** açısından bakacağım.

---

## 🔐 Nmap Savunma Açısından Neden Önemlidir?

Bir sistemde çalışan servisler ve açık portlar, sistemin dışarıdan görülebilen **saldırı yüzeyinin (attack surface)** bir parçasıdır.

Örneğin:

```text
22/tcp   open   ssh
80/tcp   open   http
443/tcp  open   https
```

Bu sonuç bize sistemde SSH, HTTP ve HTTPS servislerinin erişilebilir olduğunu gösterir.

Ancak:

> Açık port = güvenlik açığı demek değildir.

Açık portun arkasındaki servisin sürümü, yapılandırması, erişim kısıtlamaları ve gerçekten gerekli olup olmadığı da değerlendirilmelidir.

---

## 🧩 Savunma Perspektifinden Sorulması Gereken Sorular

Bir güvenlik uzmanı açık bir port gördüğünde sadece "Bu port açık." demez.

Şu soruları sorar:

### 1. Bu servis gerekli mi?

Örneğin kullanılmayan bir SSH veya FTP servisi çalışıyorsa kapatılması değerlendirilebilir.

Gereksiz servislerin azaltılması saldırı yüzeyini küçültebilir.

### 2. Servise kimler erişebiliyor?

Bir servis yalnızca yerel ağdan mı erişilebilir?

Yoksa internet üzerinden de erişilebilir durumda mı?

Erişim kapsamı güvenlik açısından önemlidir.

### 3. Servisin sürümü nedir?

Daha önce `-sV` parametresini kullanarak servis ve sürüm bilgisi elde etmiştim.

Örneğin:

```bash
nmap -sV localhost
```

Bir servis ve sürümü tespit edildiğinde, güvenlik uzmanı ilgili yazılımın güncel olup olmadığını ve bilinen güvenlik sorunlarının bulunup bulunmadığını araştırabilir.

### 4. Servis güvenli yapılandırılmış mı?

Bir servisin güncel olması tek başına yeterli değildir.

Kimlik doğrulama, erişim izinleri, şifreleme ve yapılandırma ayarları da önemlidir.

---

## 🛡️ Açık Portları Azaltmak

Savunma açısından temel yaklaşım:

```text
Gereksiz servis
       ↓
Kapat / kaldır
       ↓
Saldırı yüzeyi azalır
```

Gerekli olan servisler için ise:

```text
Gerekli servis
       ↓
Güncel tut
       ↓
Güvenli yapılandır
       ↓
Erişimi sınırlandır
       ↓
Logları izle
```

---

## 🔎 Nmap ile Savunma Amaçlı Kontrol

Kendi sistemimde çalışan servisleri kontrol etmek için örneğin:

```bash
nmap -sV localhost
```

kullanabilirim.

Belirli portları kontrol etmek için:

```bash
nmap -p 22,80,443 localhost
```

kullanabilirim.

Belirli bir portun açık olup olmadığını kontrol etmek için:

```bash
nmap -p 8000 localhost
```

gibi bir tarama yapılabilir.

Bu kontroller sayesinde sistemin hangi servisleri dışarıya sunduğu görülebilir.

---

## 🧪 Bu Çalışmada Ne Gördüm?

Bu çalışmada kendi Kali Linux ortamımda Python ile basit bir HTTP sunucusu çalıştırdım:

```bash
python3 -m http.server 8000
```

Daha sonra:

```bash
nmap -sV localhost
```

komutunu kullandım.

Sonuçta:

```text
PORT     STATE SERVICE VERSION
8000/tcp open  http    SimpleHTTPServer 0.6 (Python 3.13.7)
```

sonucunu gördüm.

Burada önemli bağlantıyı gözlemledim:

```text
Python HTTP Server
        ↓
8000 portunda dinleme
        ↓
Nmap portu açık olarak görüyor
        ↓
-sV servisi tanımlıyor
```

Bu deney sayesinde bir servisin çalışmasının ağ üzerindeki görünürlüğünü doğrudan gözlemlemiş oldum.

---

## ⚠️ Güvenlik Açısından Önemli Nokta

Bir sistemde çok sayıda açık port bulunması otomatik olarak sistemin güvensiz olduğu anlamına gelmez.

Güvenlik değerlendirmesinde:

* Açık port
* Çalışan servis
* Servis sürümü
* Yapılandırma
* Erişim kontrolü
* Güncellik
* Ağ segmentasyonu
* Loglama ve izleme

gibi birçok faktör birlikte değerlendirilir.

Bu nedenle Nmap sonucu **tek başına güvenlik açığı raporu değildir**.

---

## 🧠 Kendi Öğrendiklerim

Bu bölümde Nmap'in sadece saldırı amacıyla düşünülmemesi gerektiğini öğrendim.

Bir güvenlik uzmanı Nmap'i kendi sistemini tanımak ve dışarıdan görülebilen servisleri kontrol etmek için de kullanabilir.

Özellikle şu bağlantıyı öğrendim:

> **Açık port → çalışan servis → saldırı yüzeyi → güvenlik değerlendirmesi**

Ayrıca açık bir portun tek başına güvenlik açığı olmadığını, asıl değerlendirmenin servis ve yapılandırma üzerinden yapılması gerektiğini öğrendim.

---

## ✅ Bölüm Kontrol Listesi

* [x] Nmap'in savunma açısından kullanımını öğrendim.
* [x] Saldırı yüzeyi kavramını öğrendim.
* [x] Açık port ile güvenlik açığı arasındaki farkı öğrendim.
* [x] Gereksiz servislerin neden risk oluşturabileceğini öğrendim.
* [x] Servislerin güncel ve güvenli yapılandırılmasının önemini öğrendim.
* [x] Kendi Kali ortamımda Nmap ile çalışan HTTP servisini gözlemledim.

---

## 📌 Sonraki Bölüm

Bir sonraki bölümde Nmap ile yapılan keşif çalışmalarını daha düzenli şekilde raporlayacağım.

**Sonraki konu:** Küçük Keşif (Reconnaissance) Raporu
