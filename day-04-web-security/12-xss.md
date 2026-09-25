# 12. XSS (Cross-Site Scripting)

## 📌 XSS Nedir?

Cross-Site Scripting (XSS), bir web uygulamasının kullanıcıdan gelen güvenilmeyen verileri güvenli şekilde işlememesi sonucunda bu verilerin başka bir kullanıcının tarayıcısında web sayfasının bir parçası gibi çalışabilmesine neden olan bir web güvenlik problemidir.

XSS'in temelinde şu problem bulunur:

> Güvenilmeyen kullanıcı verisinin tarayıcı tarafından çalıştırılabilecek bir bağlamda kullanılması.

Basit bir akışla:

```text
Kullanıcı girdisi
       ↓
Web uygulaması
       ↓
Güvenli olmayan şekilde işlenir
       ↓
HTTP Response / DOM
       ↓
Kullanıcının tarayıcısı
```

Burada önemli olan nokta, kullanıcıdan gelen verinin uygulama tarafından nasıl işlendiğidir.

---

# 📌 XSS Türleri

XSS'i temel olarak üç farklı şekilde inceleyebiliriz:

* Reflected XSS
* Stored XSS
* DOM-Based XSS

---

## 🔹 1. Reflected XSS

Reflected XSS'te kullanıcı tarafından gönderilen veri genellikle bir HTTP isteğiyle uygulamaya ulaşır ve uygulama bu veriyi kalıcı olarak saklamadan response içerisinde tekrar kullanıcıya gönderir.

Örneğin bir arama sayfasında:

```text
/search?q=arama
```

şeklinde bir parametre olduğunu düşünelim.

Eğer uygulama `q` değerini güvenli şekilde işlemeyip doğrudan HTML içerisine yerleştirirse XSS riski oluşabilir.

Genel akış:

```text
Kullanıcı girdisi
      ↓
HTTP Request
      ↓
Web uygulaması
      ↓
HTTP Response
      ↓
Tarayıcı
```

"Reflected" olarak adlandırılmasının nedeni, gönderilen verinin response içerisinde tekrar yansıtılmasıdır.

---

## 🔹 2. Stored XSS

Stored XSS'te kullanıcı tarafından gönderilen içerik uygulama tarafından kalıcı olarak saklanır.

Örneğin bir yorum sistemi düşünelim:

```text
Kullanıcı
   ↓
Yorum alanı
   ↓
Web uygulaması
   ↓
Veritabanı
   ↓
Yorum sayfası
   ↓
Başka kullanıcı
```

Bir kullanıcı tarafından gönderilen içerik güvenli şekilde işlenmeden veritabanına kaydedilirse ve daha sonra başka kullanıcılara gösterilirse Stored XSS riski ortaya çıkabilir.

Bu nedenle kullanıcıların birbirlerinin içeriklerini görebildiği:

* Yorum sistemleri
* Forumlar
* Mesajlaşma alanları
* Profil açıklamaları
* Gönderi sistemleri

gibi uygulamalarda özellikle önemlidir.

---

## 🔹 3. DOM-Based XSS

DOM-Based XSS'te problem daha çok tarayıcı tarafındaki JavaScript kodunun kullanıcı tarafından kontrol edilebilen verileri güvenli olmayan şekilde DOM'a yerleştirmesiyle ortaya çıkar.

DOM, tarayıcının web sayfasını temsil ettiği yapıdır.

Basit akış:

```text
URL / Kullanıcı girdisi
          ↓
Frontend JavaScript
          ↓
DOM
          ↓
Tarayıcı
```

Bu tür XSS'te sunucunun response'u doğrudan zararlı içeriği içermeyebilir.

Sorun frontend tarafındaki JavaScript kodunun kullanıcı verisini nasıl işlediğiyle ilgili olabilir.

Bu nedenle DOM-Based XSS'i anlamak için sadece backend tarafına değil, frontend JavaScript koduna da bakmak gerekir.

---

# 📌 XSS Neden Oluşur?

XSS'in temel nedeni, güvenilmeyen kullanıcı verisinin uygun şekilde işlenmeden HTML, JavaScript veya başka bir çalıştırılabilir bağlama yerleştirilmesidir.

Web uygulamalarında kullanıcıdan gelen veriler birçok farklı kaynaktan gelebilir:

* Form alanları
* Arama kutuları
* URL parametreleri
* JSON verileri
* API parametreleri
* Cookie değerleri
* Bazı HTTP header değerleri
* Kullanıcı yorumları
* Profil bilgileri

Bu veriler güvenilir kabul edilmemelidir.

Temel problem:

```text
Güvenilmeyen veri
       ↓
Güvenli olmayan şekilde HTML/DOM içine ekleme
       ↓
Tarayıcının veriyi kod olarak yorumlaması
       ↓
XSS riski
```

Bu nedenle güvenlik açısından önemli prensiplerden biri:

> Kullanıcıdan gelen veri, varsayılan olarak güvenilmeyen veri olarak değerlendirilmelidir.

---

# 📌 Saldırganın Amacı Ne Olabilir?

XSS'in etkisi uygulamanın yapısına ve kullanıcının yetkilerine göre değişebilir.

Saldırganın amacı örneğin:

* Kullanıcının gördüğü sayfanın içeriğini değiştirmek
* Kullanıcıyı yanıltıcı bir arayüzle karşılaştırmak
* Kullanıcı etkileşimlerini manipüle etmek
* Hassas bilgileri elde etmeye çalışmak
* Kullanıcı adına bazı işlemler gerçekleştirmeye çalışmak
* Oturumla ilişkili bilgileri kötüye kullanmaya çalışmak

olabilir.

Ancak XSS oluştuğunda otomatik olarak session cookie'nin çalınacağı düşünülmemelidir.

Örneğin `HttpOnly` olarak işaretlenmiş cookie'lere JavaScript tarafından doğrudan erişilemez.

---

# 📌 Kullanıcı Açısından Etkisi

Bir XSS açığından etkilenen kullanıcı açısından sonuçlar uygulamaya göre değişebilir.

Olası etkiler:

* Sayfa içeriğinin değiştirilmesi
* Kullanıcının yanıltılması
* Hassas bilgilerin hedeflenmesi
* Kullanıcının yaptığı işlemlerin etkilenmesi
* Kullanıcının oturumu üzerinden yetkisiz işlemler yapılmaya çalışılması

Burada önemli olan nokta şudur:

> XSS sadece ekrana farklı bir yazı yazdırmak değildir. Asıl güvenlik problemi, saldırgan tarafından kontrol edilen içeriğin başka bir kullanıcının tarayıcısında uygulamanın güvenilir içeriği gibi işlenebilmesidir.

---

# 📌 Input Validation Nedir?

Input validation, uygulamaya gelen verinin beklenen kurallara uygun olup olmadığının kontrol edilmesidir.

Örneğin bir yaş alanı sadece sayı bekliyorsa:

```text
25      → uygun
abc     → uygun değil
```

şeklinde bir kontrol uygulanabilir.

Bir kullanıcı adı için de:

* Belirli uzunluk sınırı
* İzin verilen karakterler
* Boş olmama

gibi kurallar belirlenebilir.

Ancak input validation tek başına XSS'e karşı yeterli değildir.

Çünkü verinin hangi amaçla kullanılacağı da önemlidir.

Bu nedenle input validation'ın yanında uygun **output encoding** uygulanmalıdır.

---

# 📌 Output Encoding Nedir?

Output encoding, kullanıcıdan gelen verinin belirli bir bağlamda güvenli şekilde gösterilebilmesi için uygun biçimde encode edilmesidir.

Amaç, kullanıcı verisinin kod olarak değil **veri olarak** değerlendirilmesini sağlamaktır.

Genel mantık:

```text
Kullanıcı verisi
      ↓
Uygun encoding
      ↓
HTML / JavaScript / URL gibi bağlam
      ↓
Tarayıcı
```

Örneğin HTML içerisinde özel karakterlerin tarayıcı tarafından kod olarak yorumlanmasını önlemek için uygun encoding kullanılabilir.

Burada önemli bir nokta vardır:

> Her bağlam için aynı encoding yöntemi kullanılmaz.

HTML, JavaScript ve URL gibi farklı bağlamların farklı güvenlik gereksinimleri vardır.

---

# 📌 Input Validation ve Output Encoding Farkı

Bu iki kavram birbirine yakın görünse de farklı amaçlara sahiptir.

### Input Validation

```text
"Bu veri beklediğim formata uygun mu?"
```

sorusuna cevap verir.

### Output Encoding

```text
"Bu veriyi bulunduğu bağlamda güvenli şekilde nasıl gösterebilirim?"
```

sorusuna cevap verir.

Bu nedenle güvenli uygulamalarda ikisi farklı katmanlarda kullanılabilir.

---

# 📌 Content Security Policy (CSP)

Content Security Policy (CSP), tarayıcıya web sayfasının hangi kaynaklardan hangi tür içerikleri yükleyebileceğini belirten bir güvenlik politikasıdır.

CSP genellikle HTTP response header üzerinden uygulanabilir.

Örneğin:

```http
Content-Security-Policy: default-src 'self'
```

gibi bir politika kullanılabilir.

CSP ile:

* Script kaynakları
* Stil kaynakları
* Görsel kaynakları
* Frame kullanımı
* Diğer içerik kaynakları

üzerinde çeşitli kısıtlamalar uygulanabilir.

Amaç, tarayıcının yalnızca izin verilen kaynaklarla çalışmasını sağlayarak bazı saldırıların etkisini azaltmaktır.

---

## 🛡️ CSP XSS'i Tamamen Engeller mi?

Hayır.

CSP, XSS'e karşı **ek bir savunma katmanıdır**.

Tek başına güvenli kodlamanın yerini tutmaz.

Daha doğru yaklaşım:

```text
Güvenli kodlama
      +
Input Validation
      +
Uygun Output Encoding
      +
Güvenli framework kullanımı
      +
Content Security Policy
```

şeklinde birden fazla savunma katmanı kullanmaktır.

---

# 🔍 XSS Türlerinin Karşılaştırılması

| XSS Türü      | Veri Kaynağı                     |       Kalıcı mı? | Temel Problem                                                 |
| ------------- | -------------------------------- | ---------------: | ------------------------------------------------------------- |
| Reflected XSS | HTTP Request / kullanıcı girdisi |            Hayır | Girdinin response içinde yansıtılması                         |
| Stored XSS    | Kullanıcı içeriği / veritabanı   |             Evet | Güvenilmeyen içeriğin saklanıp daha sonra gösterilmesi        |
| DOM-Based XSS | URL / DOM / kullanıcı girdisi    | Genellikle hayır | Frontend JavaScript'in veriyi güvensiz şekilde DOM'a işlemesi |

---

# 🧠 SQL Injection ve XSS Arasındaki Fark

SQL Injection ile XSS'i öğrenirken ikisinin farklı katmanlarla ilişkili olduğunu gördüm.

SQL Injection'da temel problem:

```text
Kullanıcı girdisi
      ↓
Web uygulaması
      ↓
SQL sorgusu
      ↓
Veritabanı
```

üzerinde ortaya çıkar.

XSS'te ise temel problem:

```text
Kullanıcı girdisi
      ↓
Web uygulaması / Frontend
      ↓
HTML / DOM
      ↓
Tarayıcı
```

üzerinde ortaya çıkar.

Yani SQL Injection daha çok **veritabanı sorgularının güvenli oluşturulmasıyla**, XSS ise **tarayıcıda kullanıcı verisinin güvenli işlenmesiyle** ilişkilidir.

---

# 🧪 Laboratuvar Notu

Bu bölümde XSS'in çalışma mantığını teorik olarak inceledim.

Önceki bölümlerde olduğu gibi uygulamalı güvenlik çalışmalarında yalnızca kendi kurduğum **OWASP Juice Shop** gibi eğitim amaçlı laboratuvar ortamlarının kullanılması gerektiğini not ettim.

Bu aşamadaki amacım:

* XSS'in ne olduğunu anlamak
* Reflected, Stored ve DOM-Based XSS arasındaki farkı öğrenmek
* Kullanıcı girdisinin neden güvenilmeyen veri olduğunu anlamak
* Input validation ve output encoding arasındaki farkı öğrenmek
* CSP'nin XSS'e karşı nasıl ek bir savunma sağladığını anlamak

olmuştur.

---

# 📌 Kısa Özet

| Konu             | Açıklama                                                                   |
| ---------------- | -------------------------------------------------------------------------- |
| XSS              | Güvenilmeyen içeriğin tarayıcıda çalıştırılabilir bağlamda işlenebilmesi   |
| Reflected XSS    | Girdinin response içinde yansıtılması                                      |
| Stored XSS       | Güvenilmeyen içeriğin saklanıp daha sonra gösterilmesi                     |
| DOM-Based XSS    | Frontend JavaScript'in veriyi DOM'a güvensiz şekilde işlemesi              |
| Input Validation | Gelen verinin beklenen kurallara uygunluğunu kontrol etmek                 |
| Output Encoding  | Veriyi bulunduğu bağlamda güvenli şekilde göstermek                        |
| CSP              | Tarayıcıya izin verilen içerik kaynaklarını belirleyen güvenlik politikası |
| Temel yaklaşım   | Validation + Encoding + Güvenli kodlama + CSP                              |

## 🎯 Ana Fikir

```text
Kullanıcıdan gelen veri
          ↓
     Güvenilmeyen veri
          ↓
  Güvenli şekilde işle
          ↓
   Uygun output encoding
          ↓
    Ek savunma olarak CSP
          ↓
       Tarayıcı
```

XSS konusunda öğrendiğim en önemli nokta, güvenliğin sadece kullanıcıdan gelen veriyi filtrelemekten ibaret olmadığıdır. Verinin **nereden geldiği, uygulama içinde nasıl işlendiği ve tarayıcıya hangi bağlamda gönderildiği** de önemlidir.
