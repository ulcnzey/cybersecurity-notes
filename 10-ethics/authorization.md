# ⚖️ Siber Güvenlikte Etik ve Yetki

Siber güvenlikte teknik bilgi kadar önemli olan konulardan biri de **yetki ve etik kurallardır**.

Bir sistemde bir güvenlik açığı bulunması, o sistemin izinsiz şekilde test edilebileceği anlamına gelmez.

Bu nedenle güvenlik çalışmalarında:

* Yetki
* Kapsam
* İzin
* Kurallar
* Sorumlu açıklama

gibi kavramların bilinmesi gerekir.

---

# 🔐 1. Yetkili Test

**Yetkili test (Authorized Testing)**, bir sistemin güvenliğini test etmek için sistem sahibinden veya yetkili kurumdan izin alınarak yapılan güvenlik çalışmasıdır.

Örneğin bir şirket:

> "Web uygulamamızı güvenlik açısından test edebilirsiniz."

diyerek bir güvenlik araştırmacısına izin verebilir.

Bu durumda test:

* Belirlenmiş sistemler
* Belirlenmiş zaman aralığı
* Belirlenmiş yöntemler
* Belirlenmiş sınırlar

içerisinde gerçekleştirilir.

### Kısaca

> **Yetkili test = İzin verilen sistem üzerinde, belirlenen sınırlar içerisinde güvenlik testi yapmak.**

---

# 🚫 2. Yetkisiz Erişim

**Yetkisiz erişim**, bir sistem veya veriye gerekli izin olmadan erişmeye çalışmak veya erişmek anlamına gelir.

Örneğin:

Bir kişinin başka bir şirketin sistemine:

* İzin almadan erişmeye çalışması
* Kullanıcı hesaplarına erişmesi
* Verileri incelemesi
* Sistem üzerinde değişiklik yapması

yetkisiz erişim kapsamında değerlendirilebilir.

Burada önemli olan nokta:

> **Bir sisteme teknik olarak erişilebilmesi, o sisteme erişme hakkının olduğu anlamına gelmez.**

---

# 📢 3. Sorumlu Açıklama

**Responsible Disclosure (Sorumlu Açıklama)**, güvenlik açığı bulan araştırmacının bu açığı ilgili kurum veya üreticiye uygun şekilde bildirmesi ve sorunun düzeltilmesine yardımcı olması yaklaşımıdır.

Örneğin bir araştırmacı bir yazılımda güvenlik açığı fark etti.

Bunu hemen kamuya açık şekilde paylaşmak yerine:

```text id="7g0j8n"
Güvenlik açığı bulundu
        ↓
Üretici / kurum bilgilendirildi
        ↓
Açığın detayları güvenli şekilde paylaşıldı
        ↓
Üreticinin düzeltme yapması beklendi
        ↓
Gerekli koordinasyon sağlandı
```

şeklinde hareket edilebilir.

Amaç:

> **Güvenlik açığının kötüye kullanılma riskini azaltırken sorunun düzeltilmesine yardımcı olmaktır.**

Açığın nasıl bildirileceği ve ne kadar bilgi paylaşılacağı, ilgili kurumun politikalarına ve yürürlükteki kurallara göre değişebilir.

---

# 🏆 4. Hata Ödülü (Bug Bounty)

**Bug Bounty**, şirketlerin güvenlik araştırmacılarını güvenlik açıklarını sorumlu şekilde bildirmeye teşvik ettiği programlardır.

Bir şirket:

> "Sistemimizde belirlediğimiz kapsam içerisindeki güvenlik açıklarını bize bildirin."

şeklinde bir program oluşturabilir.

Belirli güvenlik açıkları için:

* Para ödülü
* Özel ödüller
* Teşekkür
* Hall of Fame gibi tanıma yöntemleri

sunulabilir.

Ancak bug bounty programlarında en önemli noktalardan biri:

> **Programın kurallarına ve kapsamına uymaktır.**

Bir şirketin bug bounty programının bulunması, şirketin bütün sistemlerinin sınırsız şekilde test edilebileceği anlamına gelmez.

---

# 🎯 5. Kapsam (Scope)

**Scope (Kapsam)**, güvenlik testinde hangi sistemlerin ve faaliyetlerin test edilebileceğini belirleyen sınırdır.

Örneğin:

```text id="70s0un"
Kapsam dahilinde:
example.com
api.example.com
```

Kapsam dışında:

```text id="2s2sxy"
mail.example.com
internal.example.com
```

olabilir.

Bu durumda araştırmacı yalnızca kapsam içerisinde izin verilen sistemleri test etmelidir.

Kapsam ayrıca yalnızca **hangi sistemlerin** test edileceğini değil, bazı durumlarda:

* Hangi testlerin yapılabileceğini
* Hangi testlerin yapılamayacağını
* Hangi zamanlarda test yapılabileceğini
* Hangi verilerin kullanılabileceğini

de belirleyebilir.

### Kısaca

> **Scope = "Nereye kadar test yapabilirim?" sınırı.**

---

# 📜 6. Çatışma Kuralları (Rules of Engagement)

**Rules of Engagement (RoE)**, yetkili güvenlik testinin nasıl gerçekleştirileceğini belirleyen kurallardır.

Kapsam ile yakından ilişkilidir ancak aynı şey değildir.

### Scope

> **Neleri test edebilirim?**

### Rules of Engagement

> **Bu testi hangi kurallara göre gerçekleştirebilirim?**

Örneğin bir güvenlik testinde:

```text id="9w5j2h"
Test edilecek sistemler:
Web uygulaması

Test zamanı:
Hafta sonu

Üretim verilerine erişim:
Yasak

Gerçek kullanıcı verilerine dokunma:
Zorunlu

Acil durumda iletişim:
Belirlenen güvenlik ekibi
```

gibi kurallar bulunabilir.

Bu kurallar test sırasında oluşabilecek istenmeyen etkileri azaltmaya yardımcı olur.

---

# 🔗 7. Bu Kavramları Birbirine Bağlayalım

Basit bir güvenlik testi örneği:

```text id="x0g9vh"
Şirket
  ↓
İzin verir
  ↓
Kapsam belirlenir
  ↓
Kurallar belirlenir
  ↓
Güvenlik testi
  ↓
Bulgu
  ↓
Sorumlu açıklama / raporlama
  ↓
Düzeltme
```

Bug bounty programında da benzer şekilde:

```text id="2gd0v5"
Şirket
   ↓
Bug Bounty Programı
   ↓
Scope
   ↓
Kurallar
   ↓
Araştırmacı
   ↓
Güvenlik açığı
   ↓
Bildirim
```

---

# ❓ 8. Bir Araştırmacının Teknik Olarak Erişebildiği Her Sistemi Test Etmesi Neden Doğru Değildir?

Çünkü **erişilebilirlik ile yetki aynı şey değildir.**

Bir sistemin teknik olarak erişilebilir olması yalnızca:

> "Bu sisteme teknik olarak ulaşılabiliyor."

anlamına gelebilir.

Bu durum:

> "Bu sistemi test etmeme izin var."

anlamına gelmez.

Örneğin internete açık bir sunucu düşünelim.

Sunucuya teknik olarak erişilebiliyor olabilir.

Ancak sistem sahibi bu sunucunun güvenlik testi için izin vermemişse araştırmacının kendi kararıyla test yapması doğru değildir.

Çünkü:

* Sistem sahibi izin vermemiş olabilir.
* Hizmet kesintisi oluşabilir.
* Gerçek kullanıcıların verileri etkilenebilir.
* Gizlilik ihlali oluşabilir.
* Kurumun operasyonları zarar görebilir.
* Araştırmacı hukuki ve etik sorunlarla karşılaşabilir.

Bu nedenle güvenlik testlerinde **önceden belirlenmiş yetki ve kapsam** temel prensiplerdendir.

---

# ⚠️ 9. "Yetkim Var" ile "Teknik Olarak Yapabiliyorum" Arasındaki Fark

Bu iki kavram birbirinden tamamen farklıdır.

## Teknik olarak yapabiliyorum

Bir sistemde belirli bir işlemi gerçekleştirebilecek teknik imkana sahip olmak anlamına gelir.

Örneğin:

```text id="l3xk1c"
Sisteme erişebiliyorum.
```

Bu sadece teknik bir durumdur.

---

## Yetkim var

Sistem sahibinin veya yetkili kurumun bu işlemi yapmana izin vermesi anlamına gelir.

Örneğin:

```text id="v6m8ub"
Şirket:
"Bu sistemi test etmenize izin veriyoruz."
```

Bu durumda belirlenen sınırlar içerisinde test yapma yetkisi olabilir.

---

# 🧠 Çok Basit Bir Örnek

Bir binanın kapısının açık olduğunu düşünelim.

```text id="u0w3pq"
Kapı açık
↓
İçeri girmek teknik olarak mümkün
```

Ama:

```text id="1c7bcz"
Kapı açık
≠
İçeri girme iznim var
```

Siber güvenlikte de aynı mantık geçerlidir.

```text id="m4qf6w"
Teknik olarak erişebiliyorum
          ≠
          ↓
Test etme yetkim var
```

Bu ayrım siber güvenlikte temel etik prensiplerden biridir.

---

# 🛡️ 10. Neden Yetki ve Kapsam Bu Kadar Önemli?

Güvenlik testlerinin amacı sistemlere zarar vermek değildir.

Ama kontrolsüz bir test:

* Hizmet kesintisine
* Veri kaybına
* Gizlilik ihlaline
* Gerçek kullanıcıların etkilenmesine
* İş süreçlerinin aksamasına

neden olabilir.

Bu yüzden profesyonel güvenlik çalışmalarında test başlamadan önce:

```text id="v9j3at"
İzin
 ↓
Kapsam
 ↓
Kurallar
 ↓
Test
 ↓
Raporlama
```

gibi bir süreç oluşturulur.

---

# 📝 11. Kendi Öğrenme Notlarım

Bu bölümden öğrendiğim en önemli noktalar:

* **Yetkili Test** → İzin verilen sistem üzerinde güvenlik testi yapmak.
* **Yetkisiz Erişim** → Gerekli izin olmadan sisteme veya veriye erişmek.
* **Responsible Disclosure** → Güvenlik açığını uygun şekilde ilgili kuruma bildirmek.
* **Bug Bounty** → Güvenlik açıklarının kurallı bir program kapsamında araştırmacılar tarafından bildirilmesini teşvik eden program.
* **Scope** → Test edilebilecek sistemlerin ve faaliyetlerin sınırı.
* **Rules of Engagement** → Testin nasıl gerçekleştirileceğini belirleyen kurallar.

En önemli öğrendiğim ayrım:

> **"Teknik olarak yapabiliyorum" demek, "bunu yapmaya yetkim var" demek değildir.**

Bir güvenlik araştırmacısı yalnızca:

> **İzin verilen + kapsam içerisinde bulunan + kurallara uygun olan**

sistemleri test etmelidir.

---

# 🎯 Kısa Özet

```text id="2sp4ap"
Yetki
  ↓
Kapsam
  ↓
Kurallar
  ↓
Güvenlik Testi
  ↓
Bulgu
  ↓
Sorumlu Bildirim
  ↓
Düzeltme
```

### En önemli üç cümle:

> **Teknik olarak erişebilmek, erişim yetkisine sahip olmak değildir.**

> **Bir güvenlik açığı bulmak, sistemi sınırsız şekilde test etme hakkı vermez.**

> **Profesyonel güvenlik testi izin, kapsam ve kurallar içerisinde gerçekleştirilir.**

---

# 📚 Kaynaklar

* NIST — Cybersecurity Framework
* NIST — Computer Security Resource Center
* CISA — Vulnerability Disclosure Policy
* OWASP — Web Security Testing Guide
* HackerOne — Bug Bounty / Disclosure Resources
* MITRE — Vulnerability and Security Resources
