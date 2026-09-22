# 🐉 01 — Kali Linux'u Tanı

## 📌 Genel Bakış

Bu bölümde siber güvenlik çalışmalarında yaygın olarak kullanılan **Kali Linux** işletim sistemini tanıdım.

Kali Linux'un yalnızca "hackleme amacıyla kullanılan bir işletim sistemi" olmadığını; bilgi toplama, ağ analizi, web güvenliği, parola güvenliği, adli bilişim ve penetrasyon testleri gibi birçok güvenlik alanında kullanılan **Debian tabanlı bir Linux dağıtımı** olduğunu öğrendim.

---

## 🐉 Kali Linux Nedir?

Kali Linux, **Offensive Security** tarafından geliştirilen, Debian tabanlı bir Linux dağıtımıdır.

Siber güvenlik uzmanlarının güvenlik testleri, penetrasyon testleri, ağ analizi, dijital adli bilişim ve güvenlik araştırmaları gibi çalışmalarında kullanabilecekleri çok sayıda aracı hazır olarak sunar.

Kali Linux'un önemli özelliklerinden biri, güvenlik çalışmalarında ihtiyaç duyulabilecek birçok aracın tek bir ortam içerisinde bulunmasıdır.

> ⚠️ Kali Linux'taki araçlar yalnızca yetkili olduğum sistemlerde ve kontrollü laboratuvar ortamlarında kullanılmalıdır.

---

## 🔐 Siber Güvenlikte Neden Kullanılır?

Kali Linux özellikle aşağıdaki çalışmalar için kullanılabilir:

* Bilgi toplama ve keşif
* Ağ güvenliği analizi
* Web uygulaması güvenlik testleri
* Kablosuz ağ güvenliği
* Parola güvenliği testleri
* Zafiyet analizi
* Penetrasyon testleri
* Dijital adli bilişim
* Tersine mühendislik
* Güvenlik araştırmaları

Kali Linux'un önemli avantajlarından biri, bu alanların çoğunda kullanılan araçların aynı işletim sistemi içerisinde bulunmasıdır.

---

## 🐧 Normal Linux Dağıtımlarından Farkı Nedir?

Kali Linux da Ubuntu, Debian veya Fedora gibi bir Linux dağıtımıdır. Ancak kullanım amacı ve varsayılan araç seti farklıdır.

| Özellik                   | Genel Linux Dağıtımları      | Kali Linux                       |
| ------------------------- | ---------------------------- | -------------------------------- |
| Günlük kullanım           | ✅                            | Mümkün                           |
| Ofis / internet kullanımı | ✅                            | ✅                                |
| Siber güvenlik araçları   | Sınırlı                      | Çok geniş                        |
| Penetrasyon testi         | Ek araç kurulumu gerekebilir | Araçların çoğu hazır             |
| Ağ analizi                | Ek araç gerekebilir          | Çok sayıda araç mevcut           |
| Adli bilişim              | Ek araç gerekebilir          | Özel araçlar mevcut              |
| Güvenlik araştırmaları    | Desteklenir                  | Temel kullanım alanlarından biri |

Bu nedenle Kali Linux'u "normal Linux'un daha gelişmiş hali" olarak değil, **siber güvenlik çalışmalarına yönelik özelleştirilmiş bir Linux ortamı** olarak değerlendirmek daha doğrudur.

---

## 🧰 Kali Linux'ta Bulunan Güvenlik Araçları

Görev kapsamında bazı araçların ne amaçla kullanıldığını araştırdım.

| Araç                     | Temel kullanım amacı                                     |
| ------------------------ | -------------------------------------------------------- |
| **Nmap**                 | Ağ keşfi ve port taraması                                |
| **Wireshark**            | Ağ trafiğini yakalama ve analiz etme                     |
| **Burp Suite**           | Web uygulamalarının güvenlik testi                       |
| **Metasploit Framework** | Güvenlik testleri ve exploit geliştirme/denemeleri       |
| **John the Ripper**      | Parola hash'lerinin güvenlik testi                       |
| **Hydra**                | Kimlik doğrulama mekanizmalarının parola güvenliği testi |
| **Gobuster**             | Web içeriklerinin ve dizinlerinin keşfi                  |
| **Nikto**                | Web sunucularında temel güvenlik kontrolleri             |

Bu araçların hepsini aynı anda kullanmak yerine, **hangi güvenlik probleminde hangi aracın kullanılacağını anlamanın** daha önemli olduğunu öğrendim.

---

## 🧩 Güvenlik Alanlarına Göre Araçlar

### 🔎 Bilgi Toplama

* Nmap
* Gobuster
* Nikto

### 🌐 Web Güvenliği

* Burp Suite
* Nikto
* Gobuster

### 🔑 Parola Güvenliği

* John the Ripper
* Hydra

### 📡 Ağ Analizi

* Wireshark
* Nmap

### 💥 Güvenlik Testleri

* Metasploit Framework
* Nmap

---

## 🧠 Bu Bölümde Ne Öğrendim?

Bu bölümün sonunda:

* Kali Linux'un ne olduğunu,
* Debian tabanlı olduğunu,
* Siber güvenlik çalışmalarında neden tercih edildiğini,
* Normal Linux dağıtımlarından temel farklarını,
* Güvenlik araçlarının hangi amaçlarla kullanıldığını,
* Bir aracın ismini bilmekten çok **hangi problem için kullanıldığını bilmenin** önemli olduğunu öğrendim.

### 🎯 Sonraki Adım

Bir sonraki bölümde Kali Linux sistemimin temel bilgilerini terminal üzerinden çıkaracağım:

```bash
whoami
hostname
pwd
uname -a
cat /etc/os-release
ip addr
ip route
```

Bu komutlarla kullanıcı, hostname, işletim sistemi, kernel, ağ arayüzleri ve varsayılan ağ geçidi gibi bilgileri inceleyeceğim.
