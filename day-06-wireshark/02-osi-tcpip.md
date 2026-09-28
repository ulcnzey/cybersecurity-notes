# OSI and TCP/IP Models

## 1. OSI Modeli Nedir?

Ağ iletişimini anlamak için kullanılan temel modellerden biri **OSI (Open Systems Interconnection)** modelidir.

OSI modeli ağ iletişimini **7 farklı katmana** ayırır. Bunun amacı, ağ iletişimindeki işlemleri daha anlaşılır ve düzenli bir şekilde incelemektir.

OSI katmanları:

1. Physical
2. Data Link
3. Network
4. Transport
5. Session
6. Presentation
7. Application

Her katmanın farklı bir görevi vardır ve katmanlar birlikte çalışarak verinin bir sistemden başka bir sisteme ulaşmasını sağlar.

---

## 2. OSI Katmanları

### 1. Physical Layer

En alt katmandır.

Verilerin fiziksel ortam üzerinden iletilmesiyle ilgilenir.

Örneğin:

* Elektriksel sinyaller
* Radyo sinyalleri
* Fiber optik üzerinden ışık sinyalleri

Bu katmanda veri fiziksel olarak taşınır.

---

### 2. Data Link Layer

Yerel ağ üzerindeki iletişimle ilgilenir.

Bu katmanda **MAC adresleri** ve **Ethernet** gibi teknolojiler bulunur.

Örneğin bir Ethernet frame içerisinde kaynak ve hedef MAC adresleri bulunabilir.

---

### 3. Network Layer

Ağlar arasındaki iletişim ve adresleme ile ilgilenir.

Bu katmanda **IP** önemli bir protokoldür.

Örneğin:

```text
Source IP:      192.168.56.10
Destination IP: 192.168.56.20
```

IP adresleri sayesinde verinin hangi kaynaktan geldiğini ve hangi hedefe gönderildiğini anlayabiliriz.

---

### 4. Transport Layer

Uçtan uca veri aktarımıyla ilgilenir.

Bu katmanda özellikle:

* TCP
* UDP

protokollerini öğreniyorum.

TCP bağlantı odaklı ve güvenilir veri aktarımı sağlarken, UDP bağlantısız bir iletişim modeli kullanır.

Wireshark analizlerinde bu katman benim için önemli olacak çünkü TCP ve UDP paketlerini doğrudan inceleyeceğim.

---

### 5. Session Layer

Sistemler arasındaki iletişim oturumlarının yönetilmesiyle ilgilenir.

Bir iletişimin başlatılması, devam ettirilmesi ve sonlandırılması gibi işlemler bu katmanla ilişkilidir.

---

### 6. Presentation Layer

Verinin nasıl temsil edildiğiyle ilgilenir.

Veri formatlama, şifreleme ve sıkıştırma gibi işlemlerle ilişkilendirilebilir.

Ancak gerçek ağ protokollerinin her zaman OSI modelindeki tek bir katmana birebir oturmadığını da öğrenmiş oldum.

---

### 7. Application Layer

Kullanıcıya en yakın katmandır.

Uygulamaların ağ üzerinden iletişim kurmasını sağlayan protokoller burada değerlendirilir.

Örneğin:

* HTTP
* DNS
* FTP
* SMTP

gibi protokoller uygulama seviyesinde çalışır.

---

# 3. TCP/IP Modeli Nedir?

**TCP/IP modeli**, internet ve ağ iletişimini açıklamak için kullanılan daha pratik bir modeldir.

Temel olarak 4 katmandan oluşur:

1. Application
2. Transport
3. Internet
4. Network Access

TCP/IP modelindeki katmanlar OSI modelindeki katmanlarla birebir aynı değildir ancak birbirleriyle ilişkilidir.

---

# 4. OSI ve TCP/IP Modellerinin İlişkisi

İki modeli birlikte düşündüğümde genel olarak şöyle eşleştirebilirim:

| OSI Modeli   | TCP/IP Modeli  |
| ------------ | -------------- |
| Application  | Application    |
| Presentation | Application    |
| Session      | Application    |
| Transport    | Transport      |
| Network      | Internet       |
| Data Link    | Network Access |
| Physical     | Network Access |

Burada önemli olan, iki modelin ağ iletişimini farklı ayrıntı seviyelerinde ele almasıdır.

OSI modeli daha ayrıntılı ve referans amaçlı 7 katmanlı bir modelken, TCP/IP modeli gerçek ağ iletişimini daha pratik şekilde açıklamak için kullanılır.

---

# 5. Protokollerin Katmanlarla İlişkisi

Wireshark kullanırken karşılaşacağım bazı temel protokolleri katmanlarla ilişkilendirebilirim.

| Protokol / Teknoloji | İlişkili Katman                                               | Temel Görevi                                  |
| -------------------- | ------------------------------------------------------------- | --------------------------------------------- |
| Ethernet             | Data Link                                                     | Yerel ağ iletişimi                            |
| ARP                  | Data Link / Network sınırı                                    | IP adresinden MAC adresini bulmak             |
| IP                   | Network / Internet                                            | Adresleme ve paketlerin yönlendirilmesi       |
| ICMP                 | Network / Internet                                            | Ağ kontrol ve hata mesajları                  |
| TCP                  | Transport                                                     | Bağlantı odaklı veri aktarımı                 |
| UDP                  | Transport                                                     | Bağlantısız veri aktarımı                     |
| DNS                  | Application                                                   | Alan adlarını IP adresleriyle ilişkilendirmek |
| HTTP                 | Application                                                   | Web iletişimini sağlamak                      |
| TLS                  | Application ile Transport arasında çalışan güvenlik protokolü | Şifreli iletişim ve güvenlik sağlamak         |

Burada özellikle ARP ve TLS gibi protokolleri tek bir OSI katmanına zorla yerleştirmemek gerektiğini öğrendim. Gerçek ağ protokollerinin katmanlaması, teorik OSI modelinden daha karmaşık olabilir.

---

# 6. Encapsulation Nedir?

Ağ iletişimini anlamam için önemli kavramlardan biri **encapsulation (kapsülleme)** kavramıdır.

Bir bilgisayar veri gönderirken veri, katmanlar boyunca ilerlerken her katman kendi bilgilerini ekler.

Basit olarak:

```text
Application Data
       ↓
TCP Header + Data
       ↓
IP Header + TCP Header + Data
       ↓
Ethernet Header + IP Header + TCP Header + Data
```

Yani üst katmandan gelen veri, alt katmanlara ilerledikçe ek bilgilerle birlikte taşınır.

Bunu paketlenmiş bir gönderi gibi düşünebilirim.

Önce veri vardır, daha sonra iletişim için gerekli bilgiler eklenir ve sonunda ağ üzerinden gönderilecek hale gelir.

---

# 7. Decapsulation Nedir?

Veri hedef sisteme ulaştığında süreç tersine gerçekleşir.

Alıcı sistem, katmanlardaki bilgileri sırayla işler.

Genel olarak:

```text
Ethernet
   ↓
IP
   ↓
TCP
   ↓
Application
```

şeklinde ilerler.

Bu sürece **decapsulation (kapsül açma)** denir.

---

# 8. Wireshark ile Bağlantısı

OSI ve TCP/IP modellerini öğrenmemin asıl amacı Wireshark'ta karşıma çıkan paketleri daha iyi anlayabilmek.

Örneğin Wireshark'ta bir paket içerisinde şu yapıyı görebilirim:

```text
Frame
└── Ethernet
    └── Internet Protocol
        └── Transmission Control Protocol
            └── HTTP
```

Artık bu yapı benim için rastgele bilgilerden oluşmuyor.

Bunun aslında katmanlı ağ iletişiminin bir gösterimi olduğunu anlayabiliyorum.

Örneğin:

```text
Ethernet
   ↓
Yerel ağ iletişimi

IP
   ↓
Kaynak ve hedef adresleme

TCP
   ↓
Taşıma ve bağlantı yönetimi

HTTP
   ↓
Web uygulaması iletişimi
```

Bu nedenle Wireshark'ta paket analizi yaparken yalnızca IP adreslerine bakmak yerine paketin hangi katmanlardan geçtiğini de inceleyebilirim.

---

# 9. Öğrendiğim Temel Mantık

Bu bölümde ağ iletişiminin tek bir işlem olmadığını, farklı görevlerin farklı katmanlar tarafından gerçekleştirildiğini öğrendim.

Benim için en önemli noktalar:

* OSI modeli ağ iletişimini 7 katmanda açıklar.
* TCP/IP modeli ağ iletişimini daha pratik bir şekilde açıklar.
* TCP ve UDP Transport katmanıyla ilişkilidir.
* IP Network/Internet katmanıyla ilişkilidir.
* Ethernet Data Link/Network Access tarafında çalışır.
* DNS ve HTTP uygulama seviyesinde çalışır.
* Bir veri ağ üzerinden gönderilirken katmanlar boyunca ek bilgiler alır.
* Bu işleme encapsulation denir.
* Alıcı tarafta bu yapı tersine açılır ve decapsulation gerçekleşir.
* Wireshark'taki paket yapısını anlayabilmek için katman mantığını bilmek gerekir.

## Kısaca

Ben artık Wireshark'ta bir paket gördüğümde:

```text
Ethernet
    ↓
IP
    ↓
TCP / UDP
    ↓
Application Protocol
```

yapısının arkasındaki mantığı okuyabilmeye başladım.

Bu temel, sonraki aşamalarda Wireshark arayüzünü ve paketlerin içindeki bilgileri incelerken kullanacağım.

---

## Sonraki Aşama

Bir sonraki bölümde **Wireshark arayüzünü** inceleyeceğim.

Özellikle:

* Interface List
* Packet List
* Packet Details
* Packet Bytes
* Display Filter
* Capture Filter
* Statistics
* Conversations
* Endpoints

bölümlerinin ne işe yaradığını öğrenerek Wireshark ekranını okumaya başlayacağım.
