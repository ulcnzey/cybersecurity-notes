# 14. Directory Traversal

## 📌 Directory Traversal Nedir?

Directory Traversal, kullanıcı tarafından kontrol edilen bir dosya veya yol bilgisinin yeterince güvenli şekilde doğrulanmaması sonucunda uygulamanın izin verilen dizinin dışındaki dosyalara erişebilmesine yol açabilen bir güvenlik problemidir.

Örneğin bir web uygulamasında:

```text
/download?file=report.pdf
```

şeklinde bir endpoint olduğunu düşünelim.

Uygulamanın amacı belirli bir klasörde bulunan `report.pdf` dosyasını kullanıcıya göndermek olabilir.

Buradaki problem, uygulamanın `file` parametresini doğrudan bir dosya yolunun parçası olarak kullanması ve gerekli kontrolleri yapmaması durumunda ortaya çıkabilir.

Temel olarak:

```text
Kullanıcı girdisi
        ↓
Dosya yolu
        ↓
Dosya sistemi
        ↓
Yetkisiz dosyaya erişim riski
```

şeklinde bir ilişki vardır.

---

# 📌 Path Traversal Nedir?

Path Traversal, dosya sistemindeki dizinler arasında yolun değiştirilerek uygulamanın normalde erişmemesi gereken bir konuma ulaşılmaya çalışılmasıdır.

Dosya sistemlerinde `..` ifadesi genellikle bir üst dizini ifade eder.

Örneğin:

```text
/home/user/project/files/
```

dizinindeyken:

```text
..
```

bir üst dizine çıkmayı ifade eder.

Bu nedenle uygulama kullanıcıdan gelen dosya yolunu güvenli şekilde kontrol etmeden kullanırsa, izin verilen dizinin dışına çıkma riski oluşabilir.

Önemli nokta:

> `..` ifadesinin tek başına bulunması güvenlik açığının kanıtı değildir. Asıl problem, uygulamanın kullanıcı kontrollü yolu güvenli şekilde işleyip işlemediğidir.

---

# 📁 Dosya Sistemi Nedir?

Dosya sistemi, işletim sisteminin dosya ve klasörleri düzenlemek, saklamak ve bunlara erişmek için kullandığı yapıdır.

Örneğin Linux'ta:

```text
/
├── home
├── etc
├── var
├── tmp
└── opt
```

gibi bir yapı bulunabilir.

Bir web uygulaması da sunucudaki dosyalara erişebilir.

Örneğin uygulamanın dosyaları:

```text
/var/www/app/uploads/
```

altında bulunabilir.

Bir kullanıcı dosya indirmek istediğinde web uygulaması bu dosya sistemine erişerek ilgili dosyayı okuyabilir.

Bu nedenle web uygulamasındaki bir kullanıcı girdisinin dosya yolu üzerinde etkili olması güvenlik açısından önemlidir.

---

# 👤 Kullanıcı Girdisi

Web uygulamalarında kullanıcıdan gelen bilgiler **güvenilmeyen veri (untrusted input)** olarak kabul edilmelidir.

Kullanıcı girdileri yalnızca form alanlarından gelmez.

Örneğin:

* URL parametreleri
* Form verileri
* JSON verileri
* Cookie'ler
* HTTP header'ları
* Dosya isimleri
* Arama alanları
* API parametreleri

kullanıcı tarafından etkilenebilir.

Örneğimizde:

```text
/download?file=report.pdf
```

isteğindeki:

```text
file=report.pdf
```

kullanıcı tarafından gönderilen bir girdidir.

Uygulama bunu doğrudan dosya yoluna dönüştürüyorsa dikkatli olunması gerekir.

---

# 🔍 Input Validation Nedir?

Input validation, kullanıcıdan gelen verinin uygulamanın beklediği kurallara uygun olup olmadığının kontrol edilmesidir.

Örneğin uygulamanın yalnızca belirli PDF dosyalarını indirmeye izin verdiğini düşünelim.

Beklenen bir değer:

```text
report.pdf
```

olabilir.

Uygulama gelen değeri kontrol ederek:

* Beklenen formatta mı?
* İzin verilen bir dosya mı?
* Beklenmeyen karakterler içeriyor mu?
* Beklenen uzunlukta mı?
* Kullanıcının erişmesine izin var mı?

gibi kontroller gerçekleştirebilir.

Ancak sadece belirli karakterleri engellemek her zaman yeterli değildir.

Bu nedenle güvenli tasarımda kullanıcı girdisini doğrudan dosya sistemi yolu olarak kullanmamak daha doğru bir yaklaşımdır.

---

# ✅ Allowlist Nedir?

Allowlist, uygulamanın kabul edeceği değerleri önceden belirleyerek yalnızca bu değerlerin kullanılmasına izin vermek anlamına gelir.

Örneğin uygulamanın yalnızca şu dosyaları indirmesine izin verdiğini düşünelim:

```text
report.pdf
manual.pdf
terms.pdf
```

Kullanıcı:

```text
file=report.pdf
```

gönderdiğinde:

```text
report.pdf → Allowlist içinde → İzin ver
```

şeklinde işlem yapılabilir.

Fakat:

```text
file=secret.pdf
```

geldiğinde:

```text
secret.pdf → Allowlist içinde değil → Reddet
```

şeklinde davranılabilir.

Allowlist yaklaşımında temel mantık:

> Her şeyi kabul edip şüpheli olanları engellemek yerine, yalnızca izin verilen değerleri kabul etmektir.

---

# ⚠️ `/download?file=report.pdf` Neden Dikkatli Tasarlanmalıdır?

Bu endpoint basit görünse de backend tarafında dosya sistemine erişim gerçekleşebilir.

Örneğin:

```text
Kullanıcı
   ↓
file=report.pdf
   ↓
Web uygulaması
   ↓
Dosya yolu oluşturulur
   ↓
Dosya sistemi
   ↓
Dosya okunur
   ↓
Kullanıcıya gönderilir
```

Burada `file` parametresi doğrudan dosya sistemindeki yolu etkiliyorsa güvenlik riski oluşabilir.

Bu nedenle uygulamanın:

1. Kullanıcı girdisini güvenilmez kabul etmesi
2. Girdiyi doğrulaması
3. İzin verilen dosyaları kontrol etmesi
4. Kullanıcının dosyaya erişim yetkisini kontrol etmesi
5. Dosya erişimini belirlenen dizinle sınırlandırması
6. Mümkünse allowlist kullanması
7. Uygulamanın dosya sistemi üzerindeki yetkilerini sınırlaması

gerekir.

---

# 🛡️ Directory Traversal Nasıl Önlenebilir?

## 1. Input Validation

Kullanıcıdan gelen dosya veya path bilgisinin beklenen kurallara uygun olup olmadığı kontrol edilmelidir.

---

## 2. Allowlist Kullanımı

Uygulamanın erişmesine izin verilen dosyalar mümkün olduğunca açık şekilde belirlenmelidir.

Örneğin:

```text
report.pdf
manual.pdf
terms.pdf
```

gibi belirli dosyalar tanımlanabilir.

---

## 3. Kullanıcı Girdisini Doğrudan Path Olarak Kullanmamak

Kullanıcının gönderdiği değeri doğrudan gerçek dosya sistemindeki bir path'e dönüştürmek yerine uygulamanın kendi belirlediği güvenli eşlemeler kullanılabilir.

Örneğin:

```text
Kullanıcı:
report.pdf
        ↓
Uygulama
        ↓
İzin verilen dosya kontrolü
        ↓
Sunucunun belirlediği güvenli path
```

şeklinde bir yapı tercih edilebilir.

---

## 4. Dosya Erişim Alanını Sınırlandırmak

Uygulamanın yalnızca ihtiyaç duyduğu dizinlerdeki dosyalara erişebilmesi sağlanmalıdır.

---

## 5. Least Privilege

Web uygulamasının çalıştığı kullanıcıya gereğinden fazla dosya sistemi yetkisi verilmemelidir.

Uygulamanın erişmesi gerekmeyen hassas dosyalara erişim yetkisi bulunmamalıdır.

---

# 🧠 Directory Traversal ile İlgili Önemli Nokta

Directory Traversal'ı sadece:

```text
../
```

ifadesinden ibaret düşünmemek gerekir.

Asıl güvenlik problemi:

```text
Kullanıcı tarafından kontrol edilen veri
             ↓
Dosya yolu üzerinde etki
             ↓
Dosya sistemi erişimi
             ↓
Yetersiz erişim kontrolü
```

zinciridir.

Yani `../` görmek tek başına bir güvenlik açığını kanıtlamaz.

Uygulamanın bu girdiyi nasıl işlediği ve dosya erişimini nasıl kontrol ettiği önemlidir.

---

# 🧪 Laboratuvar Çalışması

Bu konunun uygulamalı kısmı daha sonra kendi eğitim ortamımda, **OWASP Juice Shop** üzerinde kontrollü olarak incelenecektir.

Test sırasında temel olarak:

```text
1. Dosya indirme ile ilgili endpoint belirlenir.
2. Normal bir dosya isteği gözlemlenir.
3. Burp Suite üzerinden HTTP isteği incelenir.
4. Kullanıcı kontrollü parametre belirlenir.
5. Uygulamanın path kontrolü gözlemlenir.
6. Response değerlendirilir.
```

Bu aşamada henüz bir lab sonucu eklemedim. Test tamamlandıktan sonra gerçek request/response gözlemlerimi bu bölüme ekleyeceğim.

---

# 📌 Kısa Özet

| Kavram              | Açıklama                                                                     |
| ------------------- | ---------------------------------------------------------------------------- |
| Directory Traversal | İzin verilen dizinin dışındaki dosyalara erişim riskine yol açabilen problem |
| Path Traversal      | Dosya yolunun değiştirilerek farklı dizinlere ulaşılmaya çalışılması         |
| Dosya sistemi       | İşletim sisteminin dosya ve klasörleri yönettiği yapı                        |
| Kullanıcı girdisi   | Kullanıcı tarafından kontrol edilebilen veri                                 |
| Input Validation    | Gelen verinin beklenen kurallara uygunluğunun kontrol edilmesi               |
| Allowlist           | Yalnızca önceden izin verilen değerlerin kabul edilmesi                      |
| Temel korunma       | Güvenli dosya eşleme, validation, allowlist ve erişim sınırlandırması        |

---

# 🎯 Ana Fikir

Directory Traversal konusunu şu şekilde özetleyebilirim:

> **Kullanıcı tarafından kontrol edilen bir dosya veya yol bilgisinin güvenli şekilde doğrulanmadan dosya sistemine aktarılması, uygulamanın izin verilen alanın dışındaki dosyalara erişmesine neden olabilir.**

Bu nedenle:

```text
Kullanıcı girdisi
      ↓
Validation
      ↓
Authorization
      ↓
İzin verilen dosya/path
      ↓
Dosya sistemi
```

şeklinde kontrollü bir akış oluşturulması gerekir.
