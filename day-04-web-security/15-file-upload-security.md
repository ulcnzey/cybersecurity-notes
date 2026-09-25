# 15. File Upload Güvenliği

## 📌 File Upload Güvenliği Nedir?

Web uygulamalarında kullanıcıların dosya yüklemesine izin verilebilir. Örneğin profil fotoğrafı, CV, belge veya başka bir dosya yükleme özelliği bulunabilir.

İlk bakışta basit bir özellik gibi görünse de uygulama doğrudan kullanıcıdan bir dosya aldığı için güvenlik açısından dikkatli tasarlanması gerekir.

Kullanıcı tarafından yüklenen bir dosyanın:

* Dosya uzantısı
* MIME type
* Boyutu
* İçeriği
* Dosya adı
* Saklandığı dizin
* Çalıştırılabilir olup olmadığı

kontrol edilmelidir.

Temel olarak:

```text
Kullanıcı
   ↓
Dosya yükleme
   ↓
Dosya kontrolleri
   ↓
Güvenli şekilde saklama
   ↓
Dosyaya erişim
```

şeklinde kontrollü bir süreç oluşturulmalıdır.

---

# 📌 Dosya Uzantısı Kontrolü

Dosya uzantısı, dosya adının son kısmıdır.

Örneğin:

```text
photo.jpg
image.png
document.pdf
```

Burada:

```text
.jpg
.png
.pdf
```

dosya uzantılarıdır.

Uygulama sadece belirli dosya türlerine izin vermek için uzantı kontrolü yapabilir.

Örneğin:

```text
.jpg
.jpeg
.png
```

gibi.

Ancak yalnızca dosya uzantısına güvenmek yeterli değildir.

Çünkü bir dosyanın adının `.jpg` ile bitmesi, dosyanın gerçekten geçerli bir JPEG dosyası olduğunu garanti etmez.

Bu nedenle:

> **Dosya uzantısı kontrolü yapılabilir ancak tek başına güvenlik kontrolü olarak kullanılmamalıdır.**

---

# 📌 MIME Type Nedir?

MIME type, bir dosyanın içerik türünü belirtmek için kullanılan bilgidir.

Örneğin:

```text
image/jpeg
image/png
application/pdf
text/plain
```

gibi MIME type değerleri kullanılabilir.

Dosya yükleme sırasında HTTP isteğinde:

```text
Content-Type: image/jpeg
```

gibi bir bilgi bulunabilir.

Ancak istemciden gelen MIME type bilgisine tek başına güvenilmemelidir.

Çünkü kullanıcı tarafından gönderilen bu bilgi de değiştirilebilir.

Bu nedenle:

```text
Uzantı
+
MIME type
```

kontrollerinin birlikte kullanılması daha doğru bir yaklaşımdır.

Bununla birlikte gerçek dosya içeriğinin de mümkün olduğunca doğrulanması gerekir.

---

# 📌 Dosya Boyutu

Dosya yükleme sırasında boyut kontrolü de yapılmalıdır.

Örneğin uygulama profil fotoğrafı için:

```text
Maksimum dosya boyutu: 5 MB
```

gibi bir sınır belirleyebilir.

Dosya boyutu sınırlandırılmazsa çok büyük dosyalar:

* Disk alanını tüketebilir.
* Bellek kullanımını artırabilir.
* İşlemci kaynaklarını tüketebilir.
* Ağ trafiğini artırabilir.
* Uygulamanın performansını etkileyebilir.

Bu nedenle dosya yükleme sırasında uygun bir boyut limiti belirlenmelidir.

---

# 📌 Dosya İçeriği

Dosyanın güvenliğini kontrol ederken yalnızca adına ve MIME type bilgisine bakmak yeterli olmayabilir.

Dosyanın gerçek içeriğinin de beklenen dosya türüyle uyumlu olup olmadığı mümkün olduğunca kontrol edilmelidir.

Örneğin:

```text
photo.jpg
```

adındaki bir dosyanın gerçekten geçerli bir JPEG görüntüsü olup olmadığı yalnızca dosya adına bakılarak anlaşılamaz.

Bu nedenle:

```text
Dosya adı
      ↓
Uzantı
      ↓
MIME type
      ↓
Gerçek içerik
```

birlikte değerlendirilmelidir.

---

# 📌 Dosya Adı

Kullanıcı tarafından gönderilen dosya adı da güvenlik açısından önemlidir.

Örneğin:

```text
profile.jpg
```

normal bir dosya adı olabilir.

Ancak kullanıcı tarafından gönderilen dosya adını doğrudan sunucudaki dosya yolu olarak kullanmak güvenli bir yaklaşım değildir.

Bu nedenle uygulama:

* Dosya adını normalize edebilir.
* Beklenmeyen karakterleri sınırlayabilir.
* Güvenli bir dosya adı oluşturabilir.
* Benzersiz dosya isimleri kullanabilir.

Örneğin:

```text
profile.jpg
```

yerine sunucunun:

```text
8f3a91c2.jpg
```

gibi kendi oluşturduğu benzersiz bir isim kullanması tercih edilebilir.

Böylece hem dosya adı kaynaklı riskler azaltılabilir hem de aynı isimdeki dosyaların çakışması önlenebilir.

---

# 📌 Upload Directory

**Upload directory**, kullanıcıların yüklediği dosyaların sunucuda saklandığı dizindir.

Örneğin:

```text
/uploads/
```

gibi bir klasör kullanılabilir.

Burada önemli olan noktalardan biri, bu klasörde bulunan dosyaların web sunucusu tarafından nasıl işlendiğidir.

Özellikle yüklenen dosyaların çalıştırılabilir kod olarak değerlendirilmemesi gerekir.

Bu nedenle upload dizini:

* Uygulamanın çalıştırılabilir kod alanından ayrılabilir.
* Gereksiz script çalıştırma yetkileri kapatılabilir.
* Uygun dosya ve erişim izinleri kullanılabilir.

Amaç, kullanıcı tarafından yüklenen bir dosyanın sunucu üzerinde beklenmeyen şekilde çalıştırılmasını engellemektir.

---

# 📌 Executable File

**Executable file**, işletim sistemi veya ilgili çalışma ortamı tarafından çalıştırılabilen dosyadır.

Dosya yükleme özelliğinde önemli olan nokta, kullanıcı tarafından yüklenen dosyaların uygulama tarafından çalıştırılabilir hale gelmemesidir.

Örneğin:

```text
Kullanıcı
   ↓
Dosya yükler
   ↓
Sunucu
   ↓
Upload directory
```

şeklinde bir akış olabilir.

Eğer upload dizinindeki dosyalar çalıştırılabiliyorsa, dosya yükleme özelliği daha ciddi bir güvenlik riskine dönüşebilir.

Bu nedenle:

> **Kullanıcı tarafından yüklenen dosyalar mümkün olduğunca veri olarak ele alınmalı ve çalıştırılabilir kod olarak değerlendirilmemelidir.**

---

# 📌 Malware Upload

**Malware upload**, kullanıcının dosya yükleme özelliğini kullanarak zararlı yazılım içeren bir dosyayı sisteme yüklemesi durumudur.

Dosyanın sisteme yüklenmesi tek başına onun çalıştırıldığı anlamına gelmez.

Riskin seviyesi dosyanın:

* Nerede saklandığına,
* Nasıl işlendiğine,
* Kimlerin erişebildiğine,
* Çalıştırılabilir olup olmadığına,
* Başka sistemlere aktarılıp aktarılmadığına

bağlıdır.

Bu nedenle dosya yükleme sisteminde yalnızca uzantı kontrolü yapmak yerine birden fazla güvenlik katmanı uygulanmalıdır.

---

# 🖼️ Senaryo: Sadece JPG Yüklenebilir

Bir uygulamanın:

> "Sadece JPG yükleyebilirsiniz."

dediğini düşünelim.

Eğer uygulama yalnızca:

```text
Dosya adı .jpg ile bitiyor mu?
```

kontrolünü yapıyorsa bu yeterli bir güvenlik kontrolü değildir.

Çünkü:

```text
.jpg uzantısı
      ≠
Gerçek dosya içeriği
```

Dosyanın `.jpg` ile bitmesi onun kesin olarak geçerli ve güvenli bir JPEG olduğunu göstermez.

Bu nedenle dosya yükleme güvenliği katmanlı şekilde ele alınmalıdır.

---

# 🛡️ Daha Güvenli Dosya Yükleme Yaklaşımı

Sadece JPG kabul eden bir uygulamada aşağıdaki kontroller birlikte düşünülebilir:

### 1. Allowlist

Yalnızca izin verilen dosya türleri kabul edilmelidir.

Örneğin:

```text
JPEG
PNG
```

gibi.

---

### 2. Uzantı Kontrolü

Dosyanın beklenen uzantıya sahip olup olmadığı kontrol edilmelidir.

Örneğin:

```text
.jpg
.jpeg
```

Ancak bu kontrol tek başına yeterli değildir.

---

### 3. MIME Type Kontrolü

Dosyanın belirtilen içerik türü kontrol edilmelidir.

Örneğin:

```text
image/jpeg
```

Ancak istemciden gelen MIME type bilgisine de tek başına güvenilmemelidir.

---

### 4. Dosya İçeriği Kontrolü

Dosyanın gerçekten beklenen dosya türünde olup olmadığı mümkün olduğunca kontrol edilmelidir.

---

### 5. Dosya Boyutu Kontrolü

Uygulamanın ihtiyacına uygun maksimum dosya boyutu belirlenmelidir.

---

### 6. Güvenli Dosya Adı

Kullanıcı tarafından gönderilen dosya adı doğrudan kullanılmak yerine sunucu tarafından güvenli ve benzersiz bir dosya adı oluşturulabilir.

---

### 7. Güvenli Upload Directory

Yüklenen dosyalar uygulamanın çalıştırılabilir kod alanından ayrı bir dizinde saklanabilir.

---

### 8. Dosya Çalıştırılmasının Engellenmesi

Upload dizininde bulunan kullanıcı dosyalarının çalıştırılabilir kod olarak değerlendirilmesi engellenmelidir.

---

# 🧠 Defense in Depth

File Upload güvenliğinde tek bir kontrolün yeterli olduğunu düşünmemek gerekir.

Örneğin:

```text
Dosya
 ↓
Uzantı kontrolü
 ↓
MIME type kontrolü
 ↓
İçerik kontrolü
 ↓
Boyut kontrolü
 ↓
Dosya adı kontrolü
 ↓
Güvenli upload directory
 ↓
Çalıştırma kısıtlaması
```

şeklinde birden fazla güvenlik katmanı kullanılabilir.

Bu yaklaşım **Defense in Depth**, yani **katmanlı savunma** mantığına örnektir.

Bir kontrolün başarısız olması durumunda diğer kontroller de sistemi korumaya devam edebilir.

---

# 📌 Kısa Özet

| Konu             | Açıklama                                                                                   |
| ---------------- | ------------------------------------------------------------------------------------------ |
| Dosya uzantısı   | İzin verilen dosya türlerini sınırlamak için kullanılır ancak tek başına yeterli değildir  |
| MIME type        | Dosyanın içerik türünü belirtir fakat istemciden gelen bilgiye tek başına güvenilmemelidir |
| Dosya boyutu     | Kaynak tüketimini sınırlamak için kontrol edilmelidir                                      |
| Dosya içeriği    | Dosyanın gerçekten beklenen türde olup olmadığını anlamaya yardımcı olur                   |
| Dosya adı        | Güvenli ve benzersiz şekilde oluşturulmalıdır                                              |
| Upload directory | Yüklenen dosyaların güvenli bir alanda tutulması gerekir                                   |
| Executable file  | Yüklenen dosyaların çalıştırılması engellenmelidir                                         |
| Malware upload   | Zararlı dosyaların sisteme taşınması riskidir                                              |
| Allowlist        | Yalnızca önceden izin verilen türlerin kabul edilmesini sağlar                             |

---

# 🎯 Ana Fikir

File Upload güvenliğini şu şekilde özetleyebilirim:

> **Bir dosyanın `.jpg` uzantısına sahip olması, onun güvenli ve gerçekten bir JPEG dosyası olduğunu tek başına kanıtlamaz.**

Bu nedenle güvenli bir dosya yükleme sistemi:

```text
Uzantı
   +
MIME type
   +
Dosya içeriği
   +
Dosya boyutu
   +
Dosya adı
   +
Güvenli upload directory
   +
Çalıştırma kısıtlamaları
```

gibi birden fazla kontrolü birlikte değerlendirmelidir.

## 📌 Sonuç

Bu konuyu araştırırken file upload özelliğinin aslında sadece "dosyayı sunucuya kaydetmek" olmadığını gördüm.

Kullanıcıdan gelen dosyanın **ne olduğu, ne kadar büyük olduğu, nasıl adlandırıldığı, nerede saklandığı ve sunucu tarafından nasıl işlendiği** birlikte değerlendirilmelidir.

Özellikle sadece dosya uzantısına güvenmek yeterli değildir. Güvenli bir sistemde birden fazla kontrolün birlikte uygulanması gerekir.
