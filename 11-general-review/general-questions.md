# 🧠 Genel Değerlendirme Soruları

Bu bölümde siber güvenlik çalışması boyunca öğrendiğim temel kavramları kendi cümlelerimle özetledim.

---

## 1. Siber güvenlik neden yalnızca antivirus kullanmaktan ibaret değildir?

Antivirus daha çok zararlı yazılımları tespit etmeye ve engellemeye yardımcı olur. Ancak siber güvenlik bunun yanında ağ güvenliği, web güvenliği, kimlik doğrulama, firewall, log analizi, veri güvenliği, saldırı tespiti ve olay müdahalesi gibi birçok alanı kapsar.

---

## 2. Bir saldırganın sisteme girmesini engellemek ile sisteme girdikten sonra fark etmek arasındaki fark nedir?

Sisteme girmesini engellemek **önleme**, girdikten sonra fark etmek ise **tespit** aşamasıdır.

Örneğin firewall veya güçlü authentication saldırıyı önlemeye yardımcı olabilir. SIEM ve log analizi ise gerçekleşen şüpheli hareketlerin fark edilmesine yardımcı olabilir.

---

## 3. Loglar neden siber güvenlik açısından önemlidir?

Loglar sistemlerde gerçekleşen olayların kayıtlarını içerir. Bu kayıtlar sayesinde bir güvenlik olayının ne zaman gerçekleştiği, hangi kullanıcı veya IP ile ilişkili olduğu ve sonrasında neler yapıldığı araştırılabilir.

---

## 4. Açık port neden her zaman güvenlik açığı değildir?

Açık bir port genellikle o port üzerinden bir servisin bağlantı kabul ettiğini gösterir. Bu tek başına güvenlik açığı anlamına gelmez.

Risk değerlendirilirken servisin ne olduğu, güncel olup olmadığı, nasıl yapılandırıldığı ve dışarıya açık olup olmadığı gibi başka faktörlere de bakılır.

> **Açık port ≠ Güvenlik açığı**

---

## 5. Bir kullanıcının admin yetkisine sahip olması neden risk oluşturabilir?

Admin yetkisi kullanıcıya daha fazla işlem yapma imkanı verir. Hesap ele geçirilirse saldırgan da bu yetkilerden yararlanabilir ve daha büyük zarar verebilir.

Bu nedenle kullanıcıların yalnızca ihtiyaç duydukları yetkilere sahip olması önemlidir.

---

## 6. Phishing teknik bir saldırı mıdır, insan odaklı bir saldırı mıdır?

Phishing daha çok **insan odaklı bir saldırı yöntemidir**.

Saldırgan teknik bir sistem açığından yararlanmak yerine kullanıcıyı kandırarak şifre, kişisel bilgi veya zararlı dosya gibi şeylere ulaşmaya çalışabilir.

---

## 7. HTTPS neden HTTP'den farklıdır?

HTTP verileri tek başına şifreli bir kanal üzerinden taşımaz.

HTTPS ise HTTP iletişimini **TLS** ile korur. Bu sayede iletişimin gizliliği ve bütünlüğü korunmaya yardımcı olur ve sunucunun kimliğinin doğrulanması sağlanır.

---

## 8. Hash neden parola saklamada kullanılır?

Hash fonksiyonu parolayı doğrudan saklamak yerine paroladan bir özet değer oluşturulmasını sağlar.

Böylece veritabanı ele geçirilse bile parolaların düz metin halinde bulunması önlenmiş olur.

Parola saklamada uygun parola-hash algoritmaları ve benzersiz salt kullanılması önemlidir.

---

## 9. Firewall ile IDS/IPS arasındaki temel fark nedir?

**Firewall**, belirlenen kurallara göre ağ trafiğine izin verebilir veya trafiği engelleyebilir.

**IDS (Intrusion Detection System)** şüpheli veya saldırı niteliğindeki aktiviteleri tespit edip uyarı üretmeye odaklanır.

**IPS (Intrusion Prevention System)** ise şüpheli trafiği tespit ettikten sonra belirlenen kurallara göre engelleme gibi önleyici işlemler de gerçekleştirebilir.

Kısaca:

```text
Firewall → Trafiği kurallara göre kontrol eder

IDS → Şüpheli aktiviteyi tespit eder ve uyarır

IPS → Şüpheli aktiviteyi tespit edip engelleyebilir
```

---

## 10. SOC analisti ile penetration tester'ın görevleri nasıl farklılaşır?

**SOC Analyst**, sistemleri ve güvenlik olaylarını izler, logları ve alertleri analiz eder ve şüpheli aktiviteleri araştırır.

**Penetration Tester** ise yetkili ve kontrollü şekilde sistemlerin güvenlik açıklarını değerlendirmeye çalışır.

Kısaca:

```text
SOC Analyst
→ "Sistemde şüpheli bir şey oluyor mu?"

Penetration Tester
→ "Bu sistemde hangi güvenlik zayıflıkları bulunuyor?"
```

---

## 11. Red Team'in yaptığı bir saldırı simülasyonundan Blue Team nasıl faydalanabilir?

Blue Team, Red Team'in gerçekleştirdiği kontrollü saldırı sırasında hangi aktivitelerin görüldüğünü inceleyebilir.

Örneğin:

* Hangi loglar oluştu?
* Alert oluştu mu?
* Saldırı ne kadar sürede fark edildi?
* Hangi aşamalar tespit edilemedi?

gibi sorular incelenebilir.

Bu sonuçlara göre savunma mekanizmaları ve tespit kuralları geliştirilebilir.

---

## 12. Bir güvenlik açığının bulunması ile bu açığın istismar edilmesi aynı şey midir?

Hayır.

**Güvenlik açığı**, sistemde bulunan bir zayıflıktır.

**İstismar etmek (exploit etmek)** ise bu zayıflıktan yararlanarak belirli bir etki oluşturmaya çalışmaktır.

Örneğin:

```text
Vulnerability
↓
Sistemdeki zayıflık

Exploit
↓
Bu zayıflıktan yararlanma yöntemi
```

Bir sistemde güvenlik açığı bulunması, mutlaka bu açığın aktif olarak istismar edildiği anlamına gelmez.

---

## 13. Bir sistemin güvenli olduğunu söylemek neden tek seferlik bir işlem değildir?

Çünkü sistemler sürekli değişir.

Yeni yazılımlar yüklenebilir, yeni güvenlik açıkları ortaya çıkabilir, yapılandırmalar değişebilir ve yeni saldırı yöntemleri geliştirilebilir.

Bu nedenle güvenlik sürekli izlenmeli, güncellenmeli ve yeniden değerlendirilmelidir.

---

## 14. "Least Privilege" prensibi nedir?

**Least Privilege**, bir kullanıcıya veya sisteme yalnızca görevini gerçekleştirmek için ihtiyaç duyduğu minimum yetkinin verilmesi prensibidir.

Örneğin normal bir çalışanın yönetici yetkisine ihtiyacı yoksa admin yetkisi verilmemelidir.

Böylece hesap ele geçirilse bile oluşabilecek zararın kapsamı azaltılabilir.

> **Gerektiği kadar yetki, fazlası değil.**

---

## 15. Siber güvenlikte insan faktörü neden önemlidir?

Çünkü güvenlik sistemleri ne kadar güçlü olursa olsun insanlar hata yapabilir veya kandırılabilir.

Örneğin:

* Phishing e-postasına tıklamak
* Şifreyi başkasıyla paylaşmak
* Güvenilmeyen dosyayı açmak
* Güvenlik kurallarını dikkate almamak

gibi davranışlar güvenlik riski oluşturabilir.

Bu nedenle siber güvenlik yalnızca teknik sistemlerle değil, kullanıcı farkındalığı ve doğru güvenlik alışkanlıklarıyla da ilgilidir.

---

# 🎯 Genel Sonuç

Bu sorularla birlikte siber güvenlikte öğrendiğim temel kavramları birbirine bağlayabildiğimi görüyorum.

```text
Önleme
   ↓
Firewall / Authentication / Güvenlik kontrolleri
   ↓
Tespit
   ↓
Log / SIEM / IDS / SOC
   ↓
Analiz
   ↓
Incident
   ↓
Incident Response
   ↓
İyileştirme
```

Bunun yanında güvenliğin sadece teknik araçlardan oluşmadığını;

* İnsan faktörünün,
* Yetkilendirmenin,
* Sürekli izleme ve güncellemenin,
* Doğru güvenlik politikalarının

da önemli olduğunu öğrendim.

### En önemli öğrendiğim düşünce:

> **Siber güvenlik yalnızca saldırıyı engellemek değil; saldırıyı önlemek, tespit etmek, anlamak, müdahale etmek ve tekrar yaşanmaması için sistemi geliştirmektir.**
