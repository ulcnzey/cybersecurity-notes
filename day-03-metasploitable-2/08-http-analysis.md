# HTTP Service Analysis

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde çalışan HTTP servisinin incelenmesi, web sunucusu ve kullanılan teknolojilerin belirlenmesi, HTTP response header bilgilerinin analiz edilmesi ve elde edilen bilgilerin güvenlik açısından değerlendirilmesidir.

Çalışma yalnızca izole edilmiş Metasploitable 2 laboratuvar ortamında gerçekleştirilmiştir.

**Hedef IP:** `192.168.56.20`

---

## 2. HTTP Nedir?

HTTP (Hypertext Transfer Protocol), web istemcileri ile web sunucuları arasında veri iletişimini sağlayan bir uygulama katmanı protokolüdür.

Bir kullanıcı tarayıcı üzerinden bir web sitesine eriştiğinde istemci ile web sunucusu arasında HTTP istek ve cevapları gerçekleştirilir.

HTTP'nin standart olarak kullandığı port:

```text
80/tcp
```

HTTPS ise HTTP iletişiminin TLS ile güvenli hale getirilmiş biçimidir ve genellikle:

```text
443/tcp
```

portunu kullanır.

Bu çalışmada Metasploitable 2 üzerinde HTTP servisinin `80/tcp` portunda çalıştığı incelenmiştir.

---

## 3. Nmap ile HTTP Servisinin Tespit Edilmesi

Daha önce gerçekleştirilen servis sürüm taramasında aşağıdaki sonuç elde edilmiştir:

```text
80/tcp   open   http   Apache httpd 2.2.8 ((Ubuntu) DAV/2)
```

Bu sonuçlara göre:

| Özellik                 | Sonuç    |
| ----------------------- | -------- |
| Port                    | `80/tcp` |
| Servis                  | HTTP     |
| Web sunucusu            | Apache   |
| Sürüm                   | `2.2.8`  |
| İşletim sistemi bilgisi | Ubuntu   |
| Durum                   | Open     |

Bu bilgi, hedef sistem üzerinde web sunucusunun erişilebilir olduğunu göstermektedir.

---

## 4. HTTP Servisine Erişim

Web servisine tarayıcı üzerinden aşağıdaki adres kullanılarak erişilmiştir:

```text
http://192.168.56.20
```

Web sunucusu tarafından **Metasploitable2 - Linux** başlıklı bir web sayfası sunulduğu görülmüştür.

Sayfa başlığı daha sonra Nmap NSE kullanılarak da doğrulanmıştır.

---

## 5. HTTP Response Header Analizi

HTTP sunucusunun response header bilgilerini incelemek amacıyla `curl` kullanılmıştır.

Kullanılan komut:

```bash
curl -I http://192.168.56.20
```

Buradaki `-I` parametresi, web sayfasının içeriğini almak yerine HTTP response header bilgilerinin görüntülenmesini sağlar.

Elde edilen sonuç:

```text
HTTP/1.1 200 OK
Date: Wed, 23 Sep 2026 09:42:11 GMT
Server: Apache/2.2.8 (Ubuntu) DAV/2
X-Powered-By: PHP/5.2.4-2ubuntu5.10
Content-Type: text/html
```

---

## 6. HTTP Header Bilgilerinin İncelenmesi

### 6.1 HTTP Durum Kodu

```text
HTTP/1.1 200 OK
```

`200 OK`, HTTP isteğinin başarılı olduğunu ve sunucunun istemciye başarılı bir HTTP cevabı verdiğini gösterir.

---

### 6.2 Server Header

```text
Server: Apache/2.2.8 (Ubuntu) DAV/2
```

Bu header, web sunucusunun Apache HTTP Server olduğunu ve sürüm bilgisinin `2.2.8` olarak dışarıya bildirildiğini göstermektedir.

Ayrıca sistemin Ubuntu tabanlı olduğu bilgisi de response içerisinde yer almaktadır.

Bu tür sürüm bilgilerinin dışarıya açık olması, servis ve sürüm araştırmasını kolaylaştırabilir.

---

### 6.3 X-Powered-By Header

```text
X-Powered-By: PHP/5.2.4-2ubuntu5.10
```

Bu header, web uygulamasının PHP kullandığını ve sunucunun PHP sürüm bilgisini response içerisinde açıkladığını göstermektedir.

Bu bilgi güvenlik araştırması açısından önemlidir çünkü kullanılan yazılım ve sürümlerin belirlenmesi, ilgili güvenlik açıklarının araştırılmasına yardımcı olabilir.

---

### 6.4 Content-Type

```text
Content-Type: text/html
```

Bu değer, sunucunun istemciye HTML formatında içerik gönderdiğini göstermektedir.

---

## 7. Nmap NSE ile HTTP Bilgilerinin Doğrulanması

HTTP servisinin başlık ve sayfa başlığı bilgilerinin Nmap NSE kullanılarak incelenmesi için aşağıdaki komut çalıştırılmıştır:

```bash
nmap -p 80 --script http-title,http-headers 192.168.56.20
```

Komutta:

* `-p 80`: yalnızca 80/tcp portunun taranmasını sağlar.
* `http-title`: web sayfasının başlığını belirler.
* `http-headers`: HTTP response header bilgilerini görüntüler.

Elde edilen sonuç:

```text
PORT   STATE SERVICE
80/tcp open  http
|_http-title: Metasploitable2 - Linux
| http-headers:
|   Date: Wed, 23 Sep 2026 09:42:39 GMT
|   Server: Apache/2.2.8 (Ubuntu) DAV/2
|   X-Powered-By: PHP/5.2.4-2ubuntu5.10
|   Connection: close
|   Content-Type: text/html
|   
|_  (Request type: HEAD)
```

Bu sonuç, `curl` kullanılarak elde edilen bilgileri bağımsız bir yöntemle doğrulamıştır.

---

## 8. Elde Edilen Bilgilerin Özeti

| Bilgi                   | Sonuç                     |
| ----------------------- | ------------------------- |
| Hedef IP                | `192.168.56.20`           |
| HTTP portu              | `80/tcp`                  |
| Servis                  | HTTP                      |
| Web sunucusu            | Apache                    |
| Apache sürümü           | `2.2.8`                   |
| İşletim sistemi bilgisi | Ubuntu                    |
| Web teknolojisi         | PHP                       |
| PHP sürümü              | `5.2.4-2ubuntu5.10`       |
| Sayfa başlığı           | `Metasploitable2 - Linux` |
| İçerik türü             | `text/html`               |
| HTTP durum kodu         | `200 OK`                  |

---

## 9. Güvenlik Değerlendirmesi

HTTP servisi üzerinden web sunucusu ve kullanılan PHP sürümü hakkında bilgi elde edilebilmiştir.

Özellikle aşağıdaki bilgiler response header içerisinde doğrudan açıklanmaktadır:

```text
Apache/2.2.8
PHP/5.2.4-2ubuntu5.10
```

Yazılım ve sürüm bilgilerinin dışarıya açık olması, hedef sistem hakkında bilgi toplama sürecini kolaylaştırabilir.

Bu durum tek başına bir güvenlik açığı olduğunu kanıtlamaz. Ancak saldırı yüzeyinin belirlenmesi ve kullanılan yazılımlarla ilişkili bilinen güvenlik açıklarının araştırılması açısından değerlidir.

Güvenlik değerlendirmesinde:

> **Bilgi ifşası ile doğrudan zafiyet arasında ayrım yapılmalıdır.**

Bir yazılımın sürümünün öğrenilebilmesi, o yazılımın kesin olarak istismar edilebilir olduğu anlamına gelmez.

---

## 10. Saldırı Yüzeyine Katkısı

HTTP servisi, hedef sistemin saldırı yüzeyindeki önemli servislerden biridir.

Analiz şu şekilde özetlenebilir:

```text
80/tcp
   ↓
HTTP
   ↓
Apache 2.2.8
   ↓
PHP 5.2.4
   ↓
Web uygulaması
   ↓
Dizinler / Sayfalar / Uygulama Bileşenleri
   ↓
Zafiyet Araştırması
```

Bu yapı, web servisinin yalnızca açık bir port olarak değil, arkasında çalışan yazılım ve uygulama bileşenleriyle birlikte değerlendirilmesi gerektiğini göstermektedir.

---

## 11. Güvenlik Açısından Öneriler

Web sunucularında aşağıdaki güvenlik önlemleri uygulanmalıdır:

1. Apache ve PHP gibi yazılımlar desteklenen ve güncel sürümlerde tutulmalıdır.
2. Gereksiz sürüm bilgilerinin HTTP response header'larında açıklanması sınırlandırılmalıdır.
3. Web uygulaması ve sunucu bileşenleri düzenli olarak güvenlik açısından değerlendirilmelidir.
4. Gereksiz HTTP modülleri ve servisleri devre dışı bırakılmalıdır.
5. Web sunucusuna erişim uygun güvenlik duvarı kurallarıyla sınırlandırılmalıdır.
6. Web sunucusu logları düzenli olarak izlenmelidir.
7. Kullanılan yazılımların güvenlik güncellemeleri ve bilinen CVE kayıtları takip edilmelidir.

---
## Web Uygulaması Bileşenleri ve Güvenlik Önemi

Bir web uygulaması yalnızca web sunucusundan oluşmaz. Kullanıcıların etkileşim kurduğu farklı bileşenler de uygulamanın saldırı yüzeyinin bir parçasıdır.

Bu nedenle bir web uygulaması incelenirken yalnızca Apache veya PHP sürümüne değil, uygulamanın sunduğu işlevlere de dikkat edilmelidir.

### Login

Login bileşeni kullanıcı kimlik doğrulamasını gerçekleştirir.

Güvenlik açısından aşağıdaki konular önemlidir:

* Kimlik doğrulama mekanizmasının güvenli olması
* Parolaların güvenli şekilde saklanması
* Yetkisiz kullanıcıların giriş yapmasının engellenmesi
* Oturum yönetiminin güvenli olması

Kimlik doğrulama veya oturum yönetimindeki hatalar yetkisiz erişime neden olabilir.

### Search

Search bileşeni kullanıcı tarafından girilen arama verilerini sunucuya iletir.

Kullanıcı girdisinin güvenli şekilde işlenmemesi çeşitli saldırılara neden olabilir. Bu nedenle arama alanları kullanıcı girdilerinin nasıl işlendiği açısından incelenmelidir.

### File Upload

Dosya yükleme işlevi kullanıcıların sunucuya dosya göndermesine izin verir.

Dosya türü, boyutu, içeriği ve yüklenen dosyanın nerede saklandığı yeterince kontrol edilmezse kötü amaçlı veya istenmeyen dosyaların sunucuya yüklenmesi mümkün olabilir.

### URL Parameters

URL parametreleri uygulamaya kullanıcı tarafından gönderilen verilerdir.

Örneğin:

```text
http://example.com/product?id=10
```

adresindeki `id=10` bir URL parametresidir.

Bu tür kullanıcı girdilerinin güvenli şekilde doğrulanması ve işlenmesi gerekir. Hatalı kontroller farklı web güvenlik problemlerine yol açabilir.

### API

API'ler farklı uygulama veya istemcilerin web uygulamasıyla veri alışverişi yapmasını sağlar.

API güvenliğinde özellikle:

* Kimlik doğrulama
* Yetkilendirme
* Veri erişim kontrolleri
* Girdi doğrulama
* Hassas verilerin korunması

önemlidir.

### Admin Panel

Admin paneli, uygulamanın yönetim işlevlerine erişim sağlar.

Bu nedenle normal kullanıcıların yönetici işlevlerine erişememesi ve yönetici panelinin uygun şekilde korunması gerekir.

Yetkisiz bir kullanıcının yönetim işlevlerine erişebilmesi sistem üzerinde önemli değişiklikler yapılmasına neden olabilir.

### Genel Değerlendirme

Web uygulamasının saldırı yüzeyi aşağıdaki şekilde düşünülebilir:

```text
Web Sunucusu
      ↓
Web Uygulaması
      ↓
Login ─ Search ─ Upload
      ↓
URL Parameters ─ API
      ↓
Admin Panel
      ↓
Veri ve Sistem Kaynakları
```

Bu bileşenlerin her biri farklı güvenlik kontrolleri gerektirebilir.

Bu nedenle web güvenlik değerlendirmesinde yalnızca açık portların ve yazılım sürümlerinin belirlenmesi yeterli değildir. Uygulamanın sunduğu işlevlerin ve kullanıcı girdilerinin nasıl işlendiğinin de incelenmesi gerekir.


## 12. Sonuç

Metasploitable 2 üzerindeki HTTP servisi incelenmiştir.

Yapılan analiz sonucunda:

* `80/tcp` portunun açık olduğu,
* Apache HTTP Server `2.2.8` kullanıldığı,
* sistemin Ubuntu tabanlı olduğu,
* PHP `5.2.4-2ubuntu5.10` kullanıldığı,
* web sayfasının başlığının `Metasploitable2 - Linux` olduğu,
* HTTP response header bilgilerinin dışarıya açık olduğu,
* elde edilen bilgilerin `curl` ve Nmap NSE kullanılarak doğrulandığı

tespit edilmiştir.

Bu çalışma sonucunda HTTP servisinin saldırı yüzeyindeki konumu ve web sunucusunun dışarıya açıkladığı teknik bilgiler incelenmiştir.

---

## 13. Kullanılan Komutlar

Servis ve sürüm tespiti:

```bash
nmap -sV 192.168.56.20
```

HTTP response header analizi:

```bash
curl -I http://192.168.56.20
```

HTTP başlık ve sayfa başlığı analizi:

```bash
nmap -p 80 --script http-title,http-headers 192.168.56.20
```

Web servisine erişim:

```text
http://192.168.56.20
```

---

## 14. Ekran Görüntüleri

### HTTP Web Sayfası

Metasploitable 2 HTTP servisinin tarayıcı üzerinden görüntülenmesi.
<img width="682" height="495" alt="image" src="https://github.com/user-attachments/assets/6ca66c7f-ead3-4d56-bbe3-f89b2dbd685a" />


### HTTP Response Headers

`curl -I` komutu ile HTTP response header bilgilerinin görüntülenmesi.

<img width="344" height="140" alt="image" src="https://github.com/user-attachments/assets/e808b5c4-5c60-428b-a667-9aa91ad4618d" />


### Nmap HTTP NSE Analizi

Nmap `http-title` ve `http-headers` scriptleri ile HTTP servis bilgilerinin doğrulanması.

<img width="650" height="333" alt="image" src="https://github.com/user-attachments/assets/b06035f5-1422-4605-bd33-568574e0ea11" />

