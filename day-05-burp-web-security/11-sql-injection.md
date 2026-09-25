# SQL Injection

Bu çalışmada SQL Injection'ın temel mantığını araştırdım ve OWASP Juice Shop üzerinde Burp Suite Repeater kullanarak kontrollü bir doğrulama yaptım.

Amacım sistemi ele geçirmek değil, yalnızca:

> **"Bu giriş alanı SQL Injection açısından savunmasız olabilir mi?"**

sorusuna cevap vermekti.

---

## 1. SQL Injection Nedir?

SQL Injection, kullanıcı tarafından girilen verinin güvenli şekilde işlenmeyerek SQL sorgusunun yapısını etkileyebilmesiyle ortaya çıkan bir güvenlik problemidir.

Normalde kullanıcıdan alınan veri sadece **veri** olarak değerlendirilmelidir.

Örneğin login sırasında uygulama veritabanına kullanıcının email ve parola bilgilerini kontrol eden bir SQL sorgusu gönderir.

Eğer kullanıcı girdisi doğrudan SQL sorgusuna eklenirse, özel karakterler sorgunun yapısını etkileyebilir.

Bu durum SQL Injection riskine neden olur.

---

## 2. SQL Injection Neden Oluşur?

Temel nedenlerden biri, kullanıcı girdisinin SQL sorgusuna güvenli olmayan şekilde doğrudan eklenmesidir.

Güvensiz yaklaşımda mantık kabaca şöyledir:

```text
Kullanıcı girdisi
      ↓
SQL sorgusunun içine doğrudan eklenir
      ↓
Kullanıcı girdisi SQL yapısını etkileyebilir
```

Bu nedenle kullanıcı girdisinin SQL sorgusundan ayrılması gerekir.

---

## 3. Parametreli Sorgu Nedir?

Parametreli sorguda SQL sorgusunun yapısı ile kullanıcıdan gelen veri birbirinden ayrılır.

Örneğin:

```sql
SELECT * FROM Users WHERE email = ?
```

Buradaki `?` yerine kullanıcıdan gelen değer ayrı bir parametre olarak gönderilir.

Böylece kullanıcı tarafından girilen özel karakterlerin SQL komutu olarak yorumlanmasının önüne geçilir.

---

## 4. Prepared Statement Nedir?

Prepared Statement, SQL sorgusunun önceden belirlenen yapısının kullanıcı tarafından gönderilen veriden ayrılmasını sağlayan bir yöntemdir.

Örneğin:

```text
SQL sorgusu:
SELECT * FROM Users WHERE email = ?

Parametre:
test'
```

Burada `test'` SQL sorgusunun bir parçası değil, sadece veri olarak değerlendirilir.

Bu nedenle SQL Injection'a karşı temel savunma yöntemlerinden biridir.

---

# 5. Juice Shop Üzerinde Kontrollü Test

## Giriş Noktası

Juice Shop login endpoint'ini kullandım:

```text
POST /rest/user/login
```

Bu endpoint üzerinde Burp Suite ile normal login isteğini yakaladım ve **Repeater** üzerinden tekrar gönderdim.

Daha sonra yalnızca `email` parametresini değiştirdim.

Normal bir email yerine kontrollü olarak:

```text
test'
```

değerini gönderdim.

Gerçek parola, token, cookie veya diğer hassas bilgileri rapora eklemedim.

---

## Parametre

Test ettiğim parametre:

```text
email
```

Gönderdiğim test değeri:

```text
test'
```

Buradaki tek tırnak karakterini özellikle kullandım çünkü SQL sorgularında özel anlamı olan bir karakterdir.

---

## Test Yöntemi

Testi:

```text
Burp Suite
    ↓
Repeater
    ↓
POST /rest/user/login
    ↓
email parametresini değiştirme
    ↓
test'
```

şeklinde gerçekleştirdim.

Amaç herhangi bir veri elde etmek veya login mekanizmasını atlatmak değildi.

Sadece uygulamanın bu girdiye nasıl tepki verdiğini gözlemledim.

---

# 6. Uygulamanın Davranışı

Gönderdiğim isteğe uygulama:

```text
500 Internal Server Error
```

cevabını verdi.

Response içerisinde ayrıca:

```text
SQLITE_ERROR
SequelizeDatabaseError
```

gibi veritabanı ve ORM ile ilgili hata bilgileri görüldü.

Response içerisinde uygulamanın oluşturduğu SQL sorgusuna ilişkin detayların da açığa çıktığını gözlemledim.

Bu durum, kullanıcı girdisinin SQL sorgusunun işlenmesini etkileyebildiğini gösteren önemli bir bulguydu.

---

# 7. Zafiyet

Bu test sonucunda **SQL Injection açısından güçlü bir zafiyet göstergesi** gözlemledim.

Bunun nedeni, kullanıcı tarafından gönderilen özel karakterin SQL sorgusunun yapısını etkileyerek veritabanı hatasına neden olmasıdır.

Ancak bu testte herhangi bir veri çekme veya login mekanizmasını aşma işlemi gerçekleştirmedim.

Bu nedenle bulguyu:

> **SQL Injection açısından savunmasızlık göstergesi**

olarak değerlendiriyorum.

---

# 8. Ek Güvenlik Problemi: Hata Bilgilerinin Açığa Çıkması

Response içerisinde SQLite ve Sequelize ile ilgili teknik hata bilgileri ve SQL sorgusuna ilişkin detaylar görüldü.

Bu bilgiler normal bir kullanıcıya gösterilmemelidir.

Çünkü saldırgan açısından:

* Kullanılan veritabanı teknolojisi
* Kullanılan ORM
* SQL sorgusunun yapısı
* Uygulamanın backend çalışma şekli

hakkında bilgi sağlayabilir.

Bu nedenle aynı test **Information Disclosure** açısından da değerlendirilebilir.

---

# 9. Etkisi

Eğer uygulamada gerçekten güvenli olmayan SQL sorgusu kullanılıyorsa, saldırgan kullanıcı girdilerini kullanarak SQL sorgularının davranışını etkileyebilir.

Bunun sonucunda uygulamanın veritabanı işlemleri üzerinde beklenmeyen sonuçlar ortaya çıkabilir.

Benim gerçekleştirdiğim kontrollü testte ise:

* SQL sorgusunun hata verdiğini,
* Veritabanı hatasının dışarıya döndüğünü,
* SQL sorgusuna ilişkin teknik bilgilerin açığa çıktığını

gözlemledim.

Veri çıkarma veya daha ileri bir istismar gerçekleştirmedim.

---

# 10. Çözüm

SQL Injection riskini azaltmak için:

### 1. Parametreli sorgular kullanılmalı

Kullanıcı girdisi SQL sorgusunun yapısından ayrılmalıdır.

### 2. Prepared Statement kullanılmalı

SQL sorgusu ile kullanıcı verisi birbirinden ayrılmalıdır.

### 3. Input validation uygulanmalı

Beklenen veri formatı kontrol edilmelidir.

### 4. Hata mesajları kullanıcıya gösterilmemeli

Kullanıcıya genel bir hata mesajı gösterilmeli, detaylı hata bilgileri yalnızca sunucu tarafında loglanmalıdır.

### 5. Veritabanı hesabının yetkileri sınırlandırılmalı

Uygulamanın kullandığı veritabanı hesabına gereksiz yetkiler verilmemelidir.

---

# 11. Öğrendiğim

Bu çalışmada SQL Injection'ın sadece belirli bir payload yazıp çalıştırmaktan ibaret olmadığını öğrendim.

Asıl önemli noktanın:

> **Kullanıcı girdisinin SQL sorgusundan ayrılıp ayrılmadığını anlamak**

olduğunu gördüm.

Benim yaptığım testte `test'` girdisinin SQL sorgusunun işlenmesini etkileyerek veritabanı hatasına neden olması, SQL Injection açısından önemli bir belirti oluşturdu.

Ayrıca uygulamanın SQL ve backend hata detaylarını kullanıcıya göndermesinin ayrı bir bilgi sızıntısı problemi oluşturabileceğini de gözlemledim.

Bu nedenle güvenli uygulamalarda **parametreli sorgular ve Prepared Statement kullanımı** temel savunma yöntemlerinden biridir.
