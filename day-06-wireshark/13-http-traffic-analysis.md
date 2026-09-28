# 13. HTTP Traffic Analysis

## 1. HTTP Nedir?

HTTP (Hypertext Transfer Protocol), istemci ile sunucu arasında web üzerindeki iletişimi sağlayan uygulama katmanı protokolüdür.

Bir istemci bir kaynağı istediğinde HTTP Request gönderir. Sunucu da bu isteğe HTTP Response ile cevap verir.

Temel iletişim yapısı:

```text
Client
   |
   | HTTP Request
   v
Server
   |
   | HTTP Response
   v
Client
```

---

## 2. HTTP Methodları

### GET

Sunucudan bir kaynağı istemek için kullanılır.

Örneğimde:

```text
GET /success.txt?ipv4 HTTP/1.1
```

isteği gönderilmiştir.

### POST

Sunucuya veri göndermek için kullanılır. Örneğin form gönderimi veya kullanıcı giriş işlemleri sırasında kullanılabilir.

### PUT

Mevcut bir kaynağı güncellemek için kullanılabilir.

### DELETE

Bir kaynağın silinmesi için kullanılabilir.

---

## 3. HTTP Status Code

HTTP Response içerisinde sunucunun isteğe verdiği sonucu gösteren status code bulunur.

Örnekler:

* `200 OK` → İstek başarılı.
* `301 Moved Permanently` → Kaynak kalıcı olarak başka bir adrese taşınmış.
* `403 Forbidden` → İsteğe erişim izni yok.
* `404 Not Found` → İstenen kaynak bulunamadı.
* `500 Internal Server Error` → Sunucu tarafında hata oluştu.

---

# 4. Wireshark ile HTTP Trafiği

Wireshark'ta HTTP paketlerini görmek için aşağıdaki display filter'ı kullandım:

```text
http
```

Bu filtre sonucunda gerçek bir HTTP request ve response yakaladım.

---

# 5. HTTP Request Analizi

İncelediğim request:

```text
Frame 857
```

### Temel bilgiler

| Alan             | Değer           |
| ---------------- | --------------- |
| Source IP        | `10.0.3.15`     |
| Destination IP   | `199.232.17.91` |
| Source Port      | `42300`         |
| Destination Port | `80`            |
| Protocol         | HTTP            |
| Packet Length    | `364 bytes`     |

TCP katmanında:

```text
Source Port: 42300
Destination Port: 80
Flags: PSH, ACK
TCP Payload: 310 bytes
```

---

## 6. HTTP Request Bilgileri

Wireshark'ta HTTP bölümünde aşağıdaki bilgileri gördüm:

```text
GET /success.txt?ipv4 HTTP/1.1
Host: detectportal.firefox.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Priority: u=4
Pragma: no-cache
Cache-Control: no-cache
```

### Tespit ettiklerim

* **HTTP Method:** `GET`
* **URI:** `/success.txt?ipv4`
* **Host:** `detectportal.firefox.com`
* **User-Agent:** Firefox 128 / Linux x86_64
* **Source IP:** `10.0.3.15`
* **Destination IP:** `199.232.17.91`
* **Destination Port:** `80`
* **HTTP Version:** `HTTP/1.1`

Wireshark tam URL'yi şu şekilde gösterdi:

```text
http://detectportal.firefox.com/success.txt?ipv4
```

---

# 7. HTTP Response Analizi

Request'e karşılık gelen response:

```text
Frame 859
```

### Temel bilgiler

| Alan             | Değer           |
| ---------------- | --------------- |
| Source IP        | `199.232.17.91` |
| Destination IP   | `10.0.3.15`     |
| Source Port      | `80`            |
| Destination Port | `42300`         |
| Protocol         | HTTP            |
| Packet Length    | `422 bytes`     |

HTTP response:

```text
HTTP/1.1 200 OK
```

Bu, isteğin başarılı şekilde karşılandığını gösterir.

---

## 8. Response Header'ları

Wireshark'ta aşağıdaki header'ları gördüm:

```text
Connection: close
Content-Length: 8
Server: Varnish
Retry-After: 0
Content-Type: text/plain
Accept-Ranges: bytes
Date: Mon, 28 Sep 2026 11:08:11 GMT
Via: 1.1 varnish
X-Served-By: cache-vie6361-VIE
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1790593691.003690,VS0,VE0
Cache-Control: public, must-revalidate, max-age=0, s-maxage=3600
```

Önemli alanlar:

* **Status Code:** `200 OK`
* **Content-Type:** `text/plain`
* **Content-Length:** `8`
* **Server:** `Varnish`
* **Connection:** `close`
* **X-Cache:** `MISS`

Wireshark response gövdesinin:

```text
8 bytes
```

olduğunu gösterdi.

---

# 9. Request ve Response Arasındaki İlişki

Wireshark, Frame 859'un Frame 857'deki request'e cevap olduğunu gösterdi:

```text
[Request in frame: 857]
```

Ayrıca:

```text
[Response in frame: 859]
```

bilgisi de request paketinde yer aldı.

İstek ile cevap arasındaki süre:

```text
0.064330879 seconds
```

yani yaklaşık:

```text
64.3 ms
```

olarak görüldü.

---

# 10. HTTP Trafik Akışı

İncelediğim iletişimi şu şekilde özetleyebilirim:

```text
10.0.3.15:42300
      |
      | GET /success.txt?ipv4 HTTP/1.1
      | Host: detectportal.firefox.com
      |
      v
199.232.17.91:80
      |
      | HTTP/1.1 200 OK
      | Content-Type: text/plain
      | Content-Length: 8
      |
      v
10.0.3.15:42300
```

---

# 11. HTTP Header Nedir?

HTTP header'ları request ve response hakkında ek bilgiler taşır.

Örneğin:

```text
Host
User-Agent
Content-Type
Content-Length
Connection
Cache-Control
```

gibi alanlar iletişimin farklı özelliklerini belirtir.

---

# 12. Cookie Nedir?

Cookie, web uygulamalarının istemcide veri saklamak veya oturum gibi durum bilgilerini takip etmek için kullanabildiği küçük veri parçalarıdır.

Özellikle authentication işlemlerinde session bilgileri cookie içerisinde taşınabilir.

Bu nedenle güvenlik analizinde cookie değerlerinin nasıl taşındığı ve korunup korunmadığı önemlidir.

Gerçek kullanıcıların cookie veya session bilgileri izinsiz şekilde incelenmemelidir. Bu çalışmada yalnızca kendi laboratuvar trafiğimi analiz ettim.

---

# 13. HTTP ve Güvenlik

HTTP trafiğini Wireshark üzerinde incelemek, uygulama katmanındaki bilgilerin ağ üzerinde nasıl taşındığını görmemi sağladı.

Bu örnekte HTTP request içerisinde:

```text
GET
Host
User-Agent
URI
```

gibi bilgileri doğrudan okuyabildim.

HTTP şifreli bir iletişim protokolü değildir. Bu nedenle hassas bilgilerin HTTP üzerinden gönderilmesi güvenlik açısından risk oluşturabilir.

HTTPS kullanıldığında HTTP trafiği TLS ile korunur ve uygulama katmanındaki içerik ağ üzerinde doğrudan okunamaz.

Bu nedenle bir sonraki aşamada HTTPS ve TLS yapısını incelemek önemlidir.

---

# 14. Öğrendiklerim

Bu çalışmada:

* HTTP'nin client-server iletişimindeki rolünü öğrendim.
* HTTP request ve response arasındaki farkı gördüm.
* GET metodunu gerçek bir paket üzerinde inceledim.
* Host, URI ve User-Agent alanlarını tespit ettim.
* HTTP status code olan `200 OK` değerini inceledim.
* HTTP header'larının ne amaçla kullanıldığını gördüm.
* HTTP'nin TCP üzerinde çalıştığını gözlemledim.
* Wireshark ile gerçek HTTP trafiğini analiz ettim.
* HTTP ile HTTPS arasındaki güvenlik farkının neden önemli olduğunu anladım.
* Request ve response paketlerini Wireshark üzerinden eşleştirmeyi öğrendim.
