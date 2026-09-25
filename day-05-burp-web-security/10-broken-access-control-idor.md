# Broken Access Control / IDOR

## 1. Broken Access Control Nedir?

Broken Access Control, bir kullanıcının sahip olduğu yetkilerin dışına çıkarak erişmemesi gereken kaynaklara veya işlemlere erişebilmesi durumudur.

Bu kapsamda kullanıcı giriş yapmış olsa bile, uygulamanın her istekte kullanıcının ilgili kaynağa erişim yetkisini kontrol etmesi gerekir.

IDOR (Insecure Direct Object Reference) ise bir kaynağın ID gibi doğrudan bir referansla belirtilmesi ve uygulamanın bu ID üzerinden erişim sağlarken yeterli yetkilendirme kontrolü yapmaması durumunda ortaya çıkabilen bir Broken Access Control problemidir.

---

## 2. Juice Shop Üzerinde Test

Önce Burp Suite → **Proxy → HTTP history** üzerinden daha önce gözlemlediğim basket endpointini inceledim.

İlk olarak:

```http
GET /rest/basket/1
```

isteğini gönderdim.

Sunucunun döndürdüğü response içerisinde:

```json
{
  "id": 1,
  "UserId": 1
}
```

bilgilerini gördüm.

Bu nedenle Basket ID `1` ile UserId `1` arasında bir ilişki olduğunu gözlemledim.

Daha sonra isteği Burp Suite **Repeater** aracına göndererek yalnızca basket ID değerini değiştirdim:

```http
GET /rest/basket/2
```

İsteği tekrar gönderdikten sonra sunucu:

```http
HTTP/1.1 200 OK
```

response'u döndürdü.

Response içerisinde:

```json
{
  "id": 2,
  "UserId": 2
}
```

bilgilerini ve bu sepete ait ürünleri gördüm.

---

## 3. Test Sonucu

Test sırasında normal olarak kullandığım hesabın sepeti:

```text
Basket ID: 1
UserId: 1
```

şeklindeydi.

ID değerini:

```text
1 → 2
```

olarak değiştirdiğimde ise:

```text
Basket ID: 2
UserId: 2
```

olan başka bir sepete ait verilerin `200 OK` response'u ile döndürüldüğünü gözlemledim.

Burada isteği gönderen kullanıcının `UserId` değeri ile erişilen kaynağın `UserId` değeri arasında bir sahiplik kontrolünün yeterli şekilde yapılmadığı görülmektedir.

---

## 4. Bulguyu Raporlama

**Endpoint:**

```text
/rest/basket/2
```

**Normal kullanıcı:**

```text
UserId: 1
```

**Test edilen kaynak:**

```text
Basket ID: 2
UserId: 2
```

**Beklenen davranış:**

Normal kullanıcının yalnızca kendi sepetine erişebilmesi ve başka bir kullanıcıya ait sepet istendiğinde sunucunun erişimi reddetmesi beklenir.

**Gerçek davranış:**

Basket ID değeri `1` yerine `2` olarak değiştirildiğinde sunucu `200 OK` döndürerek `UserId: 2` olan sepetin bilgilerini ve ürünlerini gönderdi.

**Güvenlik problemi:**

Kullanıcının kaynak ID'sini değiştirerek başka bir kullanıcıya ait sepet verisine erişebilmesi, nesne seviyesinde yetkilendirme kontrolünün yetersiz olduğunu göstermektedir. Bu durum Broken Access Control / IDOR kapsamında değerlendirilebilir.

**Etki:**

Yetkisiz bir kullanıcı başka kullanıcıların sepet bilgilerini ve sepette bulunan ürünleri okuyabilir. Bu durum kullanıcı verilerinin gizliliğini etkileyebilir.

**Çözüm:**

Sunucu tarafında her basket isteğinde, istenen basket'ın oturum açmış kullanıcıya ait olup olmadığı kontrol edilmelidir.

Örneğin mantıksal olarak:

```text
İstek yapan kullanıcı → UserId 1
İstenen basket → UserId 2
                ↓
        Eşleşmiyor
                ↓
        Erişim reddedilmeli
```

Yetkisiz erişim durumunda uygulama uygun bir HTTP durum kodu, örneğin `403 Forbidden`, döndürmelidir.

---

## 5. Öğrendiğim

Bu çalışmada IDOR testinin yalnızca URL'deki bir sayıyı değiştirmekten ibaret olmadığını öğrendim.

Öncelikle ilgili ID'nin hangi kaynağı temsil ettiğini anlamak, daha sonra kaynak ile kullanıcı arasındaki sahiplik ilişkisini incelemek gerekiyor.

Bu testte:

```text
/rest/basket/1
        ↓
UserId: 1
```
<img width="1051" height="447" alt="image" src="https://github.com/user-attachments/assets/ec13cd5a-a4eb-4932-9884-c8ad2ec09e84" />

isteğini:

```text
/rest/basket/2
        ↓
UserId: 2
```
<img width="1020" height="318" alt="image" src="https://github.com/user-attachments/assets/3cfbadf5-8440-4c1e-89a9-789f8230ab07" />

şeklinde değiştirdim.

Sunucunun ikinci isteğe de `200 OK` ile cevap vermesi ve başka bir kullanıcıya ait sepet bilgisini döndürmesi, Broken Access Control açısından önemli bir bulgu ortaya çıkardı.

Bu çalışmayla birlikte **authentication'ın kullanıcının kim olduğunu doğrulaması ile authorization'ın kullanıcının hangi kaynağa erişebileceğini kontrol etmesinin farklı şeyler olduğunu** uygulamalı olarak görmüş oldum.
