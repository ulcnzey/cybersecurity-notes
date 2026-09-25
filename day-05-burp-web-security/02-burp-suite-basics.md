# 02 — Burp Suite Basics

## Burp Suite Nedir?

Burp Suite, web uygulamalarının güvenlik testlerinde kullanılan bir araçtır. Bir web uygulaması ile kullanıcı arasındaki HTTP/HTTPS trafiğini incelemeye, istekleri yakalamaya ve kontrollü şekilde değiştirmeye yardımcı olur.

Burp Suite'i özellikle web uygulamasının tarayıcı ile sunucu arasında nasıl iletişim kurduğunu görmek için kullanabilirim.

Bir web uygulamasında kullanıcı giriş yaptığında, bir sayfayı açtığında veya bir işlem gerçekleştirdiğinde tarayıcı ile sunucu arasında HTTP istekleri ve cevapları oluşur. Burp Suite bu iletişimi incelememi sağlar.

---

## Web Güvenliği Testlerinde Neden Kullanılır?

Burp Suite kullanarak bir web uygulamasının gönderdiği HTTP isteklerini ve aldığı cevapları inceleyebilirim.

Örneğin;

* Hangi HTTP metodunun kullanıldığını,
* İsteğin hangi endpoint'e gönderildiğini,
* Header bilgilerinin neler olduğunu,
* Cookie veya token kullanılıp kullanılmadığını,
* İstek içerisinde hangi parametrelerin gönderildiğini,
* Sunucunun hangi HTTP status code ile cevap verdiğini,
* İstek veya parametre değiştirildiğinde uygulamanın nasıl davrandığını

görebilirim.

Bu nedenle Burp Suite, web uygulamalarındaki güvenlik kontrollerini anlamak ve güvenlik testleri gerçekleştirmek için kullanılan önemli araçlardan biridir.

---

# Burp Suite Temel Bileşenleri

## 1. Proxy

### Araç

**Proxy**

### Ne işe yarar?

Burp Suite Proxy, tarayıcı ile web sunucusu arasındaki HTTP/HTTPS trafiğini yakalamamı sağlar.

Normalde tarayıcı bir web sunucusuna doğrudan istek gönderir. Burp Suite kullanıldığında trafik Burp Proxy üzerinden geçirilebilir.

Temel akış şu şekilde düşünülebilir:

```text
Browser
   ↓
Burp Proxy
   ↓
Web Server
   ↓
Burp Proxy
   ↓
Browser
```

### Web güvenliği testinde neden kullanılır?

Proxy sayesinde gönderilen HTTP isteklerini ve sunucudan gelen cevapları inceleyebilirim.

Ayrıca güvenlik testlerinde istekleri kontrollü olarak durdurup incelemek veya değiştirmek için kullanılabilir.

---

## 2. HTTP History

### Araç

**HTTP History**

### Ne işe yarar?

HTTP History, Burp Suite üzerinden geçen HTTP isteklerinin ve cevaplarının geçmişini gösterir.

Burada hangi endpoint'e istek gönderildiğini, kullanılan HTTP metodunu, status code'u ve diğer HTTP bilgilerini inceleyebilirim.

### Web güvenliği testinde neden kullanılır?

HTTP History sayesinde uygulamanın hangi endpoint'lerle iletişim kurduğunu görebilirim.

Örneğin;

```text
GET  /api/products
POST /api/login
GET  /api/users
```

gibi istekleri inceleyerek uygulamanın kullandığı API ve kaynaklar hakkında bilgi edinebilirim.

İlerleyen testlerde hangi isteklerin daha ayrıntılı incelenmesi gerektiğini belirlemek için de kullanılabilir.

---

## 3. Repeater

### Araç

**Repeater**

### Ne işe yarar?

Repeater, daha önce yakalanmış bir HTTP isteğini tekrar tekrar göndermemi ve isteği kontrollü şekilde değiştirmemi sağlar.

Örneğin bir isteğin;

* Parametresini,
* Header bilgisini,
* HTTP metodunu,
* Request body içeriğini

değiştirip sunucunun verdiği cevabı karşılaştırabilirim.

### Web güvenliği testinde neden kullanılır?

Repeater, belirli bir isteğin uygulama tarafından nasıl işlendiğini anlamak için kullanılır.

Örneğin bir parametrenin değerini değiştirdiğimde sunucunun cevabının değişip değişmediğini inceleyebilirim.

Bu nedenle manuel web güvenlik testlerinde önemli araçlardan biridir.

---

## 4. Intruder

### Araç

**Intruder**

### Ne işe yarar?

Intruder, belirli bir HTTP isteğindeki seçilen alanlara farklı değerler göndererek tekrarlı ve kontrollü testler yapmaya yardımcı olur.

Örneğin bir parametrenin farklı değerlerle nasıl işlendiğini test etmek için kullanılabilir.

### Web güvenliği testinde neden kullanılır?

Intruder özellikle çok sayıda kontrollü isteğin gerektiği durumlarda kullanılabilir.

Örneğin;

* Parametre davranışlarını test etmek,
* Farklı giriş değerlerini denemek,
* Belirli bir alanın uygulama tarafından nasıl işlendiğini incelemek

gibi işlemlerde kullanılabilir.

Intruder kullanılırken testin yalnızca yetkili ve izole sistemlerde gerçekleştirilmesi gerekir.

---

## 5. Decoder

### Araç

**Decoder**

### Ne işe yarar?

Decoder, çeşitli veri formatlarını decode veya encode etmeye yardımcı olan Burp Suite aracıdır.

Örneğin Base64 gibi kodlanmış verilerin okunabilir hale getirilmesinde kullanılabilir.

### Web güvenliği testinde neden kullanılır?

Web uygulamalarında bazı değerler doğrudan okunabilir şekilde gönderilmeyebilir.

Decoder kullanarak bir verinin hangi formatta olduğunu anlamaya ve gerektiğinde decode ederek içeriğini incelemeye çalışabilirim.

Burada önemli bir nokta, **encoding ile encryption'ın aynı şey olmadığıdır**.

Örneğin Base64 bir şifreleme yöntemi değildir. Kodlanmış veriyi tekrar decode etmek mümkündür.

---

## 6. Comparer

### Araç

**Comparer**

### Ne işe yarar?

Comparer, iki farklı HTTP isteğini veya cevabını karşılaştırmama yardımcı olur.

İki veri arasındaki farklılıkları daha kolay görebilirim.

### Web güvenliği testinde neden kullanılır?

Bir istekte küçük bir değişiklik yaptığımda sunucunun verdiği cevabın nasıl değiştiğini karşılaştırmak için kullanılabilir.

Örneğin;

```text
Normal Request
      ↓
Response A

Değiştirilmiş Request
      ↓
Response B
```

şeklinde iki sonucu karşılaştırarak uygulamanın davranışındaki farklılıkları inceleyebilirim.

---

# Burp Suite Bileşenlerini Kısaca Karşılaştırma

| Bileşen      | Temel amacı                                                 |
| ------------ | ----------------------------------------------------------- |
| Proxy        | HTTP/HTTPS trafiğini yakalamak ve incelemek                 |
| HTTP History | Geçmiş HTTP isteklerini ve cevaplarını görmek               |
| Repeater     | Bir isteği tekrar göndermek ve kontrollü olarak değiştirmek |
| Intruder     | Farklı değerlerle tekrarlı testler yapmak                   |
| Decoder      | Verileri encode/decode etmek                                |
| Comparer     | İki isteği veya cevabı karşılaştırmak                       |

---

# Bu Bölümde Öğrendiklerim

Bu bölümde Burp Suite'in web güvenlik testlerinde kullanılan temel bileşenlerini öğrendim.

Özellikle Proxy'nin tarayıcı ile sunucu arasındaki HTTP/HTTPS trafiğini incelemek için kullanıldığını, HTTP History'nin geçmiş istekleri görmemi sağladığını öğrendim.

Repeater ile istekleri tekrar gönderip kontrollü değişiklikler yapabileceğimi, Intruder'ın tekrarlı testlerde kullanılabildiğini, Decoder'ın veri formatlarını encode/decode etmek için kullanıldığını ve Comparer'ın iki farklı isteği veya cevabı karşılaştırmaya yardımcı olduğunu öğrendim.

Bu bölümde henüz Juice Shop üzerinde güvenlik testi gerçekleştirmedim. Burp Suite'in temel yapısını ve araçların görevlerini öğrendim. Uygulamalı HTTP trafik analizine sonraki bölümde geçeceğim.
