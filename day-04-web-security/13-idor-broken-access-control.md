# 13. IDOR / Broken Access Control

## 📌 IDOR Nedir?

IDOR (Insecure Direct Object Reference), bir web uygulamasının kullanıcı tarafından gönderilen bir nesne veya kaynak ID'sine dayanarak erişim sağlaması ancak kullanıcının o kaynağa erişmeye gerçekten yetkili olup olmadığını kontrol etmemesi durumudur.

Örneğin bir uygulamada kullanıcının kendi profiline şu adres üzerinden eriştiğini düşünelim:

```text id="g1d3bz"
/profile?id=100
```

Kullanıcı URL'deki ID değerini:

```text id="x7v0z3"
/profile?id=101
```

veya:

```text id="x5k8p2"
/profile?id=102
```

şeklinde değiştirdiğinde başka kullanıcıların bilgilerine erişebiliyorsa burada bir erişim kontrolü problemi olabilir.

Buradaki temel problem ID'nin değiştirilebilir olması değildir.

Asıl problem:

> Sunucunun, istenen kaynağın bu kullanıcıya ait olup olmadığını veya kullanıcının bu kaynağa erişim yetkisi bulunup bulunmadığını kontrol etmemesidir.

---

# 📌 Broken Access Control ile İlişkisi

IDOR, daha geniş bir güvenlik konusu olan **Broken Access Control** kapsamında değerlendirilebilir.

Broken Access Control, kullanıcıların sahip oldukları yetkilerin dışındaki kaynaklara veya işlemlere erişebilmesi durumlarını kapsar.

Örneğin:

```text id="v4a3g6"
Normal kullanıcı
      ↓
Kendi profiline erişebilir
      ↓
Başka kullanıcının profiline erişebiliyor
      ↓
Authorization kontrolü problemi
```

Bu nedenle IDOR'u sadece "URL'deki ID'yi değiştirmek" olarak düşünmemek gerekir.

Asıl konu **erişim kontrolünün sunucu tarafında doğru uygulanmasıdır.**

---

# 📌 Problem Nedir?

Normal bir kullanıcı kendi profiline erişirken uygulamanın şu kontrolü yapması gerekir:

```text id="0t8j6p"
Kullanıcı
   ↓
/profile?id=100
   ↓
Sunucu
   ↓
"Bu kullanıcı 100 numaralı profile erişebilir mi?"
   ↓
Evet → İzin ver
Hayır → Erişimi reddet
```

Eğer uygulama sadece:

```text id="j8q4m1"
"100 numaralı profil mevcut mu?"
```

kontrolünü yapıyorsa güvenlik problemi ortaya çıkabilir.

Örneğin:

```text id="4x2v7s"
/profile?id=100 → Kendi profili
/profile?id=101 → Başka kullanıcı
/profile?id=102 → Başka kullanıcı
```

isteklerinin tamamı kabul ediliyorsa kullanıcılar birbirlerinin kaynaklarına erişebilir.

---

# 📌 Neden Oluşur?

IDOR ve Broken Access Control problemlerinin temel nedenlerinden biri **authorization kontrolünün eksik veya yanlış uygulanmasıdır.**

Örneğin uygulama:

```text id="6c4v9h"
Kullanıcı giriş yapmış mı?
        ↓
       Evet
        ↓
    Veriyi göster
```

şeklinde çalışıyorsa sadece authentication kontrol edilmiş olur.

Ancak şu soru cevaplanmamıştır:

> Bu kullanıcı istediği kaynağa erişmeye yetkili mi?

Güvenli yaklaşım:

```text id="1f7s2q"
Kullanıcı
   ↓
Authentication
   ↓
Kim olduğu doğrulandı
   ↓
Authorization
   ↓
Kaynağa erişim yetkisi kontrol edildi
   ↓
Evet → Kaynak gösterilir
Hayır → Erişim reddedilir
```

---

# 📌 Authentication Var mı?

IDOR bulunan bir sistemde authentication bulunabilir.

Yani kullanıcı sisteme başarıyla giriş yapmış olabilir.

Örneğin:

```text id="4k8m3n"
Login
  ↓
Başarılı
  ↓
Session / Token
  ↓
Kullanıcı korumalı kaynağa erişiyor
```

Bu durumda kullanıcının kimliği doğrulanmıştır.

Yani:

> Authentication vardır.

Ancak authentication yapılmış olması, kullanıcının bütün kaynaklara erişebileceği anlamına gelmez.

---

# 📌 Authorization Var mı?

IDOR konusunun en önemli noktalarından biri authorization'dır.

Authentication:

> **Sen kimsin?**

sorusuna cevap verir.

Authorization:

> **Neye erişmeye yetkin var?**

sorusuna cevap verir.

Örneğin:

```text id="g6s8c2"
Authentication
      ↓
"Bu kullanıcı Zeynep."
      ↓
Authorization
      ↓
"Zeynep 101 numaralı profile erişebilir mi?"
```

Eğer ikinci kontrol yapılmıyorsa Broken Access Control ortaya çıkabilir.

Bu nedenle:

```text id="d5m0x4"
Authentication → ✅
Authorization  → ❌
```

şeklinde bir durum mümkün olabilir.

---

# 📌 IDOR Sadece Profil Sayfalarında Olmaz

IDOR farklı kaynak türlerinde ortaya çıkabilir.

Örneğin:

```text id="4s8j1n"
/profile?id=100
/order?id=500
/invoice?id=700
/document?id=120
/download?id=35
```

API tarafında da benzer durumlar görülebilir:

```text id="e1k6p9"
/api/users/101
/api/orders/501
```

Örneğin bir kullanıcı kendi siparişini:

```text id="r8w4x2"
/order?id=500
```

adresinden görebiliyor.

ID değeri:

```text id="v2z7q5"
/order?id=501
```

olarak değiştirildiğinde başka bir kullanıcının siparişi gösteriliyorsa erişim kontrolü problemi söz konusu olabilir.

---

# 📌 ID'nin Tahmin Edilebilir Olması Tek Başına Açık Değildir

Bir uygulamanın:

```text id="q8c3w5"
/profile?id=100
/profile?id=101
/profile?id=102
```

gibi sıralı ID'ler kullanması tek başına IDOR olduğunu göstermez.

Asıl önemli olan:

> Kullanıcı farklı bir ID gönderdiğinde sunucu authorization kontrolü yapıyor mu?

Örneğin:

```text id="j6d9p3"
/profile?id=101
       ↓
Sunucu
       ↓
"Bu kaynak bu kullanıcıya ait mi?"
       ↓
Hayır
       ↓
Erişim reddedilir
```

şeklinde bir kontrol yapılıyorsa ID'nin tahmin edilebilir olması tek başına IDOR anlamına gelmez.

---

# 🛡️ IDOR Nasıl Önlenebilir?

## 1. Her istekte Authorization kontrolü yapılmalı

Sunucu yalnızca kullanıcının giriş yapıp yapmadığını kontrol etmemelidir.

İstenen kaynağa erişim yetkisi de kontrol edilmelidir.

```text id="x9p2s4"
Authentication
      ↓
Kullanıcı kim?
      ↓
Authorization
      ↓
Bu kaynağa erişebilir mi?
      ↓
Evet → İşleme devam
Hayır → Erişimi reddet
```

---

## 2. Kaynak sahipliği kontrol edilmeli

Örneğin:

```text id="k3f7n8"
/profile?id=101
```

istendiğinde sunucu, istek yapan kullanıcının gerçekten bu profile erişme hakkı olup olmadığını kontrol etmelidir.

Örneğin:

```text id="w5r1c6"
İstek yapan kullanıcı = 50
İstenen profil = 101
```

eğer profil 101 kullanıcı 50'ye ait değilse erişim reddedilmelidir.

---

## 3. Authorization kontrolü backend tarafında yapılmalı

Frontend'de bir butonu gizlemek güvenlik kontrolü değildir.

Örneğin:

```text id="b6q8z2"
"Normal kullanıcı admin butonunu göremesin."
```

şeklinde bir frontend kontrolü yapılabilir.

Ancak kullanıcı HTTP isteğini doğrudan değiştirebilir.

Bu nedenle gerçek authorization kontrolü:

```text id="n7m4x1"
Backend / Server
```

tarafında yapılmalıdır.

---

## 4. En Az Yetki Prensibi

Kullanıcılara yalnızca ihtiyaç duydukları kaynak ve işlemler için yetki verilmelidir.

Örneğin:

```text id="e4c7m2"
Normal kullanıcı
→ Kendi profilini görebilir.

Admin
→ Yetkisi dahilindeki kullanıcı profillerini yönetebilir.
```

Her kullanıcının tüm kaynaklara erişebilmesi güvenli bir yaklaşım değildir.

---

# 🧠 Authentication ve Authorization Farkı

IDOR konusunu öğrenirken authentication ile authorization arasındaki farkın çok önemli olduğunu gördüm.

| Kavram         | Sorduğu soru              |
| -------------- | ------------------------- |
| Authentication | Sen kimsin?               |
| Authorization  | Neye erişmeye yetkin var? |

Örneğin:

```text id="r3p7y9"
Login
  ↓
"Kimliğin doğrulandı."
  ↓
Authentication
  ↓
/profile?id=101
  ↓
"Bu profile erişmeye yetkin var mı?"
  ↓
Authorization
```

İkinci kontrol doğru yapılmıyorsa Broken Access Control ortaya çıkabilir.

---

# 🧪 Basit IDOR Senaryosu

Bir uygulamada kullanıcı kendi profilini:

```text id="c5m8q1"
/profile?id=100
```

adresinden görüntüleyebiliyor olsun.

Kullanıcı daha sonra:

```text id="u7x2d4"
/profile?id=101
```

isteğini gönderiyor.

Eğer sunucu herhangi bir authorization kontrolü yapmadan başka kullanıcıya ait bilgileri döndürürse IDOR problemi ortaya çıkabilir.

Güvenli uygulamada ise sunucunun kaynağın kullanıcıya ait olup olmadığını kontrol etmesi gerekir.

Yetkisiz erişim durumunda örneğin:

```http id="a1s6k8"
403 Forbidden
```

gibi bir response döndürülebilir.

Ancak burada önemli olan yalnızca status code değildir. Asıl önemli olan sunucunun gerçekten authorization kontrolü yapmasıdır.

---

# 🧪 Laboratuvar Planı

Bu konuyu uygulamalı olarak anlamak için kendi kurduğum **OWASP Juice Shop** ortamında kontrollü bir test yapılabilir.

Planlanan akış:

```text id="p4n7s2"
1. Kendi hesabımla giriş yap
          ↓
2. Burp Suite ile isteği yakala
          ↓
3. Kullanıcı/kaynak ID'sini belirle
          ↓
4. ID değerinin değiştirilmesi durumunu gözlemle
          ↓
5. Sunucunun authorization kontrolü yapıp yapmadığını incele
          ↓
6. Response'u değerlendir
```

Bu aşamada henüz test sonucu eklenmemiştir. Test yapıldıktan sonra gözlemler ayrıca bu bölüme eklenecektir.

---

# 📌 Kısa Özet

| Konu                  | Açıklama                                                                     |
| --------------------- | ---------------------------------------------------------------------------- |
| IDOR                  | Kullanıcının yetkisi olmayan bir kaynağa ID/reference üzerinden erişebilmesi |
| Broken Access Control | Yetki kontrollerinin eksik veya yanlış uygulanması                           |
| Authentication        | Kullanıcının kimliğini doğrulamak                                            |
| Authorization         | Kullanıcının erişim yetkisini kontrol etmek                                  |
| ID'nin değişmesi      | Tek başına güvenlik açığı değildir                                           |
| Temel korunma         | Her istekte server-side authorization kontrolü                               |
| Kaynak sahipliği      | Kullanıcının istenen kaynağa erişim hakkı kontrol edilmeli                   |
| En az yetki           | Kullanıcıya yalnızca gerekli yetkiler verilmeli                              |

## 🎯 Ana Fikir

```text id="s9f2k4"
Authentication
      ↓
"Sen kimsin?"
      ↓
Authorization
      ↓
"Bu kaynağa erişebilir misin?"
      ↓
       ├── Evet → İzin ver
       └── Hayır → Erişimi reddet
```

IDOR'u tek cümleyle özetlersem:

> **Kullanıcının kaynak ID'sini değiştirebilmesi asıl problem değildir. Asıl problem, sunucunun değiştirilmiş ID'nin işaret ettiği kaynağa erişim yetkisini kontrol etmemesidir.**
