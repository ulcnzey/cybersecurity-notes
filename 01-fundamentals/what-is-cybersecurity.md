# Siber Güvenlik Nedir?

> Siber güvenlik; bilgisayarları, ağları, uygulamaları, cihazları ve dijital verileri tehditlere, yetkisiz erişime ve saldırılara karşı korumayı amaçlayan bir disiplindir.

---

## 1. Siber Güvenlik Neyi Korur?

Siber güvenlik denildiğinde sadece bilgisayarları veya sunucuları düşünmemek gerekir. Günümüzde birçok sistem dijital olduğu için korunması gereken alanlar oldukça geniştir.

Siber güvenlik kapsamında;

* Bilgisayarlar
* Sunucular
* Bilgisayar ağları
* Web ve mobil uygulamalar
* Mobil cihazlar
* Kullanıcı hesapları
* Dijital veriler
* Bulut sistemleri
* IoT cihazları

gibi birçok yapı korunmaya çalışılır.

Temel amaç, bu sistemlerin ve verilerin **yetkisiz kişiler tarafından ele geçirilmesini, değiştirilmesini, zarar görmesini veya kullanılamaz hâle getirilmesini** önlemektir.

---

## 2. Siber Güvenlik Sadece Hackerları Engellemek Midir?

Hayır.

Başlangıçta siber güvenliği sadece "hacker saldırılarını engellemek" olarak düşünmüştüm. Ancak siber güvenliğin bundan daha geniş bir alan olduğunu öğrendim.

Siber güvenlik;

* Saldırıları önlemeyi,
* Güvenlik açıklarını tespit etmeyi,
* Tehditleri değerlendirmeyi,
* Şüpheli aktiviteleri fark etmeyi,
* Güvenlik olaylarına müdahale etmeyi,
* Sistemleri saldırı veya arıza sonrasında tekrar güvenli hâle getirmeyi

de kapsar.

Bu nedenle siber güvenlik yalnızca saldırı gerçekleştiğinde devreye giren bir alan değildir. **Saldırı gerçekleşmeden önce hazırlık yapmak da güvenliğin önemli bir parçasıdır.**

---

## 3. Bilgi Güvenliği ve Siber Güvenlik Arasındaki Fark

Bu iki kavram birbiriyle çok yakından ilişkilidir ancak tamamen aynı değildir.

### Bilgi Güvenliği

Bilgi güvenliği, bilginin korunmasına odaklanır.

Örneğin bir şirketin müşteri bilgilerinin:

* Yetkisiz kişiler tarafından görülmemesi,
* Yetkisiz şekilde değiştirilmemesi,
* Kaybolmaması veya ihtiyaç olduğunda erişilebilir olması

bilgi güvenliği açısından önemlidir.

Burada bilgi sadece dijital olmak zorunda değildir. Fiziksel bir belge de bilgi güvenliğinin konusu olabilir.

### Siber Güvenlik

Siber güvenlik ise özellikle dijital ortamları, sistemleri, ağları, uygulamaları ve cihazları korumaya odaklanır.

Bu nedenle siber güvenlik ile bilgi güvenliği arasında güçlü bir ilişki vardır.

Kısaca:

> **Bilgi güvenliği → Bilginin korunmasına odaklanır.**
> **Siber güvenlik → Dijital sistemlerin ve ortamların güvenliğine odaklanır.**

Terminoloji kullanılan kaynağa veya kuruma göre biraz farklı ele alınabilir. Bu nedenle iki kavramı tamamen birbirinden bağımsız düşünmemek gerekir.

---

## 4. Ağ Güvenliği Nedir?

Ağ güvenliği, siber güvenliğin önemli alt alanlarından biridir.

Ağ üzerindeki cihazların ve iletişimin yetkisiz erişim, kötü amaçlı trafik ve diğer tehditlere karşı korunmasını amaçlar.

Örneğin;

* Firewall kullanılması,
* Ağ trafiğinin izlenmesi,
* Yetkisiz bağlantıların engellenmesi,
* Ağ cihazlarının güvenli şekilde yapılandırılması

ağ güvenliği kapsamında değerlendirilebilir.

### Siber Güvenlik ve Ağ Güvenliği İlişkisi

```text
                 SİBER GÜVENLİK
                       │
        ┌──────────────┼──────────────┐
        │              │              │
     Ağ Güvenliği   Web Güvenliği   SOC
        │              │              │
     Trafik        Uygulamalar      Loglar
     Firewall      API'ler          Alarmlar
     Ağ cihazları  Web saldırıları  Olaylar
```

Bu nedenle ağ güvenliği, siber güvenliğin tamamı değildir; onun önemli alt alanlarından biridir.

---

## 5. Saldırı Olmadan da Güvenlik Problemi Olabilir mi?

Evet.

Bir sistemin güvenlik problemi yaşaması için mutlaka bir saldırganın sisteme saldırması gerekmez.

Örneğin:

### Yetkisiz erişim

Bir çalışanın erişmemesi gereken özel dosyalara erişebilmesi bir güvenlik problemidir.

### Yanlış yapılandırma

Bir şirketin özel dosyalarının yanlışlıkla internette herkese açık hâle getirilmesi güvenlik problemi oluşturabilir.

### Güncellenmemiş yazılım

Eski ve güvenlik açıkları bulunan bir yazılımın kullanılmaya devam edilmesi sistemi saldırılara karşı savunmasız hâle getirebilir.

### Doğal afet

Bir sunucunun bulunduğu ortamın doğal afet nedeniyle kullanılamaması da sistemin erişilebilirliğini etkileyebilir.

Bu nedenle güvenlik sadece "bir hacker sisteme saldırdı mı?" sorusundan ibaret değildir.

---

## 6. Güvenlik Açığı Nedir?

**Güvenlik açığı (vulnerability)**, bir sistemde saldırgan tarafından kötüye kullanılabilecek zayıflık veya eksikliktir.

Örneğin:

> Güncellenmemiş bir yazılımda bilinen bir güvenlik açığının bulunması.

Bu durumda yazılımın kendisi bir sistem olabilir ancak içerisindeki zayıflık güvenlik açığıdır.

Güvenlik açığı ile tehdit aynı şey değildir.

* **Tehdit:** Sisteme zarar verme potansiyeli taşıyan durum, olay veya aktör.
* **Güvenlik açığı:** Sistemde bulunan zayıflık.
* **Saldırı:** Bu zayıflıktan yararlanmak veya sisteme zarar vermek için gerçekleştirilen eylem.

Bu kavramları ilerleyen bölümlerde daha detaylı inceleyeceğim.

---

## 7. Siber Güvenlik Neden Önemlidir?

Günümüzde kişisel bilgilerden banka işlemlerine, hastane sistemlerinden şirketlerin kritik verilerine kadar birçok bilgi dijital ortamlarda tutulmaktadır.

Bir sistem yeterince korunmazsa;

* Kişisel veriler açığa çıkabilir.
* Hesaplar ele geçirilebilir.
* Veriler değiştirilebilir veya silinebilir.
* Sistemler kullanılamaz hâle gelebilir.
* Şirketlerin çalışmaları aksayabilir.
* Maddi kayıplar oluşabilir.

Bu nedenle siber güvenlik yalnızca teknik bir konu değildir. Aynı zamanda şirketlerin, kurumların ve bireylerin güvenliği açısından önemli bir konudur.

---

## 8. Günlük Hayattan Basit Bir Örnek

Bir bankanın internet bankacılığı sistemini düşünelim.

Sistemde müşterilerin;

* Hesap bilgileri,
* Para transferleri,
* Kişisel bilgileri,
* İşlem geçmişleri

bulunuyor.

Burada siber güvenliğin amacı sadece bir saldırganın sisteme girmesini engellemek değildir.

Aynı zamanda:

* Yetkisiz kişilerin müşteri bilgilerini görmemesi,
* Hesap bilgilerinin izinsiz değiştirilmemesi,
* Bankacılık sisteminin müşteriler tarafından kullanılabilir olması,
* Şüpheli işlemlerin fark edilmesi,
* Bir güvenlik olayı olduğunda müdahale edilebilmesi

gerekir.

Bu örnek bana siber güvenliğin aslında birden fazla güvenlik ihtiyacını aynı anda ele aldığını gösteriyor.

---

## 9. Öğrendiklerim

Bu konuyu çalışmadan önce siber güvenliği daha çok hacker saldırılarını önlemek olarak düşünüyordum.

Çalışma sonrasında ise siber güvenliğin;

**önleme + tespit etme + müdahale etme + sistemi tekrar güvenli hâle getirme**

süreçlerini kapsayan daha geniş bir alan olduğunu öğrendim.

Ayrıca;

* Ağ güvenliğinin siber güvenliğin bir alt alanı olduğunu,
* Bilgi güvenliği ile siber güvenliğin ilişkili fakat aynı kavramlar olmadığını,
* Güvenlik problemlerinin saldırı olmadan da ortaya çıkabileceğini,
* Güncellenmemiş yazılımların ve yanlış yapılandırmaların da güvenlik riski oluşturabileceğini

öğrendim.

---

## 10. Kendi Kontrol Sorularım

### Soru 1

Siber güvenlik nedir?

**Cevabım:**
Siber güvenlik, bir sistemin dış veya iç saldırılardan korunması ve güvenliğinin sağlanması için kullanılan yöntemlerin ve süreçlerin genelidir. Ancak sadece saldırıları engellemekle sınırlı değildir; güvenlik açıklarının tespit edilmesi, olaylara müdahale edilmesi ve sistemin tekrar güvenli hâle getirilmesi de bu alanın içerisindedir.

### Soru 2

Siber güvenlik yalnızca hackerları engellemek midir?

**Cevabım:**
Hayır. Siber güvenlik; saldırıları önlemenin yanında güvenlik açıklarını tespit etmeyi, tehditleri değerlendirmeyi, şüpheli aktiviteleri fark etmeyi ve güvenlik olaylarına müdahale etmeyi de kapsar.

### Soru 3

Bilgi güvenliği ile siber güvenlik arasındaki fark nedir?

**Cevabım:**
Bilgi güvenliği bilginin korunmasına odaklanırken, siber güvenlik özellikle dijital sistemlerin, ağların, uygulamaların ve cihazların güvenliğine odaklanır.

### Soru 4

Ağ güvenliği siber güvenliğin neresindedir?

**Cevabım:**
Ağ güvenliği, siber güvenliğin önemli alt alanlarından biridir. Ağları, ağ üzerindeki cihazları ve iletişimi tehditlere ve yetkisiz erişime karşı korumayı amaçlar.

### Soru 5

Bir şirket saldırıya uğramadan güvenlik problemi yaşayabilir mi?

**Cevabım:**
Evet. Örneğin yetkisiz bir kullanıcının özel dosyalara erişebilmesi, bir dosyanın yanlışlıkla herkese açık hâle gelmesi, eski ve güvenlik açığı bulunan yazılımların kullanılması veya doğal afet nedeniyle sistemlerin kullanılamaması güvenlik problemi oluşturabilir.

---

## 11. Kısa Özet

```text
SİBER GÜVENLİK
      │
      ├── Sistemleri korur
      ├── Ağları korur
      ├── Uygulamaları korur
      ├── Cihazları korur
      ├── Dijital verileri korur
      │
      ├── Önleme
      ├── Tespit
      ├── Müdahale
      └── Kurtarma
```

### Akılda Tutulması Gerekenler

> **Siber güvenlik = sadece hackerları engellemek değildir.**

> **Ağ güvenliği = siber güvenliğin önemli bir alt alanıdır.**

> **Güvenlik problemi oluşması için mutlaka saldırı gerçekleşmesi gerekmez.**

> **Güvenlik açığı = sistemdeki zayıflıktır.**

---

## Kaynaklar

* NIST — National Institute of Standards and Technology
* CISA — Cybersecurity and Infrastructure Security Agency
* NIST Cybersecurity Framework
* NIST Computer Security Resource Center
