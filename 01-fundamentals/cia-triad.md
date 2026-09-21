# CIA Triad

CIA Triad, siber güvenliğin temel prensiplerinden biridir.

CIA; **Confidentiality (Gizlilik), Integrity (Bütünlük) ve Availability (Erişilebilirlik)** kelimelerinin baş harflerinden oluşur.

Bir sistemin güvenliğini değerlendirirken temel olarak şu üç soruya bakabiliriz:

> 🔒 **Confidentiality:** Kim görebilir?
> 🛡️ **Integrity:** Veri doğru ve güvenilir mi?
> 🟢 **Availability:** İhtiyaç olduğunda erişilebilir mi?

---
<img width="600" height="427" alt="image" src="https://github.com/user-attachments/assets/b943025c-31ed-4826-9b33-3f08d5ab6313" />


## 1. Confidentiality — Gizlilik

Confidentiality, bilgilerin **yetkisiz kişiler tarafından görülmesini veya erişilmesini engellemek** anlamına gelir.

Bir bilginin yalnızca erişim yetkisi bulunan kişiler tarafından görülebilmesi gerekir.

### Örnek

Bir şirketin çalışan maaşlarının bulunduğu bir dosya olduğunu düşünelim:

```text
maaslar.xlsx
```

Bu dosyaya sadece yetkili İnsan Kaynakları çalışanlarının erişebilmesi gerekir.

Eğer dosya internette herkese açık hâle gelirse:

❌ **Confidentiality ihlal edilmiştir.**

Çünkü yetkisiz kişiler hassas bilgileri görebilmektedir.

### Gizliliği korumaya yardımcı olan yöntemler

* Erişim kontrolü
* Güçlü parolalar
* Çok faktörlü kimlik doğrulama (MFA)
* Şifreleme
* Yetkilendirme

---

## 2. Integrity — Bütünlük

Integrity, verilerin **doğru, güvenilir ve yetkisiz şekilde değiştirilmemiş olması** anlamına gelir.

Burada önemli bir nokta vardır:

> Bütünlük, verinin hiçbir zaman değişmemesi anlamına gelmez.

Yetkili ve normal bir işlem sonucunda verinin değişmesi bütünlük ihlali değildir.

### Örnek

Bir banka hesabında:

```text
Bakiye: 50.000 TL
```

bulunduğunu düşünelim.

Saldırgan bu bilgiyi izinsiz şekilde:

```text
Bakiye: 5.000 TL
```

olarak değiştirirse:

❌ **Integrity ihlal edilmiştir.**

Çünkü veri yetkisiz şekilde değiştirilmiştir.

Ancak kullanıcı bankadan 5.000 TL harcadığında bakiyenin değişmesi normal ve yetkili bir işlemdir.

### Bütünlüğü korumaya yardımcı olan yöntemler

* Erişim kontrolü
* Hash kullanımı
* Dijital imzalar
* Dosya bütünlüğü kontrolleri
* Yetkilendirme
* Loglama ve değişiklik takibi

---

## 3. Availability — Erişilebilirlik

Availability, **yetkili kullanıcıların ihtiyaç duydukları zamanda sistemlere ve verilere erişebilmesi** anlamına gelir.

Bir sistem güvenli olsa bile kullanıcılar ihtiyaç duyduklarında sisteme erişemiyorsa erişilebilirlik problemi vardır.

### Örnek

Bir bankanın mobil uygulamasının 5 saat boyunca çalışmadığını düşünelim.

Kullanıcılar:

* Hesaplarını görüntüleyemiyor.
* Para transferi yapamıyor.
* İşlemlerini kontrol edemiyor.

Bu durumda:

❌ **Availability problemi vardır.**

### DDoS örneği

Bir saldırgan bir web sunucusuna çok fazla sayıda istek göndererek sunucunun normal kullanıcıların isteklerini karşılayamaz hâle gelmesine neden olabilir.

Normal kullanıcılar web sitesine erişemez.

Bu durumda saldırının önemli etkilerinden biri:

**Availability → Erişilebilirlik**

üzerindedir.

### Erişilebilirliği korumaya yardımcı olan yöntemler

* Yedekleme
* Yük dengeleme
* Failover sistemleri
* DDoS koruması
* Sistem izleme
* Felaket kurtarma planları

---

# 4. CIA Triad'ı Bir Banka Örneğiyle Anlamak

Bir bankanın dijital bankacılık sistemini düşünelim.

### 🔒 Confidentiality

Müşterilerin kişisel ve finansal bilgilerini yalnızca yetkili kişilerin görebilmesi gerekir.

**Soru:**

> Kim görebilir?

---

### 🛡️ Integrity

Hesap bakiyeleri ve işlem bilgilerinin yetkisiz kişiler tarafından değiştirilmemesi gerekir.

**Soru:**

> Veri doğru ve güvenilir mi?

---

### 🟢 Availability

Müşterilerin ihtiyaç duyduklarında bankacılık sistemine erişebilmesi gerekir.

**Soru:**

> İhtiyacım olduğunda sisteme erişebiliyor muyum?

---

# 5. CIA Triad Görseli

```text
                    CIA TRIAD
                       ▲
                      / \
                     /   \
                    /     \
                   /       \
          CONFIDENTIALITY
              🔒 Gizlilik
             /             \
            /               \
           /                 \
          ▼                   ▼
     INTEGRITY           AVAILABILITY
      🛡️ Bütünlük          🟢 Erişilebilirlik
```

CIA Triad'ı şu şekilde düşünebilirim:

```text
🔒 Gizlilik
Kim görebilir?

        +

🛡️ Bütünlük
Veri doğru mu ve izinsiz değiştirilmiş mi?

        +

🟢 Erişilebilirlik
İhtiyacım olduğunda erişebiliyor muyum?
```

---

# 6. Güvenlik Olaylarını CIA ile Eşleştirme

Bir güvenlik olayını incelerken olayın CIA Triad'ın hangi bileşenini etkilediğini değerlendirebiliriz.

| Olay                                           | Etkilenen Alan  | Neden?                                  |
| ---------------------------------------------- | --------------- | --------------------------------------- |
| Müşteri bilgilerinin internete sızması         | Confidentiality | Yetkisiz kişiler bilgileri görebiliyor  |
| Veritabanındaki maaş bilgisinin değiştirilmesi | Integrity       | Veri yetkisiz şekilde değiştirilmiş     |
| Banka uygulamasının kullanılamaması            | Availability    | Kullanıcılar sisteme erişemiyor         |
| Sunucunun DDoS nedeniyle hizmet verememesi     | Availability    | Normal kullanıcılar sisteme erişemiyor  |
| Yetkisiz kullanıcının özel dosyaları okuması   | Confidentiality | Hassas bilgiler yetkisiz kişiye açılmış |

---

# 7. Bir Güvenlik Olayını Analiz Etme

Bir olayla karşılaştığımda kendime şu üç soruyu sorabilirim:

### 1. Yetkisiz biri bilgiyi görebiliyor mu?

Evetse:

> 🔒 **Confidentiality problemi olabilir.**

### 2. Veri izinsiz veya hatalı şekilde değiştirilmiş mi?

Evetse:

> 🛡️ **Integrity problemi olabilir.**

### 3. Yetkili kullanıcılar ihtiyaç duyduklarında sisteme erişebiliyor mu?

Hayırsa:

> 🟢 **Availability problemi olabilir.**

Bu üç soru, güvenlik olaylarını ilk aşamada sınıflandırmak için kullanabileceğim temel bir düşünme yöntemidir.

---

# 8. Kendi Öğrenme Notlarım

Bu konuyu çalışmadan önce CIA kavramlarını ayrı ayrı düşünmemiştim.

Çalışma sonrasında üç kavramı şu şekilde ayırabiliyorum:

* **Confidentiality:** Yetkisiz kişilerin bilgileri görmesini engellemek.
* **Integrity:** Verilerin doğru ve güvenilir kalmasını, yetkisiz şekilde değiştirilmemesini sağlamak.
* **Availability:** Yetkili kullanıcıların ihtiyaç duyduklarında sistemlere ve verilere erişebilmesini sağlamak.

Benim için en kolay hatırlama yöntemi:

> 🔒 **Gizlilik → Kim görebilir?**

> 🛡️ **Bütünlük → Veri değiştirilmiş mi?**

> 🟢 **Erişilebilirlik → Kullanabiliyor muyum?**

---

# 9. Kısa Özet

```text
                 CIA TRIAD
                     │
       ┌─────────────┼─────────────┐
       │             │             │
       ▼             ▼             ▼
 CONFIDENTIALITY  INTEGRITY   AVAILABILITY
    Gizlilik      Bütünlük    Erişilebilirlik
       │             │             │
   Kim görebilir?  Veri doğru mu?  Kullanabiliyor muyum?
```

### Akılda Tutulması Gerekenler

> **Confidentiality = Yetkisiz erişimi/görmeyi önlemek**

> **Integrity = Verinin doğru ve güvenilir kalmasını sağlamak**

> **Availability = Yetkili kullanıcıların gerektiğinde erişebilmesini sağlamak**

---

# Kaynaklar

* NIST — National Institute of Standards and Technology
* NIST Cybersecurity Framework
* NIST Computer Security Resource Center
* CISA — Cybersecurity and Infrastructure Security Agency
