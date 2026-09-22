# 13 - Açık Portların Yorumlanması

## 🎯 Görevin Amacı

Bu bölümde Nmap taraması sonucunda bulunan açık portların nasıl yorumlanacağını öğrenmek amaçlanmıştır.

Bir portun açık olması tek başına güvenlik açığı olduğu anlamına gelmez. Açık port, öncelikle o port üzerinde bir servisin bağlantı kabul ettiğini gösterir.

Güvenlik değerlendirmesinde asıl önemli olan; **hangi servisin çalıştığı, hangi sürümün kullanıldığı, servisin nasıl yapılandırıldığı ve gerçekten erişilebilir olmasının gerekli olup olmadığıdır.**

---

## 🧠 Açık Port Nedir?

Bilgisayarlar ağ üzerinden farklı servislerle iletişim kurabilmek için portları kullanır.

Basit şekilde:

```text
IP adresi → Hangi bilgisayar?
Port      → Hangi iletişim noktası?
Servis    → Bu noktada hangi uygulama çalışıyor?
```

Örneğin:

```text
192.168.1.10:22
```

ifadesinde:

* `192.168.1.10` → hedef bilgisayarın IP adresi
* `22` → port numarası
* Port 22 üzerinde çalışan servis genellikle **SSH** olabilir.

---

## 🔎 Örnek Nmap Çıktısı

Gerçek bir sistemden alınmış gibi bir sonuç üretmek yerine, aşağıdaki çıktıyı **öğrenme amaçlı örnek senaryo** olarak değerlendireceğim:

```text
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
443/tcp  open  https
```

Bu çıktı, üç farklı TCP portunun açık olduğunu göstermektedir.

---

## 📊 Portları Tek Tek İnceleyelim

| Port    | Servis | Genel kullanım       |
| ------- | ------ | -------------------- |
| 22/tcp  | SSH    | Uzak sistem yönetimi |
| 80/tcp  | HTTP   | Web trafiği          |
| 443/tcp | HTTPS  | Şifreli web trafiği  |

### 🔹 22/tcp - SSH

SSH, uzak bir bilgisayara güvenli şekilde bağlanmak ve sistemi yönetmek için kullanılan bir protokoldür.

Port 22'nin açık olması:

```text
22/tcp open ssh
```

şeklinde görülürse, hedef sistemde SSH servisine erişilebildiği düşünülebilir.

Ancak bundan doğrudan:

> "Sistem güvenlik açığı içeriyor."

sonucu çıkarılamaz.

Güvenlik değerlendirmesinde daha sonra SSH servisinin sürümü, yapılandırması, erişim kısıtlamaları ve kimlik doğrulama mekanizmaları gibi bilgiler incelenebilir.

---

### 🔹 80/tcp - HTTP

Port 80 genellikle HTTP web trafiği için kullanılır.

Örneğin bir web sunucusu:

```text
80/tcp open http
```

şeklinde görünebilir.

Bu durumda hedef sistemde bir web servisi bulunabileceği anlaşılır.

Ancak web servisinin bulunması tek başına güvenlik açığı değildir.

Web uygulamasının kendisi, sunucu yapılandırması, kullanılan yazılımlar ve sürümler ayrıca değerlendirilmelidir.

---

### 🔹 443/tcp - HTTPS

Port 443 genellikle HTTPS bağlantıları için kullanılır.

HTTPS, HTTP iletişiminin TLS kullanılarak korunmasını sağlar.

Örneğin:

```text
443/tcp open https
```

sonucu bir web servisinin HTTPS üzerinden erişilebilir olduğunu gösterebilir.

Burada da yalnızca portun açık olması güvenlik açığı anlamına gelmez.

Güvenlik değerlendirmesinde TLS yapılandırması, sertifikalar, kullanılan web teknolojileri ve uygulamanın kendisi gibi farklı noktalar incelenebilir.

---

# 🛡️ Açık Port Neden Güvenlik Açığı Değildir?

Bu bölümde öğrendiğim en önemli noktalardan biri budur.

Bir portun açık olması yalnızca:

> **"Bu port üzerinde bağlantı kabul eden bir servis var."**

anlamına gelir.

Örneğin bir web sunucusunun 443 numaralı portunun açık olması çoğu durumda beklenen bir durumdur. Çünkü kullanıcıların HTTPS üzerinden web sitesine bağlanabilmesi gerekir.

Bu nedenle:

```text
Açık port
   ↓
Çalışan servis
   ↓
Servisin amacı
   ↓
Yapılandırması
   ↓
Sürümü
   ↓
Güvenlik durumu
```

şeklinde değerlendirme yapılmalıdır.

---

## 🔍 Bir Güvenlik Uzmanı Neye Bakar?

Bir port açık bulunduğunda yalnızca port numarasına bakmak yeterli değildir.

Örneğin:

```text
22/tcp open ssh
```

sonucunu gördüğümüzde şu sorular sorulabilir:

1. SSH servisinin açık olması gerekli mi?
2. Hangi SSH sürümü kullanılıyor?
3. Servis dış ağlardan erişilebilir mi?
4. Kimlik doğrulama nasıl yapılıyor?
5. Root kullanıcısıyla doğrudan giriş mümkün mü?
6. Güvenlik politikaları doğru yapılandırılmış mı?

Buradaki amaç hemen saldırı gerçekleştirmek değil, **servisin güvenlik açısından doğru yapılandırılıp yapılandırılmadığını anlamaktır.**

---

## 🧩 Port + Servis + Sürüm İlişkisi

Önceki bölümde `-sV` parametresini kullanmıştım.

Örneğin:

```bash
nmap -sV localhost
```

komutu açık portlarda çalışan servisleri ve mümkün olduğunda sürüm bilgilerini belirlemeye çalışır.

Örnek olarak:

```text
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH ...
80/tcp   open  http    Apache ...
443/tcp  open  https   nginx ...
```

gibi bir sonuç elde edilebilir.

Bu durumda güvenlik değerlendirmesi:

```text
Port
 ↓
Servis
 ↓
Sürüm
 ↓
Yapılandırma
 ↓
Güvenlik değerlendirmesi
```

şeklinde ilerleyebilir.

---

## ⚠️ Önemli Kavram: Attack Surface

**Attack surface (saldırı yüzeyi)**, bir sistemin dışarıdan erişilebilen ve güvenlik açısından değerlendirilmesi gereken noktalarının genelini ifade eder.

Açık portlar saldırı yüzeyinin parçalarından biri olabilir.

Örneğin:

```text
22 → SSH
80 → HTTP
443 → HTTPS
```

gibi birçok servis dışarıdan erişilebilir durumdaysa, güvenlik uzmanı bu servisleri ve yapılandırmalarını değerlendirebilir.

Ancak burada da önemli bir ayrım vardır:

> **Daha fazla açık port, otomatik olarak daha fazla güvenlik açığı anlamına gelmez.**

Her açık servis kendi amacı ve yapılandırması kapsamında değerlendirilmelidir.

---

## 🧪 Bu Çalışmada Neden Gerçek Açık Port Kullanmadım?

Kendi Kali Linux sistemimde daha önce yaptığım:

```bash
nmap localhost
```

taramasında:

```text
Not shown: 1000 closed tcp ports (reset)
```

sonucunu almıştım.

Yani o anda localhost üzerinde Nmap'in varsayılan olarak kontrol ettiği 1000 TCP portunun tamamı kapalıydı.

Daha sonra eğitim amacıyla Python ile geçici bir HTTP sunucusu çalıştırarak 8000 numaralı portun açıldığını gözlemledim.

Bu çalışma sayesinde:

```text
Servis çalıştırıldı
       ↓
Port 8000 üzerinde dinleme başladı
       ↓
Nmap portu açık olarak gördü
       ↓
-sV ile HTTP servisi tespit edildi
```

ilişkisini uygulamalı olarak gözlemledim.

Bu nedenle açık port kavramını yalnızca teorik olarak değil, kendi laboratuvar ortamımda da görmüş oldum.

---

## 📝 Kendi Öğrendiklerim

Bu bölümde açık bir portun tek başına güvenlik açığı olmadığını öğrendim.

Bir port açık olduğunda bunun arkasında çalışan servisin ne olduğu, hangi sürümün kullanıldığı, servisin gerekli olup olmadığı ve nasıl yapılandırıldığı gibi bilgiler daha önemlidir.

Örneğin 443 numaralı portun açık olması HTTPS kullanan bir web sitesinde normal olabilir.

Bu nedenle bir Nmap sonucunu değerlendirirken sadece:

```text
"Port açık."
```

demek yerine:

```text
"Bu port neden açık?"
"Burada hangi servis çalışıyor?"
"Bu servisin sürümü nedir?"
"Bu servis nasıl yapılandırılmış?"
"Dışarıdan erişilmesi gerekiyor mu?"
```

gibi sorular sormak gerektiğini öğrendim.

Bu yaklaşımın siber güvenlikte yalnızca araç kullanmaktan daha önemli olduğunu düşünüyorum.

---

## ✅ Kontrol Listesi

* [x] Açık port kavramını öğrendim.
* [x] Port ile servis arasındaki ilişkiyi anladım.
* [x] 22, 80 ve 443 numaralı portların yaygın kullanım alanlarını öğrendim.
* [x] Açık portun tek başına güvenlik açığı olmadığını öğrendim.
* [x] Servis ve sürüm bilgisinin neden önemli olduğunu öğrendim.
* [x] Attack surface kavramını öğrendim.
* [x] Nmap sonuçlarını yalnızca port numarasına bakarak yorumlamamak gerektiğini öğrendim.
* [x] Kendi laboratuvarımda 8000 numaralı portun bir servis çalıştırıldığında nasıl açık hale geldiğini gözlemledim.

---

## 🚀 Sonraki Adım

Bir sonraki bölümde farklı Nmap parametrelerini inceleyeceğim.

Özellikle:

```text
-sV
-O
-p
-sT
-sn
```

parametrelerinin ne işe yaradığını ve hangi durumlarda kullanılabileceğini öğreneceğim.
