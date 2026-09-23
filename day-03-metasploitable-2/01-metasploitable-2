# 🔐 Kısım 1 — Metasploitable 2 ve Zafiyet Analizine Giriş

## 1. Metasploitable 2 Nedir?

Metasploitable 2, siber güvenlik eğitimlerinde ve güvenlik testlerinde kullanılmak amacıyla hazırlanmış, içerisinde bilerek güvenlik açıkları ve eski sürüm servisler bulunan bir Linux sanal makinesidir.

Normal bir Linux sunucusundan farklı olarak güvenli olması amaçlanmamıştır. Tam tersine, güvenlik uzmanlarının bir sistem üzerinde keşif, servis analizi, zafiyet araştırması ve güvenlik değerlendirmesi gibi işlemleri kontrollü bir ortamda öğrenebilmesi için tasarlanmıştır.

Bu çalışmada:

* **Kali Linux** → test/saldırı makinesi
* **Metasploitable 2** → hedef/test makinesi

olarak kullanılmaktadır.

Metasploitable 2'nin temel amacı gerçek sistemler üzerinde risk oluşturmadan güvenlik çalışmalarının yapılabileceği bir laboratuvar ortamı sağlamaktır.

> **Önemli:** Metasploitable 2 gibi bilerek zafiyetli sistemler eğitim amacıyla kullanılmalıdır. Gerçek sistemlerde izinsiz tarama veya test yapılmamalıdır.

---

## 2. Vulnerable Machine — Zafiyetli Makine

**Vulnerable Machine**, üzerinde güvenlik zayıflıkları bulunan sistemdir.

Bu zayıflıklar;

* eski yazılım sürümleri,
* hatalı yapılandırmalar,
* gereksiz açık servisler,
* zayıf kimlik doğrulama,
* güvenlik açısından yanlış ayarlar

gibi farklı nedenlerden ortaya çıkabilir.

Metasploitable 2 bu kavramı öğrenmek için hazırlanmış özel bir örnektir. Sistem üzerinde farklı servisler ve bilerek bırakılmış güvenlik zayıflıkları bulunmaktadır.

---

## 3. Attack Surface — Saldırı Yüzeyi

**Attack Surface (Saldırı Yüzeyi)**, bir sistemin dışarıdan etkileşime veya saldırıya açık olan noktalarının tamamını ifade eder.

Bunu sadece açık portlar olarak düşünmemek gerekir.

Örneğin bir sistemde:

```text
Açık Port
   ↓
Çalışan Servis
   ↓
Web Uygulaması
   ↓
Login
   ↓
Dosya Upload
   ↓
API
   ↓
Admin Paneli
```

gibi farklı erişim noktaları bulunabilir.

Dolayısıyla açık bir port, saldırı yüzeyinin bir parçası olabilir fakat **saldırı yüzeyi sadece portlardan oluşmaz.**

Örneğin:

* FTP servisi
* SSH servisi
* HTTP servisi
* SMB servisi
* Web login ekranı
* Dosya yükleme alanı
* API endpointleri
* Yönetim panelleri

bir sistemin saldırı yüzeyini oluşturabilecek noktalardır.

### Önemli ayrım

> **Açık port = saldırı yüzeyinin bir parçası olabilir.**

Ancak:

> **Açık port = kesinlikle zafiyet var** anlamına gelmez.

---

## 4. Vulnerability Assessment Nedir?

**Vulnerability Assessment (Zafiyet Değerlendirmesi)**, bir sistemde bulunan güvenlik zayıflıklarını keşfetme, inceleme ve değerlendirme sürecidir.

Bu süreçte temel olarak şu sorulara cevap aranır:

* Sistemde hangi servisler çalışıyor?
* Hangi sürümler kullanılıyor?
* Bu sürümlerde bilinen güvenlik açıkları var mı?
* Yapılandırmada güvenlik problemi var mı?
* Bulunan problemin etkisi ne olabilir?
* Riskin azaltılması için ne yapılabilir?

Örneğin:

```text
Port 21 açık
      ↓
FTP servisi bulundu
      ↓
FTP sürümü belirlendi
      ↓
Sürüm araştırıldı
      ↓
Bilinen CVE'ler araştırıldı
      ↓
Etkilenme koşulları incelendi
```

Bu süreç bir zafiyet değerlendirmesi yaklaşımıdır.

---

## 5. Penetration Testing Nedir?

**Penetration Testing (Sızma Testi)**, belirlenen kapsam ve izinler dahilinde bir sistemin güvenlik zayıflıklarının kontrollü şekilde test edilmesidir.

Buradaki önemli nokta, yalnızca:

> "Bu sistemde zafiyet olabilir."

demekle kalmamaktır.

Kontrollü bir sızma testinde, uygun olduğunda zafiyetin gerçekten kullanılabilir olup olmadığı ve etkisinin ne olduğu test edilebilir.

Bu nedenle:

### Vulnerability Assessment

> Zafiyetleri bulur, analiz eder ve değerlendirir.

### Penetration Testing

> Belirlenen zafiyetlerin kontrollü şekilde kullanılabilirliğini ve etkisini test eder.

Bu iki kavram birbirine yakın olsa da aynı şey değildir.

---

# 6. Exploit ve Exploitation

Bu iki kavram özellikle birbirine karıştırılmamalıdır.

## Exploit

**Exploit**, bir güvenlik açığından yararlanmak için kullanılan kod, komut, araç veya yöntemdir.

Basit şekilde:

```text
Vulnerability
      ↓
Exploit
      ↓
Zafiyetten yararlanma yöntemi
```

## Exploitation

**Exploitation**, bulunan zafiyetin gerçekten kullanılmaya çalışılması, yani zafiyetten yararlanma sürecidir.

Kısaca:

> **Exploit = kullanılan yöntem/araç**

> **Exploitation = zafiyetten yararlanma işlemi**

---

# 7. CVE Nedir?

**CVE (Common Vulnerabilities and Exposures)**, belirli güvenlik açıklarının standart şekilde tanımlanmasını sağlayan kimliklendirme sistemidir.

Bir güvenlik açığı için örneğin:

```text
CVE-XXXX-XXXXX
```

şeklinde bir kimlik bulunabilir.

CVE araştırması yaparken yalnızca CVE numarasına bakmak yeterli değildir.

Şunlar da incelenmelidir:

* Etkilenen yazılım
* Etkilenen sürümler
* Güvenlik açığının açıklaması
* Etki
* CVSS skoru
* Saldırının gerçekleşmesi için gereken koşullar
* İlgili kaynaklar

---

# 8. CVE Bulmak Sistemin Ele Geçirildiği Anlamına Gelmez

Bu çalışmanın en önemli noktalarından biri budur.

Örneğin:

```text
Nmap
 ↓
Servis bulundu
 ↓
Versiyon bulundu
 ↓
CVE araştırıldı
```

Burada henüz sistemi ele geçirmiş olmadık.

Hatta bulunan CVE'nin hedef sistem için kesin olarak kullanılabilir olduğunu da söyleyemeyiz.

Çünkü bunun için;

* kullanılan sürümün gerçekten etkilenip etkilenmediği,
* yapılandırmanın uygun olup olmadığı,
* gerekli koşulların bulunup bulunmadığı,
* zafiyetin gerçekten doğrulanıp doğrulanmadığı

gibi bilgiler incelenmelidir.

Bu nedenle çalışma boyunca şu mantığı kullanacağım:

```text
Versiyon Bilgisi
       ↓
Potansiyel Zafiyet
       ↓
Doğrulama
       ↓
Gerçek Risk
```

**CVE bulmak ≠ sistemi ele geçirmek**

---

# 9. Vulnerability, Threat ve Risk Arasındaki Fark

Bu üç kavram da birbirinden ayrılmalıdır.

### Vulnerability — Zafiyet

Sistemde bulunan güvenlik zayıflığıdır.

Örnek:

> Eski ve güvenlik açığı bulunan bir yazılım sürümü.

### Threat — Tehdit

Bir zafiyetten yararlanabilecek kişi, olay veya durumdur.

Örneğin:

> Sisteme saldırmaya çalışan bir saldırgan.

### Risk

Bir zafiyetin kullanılmasının oluşturabileceği olası zarar ve bunun gerçekleşme ihtimaliyle ilgilidir.

Basit bir model:

```text
Vulnerability
      +
Threat
      ↓
    Risk
```

---

# 10. Bu Bölümden Çıkardığım Temel Ayrımlar

Bu bölümde benim için en önemli kavram ayrımları şunlar oldu:

| Kavram                   | Anlamı                                                         |
| ------------------------ | -------------------------------------------------------------- |
| Metasploitable 2         | Bilerek zafiyetli eğitim/test sistemi                          |
| Vulnerable Machine       | Güvenlik zayıflıkları bulunan sistem                           |
| Attack Surface           | Sistemin etkileşime/saldırıya açık noktaları                   |
| Vulnerability Assessment | Zafiyetleri bulma ve değerlendirme                             |
| Penetration Testing      | Zafiyetleri kontrollü şekilde test etme                        |
| Vulnerability            | Güvenlik zayıflığı                                             |
| Exploit                  | Zafiyetten yararlanmak için kullanılan yöntem/araç             |
| Exploitation             | Zafiyetten yararlanma süreci                                   |
| CVE                      | Güvenlik açığı için standart kimliklendirme                    |
| Threat                   | Zafiyetten yararlanabilecek tehdit                             |
| Risk                     | Zafiyetin oluşturabileceği olası zarar ve gerçekleşme ihtimali |

---

## 🎯 Bölümün Ana Mantığı

Siber güvenlikte bir sistemi değerlendirirken sadece:

> **"Hangi portlar açık?"**

sorusuna bakmak yeterli değildir.

Asıl düşünce zinciri:

```text
Hangi sistem?
      ↓
Hangi ağ?
      ↓
Hangi portlar açık?
      ↓
Hangi servisler çalışıyor?
      ↓
Hangi sürümler kullanılıyor?
      ↓
Bilinen zafiyetler var mı?
      ↓
Bu zafiyet gerçekten etkili mi?
      ↓
Risk ne?
      ↓
Nasıl önlenebilir?
```

Bu nedenle bir güvenlik uzmanının amacı sadece açık port bulmak değil, **bulduğu bilginin güvenlik açısından ne ifade ettiğini anlamaktır.**
