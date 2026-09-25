# 03 — Burp Suite Ortamını Hazırlama

## Burp Suite'i Açma

Bu bölümde Kali Linux üzerinde Burp Suite'i açarak temel bölümlerini inceledim.

Amacım henüz bir güvenlik açığı aramak veya saldırı gerçekleştirmek değil, Burp Suite'in web güvenlik testlerinde kullanılan temel araçlarını tanımaktı.

İncelediğim bölümler:

* Proxy
* HTTP History
* Repeater
* Intruder
* Decoder
* Comparer

---

## 1. Proxy

### Araç: Proxy

**Ne işe yarar?**

Proxy, tarayıcı ile web sunucusu arasındaki HTTP/HTTPS trafiğini yakalamamı ve incelememi sağlar.

Normalde tarayıcı ile sunucu arasında gerçekleşen iletişim Burp Suite üzerinden geçirilebilir.

```text
Browser
   ↓
Burp Proxy
   ↓
Web Server
```

**Web güvenliği testinde neden kullanılır?**

Uygulamanın gönderdiği HTTP isteklerini ve sunucunun verdiği cevapları incelemek için kullanılır. İlerleyen güvenlik testlerinde istekleri kontrollü şekilde analiz etmek için temel araçlardan biridir.

---

## 2. HTTP History

### Araç: HTTP History

**Ne işe yarar?**

HTTP History, Burp Suite üzerinden geçen HTTP isteklerini ve cevaplarını geçmiş şeklinde gösterir.

Burada GET ve POST gibi HTTP metodlarını, endpoint'leri, status code'ları ve isteklerin diğer ayrıntılarını görebilirim.

**Web güvenliği testinde neden kullanılır?**

Bir web uygulamasının hangi endpoint'lerle iletişim kurduğunu ve hangi istekleri gönderdiğini anlamak için kullanılır.

İnceleme sırasında örneğin:

```text
GET /api/...
POST /api/...
```

gibi istekleri görebilirim.

<img width="680" height="601" alt="image" src="https://github.com/user-attachments/assets/647439de-1a2c-4759-ade5-e48bab99639a" />


---

## 3. Repeater

### Araç: Repeater

**Ne işe yarar?**

Repeater, bir HTTP isteğini tekrar göndermemi ve göndermeden önce isteğin belirli bölümlerini değiştirmemi sağlar.

Örneğin bir parametreyi değiştirdikten sonra isteği tekrar göndererek sunucunun verdiği cevabı inceleyebilirim.

**Web güvenliği testinde neden kullanılır?**

Bir istekte yaptığım kontrollü bir değişikliğin uygulamanın davranışını nasıl etkilediğini görmek için kullanılır.

Kısaca:

```text
İsteği al
   ↓
Değiştir
   ↓
Tekrar gönder
   ↓
Cevabı incele
```

Repeater özellikle manuel web güvenlik testlerinde kullanışlıdır.

<img width="682" height="576" alt="image" src="https://github.com/user-attachments/assets/cf115912-76f3-4396-8775-02a1a4fc6597" />

---

## 4. Intruder

### Araç: Intruder

**Ne işe yarar?**

Intruder, bir HTTP isteğindeki belirli alanlara farklı değerler göndererek tekrarlı ve kontrollü testler yapmamı sağlar.

**Web güvenliği testinde neden kullanılır?**

Bir parametrenin farklı değerlerde nasıl davrandığını incelemek veya çok sayıda kontrollü isteği otomatik olarak göndermek için kullanılabilir.

Repeater'dan farkı, Repeater'da istekleri daha çok manuel olarak tekrar gönderirken Intruder'ın belirlenen alanlara farklı değerleri sistematik olarak uygulayabilmesidir.

<img width="682" height="588" alt="image" src="https://github.com/user-attachments/assets/63120fb1-9851-4a7f-8c79-bbaad66684f0" />

---

## 5. Decoder

### Araç: Decoder

**Ne işe yarar?**

Decoder, kodlanmış verileri farklı formatlarda decode veya encode etmeye yarar.

Örneğin Base64 veya URL encoding ile karşılaşılan verileri incelemek için kullanılabilir.

**Web güvenliği testinde neden kullanılır?**

Bir verinin hangi formatta kodlandığını anlamak ve gerektiğinde okunabilir hale getirerek incelemek için kullanılır.

Burada önemli bir nokta öğrendim:

**Encoding ile encryption aynı şey değildir.**

Örneğin Base64 bir şifreleme yöntemi değildir. Kodlanmış veri tekrar decode edilebilir.

<img width="682" height="401" alt="image" src="https://github.com/user-attachments/assets/25858e87-874b-4866-8726-f2bfb6b737d3" />

---

## 6. Comparer

### Araç: Comparer

**Ne işe yarar?**

Comparer, iki farklı HTTP isteğini veya cevabını karşılaştırmamı sağlar.

**Web güvenliği testinde neden kullanılır?**

Bir istekte değişiklik yaptıktan sonra sunucunun verdiği cevabın önceki cevaptan nasıl farklılaştığını görmek için kullanılabilir.

Örneğin:

```text
Normal Request
      ↓
Response A

Değiştirilmiş Request
      ↓
Response B

      ↓
   Comparer
      ↓
 Farkları incele
```

Özellikle uzun HTTP cevaplarında farklılıkları daha kolay fark etmeme yardımcı olur.

<img width="678" height="597" alt="image" src="https://github.com/user-attachments/assets/589cbc26-7c77-499b-b62f-f4d70d34cd29" />

---

# Kısa Özet

| Araç             | Ne işe yarar?                                                   |
| ---------------- | --------------------------------------------------------------- |
| **Proxy**        | HTTP/HTTPS trafiğini yakalar ve incelemeyi sağlar.              |
| **HTTP History** | Geçmiş HTTP isteklerini ve cevaplarını gösterir.                |
| **Repeater**     | Bir isteği değiştirip tekrar göndermeyi sağlar.                 |
| **Intruder**     | Farklı değerlerle tekrarlı ve kontrollü testler yapmayı sağlar. |
| **Decoder**      | Kodlanmış verileri encode/decode etmeye yarar.                  |
| **Comparer**     | İki request veya response arasındaki farkları gösterir.         |

# Bu Bölümde Öğrendiklerim

Bu bölümde Burp Suite'in temel araçlarını tanıdım ve her birinin web güvenliği testlerindeki kullanım amacını öğrendim.

Özellikle Proxy ve HTTP History'nin uygulamanın HTTP trafiğini incelemek için, Repeater'ın istekleri değiştirip tekrar göndermek için, Intruder'ın tekrarlı testler için, Decoder'ın kodlanmış verileri incelemek için ve Comparer'ın farklı istek veya cevapları karşılaştırmak için kullanıldığını öğrendim.

Bu bölümde henüz bir güvenlik açığı araştırmadım veya saldırı testi gerçekleştirmedim. Burp Suite ortamını ve temel araçlarını tanıdım.

Bir sonraki bölümde Burp Suite üzerinden **HTTP trafik analizine** geçeceğim.
