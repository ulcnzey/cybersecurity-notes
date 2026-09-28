# 12. DNS Güvenlik Analizi

## 1. DNS'i Güvenlik Açısından İncelemek

DNS yalnızca domain adlarını IP adreslerine çevirmek için kullanılan bir sistem değildir. Aynı zamanda bir bilgisayarın hangi domainlerle iletişim kurmaya çalıştığını görmek açısından da önemli bir veri kaynağıdır.

Bu nedenle SOC ve network security analizlerinde DNS trafiği incelenebilir.

DNS trafiğini incelerken sadece domain adına değil;

* Kaynak bilgisayara
* DNS sunucusuna
* Sorgu sıklığına
* Domain yapısına
* Sorgu tipine
* Cevaplara
* Zaman bilgisine

birlikte bakılması gerekir.

---

# 2. DNS Spoofing

DNS Spoofing, DNS sorgusuna yanlış veya sahte bir DNS cevabı verilmesi durumudur.

Normal iletişim:

```text
Client
   ↓
DNS Query
   ↓
DNS Server
   ↓
Gerçek IP Adresi
```

Spoofing durumunda ise istemciye yanlış bir IP adresi döndürülmesi hedeflenebilir:

```text
Client
   ↓
DNS Query
   ↓
Yanlış DNS Response
   ↓
Farklı IP Adresi
```

Böylece kullanıcı doğru domain adına eriştiğini düşünürken farklı bir sunucuya yönlendirilebilir.

---

# 3. DNS Cache Poisoning

DNS Cache Poisoning, DNS cache içerisinde yanlış DNS bilgilerinin bulunmasına veya yerleştirilmesine dayanan bir saldırı türüdür.

Örneğin normal durumda:

```text
example.com → Gerçek IP
```

şeklinde olan kayıt yerine:

```text
example.com → Yanlış IP
```

gibi bir kayıt bulunabilir.

İstemci bu yanlış bilgiyi cache'den aldığı sürece yanlış IP adresine yönlendirilebilir.

DNS Spoofing ve DNS Cache Poisoning birbirleriyle ilişkili kavramlardır. Cache poisoning özellikle DNS cache içerisindeki yanlış bilgiyi hedef alır.

---

# 4. DNS Tunneling

DNS Tunneling, DNS protokolünün normal domain çözümlemesinin dışında veri taşımak amacıyla kullanılmasıdır.

Normal DNS iletişiminde:

```text
Client
   ↓
example.com?
   ↓
DNS Server
```

gibi sorgular gerçekleştirilir.

DNS tunneling kullanılan bir senaryoda ise DNS sorgularının içerisine veri taşınmaya çalışılabilir.

Bu nedenle aşağıdaki gibi olağandışı domain yapıları incelenebilir:

```text
x82kd91.example.com
a91kd72.example.com
p72ks83.example.com
```

Özellikle çok uzun, anlamsız ve sürekli değişen subdomainler dikkat gerektirebilir.

Ancak böyle bir yapı tek başına DNS tunneling olduğunu kanıtlamaz. Trafiğin sıklığı, domain yapısı ve diğer sistem kayıtları birlikte incelenmelidir.

---

# 5. DGA (Domain Generation Algorithm)

DGA (Domain Generation Algorithm), bazı zararlı yazılımların çok sayıda domain adı üretmek için kullandığı algoritmaları ifade eder.

Örneğin teorik olarak:

```text
xk29d8a.com
q91mks2.net
a82jd91.org
p72kd83.com
```

gibi rastgele görünen domainler üretilebilir.

Bu tür domainlerin arkasındaki amaçlardan biri, zararlı yazılımın iletişim kurduğu altyapının tespit edilmesini veya engellenmesini zorlaştırmaktır.

SOC analisti bu nedenle aynı bilgisayardan çok sayıda rastgele görünen domain sorgusu yapıldığını görürse bu davranışı araştırabilir.

---

# 6. Şüpheli Domainler

Bir domainin yalnızca ismine bakarak zararlı olduğunu söylemek doğru değildir.

Ancak aşağıdaki özellikler daha ayrıntılı inceleme gerektirebilir:

* Çok uzun domainler
* Rastgele karakterlerden oluşan domainler
* Uzun ve anlamsız subdomainler
* Daha önce ağda görülmeyen domainler
* Çok sayıda farklı domain sorgusu
* Çok yüksek DNS sorgu hacmi
* Çok sayıda başarısız DNS sorgusu
* Düzenli aralıklarla tekrarlanan olağandışı sorgular

Bu göstergelerden herhangi biri tek başına saldırı kanıtı değildir.

SOC analisti bunları diğer ağ ve sistem verileriyle birlikte değerlendirmelidir.

---

# 7. SOC Analisti Neden DNS Sorgularını İnceler?

Bir SOC analisti, bilgisayarların yaptığı DNS sorgularını inceleyerek hangi domainlerle iletişim kurulmaya çalışıldığını görebilir.

DNS trafiği;

* Zararlı yazılım iletişimi
* Komuta ve kontrol (C2) iletişimi
* DNS Tunneling
* DGA davranışı
* Şüpheli yönlendirmeler
* Yanlış yapılandırmalar

gibi olayların araştırılmasında başlangıç noktası olabilir.

Örneğin bir bilgisayar normalde bilinen servislerle iletişim kurarken aniden daha önce görülmeyen çok sayıda rastgele domain sorgulamaya başlarsa bu davranış incelenebilir.

---

# 8. Şüpheli DNS Trafiği Göstergeleri

Bir saldırı senaryosunda aşağıdaki DNS göstergeleri şüpheli olabilir:

## 1. Çok uzun ve rastgele görünen domainler

Örneğin:

```text
xk29d8a91kd72m.example.com
```

gibi uzun ve anlamsız domainler DGA veya DNS Tunneling açısından araştırılabilir.

---

## 2. Çok yüksek DNS sorgu hacmi

Bir bilgisayarın kısa bir zaman içerisinde normalden çok daha fazla DNS sorgusu göndermesi incelenebilir.

Bu durum otomatik çalışan bir uygulamadan da kaynaklanabilir. Bu nedenle bağlam önemlidir.

---

## 3. Çok sayıda başarısız DNS sorgusu

Çok sayıda `NXDOMAIN` veya başarısız DNS cevabı görülmesi araştırılabilir.

Özellikle aynı istemcinin sürekli farklı ve rastgele görünen domainleri sorgulaması DGA davranışı açısından incelenebilir.

---

## 4. Daha önce görülmeyen domainler

Ağ içerisinde daha önce görülmeyen yeni domainlere yapılan sorguların kaynağı ve amacı araştırılabilir.

Yeni bir uygulama veya servis de yeni bir domain kullanabileceğinden, bu durum tek başına kötü amaçlı faaliyet anlamına gelmez.

---

## 5. Düzenli aralıklarla tekrarlanan DNS sorguları

Bir bilgisayarın belirli aralıklarla benzer DNS sorguları göndermesi incelenebilir.

Örneğin:

```text
10:00 → DNS Query
10:05 → DNS Query
10:10 → DNS Query
10:15 → DNS Query
```

gibi düzenli bir davranış gözlemlendiğinde bunun arkasındaki uygulama araştırılabilir.

---

## 6. Uzun ve anlamsız subdomainler

Örneğin:

```text
a83kd92kds91.example.com
```

gibi sürekli değişen uzun subdomainler DNS Tunneling açısından incelenebilir.

---

## 7. Beklenmeyen DNS sunucularına yapılan sorgular

Kurumsal bir ağda istemcilerin belirli DNS sunucularını kullanması bekleniyorsa, farklı veya beklenmeyen DNS sunucularına yapılan sorgular araştırılabilir.

---

## 8. Çok sayıda farklı domain sorgusu

Aynı istemcinin kısa süre içerisinde çok fazla farklı domain sorgulaması olağandışı bir davranış olabilir.

Bu durum DGA veya başka bir otomatik işlem açısından araştırılabilir.

---

# 9. DNS Güvenlik Analizinde Temel Yaklaşım

DNS trafiğinde şüpheli bir gösterge görüldüğünde doğrudan saldırı sonucu çıkarılmamalıdır.

Benim için daha doğru analiz yaklaşımı şu şekilde:

```text
DNS Sorgusu
     ↓
Domain Analizi
     ↓
Kaynak Bilgisayar
     ↓
Sorgu Sıklığı
     ↓
Zaman Bilgisi
     ↓
DNS Response
     ↓
Diğer Network Trafiği
     ↓
Endpoint / Sistem Logları
     ↓
Olay Değerlendirmesi
```

Bu yaklaşım sayesinde tek bir pakete bakmak yerine olayın tamamını değerlendirebilirim.

---

# 10. Kendi Değerlendirmem

Bu çalışmada DNS'in yalnızca domain çözümlemek için kullanılan bir servis olmadığını, güvenlik analizinde de önemli bilgiler sağlayabileceğini öğrendim.

Bir SOC analisti DNS sorgularını inceleyerek:

* Hangi domainlerin sorgulandığını,
* Hangi bilgisayarın sorgu yaptığını,
* Hangi DNS sunucusunun kullanıldığını,
* Sorguların ne sıklıkta yapıldığını,
* DNS cevaplarının başarılı olup olmadığını

görebilir.

Özellikle çok uzun ve rastgele domainler, yüksek DNS sorgu hacmi, çok sayıda başarısız sorgu, beklenmeyen domainler ve düzenli aralıklarla tekrarlanan olağandışı sorgular daha ayrıntılı araştırma gerektirebilir.

Ancak bu göstergelerin hiçbiri tek başına saldırı kanıtı değildir. Güvenlik analizinde DNS verileri diğer network ve sistem kayıtlarıyla birlikte değerlendirilmelidir.
