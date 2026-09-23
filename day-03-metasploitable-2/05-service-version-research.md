# 05 - Servis Sürümlerinin Araştırılması

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde gerçekleştirilen servis ve sürüm tespitinden elde edilen bilgileri incelemek ve açık portlarda çalışan yazılımların sürümlerini belirlemektir.

Bir servisin yalnızca çalışıyor olması güvenlik açısından yeterli bilgi sağlamaz. Servisin hangi yazılım tarafından sağlandığı ve bu yazılımın hangi sürümünün kullanıldığı, daha sonraki güvenlik açığı araştırmalarının temelini oluşturur.

Bu nedenle çalışma aşağıdaki bilgi zinciri üzerinden yürütülmüştür:

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
Güvenlik Açığı Araştırması
```

---

## 2. Kullanılan Araç

Bu bölümde servis ve sürüm bilgilerini belirlemek için **Nmap** kullanılmıştır.

Kullanılan komut:

```bash
nmap -sV 192.168.56.20
```

Burada:

* `nmap` → Ağ keşfi ve tarama aracıdır.
* `-sV` → Açık portlarda çalışan servislerin sürüm bilgilerinin tespit edilmesini sağlar.
* `192.168.56.20` → Metasploitable 2'nin laboratuvar ortamındaki IP adresidir.

Bu tarama yalnızca izole edilmiş ve yetkilendirilmiş laboratuvar ortamındaki Metasploitable 2 makinesi üzerinde gerçekleştirilmiştir.

---

## 3. Nmap Servis ve Sürüm Tespit Sonucu

Nmap tarafından elde edilen sonuç:

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

---

## 4. Servis ve Sürüm Tablosu

Nmap çıktısındaki bilgiler aşağıdaki şekilde sınıflandırılmıştır:

| Port | Servis     | Yazılım              | Sürüm / Bilgi    |
| ---: | ---------- | -------------------- | ---------------- |
|   21 | FTP        | vsftpd               | 2.3.4            |
|   22 | SSH        | OpenSSH              | 4.7p1            |
|   23 | Telnet     | Linux telnetd        | Belirtilmedi     |
|   25 | SMTP       | Postfix              | smtpd            |
|   53 | DNS        | ISC BIND             | 9.4.2            |
|   80 | HTTP       | Apache HTTP Server   | 2.2.8            |
|  139 | SMB        | Samba                | 3.X - 4.X        |
|  445 | SMB        | Samba                | 3.X - 4.X        |
| 1099 | Java RMI   | GNU Classpath        | grmiregistry     |
| 1524 | Bind Shell | Metasploitable       | root shell       |
| 2049 | NFS        | NFS                  | 2-4              |
| 2121 | FTP        | ProFTPD              | 1.3.1            |
| 3306 | MySQL      | MySQL                | 5.0.51a-3ubuntu5 |
| 5432 | PostgreSQL | PostgreSQL           | 8.3.0 - 8.3.7    |
| 5900 | VNC        | VNC                  | Protocol 3.3     |
| 6000 | X11        | X11                  | Access denied    |
| 6667 | IRC        | UnrealIRCd           | Belirtilmedi     |
| 8009 | AJP        | Apache Jserv         | Protocol v1.3    |
| 8180 | HTTP       | Apache Tomcat/Coyote | JSP engine 1.1   |

---

## 5. Servis Sürümü Neden Önemlidir?

Bir servisin adını bilmek, güvenlik değerlendirmesi için tek başına yeterli değildir.

Örneğin:

```text
21/tcp → FTP
```

bilgisi yalnızca FTP servisinin çalıştığını gösterir.

Ancak:

```text
21/tcp → FTP → vsftpd 2.3.4
```

bilgisi daha ayrıntılıdır ve güvenlik araştırması yapılabilmesine olanak sağlar.

Çünkü farklı yazılımların ve farklı sürümlerin güvenlik geçmişleri birbirinden farklı olabilir.

Bu nedenle güvenlik değerlendirmesinde aşağıdaki bilgiler birlikte ele alınır:

```text
Port
  ↓
Servis
  ↓
Yazılım
  ↓
Sürüm
  ↓
Bilinen güvenlik açıkları
```

---

## 6. Dikkat Çeken Servis Sürümleri

Araştırmanın sonraki aşamalarında güvenlik açığı araştırması yapılmak üzere aşağıdaki servisler özellikle seçilmiştir:

### 6.1 vsftpd 2.3.4

```text
21/tcp
FTP
vsftpd 2.3.4
```

FTP servisi için kullanılan yazılım ve sürüm bilgisi Nmap tarafından açık şekilde tespit edilmiştir.

### 6.2 Apache HTTP Server 2.2.8

```text
80/tcp
HTTP
Apache httpd 2.2.8
```

HTTP servisi Apache HTTP Server tarafından sağlanmaktadır.

### 6.3 ProFTPD 1.3.1

```text
2121/tcp
FTP
ProFTPD 1.3.1
```

Metasploitable 2 üzerinde ikinci bir FTP servisi farklı bir port üzerinden çalışmaktadır.

Bu örnek, aynı servis türünün farklı yazılımlar tarafından sağlanabileceğini göstermektedir.

---

## 7. Eski Sürüm ile Güvenlik Açığı Arasındaki İlişki

Bir yazılımın eski bir sürümünün kullanılması güvenlik açısından dikkat edilmesi gereken bir durumdur. Ancak yalnızca sürümün eski olması, yazılımın kesin olarak belirli bir güvenlik açığından etkilendiği anlamına gelmez.

Güvenlik açığı değerlendirmesi yapılırken:

* Güvenlik açığının ilgili yazılımı etkileyip etkilemediği,
* Etkilenen sürüm aralığı,
* Güvenlik açığının türü,
* Etkinin ne olduğu,
* Üretici tarafından yayınlanan güvenlik güncellemeleri,
* İlgili CVE kayıtları

gibi bilgiler kontrol edilmelidir.

Bu nedenle sürüm tespitinden sonra doğrudan bir güvenlik açığı sonucuna varılmamalıdır.

Doğru değerlendirme süreci:

```text
Yazılım ve sürüm tespiti
        ↓
Güvenlik açığı araştırması
        ↓
CVE / güvenlik duyurusu kontrolü
        ↓
Etkilenen sürüm aralığının doğrulanması
        ↓
Etkinin değerlendirilmesi
```

---

## 8. Servis Sürümü ile CVE Araştırması Arasındaki İlişki

Bu bölümde elde edilen sürüm bilgileri bir sonraki aşamadaki CVE araştırmasının temelini oluşturacaktır.

Örneğin:

```text
vsftpd 2.3.4
```

tespit edildiğinde sonraki aşamada bu sürümle ilişkili bilinen güvenlik açıkları araştırılabilir.

Aynı şekilde:

```text
Apache httpd 2.2.8
```

ve

```text
ProFTPD 1.3.1
```

için de ilgili güvenlik kayıtları incelenebilir.

Bu yaklaşım sayesinde güvenlik değerlendirmesi tahmine dayalı olmaktan çıkarılarak belirli yazılım ve sürüm bilgilerine dayandırılır.

---

## 9. Güvenlik Değerlendirmesi Açısından Önemi

Servis sürümlerinin tespit edilmesi aşağıdaki güvenlik çalışmalarına temel oluşturur:

* CVE araştırması
* Güvenlik açığı doğrulama
* Risk değerlendirmesi
* Saldırı yüzeyi analizi
* Zafiyet yönetimi
* Güncelleme ve yama ihtiyacının belirlenmesi
* Güvenlik raporlaması

Bu nedenle servis sürüm tespiti, zafiyet analizinin önemli aşamalarından biridir.

---

## 10. Çalışmada Kullanılan Komut

```bash
nmap -sV 192.168.56.20
```

Komutun amacı:

> Metasploitable 2 üzerinde açık olan portlarda çalışan servisleri ve mümkün olduğu ölçüde bu servislerin yazılım ve sürüm bilgilerini belirlemek.

---

## 11. Sonuç

Nmap'in servis ve sürüm tespit özelliği kullanılarak Metasploitable 2 üzerinde çalışan servisler incelenmiştir.

Tarama sonucunda FTP, SSH, Telnet, SMTP, DNS, HTTP, SMB, NFS, MySQL, PostgreSQL, VNC, IRC, AJP ve Tomcat gibi çeşitli servislerin açık portlar üzerinden erişilebilir olduğu görülmüştür.

Servislerin yazılım ve sürüm bilgilerinin belirlenmesi, sonraki aşamada gerçekleştirilecek CVE ve güvenlik açığı araştırmaları için temel veri sağlamıştır.

Bu çalışma sonucunda aşağıdaki güvenlik değerlendirme zinciri oluşturulmuştur:

```text
Açık Port
    ↓
Servis
    ↓
Yazılım
    ↓
Sürüm
    ↓
CVE / Güvenlik Açığı
    ↓
Etki
    ↓
Risk
```

Bir sonraki aşamada belirlenen servis ve sürümler üzerinden bilinen güvenlik açıkları ve CVE kayıtları araştırılacaktır.

---


