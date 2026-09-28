# 14. HTTP ve HTTPS Karşılaştırması

## 1. HTTP ve HTTPS Nedir?

HTTP (Hypertext Transfer Protocol), istemci ile sunucu arasındaki web iletişimini sağlayan bir uygulama katmanı protokolüdür.

HTTPS ise HTTP'nin TLS (Transport Layer Security) ile korunmuş halidir.

Basit olarak:

```text
HTTP
  ↓
TLS ile korunur
  ↓
HTTPS
```

---

## 2. HTTP ve HTTPS Karşılaştırması

| Özellik             | HTTP        | HTTPS                                                        |
| ------------------- | ----------- | ------------------------------------------------------------ |
| Şifreleme           | Yok         | TLS ile şifrelenir                                           |
| Varsayılan Port     | `80`        | `443`                                                        |
| TLS                 | Kullanılmaz | Kullanılır                                                   |
| Veri gizliliği      | Sağlamaz    | Sağlar                                                       |
| MITM'e karşı koruma | Sağlamaz    | TLS sertifika doğrulaması doğru uygulandığında koruma sağlar |

---

## 3. Şifreleme

HTTP'de uygulama katmanındaki veriler şifrelenmeden taşınabilir.

Önceki çalışmamda HTTP request içerisinde aşağıdaki bilgileri doğrudan görebildim:

```text
GET /success.txt?ipv4 HTTP/1.1
Host: detectportal.firefox.com
User-Agent: Mozilla/5.0 ...
```

HTTPS'te ise HTTP içeriği TLS tarafından korunur.

```text
HTTP
   ↓
TLS
   ↓
Encrypted Application Data
```

Bu nedenle normal şartlarda Wireshark üzerinde HTTP içeriğini açık şekilde göremeyiz.

---

# 4. Wireshark ile HTTPS Trafiği

Wireshark'ta aşağıdaki display filter ile HTTPS/TLS trafiğini incelemeye başladım:

```text
tcp.port == 443
```

Yakalanan örnek paket:

```text
Frame 821
```

### Paket bilgileri

| Alan               | Değer           |
| ------------------ | --------------- |
| Source IP          | `199.232.17.91` |
| Destination IP     | `10.0.3.15`     |
| Source Port        | `443`           |
| Destination Port   | `33570`         |
| Protocol           | `TLSv1.3`       |
| Packet Length      | `1446 bytes`    |
| TCP Segment Length | `1392 bytes`    |
| TCP Flags          | `PSH, ACK`      |

Wireshark'ta Transport Layer Security bölümünde:

```text
TLSv1.3 Record Layer: Application Data
Protocol: Hypertext Transfer Protocol
```

ifadesini gördüm.

Bu, TLS üzerinden HTTP uygulama verisinin taşındığını gösterir.

---

# 5. HTTPS Kullanıldığında Wireshark'ta Hiçbir Bilgi Görülür mü?

Hayır.

HTTPS kullanıldığında Wireshark trafiği görmeye devam eder. Ancak TLS tarafından şifrelenen HTTP içeriği normal şartlarda doğrudan okunamaz.

Örneğin aşağıdaki bilgiler ağ analizinde görülebilir:

* Source IP
* Destination IP
* Source Port
* Destination Port
* TCP bilgileri
* Paket boyutu
* Zamanlama
* TLS sürümü
* Bazı TLS metadata bilgileri

Ancak normal şartlarda aşağıdaki HTTP içeriği okunamaz:

* HTTP URL path'i
* GET veya POST içeriği
* HTTP header'ları
* Cookie değerleri
* Session token'ları
* HTTP response body

---

# 6. HTTP ve HTTPS'i Gerçek Paketler Üzerinden Karşılaştırma

## HTTP

Önceki çalışmamda Frame 857 içerisinde:

```text
GET /success.txt?ipv4 HTTP/1.1
Host: detectportal.firefox.com
User-Agent: Mozilla/5.0 ...
```

bilgilerini doğrudan okuyabildim.

İletişim:

```text
10.0.3.15:42300
        ↓
199.232.17.91:80
        ↓
HTTP
```

şeklindeydi.

---

## HTTPS

Frame 821 içerisinde ise:

```text
199.232.17.91:443
        ↓
10.0.3.15:33570
        ↓
TLSv1.3
        ↓
Application Data
```

yapısını gördüm.

Burada HTTP uygulama verisinin TLS tarafından korunduğunu gördüm fakat HTTP içeriğini açık metin olarak okuyamadım.

---

# 7. HTTPS'in Sağladığı Güvenlik Özellikleri

### Confidentiality — Gizlilik

TLS, uygulama verilerinin ağ üzerinde doğrudan okunmasını engellemek için şifreleme sağlar.

### Integrity — Bütünlük

TLS, iletişim sırasında verinin değiştirilmesinin tespit edilmesine yardımcı olur.

### Authentication — Kimlik Doğrulama

TLS sertifikaları sayesinde istemci, bağlandığı sunucunun kimliği hakkında doğrulama yapabilir.

---

# 8. MITM ve HTTPS

HTTPS, TLS sertifika doğrulaması doğru şekilde uygulandığında istemci ile sunucu arasındaki iletişimin sahte bir sunucu tarafından ele geçirilmesine karşı koruma sağlar.

Ancak HTTPS kullanılıyor olması tek başına bütün MITM senaryolarının imkânsız olduğu anlamına gelmez.

Sertifika doğrulama problemleri veya yanlış yapılandırmalar güvenliği etkileyebilir.

---

# 9. SOC Analizi Açısından Önemi

HTTPS kullanılması ağ trafiğinin tamamen görünmez olduğu anlamına gelmez.

Bir SOC analisti şifrelenmiş trafikte bile:

```text
IP
Port
Zaman
Paket boyutu
TCP davranışı
TLS bilgileri
```

gibi metadata üzerinden analiz yapabilir.

Bu nedenle HTTPS trafiğinde içerik analizi zorlaşırken ağ davranışı ve bağlantı özelliklerinin analizi önemini korur.

---

# 10. Sonuç

Bu çalışmada HTTP ile HTTPS arasındaki temel farkları öğrendim.

HTTP trafiğinde uygulama katmanındaki bilgileri Wireshark üzerinde açık şekilde görebildim.

HTTPS trafiğinde ise TLS 1.3 kullanıldığını ve HTTP uygulama verisinin `Application Data` içerisinde taşındığını gördüm.

En önemli öğrendiğim nokta:

> HTTPS trafiği görünmez hale getirmez; HTTP uygulama içeriğini TLS ile korur.

Bu nedenle Wireshark'ta HTTPS bağlantısının IP, port, zamanlama, paket boyutu ve TLS gibi bazı özellikleri görülebilirken HTTP'nin şifrelenmiş içeriği normal şartlarda doğrudan okunamaz.
