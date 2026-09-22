# 14 - Risk Analizi Senaryosu

## 🎯 Görevin Amacı

Bu bölümde Nmap taraması sonucunda elde edilen bilgilerin güvenlik açısından nasıl değerlendirilebileceğini öğrenmek amaçlanmıştır.

Buradaki amaç bir sisteme saldırmak değil, **tespit edilen servislerin oluşturabileceği güvenlik risklerini düşünmeyi öğrenmektir.**

Bir güvenlik uzmanı için Nmap çıktısı yalnızca teknik bir sonuç değildir. Bu sonuçlar, hedef sistemin saldırı yüzeyini anlamaya yardımcı olan bilgiler sağlar.

---

# 🧩 Örnek Senaryo

Yetkili olduğumuz bir sunucuda aşağıdaki Nmap sonucunu aldığımızı varsayalım:

```text
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
443/tcp  open  https
445/tcp  open  microsoft-ds
```

Bu sonucu tek başına "sistem saldırıya açık" şeklinde yorumlamak doğru değildir.

Bunun yerine her servisi ayrı ayrı değerlendirmek gerekir.

---

## 🔎 22/tcp - SSH

```text
22/tcp   open  ssh
```

SSH, uzak sistem yönetimi için kullanılan bir protokoldür.

SSH'nin açık olması tek başına bir güvenlik açığı değildir. Sunucuyu uzaktan yönetmek için gerekli olabilir.

Ancak güvenlik değerlendirmesinde şu sorular sorulabilir:

* SSH gerçekten gerekli mi?
* Kimlerin erişmesine izin veriliyor?
* Güçlü kimlik doğrulama kullanılıyor mu?
* Servisin sürümü güncel mi?
* Root ile doğrudan giriş engellenmiş mi?
* Erişim yalnızca belirli IP adresleriyle sınırlandırılabilir mi?

### Değerlendirme

SSH gerekli ve doğru yapılandırılmışsa açık olması normal olabilir.

Risk, servisin kendisinden çok **yanlış yapılandırma veya güncel olmayan yazılım** gibi faktörlerle ilişkili olabilir.

---

# ⚠️ 23/tcp - Telnet

```text
23/tcp   open  telnet
```

Telnet, uzak sistemlere bağlantı sağlamak için kullanılan eski bir protokoldür.

Telnet'in önemli güvenlik problemlerinden biri, iletişimin güvenli şekilde şifrelenmemesidir.

Bu nedenle yetkili bir güvenlik değerlendirmesinde Telnet kullanılıyorsa bunun gerçekten gerekli olup olmadığı özellikle incelenebilir.

### Değerlendirme

```text
Telnet açık
      ↓
Servisin gerekli olup olmadığı araştırılır
      ↓
Kullanılıyorsa güvenli alternatifler değerlendirilir
```

Burada amaç doğrudan sisteme saldırmak değil, **güvenlik açısından gereksiz veya zayıf servisleri belirlemektir.**

---

# 🌐 80/tcp - HTTP

```text
80/tcp   open  http
```

Port 80 genellikle HTTP web trafiği için kullanılır.

Bir web sunucusunun 80 numaralı portunun açık olması normal olabilir.

Ancak güvenlik değerlendirmesinde:

* Web sunucusu hangi yazılımı kullanıyor?
* Hangi sürüm kullanılıyor?
* HTTP neden açık?
* HTTPS'e yönlendirme yapılıyor mu?
* Web uygulamasının yapılandırması nasıl?
* Gereksiz servisler çalışıyor mu?

gibi sorular incelenebilir.

### Değerlendirme

HTTP'nin açık olması otomatik olarak güvenlik açığı değildir.

Burada önemli olan **web servisinin nasıl yapılandırıldığıdır.**

---

# 🔐 443/tcp - HTTPS

```text
443/tcp   open  https
```

443 numaralı port genellikle HTTPS için kullanılır.

HTTPS, web iletişiminin TLS üzerinden korunmasını sağlar.

Burada güvenlik değerlendirmesinde örneğin:

* TLS yapılandırması
* Sertifika durumu
* Kullanılan web sunucusu
* Web uygulamasının güvenliği

gibi konular incelenebilir.

### Değerlendirme

443 numaralı portun açık olması çoğu web sunucusu için beklenen bir durumdur.

Dolayısıyla:

> "443 açık → sistem güvensiz."

şeklinde bir çıkarım yapılmaz.

---

# 🗂️ 445/tcp - SMB

```text
445/tcp   open  microsoft-ds
```

Port 445 genellikle SMB (Server Message Block) ile ilişkilidir.

SMB; dosya, yazıcı ve çeşitli ağ kaynaklarının paylaşımında kullanılabilir.

Kurumsal Windows ağlarında SMB önemli bir servis olabilir.

Ancak dışarıdan gereksiz şekilde erişilebilir olması güvenlik açısından ayrıca değerlendirilmelidir.

Sorulabilecek sorular:

* SMB'nin dışarıdan erişilebilir olması gerekiyor mu?
* Hangi sistemler erişebiliyor?
* Paylaşılan kaynaklar doğru yapılandırılmış mı?
* Kullanılan SMB sürümü ve güvenlik yapılandırması nedir?
* Güvenlik duvarı kuralları uygun mu?

---

# 📊 Genel Risk Değerlendirmesi

Bu senaryoda servisleri yalnızca açık veya kapalı olmalarına göre değerlendirmek yerine aşağıdaki şekilde düşünebiliriz:

| Port | Servis | Genel amaç         | Değerlendirilmesi gereken konu          |
| ---- | ------ | ------------------ | --------------------------------------- |
| 22   | SSH    | Uzak yönetim       | Erişim ve kimlik doğrulama              |
| 23   | Telnet | Uzak bağlantı      | Güvenli olmayan eski protokol kullanımı |
| 80   | HTTP   | Web                | Web sunucusu ve yapılandırma            |
| 443  | HTTPS  | Güvenli web        | TLS ve web yapılandırması               |
| 445  | SMB    | Dosya/ağ paylaşımı | Erişim kontrolü ve yapılandırma         |

Bu tablo bize önemli bir şey gösteriyor:

> **Aynı "open" sonucu, farklı servislerde farklı güvenlik değerlendirmeleri gerektirebilir.**

---

# 🧠 Risk Nasıl Düşünülür?

Bir güvenlik uzmanı yalnızca:

```text
Port açık mı?
```

diye bakmaz.

Daha geniş bir değerlendirme yapar:

```text
Port açık mı?
       ↓
Hangi servis çalışıyor?
       ↓
Hangi sürüm?
       ↓
Bu servis gerekli mi?
       ↓
Kimler erişebiliyor?
       ↓
Nasıl yapılandırılmış?
       ↓
Güvenlik kontrolleri uygulanmış mı?
       ↓
Bir güvenlik riski var mı?
```

Bu yaklaşım **risk analizi** açısından daha sağlıklıdır.

---

# ⚠️ Önemli Ayrım: Tehdit ≠ Güvenlik Açığı ≠ Risk

Bu kavramları birbirinden ayırmak önemlidir.

### Tehdit

Sisteme zarar verme potansiyeline sahip kişi, olay veya durumdur.

Örneğin yetkisiz bir saldırgan bir tehdit aktörü olabilir.

### Güvenlik Açığı

Bir sistemde bulunan ve güvenliğin zayıflamasına neden olabilecek kusur veya zayıflıktır.

Örneğin güncel olmayan bir yazılım güvenlik açığı içerebilir.

### Risk

Bir tehdidin mevcut bir zayıflıktan yararlanması sonucunda oluşabilecek olumsuz etkinin değerlendirilmesidir.

Basitleştirirsek:

```text
Tehdit
   +
Güvenlik açığı
   +
Olası etki
   ↓
Risk değerlendirmesi
```

Bu nedenle sadece açık port görmek, tek başına güvenlik açığı veya yüksek risk olduğunu kanıtlamaz.

---

# 🛡️ Savunma Perspektifi

Aynı Nmap sonucu savunma açısından da değerlendirilebilir.

Örneğin bir sistem yöneticisi:

```text
22 → Gerekli mi?
23 → Gereksizse kapatılabilir mi?
80 → Gerçekten gerekli mi?
443 → HTTPS yapılandırması doğru mu?
445 → Kimlerin erişmesine izin veriliyor?
```

gibi sorular sorabilir.

Buradaki temel prensip:

> **Gereksiz servisleri çalıştırmamak ve gerekli servisleri uygun şekilde sınırlandırmak.**

Bu yaklaşım saldırı yüzeyinin gereksiz şekilde büyümesini engellemeye yardımcı olur.

---

# 📝 Kendi Öğrendiklerim

Bu senaryoda Nmap sonucundaki açık portların doğrudan "güvenlik açığı" olarak değerlendirilmemesi gerektiğini öğrendim.

Bir portun açık olması öncelikle o port üzerinde bir servisin erişilebilir olduğunu gösterir.

Risk değerlendirmesi yapabilmek için servis, sürüm, yapılandırma, erişim kontrolü ve gereklilik gibi faktörlerin birlikte değerlendirilmesi gerekir.

Özellikle Telnet gibi eski protokoller ile SSH, HTTP, HTTPS ve SMB gibi servislerin aynı şekilde değerlendirilmemesi gerektiğini gördüm.

Bu bölüm sayesinde Nmap çıktısını yalnızca teknik olarak okumak yerine, **güvenlik perspektifinden yorumlamaya** başladım.

---

## ✅ Kontrol Listesi

* [x] Nmap sonucundaki açık portları yorumladım.
* [x] 22, 23, 80, 443 ve 445 numaralı portları değerlendirdim.
* [x] Açık port ile güvenlik açığı arasındaki farkı öğrendim.
* [x] Tehdit, güvenlik açığı ve risk kavramlarını ayırdım.
* [x] Servis yapılandırmasının neden önemli olduğunu öğrendim.
* [x] Gereksiz servislerin saldırı yüzeyini artırabileceğini öğrendim.
* [x] Nmap sonuçlarına savunma perspektifinden bakmayı öğrendim.

---

## 🚀 Sonraki Adım

Bir sonraki bölümde Nmap'in farklı parametrelerini inceleyeceğim:

```text
-sV
-O
-p
-sT
-sn
```

Bu parametrelerin taramayı nasıl değiştirdiğini ve hangi amaçlarla kullanıldığını uygulamalı olarak öğreneceğim.
