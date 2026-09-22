# 12 - Nmap ile İşletim Sistemi Tespiti

## 🎯 Görevin Amacı

Bu bölümde Nmap'in bir hedef sistemin işletim sistemi hakkında nasıl bilgi edinmeye çalıştığını öğrenmek amaçlanmıştır.

İşletim sistemi tespiti, siber güvenlikte **keşif (reconnaissance)** aşamasının bir parçasıdır. Bir sistemin hangi işletim sistemi ve teknoloji altyapısını kullandığı hakkında bilgi sahibi olmak, hedefin teknik yapısını anlamaya yardımcı olur.

---

## 🧠 İşletim Sistemi Neden Önemlidir?

Bir sistemin işletim sistemi, üzerinde çalışan servisleri, kullanılan teknolojileri ve olası yapılandırmaları hakkında önemli ipuçları verebilir.

Örneğin:

* Windows sistemlerde **SMB (445)** veya **RDP (3389)** gibi servislerle karşılaşılabilir.
* Linux sistemlerde **SSH (22)** gibi servisler yaygın olarak kullanılabilir.
* Web sunucuları işletim sistemine göre farklı yazılımlar ve yapılandırmalar kullanabilir.

Bu nedenle bir güvenlik uzmanı, yetkili bir sistem üzerinde çalışma yaparken yalnızca **“hangi portlar açık?”** sorusuna değil, aynı zamanda **“bu sistemin teknik yapısı hakkında neler öğrenebilirim?”** sorusuna da bakar.

> **Not:** Bir işletim sisteminin tespit edilmesi tek başına sistemin güvenlik açığı olduğu anlamına gelmez. Bu bilgi, güvenlik değerlendirmesinde kullanılan teknik bağlamlardan biridir.

---

## 🔎 Nmap ile OS Detection

Nmap'te işletim sistemi tespiti için:

```bash
nmap -O localhost
```

komutu kullanılabilir.

Buradaki:

```text
-O
```

parametresi **OS Detection (Operating System Detection)** anlamına gelir.

Nmap bu işlem sırasında işletim sisteminin adını doğrudan bilgisayardan okumaz. Bunun yerine hedef sistemin ağ üzerinden verdiği yanıtları inceleyerek bir **işletim sistemi parmak izi (OS fingerprint)** oluşturmaya ve bunu bilinen işletim sistemi davranışlarıyla karşılaştırmaya çalışır.

---

## 💻 Gerçekleştirilen Uygulama

Kendi Kali Linux sistemimde aşağıdaki komutu çalıştırdım:

```bash
nmap -O localhost
```

### 📌 Alınan Çıktı

```text
Starting Nmap 7.95 ( https://nmap.org ) at 2026-09-22 07:02 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000050s latency).
Other addresses for localhost (not scanned): ::1
All 1000 scanned ports on localhost (127.0.0.1) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
Too many fingerprints match this host to give specific OS details
Network Distance: 0 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.61 seconds
```


## 🔍 Çıktının İncelenmesi

### `Host is up`

```text
Host is up
```

Hedef sistemin erişilebilir olduğunu gösterir.

Buradaki hedef:

```text
localhost (127.0.0.1)
```

olduğu için taranan sistem kendi bilgisayarımdır.

---

### `1000 closed tcp ports`

```text
Not shown: 1000 closed tcp ports (reset)
```

Nmap'in varsayılan olarak kontrol ettiği 1000 TCP portunun kapalı olduğunu gösterir.

Bu durumda çalışan ve dışarıya açık bir TCP servisi bulunmadığı için Nmap'in işletim sistemini ayırt edebilmesini sağlayacak ağ davranışı da sınırlı kalmıştır.

---

### `Too many fingerprints match this host`

Çıktının en önemli kısmı:

```text
Too many fingerprints match this host to give specific OS details
```

Bu mesaj, Nmap'in gözlemlediği davranışların birden fazla işletim sistemi parmak iziyle eşleştiğini gösterir.

Yani Nmap:

> "Bu davranışlardan kesin olarak şu işletim sistemi olduğunu söyleyemiyorum."

sonucuna ulaşmıştır.

Bu bir hata değildir.

Tam tersine, OS Detection'ın **kesin bir bilgi okumak yerine ağ davranışlarından çıkarım yaptığını** gösteren güzel bir örnektir.

---

## 🌐 `Network Distance: 0 hops`

```text
Network Distance: 0 hops
```

Hedef sistem ile taramayı yapan sistem arasında ağ üzerinde herhangi bir yönlendirici geçişi olmadığını gösterir.

Bunun nedeni hedefimizin:

```text
localhost
127.0.0.1
```

olmasıdır.

Yani hedef doğrudan kendi bilgisayarımızdır.

---

## 🧩 OS Detection ile Service Detection Arasındaki Fark

Önceki bölümde:

```bash
nmap -sV localhost
```

komutunu kullanmıştım.

Buradaki iki parametrenin amacı farklıdır:

| Parametre | Amaç                                                             |
| --------- | ---------------------------------------------------------------- |
| `-sV`     | Açık portlardaki servis ve sürüm bilgilerini belirlemeye çalışır |
| `-O`      | Hedef işletim sistemini belirlemeye çalışır                      |

Örneğin:

```text
22/tcp open ssh
```

gibi bir sonuç elde edilirse `-sV`, SSH servisinin hangi sürümünün çalıştığını araştırabilir.

`-O` ise hedef sistemin ağ davranışlarını inceleyerek işletim sistemi hakkında tahmin yapmaya çalışır.

---

## 🛡️ Siber Güvenlik Açısından Neden Önemli?

OS Detection, yetkili güvenlik testlerinde hedef hakkında **teknik profil oluşturma** aşamasına katkı sağlar.

Örneğin bir güvenlik uzmanı şu bilgileri birlikte değerlendirebilir:

```text
IP adresi
   ↓
Açık portlar
   ↓
Çalışan servisler
   ↓
Servis sürümleri
   ↓
İşletim sistemi hakkında bilgiler
   ↓
Hedef sistemin teknik profili
```

Bu bilgiler daha sonraki güvenlik değerlendirmelerinde kullanılabilecek bir temel oluşturur.

Ancak yalnızca işletim sistemini bilmek, sistemin saldırıya açık olduğunu göstermez. Güvenlik değerlendirmesi için servisler, sürümler, yapılandırmalar ve güvenlik kontrolleri gibi başka bilgiler de gerekir.

---

## ⚠️ Önemli Öğrenme Noktası

Bu çalışmada Nmap'in işletim sistemini kesin olarak tespit edemediğini gördüm.

Bu nedenle:

> **Nmap'in OS Detection sonucu her zaman kesin değildir.**

Sonuç; açık portların bulunmasına, hedefin ağ davranışına, firewall yapılandırmasına ve Nmap'in sahip olduğu işletim sistemi parmak izi verilerine bağlı olarak değişebilir.

Bu uygulamada 1000 TCP portunun kapalı olması ve yeterli ayırt edici ağ davranışının bulunmaması nedeniyle Nmap belirli bir işletim sistemi bilgisi verememiştir.

Bu sonucu doğrudan **“işletim sistemi şu”** şeklinde yorumlamak doğru değildir.

---

## 📝 Kendi Öğrendiklerim

Bu çalışmada `-O` parametresinin işletim sistemi tespiti için kullanıldığını öğrendim.

Özellikle OS Detection'ın bilgisayardan işletim sistemi bilgisini doğrudan okumadığını, ağ üzerinden gözlemlenen davranışlardan bir işletim sistemi parmak izi oluşturmaya çalıştığını öğrendim.

Benim taramamda Nmap belirli bir işletim sistemi tespit edemedi. Bunun temel nedeni hedefin `localhost` olması ve taranan 1000 TCP portunun tamamının kapalı olması nedeniyle yeterli ayırt edici bilgi oluşmamasıydı.

Bu uygulama sayesinde bir güvenlik aracının her zaman kesin sonuç vermesinin beklenmemesi gerektiğini de görmüş oldum.

---

## ✅ Kontrol Listesi

* [x] `-O` parametresinin ne olduğunu öğrendim.
* [x] OS Detection komutunu çalıştırdım.
* [x] OS fingerprint kavramını öğrendim.
* [x] `Too many fingerprints match this host` mesajını inceledim.
* [x] `Network Distance: 0 hops` bilgisini anladım.
* [x] `-sV` ile `-O` arasındaki farkı öğrendim.
* [x] OS Detection'ın neden kesin sonuç vermeyebileceğini öğrendim.
* [x] İşletim sistemi bilgisinin siber güvenlikte neden önemli olduğunu öğrendim.

---

## 🚀 Sonraki Adım

Bir sonraki bölümde Nmap taramalarında karşılaşılabilecek **açık portları ve bu portların arkasında çalışan servisleri** inceleyeceğim.

Özellikle yaygın portları ve bir port açık olduğunda bunun güvenlik açısından nasıl yorumlanabileceğini ele alacağım.
