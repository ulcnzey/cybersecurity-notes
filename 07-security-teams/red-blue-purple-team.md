# 🔴🔵 Kırmızı Takım ve Mavi Takım

Siber güvenlikte farklı ekipler farklı bakış açılarıyla çalışır.

En temel ayrım:

```text
🔴 Red Team → Saldırgan perspektifi
🔵 Blue Team → Savunmacı perspektifi
🟣 Purple Team → Red + Blue iş birliği
```

Bu ekiplerin amacı birbirleriyle gerçek anlamda savaşmak değil, kurumun güvenlik seviyesini geliştirmektir.

---

# 🔴 1. Red Team Nedir?

**Red Team (Kırmızı Takım)**, bir kurumun güvenliğini saldırgan perspektifinden değerlendiren güvenlik ekibidir.

Red Team'in temel sorusu:

> **"Bir saldırgan olsaydım, bu sisteme nasıl ulaşmaya çalışırdım?"**

Red Team çalışmaları yalnızca teknik sistemleri değil, gerektiğinde insanların ve fiziksel süreçlerin güvenlik durumunu da değerlendirebilir.

Ancak bu çalışmaların tamamı **önceden belirlenmiş yetki ve kapsam içerisinde** gerçekleştirilir.

---

## 🎯 Red Team'in Görevi

Red Team'in amacı:

* Güvenlik zayıflıklarını tespit etmek
* Saldırganların kullanabileceği yolları değerlendirmek
* Savunma mekanizmalarının ne kadar etkili olduğunu test etmek
* Tespit edilemeyen saldırı yollarını ortaya çıkarmak
* Kurumun güvenlik seviyesini geliştirmeye yardımcı olmak

gibi çalışmalardır.

---

## 👀 Red Team Hangi Bakış Açısıyla Hareket Eder?

Red Team saldırgan perspektifinden düşünür.

Örneğin:

```text
"Sistemde hangi zayıflıklar var?"
```

```text
"Bu zayıflık kullanılırsa nereye kadar ilerlenebilir?"
```

```text
"Bu aktivite güvenlik ekipleri tarafından fark edilir mi?"
```

gibi sorular sorabilir.

Buradaki amaç sisteme zarar vermek değil, **kurumun güvenlik açıklarını kontrollü şekilde ortaya çıkarmaktır.**

---

# 🧪 Red Team Hangi Tür Çalışmalar Yapar?

Yetkilendirilmiş bir Red Team çalışmasında örneğin:

* Saldırı yüzeyinin değerlendirilmesi
* Güvenlik kontrollerinin test edilmesi
* Kimlik doğrulama mekanizmalarının değerlendirilmesi
* Web uygulamalarının güvenlik testleri
* Ağ güvenliğinin değerlendirilmesi
* Fiziksel güvenlik kontrollerinin değerlendirilmesi
* Sosyal mühendislik farkındalığının kontrollü şekilde test edilmesi
* Tespit ve müdahale yeteneklerinin değerlendirilmesi

gibi çalışmalar bulunabilir.

Bu çalışmaların kapsamı kurum tarafından önceden belirlenir.

Örneğin:

```text
Kapsam:
example.com

Test zamanı:
22:00 - 04:00

Test dışı sistemler:
Üretim veritabanları

Amaç:
Savunma mekanizmalarının değerlendirilmesi
```

Bu nedenle Red Team çalışmasının en önemli noktalarından biri:

> **Yetkilendirme ve kapsamdır.**

---

# 🔵 2. Blue Team Nedir?

**Blue Team (Mavi Takım)**, kurumun sistemlerini ve verilerini tehditlere karşı korumaya, saldırıları tespit etmeye ve güvenlik olaylarına müdahale etmeye odaklanan savunma ekibidir.

Blue Team'in temel sorusu:

> **"Sistemimizi nasıl korur, saldırıyı nasıl tespit eder ve gerçekleşirse nasıl müdahale ederiz?"**

---

# 🎯 Blue Team'in Görevi

Blue Team'in görevleri arasında:

* Sistemleri izlemek
* Güvenlik olaylarını tespit etmek
* Logları analiz etmek
* Alertleri incelemek
* Güvenlik kontrollerini geliştirmek
* Zafiyetleri gidermeye yardımcı olmak
* Incident Response süreçlerine katılmak
* Güvenlik politikalarını geliştirmek
* Saldırıların etkisini azaltmak

bulunabilir.

SOC ekipleri çoğu kurumda Blue Team'in önemli bir parçasıdır.

---

# 👀 Blue Team Hangi Bakış Açısıyla Hareket Eder?

Blue Team savunmacı perspektifinden hareket eder.

Örneğin:

```text
"Bu saldırıyı nasıl tespit ederim?"
```

```text
"Bu saldırı gerçekleşirse hangi logları görürüm?"
```

```text
"Bu sistemi nasıl daha güvenli hale getiririm?"
```

```text
"Bir incident olduğunda nasıl müdahale ederim?"
```

gibi sorulara odaklanır.

---

# 🛡️ Blue Team Hangi Tür Çalışmalar Yapar?

Örneğin:

* SIEM izleme
* Log analizi
* Alert analizi
* Threat detection
* Incident Response
* Firewall yönetimi
* Endpoint güvenliği
* Ağ izleme
* Güvenlik politikalarının uygulanması
* Zafiyet yönetimi
* Tehdit avcılığı (Threat Hunting)

gibi çalışmalar yapabilir.

Örneğin SOC Analyst'in:

```text
SIEM
 ↓
Alert
 ↓
Log analizi
 ↓
Şüpheli davranış
 ↓
Incident
 ↓
Müdahale
```

süreci Blue Team çalışmalarının bir parçası olabilir.

---

# 🟣 3. Purple Team Nedir?

**Purple Team (Mor Takım)**, Red Team ve Blue Team'in birlikte çalışarak güvenlik kontrollerini geliştirmesine yönelik yaklaşımı ifade eder.

Purple Team'i ayrı bir "üçüncü takım" olarak düşünmek her zaman doğru değildir.

Daha çok:

> **Red Team'in saldırı bilgisi + Blue Team'in savunma bilgisi = birlikte öğrenme ve geliştirme**

mantığı vardır.

---

## 🔴 + 🔵 → 🟣

Örneğin Red Team kontrollü bir saldırı simülasyonu gerçekleştirir.

Blue Team ise:

```text
Bu aktiviteyi gördük mü?
Hangi log oluştu?
Alert oluştu mu?
Ne kadar sürede fark ettik?
Müdahale edebildik mi?
```

sorularını inceler.

Daha sonra iki ekip sonuçları paylaşır.

```text
Red Team
   ↓
"Saldırı şu şekilde gerçekleşti."

Blue Team
   ↓
"Şu kısmı tespit ettik,
şu kısmı kaçırdık."

         ↓

Birlikte geliştirme
         ↓

Daha iyi güvenlik
```

---

# 🤝 4. Red Team ve Blue Team Neden Birlikte Çalışır?

Çünkü yalnızca saldırıyı gerçekleştirebilmek veya yalnızca savunma sistemi kurmak yeterli olmayabilir.

Örneğin Red Team bir saldırı yolunu başarıyla kullanmış olabilir.

Ancak asıl önemli sorulardan biri:

> **Blue Team bu saldırıyı görebildi mi?**

Eğer göremediyse:

* Gerekli log oluşmamış olabilir.
* SIEM kuralı eksik olabilir.
* Alert oluşturulmamış olabilir.
* Güvenlik ekibi saldırıyı geç fark etmiş olabilir.

Bu bilgiler savunmanın geliştirilmesine yardımcı olur.

---

# 🏢 5. Gerçek Bir Şirket Neden Saldırı Simülasyonu Yaptırır?

Bir şirket güvenlik sistemlerinin yalnızca teorik olarak değil, kontrollü bir senaryoda da çalışıp çalışmadığını görmek isteyebilir.

Örneğin şirket:

```text
Firewall var mı?
        ↓
Evet

SIEM var mı?
        ↓
Evet

SOC var mı?
        ↓
Evet
```

diyebilir.

Ancak şu sorular hâlâ cevaplanmamış olabilir:

```text
Gerçek bir saldırı simülasyonunda
bunların hepsi doğru çalışıyor mu?
```

Saldırı simülasyonları bu tür soruların değerlendirilmesine yardımcı olabilir.

---

## 🎯 Şirket Neleri Öğrenebilir?

Kontrollü bir saldırı simülasyonu sonucunda şirket:

* Hangi güvenlik açıklarının bulunduğunu
* Hangi saldırıların tespit edilebildiğini
* Hangi saldırıların gözden kaçtığını
* Alertlerin doğru çalışıp çalışmadığını
* Logların yeterli olup olmadığını
* SOC ekibinin müdahale sürecini
* Güvenlik kontrollerinin nerede geliştirilmesi gerektiğini

öğrenebilir.

Örneğin:

```text
Red Team:
"Saldırıyı gerçekleştirdik."

Blue Team:
"İlk aşamayı fark ettik."

Red Team:
"Ancak sonraki aşamayı fark etmediniz."

Blue Team:
"SIEM kuralımız eksikmiş."

         ↓

SIEM kuralı geliştirildi
         ↓
Yeni test
         ↓
Daha iyi tespit
```

Bu döngü güvenliğin sürekli geliştirilmesine yardımcı olur.

---

# ⚖️ 6. Red Team ve Blue Team Karşılaştırması

| Özellik           | 🔴 Red Team                             | 🔵 Blue Team                                   |
| ----------------- | --------------------------------------- | ---------------------------------------------- |
| Bakış açısı       | Saldırgan                               | Savunmacı                                      |
| Temel amaç        | Güvenlik zayıflıklarını ortaya çıkarmak | Sistemleri korumak ve saldırıları tespit etmek |
| Odak              | Saldırı yolları                         | Savunma ve tespit                              |
| Log analizi       | Çalışmanın bir parçası olabilir         | Temel görevlerden biri olabilir                |
| Incident Response | Genellikle ana görev değildir           | Önemli görevlerden biridir                     |
| Güvenlik testi    | Evet                                    | Test sonuçlarına göre savunmayı geliştirir     |
| SIEM              | Test edebilir                           | Aktif olarak kullanabilir                      |
| Perspektif        | "Nasıl ulaşırım?"                       | "Nasıl korurum?"                               |

---

# 🟣 7. Purple Team'in Rolü

Purple Team yaklaşımında amaç:

> **Red Team ve Blue Team'in birbirinden öğrenmesini sağlamak.**

Örneğin:

```text
🔴 Red Team
Saldırı tekniğini gösterir
        ↓
🔵 Blue Team
Tespit yöntemini geliştirir
        ↓
🟣 Purple Team yaklaşımı
Sonuçlar birlikte değerlendirilir
        ↓
Yeni güvenlik kontrolü
        ↓
Tekrar test
```

Bu süreç sayesinde saldırı ve savunma tarafları birbirinden kopuk çalışmak yerine birlikte gelişebilir.

---

# 🧠 8. Öğrendiğim Kavramları Birbirine Bağlayalım

Daha önce öğrendiğimiz SOC konusu ile bağlantı kurarsak:

```text
🔴 Red Team
      ↓
Kontrollü saldırı simülasyonu
      ↓
Saldırı aktivitesi
      ↓
🔵 Blue Team
      ↓
SIEM / Log / Alert
      ↓
SOC Analyst
      ↓
Analiz
      ↓
Incident Response
      ↓
Savunmanın geliştirilmesi
```

Purple Team yaklaşımı ise bu iki tarafın arasındaki bilgi paylaşımını güçlendirir.

---

# 📝 Kendi Öğrenme Notlarım

Bu bölümden öğrendiğim temel noktalar:

* **Red Team** → Saldırgan perspektifinden güvenliği test eder.
* **Blue Team** → Savunma, tespit ve müdahaleye odaklanır.
* **Purple Team** → Red ve Blue ekiplerinin birlikte öğrenip güvenliği geliştirmesine yönelik yaklaşımdır.
* Red Team'in amacı gerçek zarar vermek değil, **yetkili ve kontrollü şekilde güvenliği değerlendirmektir.**
* Blue Team saldırıları tespit etmeye ve sistemleri korumaya çalışır.
* Red Team ve Blue Team birlikte çalışarak güvenlik kontrollerindeki eksiklikleri ortaya çıkarabilir.
* Saldırı simülasyonları şirketlere gerçek bir saldırı gerçekleşmeden önce savunmalarını değerlendirme fırsatı verebilir.

### En önemli ayrım:

> 🔴 **Red Team:** "Bir saldırgan olsaydım ne yapardım?"

> 🔵 **Blue Team:** "Bunu nasıl önler ve tespit ederim?"

> 🟣 **Purple Team:** "Saldırı ve savunma bilgisini nasıl birleştirip güvenliği geliştiririz?"

---

# 🎯 Kısa Özet

```text
🔴 RED TEAM
Saldırgan perspektifi
       ↓
Güvenliği test eder

🔵 BLUE TEAM
Savunmacı perspektifi
       ↓
Korur + Tespit eder + Müdahale eder

🟣 PURPLE TEAM
Red + Blue iş birliği
       ↓
Öğrenme + Geliştirme
```

Gerçek şirketlerde saldırı simülasyonlarının temel amaçlarından biri:

> **Gerçek bir saldırı gerçekleşmeden önce güvenlik kontrollerinin, tespit mekanizmalarının ve müdahale süreçlerinin ne kadar etkili olduğunu değerlendirmektir.**

---


