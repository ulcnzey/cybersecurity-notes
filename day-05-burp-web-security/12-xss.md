# XSS Araştırması

Bu çalışmada Cross-Site Scripting (XSS) konusunu araştırdım ve OWASP Juice Shop üzerinde **DOM XSS** challenge'ını incelemeye başladım.

Amacım, kullanıcı tarafından girilen bir verinin web uygulaması tarafından nasıl işlendiğini ve tarayıcı tarafında DOM'a güvenli olmayan şekilde aktarılıp aktarılmadığını anlamaktı.

---

## 1. XSS Nedir?

XSS (Cross-Site Scripting), kullanıcı tarafından kontrol edilebilen bir girdinin web uygulaması tarafından güvenli şekilde işlenmemesi sonucunda tarayıcıda HTML veya JavaScript olarak yorumlanabilmesiyle ortaya çıkan bir güvenlik problemidir.

Temel mantık:

```text
Kullanıcı girdisi
      ↓
Web uygulaması
      ↓
Güvenli şekilde işlenmezse
      ↓
Tarayıcı / DOM
      ↓
Kod olarak yorumlanabilir
```

---

## 2. XSS Türleri

### Reflected XSS

Kullanıcı tarafından gönderilen veri sunucuya gider ve response içerisinde tekrar kullanıcıya yansıtılır.

```text
Kullanıcı girdisi
      ↓
Sunucu
      ↓
Response
      ↓
Tarayıcı
```

Veri genellikle kalıcı olarak kaydedilmez.

---

### Stored XSS

Kullanıcı tarafından gönderilen veri veritabanı gibi kalıcı bir alana kaydedilir.

Daha sonra bu veri başka bir kullanıcıya gösterildiğinde tarayıcı tarafından güvenli olmayan şekilde işlenirse XSS oluşabilir.

```text
Kullanıcı girdisi
      ↓
Sunucu
      ↓
Veritabanı
      ↓
Başka kullanıcı
      ↓
Tarayıcı
```

---

### DOM XSS

DOM XSS'te problem özellikle tarayıcı tarafındaki JavaScript kodunun kullanıcı tarafından kontrol edilebilen veriyi DOM'a güvenli olmayan şekilde yerleştirmesiyle ortaya çıkar.

```text
Kullanıcı girdisi
      ↓
JavaScript
      ↓
DOM
      ↓
Tarayıcı
```

Bu nedenle DOM XSS araştırmasında yalnızca HTTP request ve response'a bakmak yeterli olmayabilir. Uygulamanın istemci tarafında veriyi nasıl işlediğini de incelemek gerekir.

---

# 3. Juice Shop DOM XSS Challenge

Juice Shop'ın Score Board bölümünde XSS filtresini kullanarak ilgili challenge'ları araştırdım.

XSS ile ilgili iki challenge gördüm:

* DOM XSS
* Bonus Payload

Bu çalışmada **DOM XSS** challenge'ı üzerinden ilerledim.

Challenge'ın verdiği ipucunda, bir form gönderildiğinde kullanıcının girdiği içeriğin HTML içerisinde göründüğü bir input alanının bulunması gerektiği belirtiliyordu.

Ayrıca challenge, DOM XSS ile Reflected XSS arasındaki farkı anlamak için uygulamanın kullanıcı girdisini arka planda nasıl işlediğinin incelenmesini istiyordu.

---

# 4. İlk İnceleme: Ürün Arama Alanı

İlk olarak Juice Shop içerisindeki ürün arama alanını incelemeye başladım.

Burp Suite üzerinden arama işlemi sırasında aşağıdaki endpoint'i gözlemledim:

```http
GET /rest/products/search?q=
```

Burada:

* **Method:** GET
* **Endpoint:** `/rest/products/search`
* **Parametre:** `q`

Parametrenin ürün araması için kullanıldığını gözlemledim.

---

## 5. Kontrollü Veri Testi

Arama endpoint'ini Burp Suite Repeater üzerinden incelemek için `q` parametresine zararsız bir test değeri gönderdim:

```text
test123
```

İstek:

```http
GET /rest/products/search?q=test123
```

Uygulamanın response'u:

```json
{
  "status": "success",
  "data": []
}
```

şeklinde oldu.

Response içerisinde `test123` değerinin doğrudan geri döndürülmediğini gözlemledim.

Bu nedenle yalnızca bu endpoint üzerinden **DOM XSS bulunduğu sonucuna varmadım.**

---

# 6. Mevcut Bulgum

Bu aşamada ürün arama alanının:

* `q` isimli bir parametre kullandığını,
* bu parametrenin `/rest/products/search` endpoint'ine gönderildiğini,
* gönderilen test değerinin response içerisinde doğrudan geri döndürülmediğini

gözlemledim.

Dolayısıyla bu test, DOM XSS'in doğrulanması için yeterli değildir.

Challenge'ın istediği giriş noktasını belirlemek için uygulamadaki diğer form alanlarının ve istemci tarafındaki veri işleme sürecinin incelenmesi gerekmektedir.

---

# 7. Bulguyu Açıklama

### XSS türü:

**DOM XSS araştırması**

### Giriş noktası:

İlk incelenen giriş noktası ürün arama alanındaki `q` parametresidir.

```text
/rest/products/search?q=
```

### Veri nereye gönderiliyor?

Kullanıcı tarafından girilen değer `q` parametresi üzerinden `/rest/products/search` endpoint'ine gönderilmektedir.

### Uygulama nasıl davranıyor?

`test123` değeri gönderildiğinde uygulama:

```json
{
  "status": "success",
  "data": []
}
```

response'unu döndürmüştür.

Test değeri response içerisinde doğrudan görünmemiştir.

### Potansiyel etki:

Eğer kullanıcı kontrollü bir veri istemci tarafındaki JavaScript tarafından güvenli olmayan şekilde DOM'a aktarılırsa DOM XSS oluşabilir.

Ancak yaptığım bu ilk testte DOM XSS'in gerçekleştiğine dair yeterli kanıt elde edilmemiştir.

### Korunma yöntemi:

* Kullanıcı girdileri güvenli şekilde işlenmelidir.
* HTML içerisine veri eklenirken uygun output encoding uygulanmalıdır.
* Güvenilmeyen veriler doğrudan HTML olarak yorumlanmamalıdır.
* İstemci tarafındaki JavaScript'te güvenli DOM API'leri tercih edilmelidir.
* Gerektiğinde Content Security Policy (CSP) gibi ek güvenlik mekanizmaları kullanılmalıdır.

---

# 8. Öğrendiğim

Bu çalışmada XSS'in tek bir türden oluşmadığını ve Reflected, Stored ve DOM XSS arasındaki temel farkların veri akışıyla ilgili olduğunu öğrendim.

Özellikle DOM XSS'te sadece HTTP request ve response'a bakmanın yeterli olmayabileceğini, uygulamanın JavaScript tarafında kullanıcı verisini DOM'a nasıl aktardığının da incelenmesi gerektiğini gördüm.

Ayrıca bir input alanının request oluşturması tek başına XSS bulunduğu anlamına gelmemektedir. Bir güvenlik bulgusu oluşturabilmek için kullanıcının kontrol ettiği verinin uygulamada nasıl işlendiğini ve tarayıcıda nasıl yorumlandığını gözlemlemek gerekir.
