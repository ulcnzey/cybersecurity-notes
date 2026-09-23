# 03 - Network Discovery with Nmap

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde çalışan ağ servislerini Nmap kullanarak keşfetmek ve elde edilen servis/sürüm bilgilerini güvenlik analizi açısından değerlendirmektir.

Bu aşamada herhangi bir istismar işlemi gerçekleştirilmemiştir. Çalışma yalnızca yetkilendirilmiş ve izole edilmiş laboratuvar ortamında ağ keşfi ve servis tespiti amacıyla gerçekleştirilmiştir.

---

## 2. Kullanılan Laboratuvar Ortamı

| Bileşen           | Bilgi             |
| ----------------- | ----------------- |
| Test Makinesi     | Kali Linux        |
| Hedef Makine      | Metasploitable 2  |
| Kali IP           | `192.168.56.10`   |
| Metasploitable IP | `192.168.56.20`   |
| Ağ                | `192.168.56.0/24` |
| Sanal Ağ          | `cyber-lab`       |
| Tarama Aracı      | Nmap 7.95         |

Metasploitable 2, güvenlik eğitimi ve penetrasyon testi çalışmalarında kullanılmak üzere bilerek zafiyetler içeren bir sanal makinedir.

---

## 3. Nmap Nedir?

Nmap (Network Mapper), ağ üzerindeki sistemleri ve ağ servislerini keşfetmek için kullanılan açık kaynaklı bir ağ tarama aracıdır.

Nmap ile aşağıdaki bilgiler elde edilebilir:

* Hedef sistemin erişilebilir olup olmadığı
* Açık TCP/UDP portları
* Çalışan ağ servisleri
* Servislerin yazılım ve sürüm bilgileri
* İşletim sistemi hakkında bazı bilgiler
* Bazı servislerin yapılandırma bilgileri

Bu çalışmada Nmap, Metasploitable 2'nin ağ üzerindeki saldırı yüzeyini belirlemek amacıyla kullanılmıştır.

---

## 4. Port, Servis ve Sürüm İlişkisi

Bir IP adresi bir ağ üzerindeki sistemi belirtirken, portlar sistem üzerindeki farklı iletişim noktalarını temsil eder.

Örneğin:

```text
192.168.56.20:21
```

ifadesinde:

* `192.168.56.20` → hedef sistemin IP adresi
* `21` → hedef port
* FTP → bu port üzerinde çalışan servis
* `vsftpd 2.3.4` → servisin tespit edilen yazılım ve sürümü

Güvenlik analizinde bu bilgiler birlikte değerlendirilir.

Temel ilişki:

```text
IP Adresi
    ↓
Port
    ↓
Servis
    ↓
Yazılım
    ↓
Sürüm
    ↓
Zafiyet Araştırması
```

---

## 5. Temel Nmap Taraması

İlk olarak hedef sistemde açık TCP portlarını belirlemek amacıyla temel Nmap taraması gerçekleştirilmiştir.

Kullanılan komut:

```bash
nmap 192.168.56.20
```

### Tarama Sonucu

```text
Starting Nmap 7.95 ( https://nmap.org ) at 2026-09-23 04:19 EDT
Nmap scan report for 192.168.56.20
Host is up (0.00084s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 08:00:27:05:CF:C8 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 14.02 seconds
```

---

## 6. Temel Tarama Sonucunun Değerlendirilmesi

Tarama sonucunda hedef sistemin erişilebilir olduğu görülmüştür:

```text
Host is up
```

Nmap'in varsayılan taramasında 1000 TCP portu kontrol edilmiş ve bunların:

* 23 tanesi açık
* 977 tanesi kapalı

olarak tespit edilmiştir.

Açık portlar, hedef sistem üzerinde ağ üzerinden erişilebilen servislerin bulunduğunu göstermektedir.

### Nmap Çıktısındaki Alanlar

| Alan    | Açıklama                             |
| ------- | ------------------------------------ |
| PORT    | Port numarası ve iletişim protokolü  |
| STATE   | Portun durumu                        |
| SERVICE | Nmap tarafından tahmin edilen servis |

Örneğin:

```text
80/tcp open http
```

ifadesi:

* `80` → port numarası
* `tcp` → kullanılan taşıma protokolü
* `open` → port üzerinde bağlantı kabul eden bir servis bulunuyor
* `http` → Nmap tarafından HTTP servisi olarak tanımlanmış

anlamına gelir.

---

## 7. Port Durumları

Nmap taramalarında temel olarak farklı port durumları görülebilir.

### Open

Port üzerinde bir servis çalışıyor ve bağlantı kabul ediyor.

```text
80/tcp open http
```

### Closed

Hedef sisteme ulaşılabiliyor ancak ilgili port üzerinde bağlantı kabul eden bir servis bulunmuyor.

### Filtered

Nmap, portun açık veya kapalı olduğunu kesin olarak belirleyemiyor. Genellikle güvenlik duvarı veya paket filtreleme mekanizmaları nedeniyle bu durum ortaya çıkabilir.

Bu çalışmada hedef sistem üzerinde çok sayıda açık port bulunduğu görülmüştür.

---

# 8. Servis ve Sürüm Tespiti

İlk taramada yalnızca port ve servis bilgileri elde edilmiştir. Güvenlik araştırmasının daha ayrıntılı yapılabilmesi için servislerin hangi yazılım ve sürümleri kullandığının belirlenmesi gerekir.

Bu amaçla Nmap'in `-sV` seçeneği kullanılmıştır.

Kullanılan komut:

```bash
nmap -sV 192.168.56.20
```

`-sV` seçeneği, açık portlarda çalışan servislerin yazılım ve sürüm bilgilerini belirlemeye çalışır.

---

## 9. Servis Sürüm Taraması Sonucu

```text
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login
514/tcp  open  shell       Netkit rshd
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
```

Ek olarak Nmap tarafından sistemin Linux/Unix tabanlı olduğu ve bazı hostname bilgilerinin bulunduğu tespit edilmiştir.

---

# 10. Tespit Edilen Servisler

| Port | Servis      | Tespit Edilen Yazılım / Sürüm |
| ---: | ----------- | ----------------------------- |
|   21 | FTP         | vsftpd 2.3.4                  |
|   22 | SSH         | OpenSSH 4.7p1                 |
|   23 | Telnet      | Linux telnetd                 |
|   25 | SMTP        | Postfix                       |
|   53 | DNS         | ISC BIND 9.4.2                |
|   80 | HTTP        | Apache httpd 2.2.8            |
|  111 | RPC         | rpcbind                       |
|  139 | NetBIOS/SMB | Samba smbd 3.X - 4.X          |
|  445 | SMB         | Samba smbd 3.X - 4.X          |
|  512 | exec        | netkit rexecd                 |
|  513 | login       | login                         |
|  514 | shell       | Netkit rshd                   |
| 1099 | Java RMI    | GNU Classpath grmiregistry    |
| 1524 | bindshell   | Metasploitable root shell     |
| 2049 | NFS         | NFS 2-4                       |
| 2121 | FTP         | ProFTPD 1.3.1                 |
| 3306 | MySQL       | MySQL 5.0.51a                 |
| 5432 | PostgreSQL  | PostgreSQL 8.3.x              |
| 5900 | VNC         | VNC 3.3                       |
| 6000 | X11         | X11                           |
| 6667 | IRC         | UnrealIRCd                    |
| 8009 | AJP         | Apache JServ Protocol v1.3    |
| 8180 | HTTP        | Apache Tomcat/Coyote          |

---

# 11. Saldırı Yüzeyi Açısından İlk Değerlendirme

Açık portların sayısı ve çalışan servislerin çeşitliliği, Metasploitable 2'nin geniş bir ağ saldırı yüzeyine sahip olduğunu göstermektedir.

Özellikle aşağıdaki servisler sonraki güvenlik araştırmaları açısından dikkat çekmektedir:

* FTP
* SSH
* Telnet
* HTTP
* SMB/Samba
* NFS
* MySQL
* PostgreSQL
* VNC
* Java RMI
* IRC
* Apache Tomcat

Bu aşamada bu servislerin zafiyetli olduğu sonucuna varılmamıştır. Yalnızca açık oldukları ve bazı servislerin yazılım/sürüm bilgilerinin tespit edildiği belirlenmiştir.

Zafiyet değerlendirmesi sonraki aşamalarda CVE ve diğer güvenilir güvenlik kaynakları üzerinden yapılacaktır.

---

# 12. Keşif ve Enumeration Arasındaki İlişki

Bu çalışmada iki farklı bilgi toplama seviyesi görülmüştür.

### Temel keşif

```bash
nmap 192.168.56.20
```

Temel olarak:

```text
Hedef → Açık Portlar → Servisler
```

bilgisini sağlamıştır.

### Servis enumeration

```bash
nmap -sV 192.168.56.20
```

ise:

```text
Hedef
   ↓
Açık Port
   ↓
Servis
   ↓
Yazılım
   ↓
Sürüm
```

şeklinde daha ayrıntılı bilgi sağlamıştır.

Bu bilgiler daha sonra güvenlik araştırmasında kullanılabilecek temel verileri oluşturur.

---

# 13. Güvenlik Analizi Açısından Önemi

Bir sistemin güvenlik değerlendirmesinde ilk adımlardan biri, dışarıya açık servislerin belirlenmesidir.

Açık bir port doğrudan bir zafiyet anlamına gelmez. Ancak çalışan her ağ servisi, sistemin saldırı yüzeyinin bir parçasını oluşturur.

Bu nedenle analiz sırasında şu sorular sorulmalıdır:

1. Bu servis gerekli mi?
2. Servisin hangi yazılımı kullanılıyor?
3. Kullanılan yazılımın sürümü nedir?
4. Bu sürüm için bilinen güvenlik zafiyetleri var mı?
5. Servis güvenli şekilde yapılandırılmış mı?
6. Servise kimlerin erişmesi gerekiyor?
7. Gereksizse servis kapatılabilir mi?
8. Servisin erişimi ağ seviyesinde sınırlandırılabilir mi?

Bu sorular sonraki zafiyet araştırması ve risk değerlendirmesinin temelini oluşturacaktır.

---

# 14. Çalışmanın Sonucu

Nmap kullanılarak `192.168.56.20` adresindeki Metasploitable 2 sistemi üzerinde temel ağ keşfi ve servis sürüm tespiti gerçekleştirilmiştir.

Çalışma sonucunda:

* Hedef sistemin erişilebilir olduğu doğrulanmıştır.
* 1000 TCP portu içerisinden 23 açık port tespit edilmiştir.
* Açık portlarda çalışan servisler belirlenmiştir.
* `-sV` seçeneği kullanılarak servislerin yazılım/sürüm bilgileri elde edilmiştir.
* Metasploitable 2'nin geniş bir ağ saldırı yüzeyine sahip olduğu gözlemlenmiştir.
* Elde edilen servis ve sürüm bilgileri, sonraki CVE ve zafiyet araştırmaları için temel veri olarak kaydedilmiştir.

Bu aşamada herhangi bir exploit veya yetkisiz erişim işlemi gerçekleştirilmemiştir.

---

## 15. Kullanılan Komutlar

### Temel Nmap taraması

```bash
nmap 192.168.56.20
```

### Servis ve sürüm tespiti

```bash
nmap -sV 192.168.56.20
```

---

## 16. Ekran Görüntüleri

### Temel Nmap Taraması

<img width="655" height="512" alt="image" src="https://github.com/user-attachments/assets/9e7be9ee-0e45-4dfa-a2ce-7eb17d6ee51b" />


### Servis ve Sürüm Taraması

<img width="655" height="536" alt="image" src="https://github.com/user-attachments/assets/98d78685-3cc7-44d3-a23d-eee88c32bac2" />


---

## 17. Sonraki Çalışma

Bir sonraki aşamada tespit edilen servisler ve oluşturdukları saldırı yüzeyi daha ayrıntılı incelenecektir.

Özellikle:

* FTP
* HTTP
* SMB/Samba
* Servis sürümleri
* Bilinen güvenlik zafiyetleri
* CVE kayıtları

üzerinde araştırma yapılacaktır.
