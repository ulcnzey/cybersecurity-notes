# 11. SQL Injection

## 📌 SQL Nedir?

SQL (Structured Query Language), ilişkisel veritabanlarında veri sorgulamak ve yönetmek için kullanılan bir dildir.

Örneğin bir uygulama kullanıcı listesini veritabanından almak istediğinde SQL sorgusu kullanabilir:

```sql
SELECT * FROM users;
```

Bir web uygulamasında genel akış şu şekilde düşünülebilir:

```text
Kullanıcı
   ↓
Tarayıcı
   ↓
Web Uygulaması
   ↓
SQL Sorgusu
   ↓
Veritabanı
   ↓
Sonuç
   ↓
Web Uygulaması
   ↓
Tarayıcı
```

Burada kullanıcı doğrudan veritabanına bağlanmaz. Kullanıcının gönderdiği bilgiler uygulama tarafından işlenir ve uygulama gerekli veritabanı sorgusunu oluşturur.

---

## 📌 SQL Injection Nedir?

SQL Injection (SQLi), kullanıcı tarafından gönderilen güvenilmeyen verilerin güvenli olmayan bir şekilde SQL sorgusuna dahil edilmesi sonucunda ortaya çıkabilen bir web güvenlik açığıdır.

Temel problem, **kullanıcı verisi ile SQL kodunun birbirine karışmasıdır.**

Örneğin uygulamanın kullanıcıdan aldığı bir değeri doğrudan SQL sorgusunun içine eklediğini düşünelim:

```text
Kullanıcı girdisi
       ↓
Web uygulaması
       ↓
SQL sorgusu
       ↓
Veritabanı
```

Eğer kullanıcı girdisi sorgunun yapısını etkileyebiliyorsa, uygulamanın beklemediği sonuçlar ortaya çıkabilir.

Bu nedenle kullanıcıdan gelen veriler güvenilir kabul edilmemelidir.

---

## 📌 SQL Injection Neden Oluşur?

SQL Injection'ın temel nedeni genellikle kullanıcı girdisinin SQL sorgusuna güvenli olmayan şekilde eklenmesidir.

Örneğin uygulamanın sorguyu string birleştirme yöntemiyle oluşturması riskli olabilir:

```text
"SELECT * FROM users WHERE username = '" + username + "'"
```

Burada `username` değeri kullanıcıdan geliyorsa, uygulama bu veriyi doğrudan SQL sorgusunun içine yerleştirmiş olur.

Güvenli yaklaşımda ise SQL sorgusunun yapısı ile kullanıcı tarafından gönderilen veri birbirinden ayrılır.

---

## 📌 Kullanıcı Girdisi Neden Önemlidir?

Web uygulamalarında kullanıcıdan gelen birçok farklı veri vardır:

* Form alanları
* Arama kutuları
* URL parametreleri
* JSON verileri
* API parametreleri
* Cookie değerleri
* Bazı HTTP header değerleri
* Dosya isimleri

Bu verilerin tamamı uygulama açısından **güvenilmeyen girdi (untrusted input)** olarak değerlendirilmelidir.

Örneğin Juice Shop üzerinde ürün araması yaptığımda tarayıcı tarafında şu URL oluştu:

```text
http://localhost:3000/#/search?q=banana
```

Buradaki:

```text
q=banana
```

kısmı kullanıcı tarafından girilen arama değeridir.

Ancak `#` işaretinden sonraki bölüm doğrudan HTTP isteğinin parçası değildir. Frontend bu değeri okuyarak backend'e ayrı bir API isteği gönderebilir.

Daha önce Burp Suite üzerinde şu endpoint'i gözlemledim:

```http
GET /rest/products/search?q= HTTP/1.1
```

Buradaki `q` parametresi arama girdisini temsil etmektedir.

Bu noktada önemli olan şudur:

> Bir parametrenin kullanıcı tarafından kontrol edilebilmesi, tek başına SQL Injection olduğu anlamına gelmez.

Backend'in bu değeri nasıl işlediğini incelemek gerekir.

---

## 📌 Prepared Statement Nedir?

Prepared Statement, SQL sorgusunun yapısının önceden belirlenmesini ve kullanıcıdan gelen değerlerin sorgu yapısından ayrı parametreler olarak gönderilmesini sağlayan bir yöntemdir.

Örneğin:

```sql
SELECT * FROM users WHERE username = ?
```

Buradaki `?` kullanıcıdan gelecek değerin yeridir.

Kullanıcı değeri SQL kodunun bir parçası olarak değil, **veri** olarak ele alınır.

Bu yaklaşım SQL Injection'a karşı en önemli korunma yöntemlerinden biridir.

---

## 📌 Parameterized Query Nedir?

Parameterized Query, SQL sorgusundaki değişken değerlerin sorgu metnine doğrudan eklenmesi yerine parametre olarak gönderilmesi yaklaşımıdır.

Örneğin:

```sql
SELECT * FROM products WHERE name = ?
```

Buradaki ürün adı ayrı bir parametre olarak gönderilir.

Böylece kullanıcı tarafından gönderilen veri ile SQL sorgusunun yapısı birbirinden ayrılmış olur.

Prepared Statement ve Parameterized Query kavramları pratikte birbirine çok yakın şekilde kullanılabilir. Kullanılan programlama dili ve veritabanı kütüphanesine göre uygulama yöntemi değişebilir.

---

## 📌 ORM Kullanmak SQL Injection'ı Tamamen Ortadan Kaldırır mı?

Hayır.

ORM (Object-Relational Mapping), uygulamanın veritabanıyla nesneler üzerinden çalışmasını kolaylaştıran bir yapıdır.

ORM kullanmak birçok durumda doğrudan SQL yazma ihtiyacını azaltabilir ve güvenli sorgu mekanizmaları sağlayabilir.

Ancak ORM kullanılması tek başına SQL Injection riskini tamamen ortadan kaldırmaz.

Örneğin:

* Raw SQL kullanılması
* Dinamik sorguların güvenli olmayan şekilde oluşturulması
* ORM'nin yanlış kullanılması
* Kullanıcı girdisinin sorgu yapısına dahil edilmesi

gibi durumlarda risk devam edebilir.

Bu nedenle güvenlik sadece kullanılan teknolojiye değil, teknolojinin nasıl kullanıldığına da bağlıdır.

---

## 📌 SQL Injection'ın Olası Etkileri

Bir SQL Injection açığının etkisi uygulamanın yapısına ve veritabanı kullanıcısının yetkilerine göre değişebilir.

Olası etkiler arasında:

* Yetkisiz veri okunması
* Verilerin değiştirilmesi
* Verilerin silinmesi
* Hassas bilgilerin açığa çıkması
* Bazı durumlarda kimlik doğrulama mekanizmalarının etkilenmesi
* Veritabanı üzerinde yetkisiz işlemler

bulunabilir.

Bu nedenle SQL Injection yalnızca bir "arama problemi" değil, uygulamanın veri katmanını etkileyebilecek ciddi bir güvenlik problemi olabilir.

---

# 🧪 Juice Shop Laboratuvar Çalışması

Bu konuyu öğrenmek için OWASP Juice Shop'u kendi Kali Linux ortamımda Docker üzerinden çalıştırdım.

Kullandığım uygulama:

```text
OWASP Juice Shop
http://localhost:3000
```

Burp Suite'i proxy olarak kullanarak uygulamanın HTTP trafiğini gözlemledim.

Öncelikle ürün arama işlemini inceledim.

Tarayıcı tarafında örneğin:

```text
http://localhost:3000/#/search?q=banana
```

şeklinde bir URL gördüm.

Buradaki `#` sonrasındaki bölümün doğrudan HTTP isteğine gönderilmediğini öğrendim. Frontend bu bilgiyi kullanarak backend tarafındaki API'ye ayrı bir istek oluşturabiliyor.

Burp Suite üzerinde daha önce şu API isteğini gözlemledim:

```http
GET /rest/products/search?q= HTTP/1.1
Host: localhost:3000
```

Bu istekte `q` parametresinin ürün arama işlemiyle ilişkili olduğunu gördüm.

### Burada öğrendiğim önemli nokta

Bir HTTP parametresinin kullanıcı tarafından kontrol edilebilmesi, o parametrenin otomatik olarak SQL Injection açığı olduğu anlamına gelmez.

Öncelikle:

```text
Kullanıcı girdisi
       ↓
HTTP isteği
       ↓
Backend
       ↓
SQL sorgusu
       ↓
Veritabanı
```

akışının nasıl gerçekleştiğini anlamak gerekir.

Burp Suite bana HTTP katmanını gösterirken, SQL sorgusunun kendisi backend ile veritabanı arasındaki katmanda gerçekleşir.

Bu nedenle Burp'ta gördüğüm:

```text
q=banana
```

değeri, tek başına backend'in bu değeri SQL sorgusuna nasıl dahil ettiğini göstermez.

---

## 🛡️ SQL Injection'dan Korunma

Temel korunma yöntemleri:

* Prepared Statement kullanmak
* Parameterized Query kullanmak
* Kullanıcı girdilerini güvenilir kabul etmemek
* Uygun input validation uygulamak
* ORM'leri güvenli şekilde kullanmak
* Gereksiz veritabanı yetkilerinden kaçınmak
* Veritabanı kullanıcısına en az yetki vermek
* Hata mesajlarında hassas verileri göstermemek
* Güvenlik testleri yapmak

En önemli prensiplerden biri:

> Kullanıcı verisi ile SQL kodunu birbirinden ayırmak.

---

## 🧠 Kendi Çıkardığım Sonuç

SQL Injection'ı öğrenirken sadece özel karakterlerden veya saldırı ifadelerinden bahsetmenin yeterli olmadığını gördüm.

Asıl önemli olan, bir web uygulamasında kullanıcıdan gelen verinin hangi aşamalardan geçtiğini anlamak.

Örneğin:

```text
Kullanıcı
   ↓
Arama kutusu
   ↓
Frontend
   ↓
HTTP Request
   ↓
Backend
   ↓
SQL Query
   ↓
Database
```

Bu zincirde kullanıcıdan gelen verinin güvenli olmayan şekilde SQL sorgusunun yapısına dahil edilmesi SQL Injection riskine yol açabilir.

Bu nedenle SQL Injection'ın temelinde sadece "kötü amaçlı veri" değil, **verinin uygulama tarafından nasıl işlendiği** vardır.

---

## ⚠️ Laboratuvar Notu

Bu çalışmadaki gözlemler yalnızca kendi kurduğum **OWASP Juice Shop** laboratuvar ortamında yapılmıştır.

Gerçek sistemler üzerinde izinsiz SQL Injection testi yapılmamalıdır.

Bu aşamada amacım bir sistemi istismar etmekten çok:

* HTTP isteğini anlamak
* Kullanıcı girdisinin uygulamaya nasıl ulaştığını görmek
* Backend ve veritabanı arasındaki ilişkiyi anlamak
* SQL Injection'ın oluşma mantığını öğrenmek
* Güvenli kodlama yöntemlerini öğrenmek

olmuştur.

---

## 📌 Kısa Özet

| Konu                | Açıklama                                                                            |
| ------------------- | ----------------------------------------------------------------------------------- |
| SQL                 | Veritabanlarıyla iletişim kurmak için kullanılan sorgulama dili                     |
| SQL Injection       | Kullanıcı girdisinin SQL sorgusunun yapısını etkileyebilmesi sonucu oluşabilen açık |
| Untrusted Input     | Kullanıcıdan gelen ve güvenilir kabul edilmemesi gereken veri                       |
| Prepared Statement  | Sorgu yapısını ve veriyi birbirinden ayıran güvenli yaklaşım                        |
| Parameterized Query | Değişken değerlerin sorguya parametre olarak verilmesi                              |
| ORM                 | Veritabanı işlemlerini nesne tabanlı şekilde kolaylaştıran yapı                     |
| Temel korunma       | Parametreli sorgular, input validation ve en az yetki prensibi                      |
| Lab                 | OWASP Juice Shop + Burp Suite                                                       |

### 🎯 Ana fikir

```text
Kullanıcı girdisi ≠ SQL kodu

Güvenli uygulamada kullanıcı verisi,
SQL sorgusunun yapısından ayrı tutulmalıdır.
```
