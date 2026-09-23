# 09 - SMB / Samba Analizi

## 1. Amaç

Bu çalışmanın amacı, Metasploitable 2 üzerinde çalışan SMB/Samba servisinin incelenmesi, açık SMB portlarının belirlenmesi, servis hakkında bilgi toplanması, paylaşılan kaynakların ve kullanıcı hesaplarının enumerate edilmesi ve elde edilen bilgilerin güvenlik açısından değerlendirilmesidir.

Çalışma yalnızca izole edilmiş **cyber-lab** sanal ağında gerçekleştirilmiştir.

---

## 2. SMB Nedir?

**SMB (Server Message Block)**, ağ üzerinde dosya, klasör, yazıcı ve çeşitli kaynakların paylaşılmasını sağlayan bir ağ protokolüdür.

SMB özellikle Windows sistemlerinde yaygın olarak kullanılmakla birlikte Linux/Unix sistemlerinde de Samba aracılığıyla desteklenmektedir.

SMB üzerinden bir bilgisayardaki kaynaklar ağdaki başka bilgisayarlar tarafından erişilebilir hale getirilebilir.

Örnek bir SMB paylaşımı:

```text
\\192.168.56.20\shared
```

Burada:

* `192.168.56.20` → hedef bilgisayar
* `shared` → SMB üzerinden paylaşılan kaynak

---

## 3. Samba Nedir?

**Samba**, Linux ve Unix sistemlerin SMB protokolünü kullanarak Windows sistemleriyle dosya ve kaynak paylaşımı yapabilmesini sağlayan yazılım paketidir.

Bu nedenle:

```text
SMB → Protokol
Samba → SMB'yi Linux/Unix üzerinde uygulayan yazılım
```

şeklinde düşünülebilir.

Metasploitable 2 üzerinde SMB hizmeti Samba tarafından sağlanmaktadır.

---

## 4. SMB ile İlişkili Portlar

Çalışma sırasında aşağıdaki portlar açık olarak tespit edilmiştir:

| Port    | Protokol/Hizmet         | Açıklama                                          |
| ------- | ----------------------- | ------------------------------------------------- |
| 139/tcp | NetBIOS Session Service | SMB'nin eski taşıma yöntemlerinden biri           |
| 445/tcp | Microsoft-DS / SMB      | Doğrudan SMB iletişimi için kullanılan temel port |

İki portun açık olması, hedef sistemde SMB ile ilişkili hizmetlerin ağ üzerinden erişilebilir olduğunu göstermektedir.

---

## 5. İlk SMB Tespiti

Daha önce gerçekleştirilen Nmap servis taramasında aşağıdaki sonuç elde edilmiştir:

```text
139/tcp open  netbios-ssn   Samba smbd 3.X - 4.X
445/tcp open  netbios-ssn   Samba smbd 3.X - 4.X
```

Bu sonuç SMB/Samba servisinin hedef sistem üzerinde çalıştığını göstermektedir.

Ancak yalnızca servis adından kesin sürüm bilgisi elde edilemediği için daha detaylı enumeration gerçekleştirilmiştir.

---

## 6. SMB İşletim Sistemi ve Sistem Bilgisi Enumeration

SMB servisinden sistem bilgisi toplamak için aşağıdaki komut kullanılmıştır:

```bash
nmap -p 139,445 --script smb-os-discovery 192.168.56.20
```

### Elde edilen sonuç

```text
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Host script results:
| smb-os-discovery:
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name:
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
```

### Sonuçların değerlendirilmesi

Enumeration sonucunda:

* İşletim sistemi ailesi: Unix
* SMB yazılımı: Samba
* Samba sürümü: `3.0.20-Debian`
* Bilgisayar adı: `metasploitable`
* Domain: `localdomain`
* FQDN: `metasploitable.localdomain`

olarak tespit edilmiştir.

Bu bilgiler hedef sistemin ağ üzerindeki kimliği ve kullandığı SMB yazılımı hakkında daha ayrıntılı bilgi sağlamaktadır.

Bu aşama **enumeration** işlemidir; herhangi bir exploit veya yetki yükseltme işlemi gerçekleştirilmemiştir.

---

## 7. SMB Share Enumeration

SMB üzerinde hangi paylaşımların bulunduğunu belirlemek amacıyla aşağıdaki Nmap NSE script'i kullanılmıştır:

```bash
nmap -p 139,445 --script smb-enum-shares 192.168.56.20
```

Bu script, SMB sunucusunun sunduğu paylaşım kaynaklarını ve erişim izinlerini belirlemeye yardımcı olur.

### Tespit edilen paylaşımlar

| Share    | Tür            | Anonymous Access |
| -------- | -------------- | ---------------- |
| `ADMIN$` | IPC            | Yok              |
| `IPC$`   | IPC            | READ/WRITE       |
| `opt`    | Disk paylaşımı | Yok              |
| `print$` | Disk paylaşımı | Yok              |
| `tmp`    | Disk paylaşımı | READ/WRITE       |

---

## 8. Anonymous Access

**Anonymous access**, bir SMB kaynağına geçerli kullanıcı kimlik bilgileri sağlanmadan erişilebilmesini ifade eder.

Tarama sonucunda özellikle iki paylaşım dikkat çekmiştir:

```text
\\192.168.56.20\IPC$
Anonymous access: READ/WRITE
```

ve:

```text
\\192.168.56.20\tmp
Anonymous access: READ/WRITE
```

Buradaki `READ/WRITE` ifadesi anonim kullanıcıların ilgili kaynak üzerinde okuma ve yazma yetkisine sahip olduğunu göstermektedir.

Bu durum, uygun şekilde sınırlandırılmamış SMB yapılandırmalarında yetkisiz veri erişimi veya veri değişikliği gibi risklere neden olabilir.

Ancak bu tarama sonucundan tek başına belirli bir dosyanın okunabildiği veya değiştirilebildiği sonucu çıkarılmamıştır.

---

## 9. SMB Share Türleri

Tarama sonucunda farklı share türleri görülmüştür.

### 9.1. IPC$

`IPC$`, SMB ortamlarında süreçler arası iletişim ve çeşitli SMB işlemleri için kullanılan özel bir paylaşımdır.

Tarama sonucunda:

```text
Type: STYPE_IPC
Anonymous access: READ/WRITE
```

bilgisi elde edilmiştir.

### 9.2. Disk Paylaşımları

`opt` ve `tmp` gibi paylaşımlar:

```text
Type: STYPE_DISKTREE
```

olarak raporlanmıştır.

Bu tür paylaşımlar dosya sistemi üzerindeki kaynakların SMB üzerinden paylaşılmasıyla ilişkilidir.

`tmp` paylaşımı için:

```text
Anonymous access: READ/WRITE
```

sonucu elde edilmiştir.

### 9.3. print$

`print$`, Samba/Windows ortamlarında yazıcı sürücülerinin paylaşılmasıyla ilişkili özel bir SMB paylaşımıdır.

Tarama sonucunda anonim erişim bulunmadığı görülmüştür.

---

## 10. SMB User Enumeration

SMB üzerinden kullanıcı hesapları hakkında bilgi toplanması amacıyla aşağıdaki komut kullanılmıştır:

```bash
nmap -p 139,445 --script smb-enum-users 192.168.56.20
```

### Tespit edilen kullanıcı hesaplarından bazıları

Tarama sonucunda çok sayıda hesap tespit edilmiştir:

```text
backup
bin
bind
daemon
dhcp
distccd
ftp
games
gnats
irc
mysql
postgres
proftpd
proxy
root
sshd
syslog
telnetd
tomcat55
user
www-data
msfadmin
...
```

Nmap çıktısında bazı hesaplar:

```text
Account disabled
```

olarak raporlanırken bazı hesaplarda bu ifade bulunmamaktadır.

Örneğin:

```text
METASPLOITABLE\msfadmin (RID: 3000)
    Full name: msfadmin,,,
    Flags: Normal user account
```

ve:

```text
METASPLOITABLE\user (RID: 3002)
    Full name: just a user,111,,
    Flags: Normal user account
```

şeklinde sonuçlar elde edilmiştir.

---

## 11. RID Nedir?

**RID (Relative Identifier)**, Windows/Samba kimliklendirme yapısında kullanıcı ve grup hesaplarını tanımlamak için kullanılan bir kimlik bileşenidir.

Örneğin tarama sonucunda:

```text
msfadmin → RID 3000
user     → RID 3002
backup   → RID 1068
```

gibi değerler görülmüştür.

Bu değerlerin elde edilmesi, SMB enumeration sırasında sistemdeki hesapların daha ayrıntılı şekilde tanımlanmasına yardımcı olmaktadır.

---

## 12. Servis Hesapları

Tarama sonucunda yalnızca kullanıcıların değil, çeşitli servislerle ilişkili hesapların da listelendiği görülmüştür.

Örnekler:

| Hesap      | İlişkili servis / kullanım |
| ---------- | -------------------------- |
| `mysql`    | MySQL                      |
| `postgres` | PostgreSQL                 |
| `proftpd`  | ProFTPD                    |
| `sshd`     | SSH                        |
| `www-data` | Web servisleri             |
| `tomcat55` | Apache Tomcat              |

Bu hesapların listelenmesi, hedef sistemde çalışan servisler hakkında ek bilgi sağlayabilir.

---

## 13. Güvenlik Değerlendirmesi

SMB enumeration sonucunda hedef sistem hakkında aşağıdaki bilgilerin dışarıdan elde edilebildiği görülmüştür:

* SMB servisinin erişilebilir olduğu
* 139/tcp ve 445/tcp portlarının açık olduğu
* Samba sürümünün `3.0.20-Debian` olduğu
* Sistem adının `metasploitable` olduğu
* Domain/FQDN bilgilerinin öğrenilebildiği
* SMB paylaşım isimlerinin öğrenilebildiği
* Bazı paylaşımlarda anonymous `READ/WRITE` erişiminin bulunduğu
* Kullanıcı hesaplarının enumerate edilebildiği
* Bazı servis hesaplarının isimlerinin öğrenilebildiği

Bu bilgiler, hedef sistemin saldırı yüzeyinin daha ayrıntılı şekilde anlaşılmasını sağlayabilir.

Özellikle anonim erişime açık `READ/WRITE` paylaşımlar ve kullanıcı enumeration özelliği, güvenlik açısından incelenmesi gereken yapılandırmalardır.

---

## 14. Güvenlik Açığı ile Yapılandırma Bulgusu Arasındaki Ayrım

Bu çalışma sırasında önemli bir ayrım yapılmıştır:

```text
Açık port
    ≠
Güvenlik açığı

Eski sürüm
    ≠
Kesin olarak istismar edilebilir sistem

Kullanıcı adı bilgisi
    ≠
Parola bilgisi

Anonymous access
    ≠
Sistemin ele geçirilmesi
```

Bu nedenle elde edilen sonuçlar doğrudan sistemin ele geçirildiğini göstermemektedir.

Bulguların gerçek etkisini belirlemek için ayrıca yapılandırma, erişim izinleri, ilgili CVE'ler ve etkilenen bileşenler değerlendirilmelidir.

---

## 15. Saldırı Yüzeyi Açısından Değerlendirme

SMB servisinin saldırı yüzeyi aşağıdaki şekilde özetlenebilir:

```text
192.168.56.20
      │
      ├── 139/tcp
      │
      └── 445/tcp
             │
             ▼
           SMB
             │
             ▼
      Samba 3.0.20-Debian
             │
       ┌─────┴─────┐
       ▼           ▼
    Shares       Users
       │           │
       ▼           ▼
 IPC$, tmp...   msfadmin,
 Anonymous      user,
 READ/WRITE     service accounts...
```

Bu yapı, SMB servisinin yalnızca bir porttan ibaret olmadığını; servis üzerinden sistem, paylaşım ve kullanıcı bilgileri gibi birden fazla bilgi türünün elde edilebildiğini göstermektedir.

---

## 16. Önerilen Güvenlik Önlemleri

Gerçek sistemlerde aşağıdaki güvenlik önlemleri değerlendirilebilir:

1. Anonymous SMB erişiminin gereksizse devre dışı bırakılması.
2. SMB share izinlerinin en az yetki prensibine göre düzenlenmesi.
3. Gereksiz SMB servislerinin kapatılması.
4. Kullanıcı enumeration imkanlarının sınırlandırılması.
5. SMB servislerinin güncel ve desteklenen sürümlerde tutulması.
6. Ağ segmentasyonu ve firewall kuralları ile SMB erişiminin sınırlandırılması.
7. Hassas dosyaların genel paylaşımlarda tutulmaması.
8. SMB erişimlerinin loglanması ve izlenmesi.
9. Kullanıcı ve servis hesaplarının düzenli olarak gözden geçirilmesi.
10. Gereksiz veya kullanılmayan hesapların devre dışı bırakılması.

---

## 17. Kullanılan Komutlar

### SMB işletim sistemi ve sistem bilgisi

```bash
nmap -p 139,445 --script smb-os-discovery 192.168.56.20
```

### SMB share enumeration

```bash
nmap -p 139,445 --script smb-enum-shares 192.168.56.20
```

### SMB user enumeration

```bash
nmap -p 139,445 --script smb-enum-users 192.168.56.20
```

---

## 18. Çalışma Sonucu

Bu çalışmada Metasploitable 2 üzerindeki SMB/Samba servisi temel seviyeden başlayarak incelenmiştir.

139/tcp ve 445/tcp portlarının açık olduğu, SMB servisinin Samba 3.0.20-Debian kullandığı ve sistem hakkında çeşitli bilgilerin SMB enumeration aracılığıyla elde edilebildiği görülmüştür.

Ayrıca SMB paylaşım kaynakları ve kullanıcı hesapları enumerate edilmiştir. `IPC$` ve `tmp` paylaşımlarında anonim `READ/WRITE` erişimi tespit edilmiş, kullanıcı hesaplarının da SMB üzerinden listelenebildiği görülmüştür.

Bu çalışma sonucunda SMB servisinin saldırı yüzeyindeki rolü ve enumeration aşamasının güvenlik değerlendirmesindeki önemi anlaşılmıştır.

---

## 19. Ekran Görüntüleri

### SMB OS Discovery

<img width="652" height="343" alt="image" src="https://github.com/user-attachments/assets/37447090-9993-4914-b56a-7b2064f3e7c9" />


### SMB Share Enumeration

<img width="653" height="519" alt="image" src="https://github.com/user-attachments/assets/b71731ac-36c6-454f-98a4-969a679c3b47" />


### SMB User Enumeration

<img width="655" height="560" alt="image" src="https://github.com/user-attachments/assets/cda3935b-e456-4a58-b50f-051617031939" />


---

## 20. Öğrenilenler

* SMB'nin ağ üzerindeki kaynak paylaşımındaki rolü
* Samba'nın Linux/Unix üzerindeki SMB uygulaması
* 139/tcp ve 445/tcp portlarının işlevleri
* SMB share kavramı
* Anonymous access
* READ/WRITE izinleri
* IPC$ ve disk tabanlı SMB paylaşımları
* SMB user enumeration
* RID kavramı
* Servis hesapları ve normal kullanıcı hesapları arasındaki fark
* Enumeration ile exploitation arasındaki fark
* Yapılandırma bulgusu ile doğrulanmış güvenlik açığı arasındaki fark
* SMB servisinin saldırı yüzeyindeki önemi

---

## Kaynaklar

* Nmap Documentation — NSE Scripts
* Samba Documentation
* MITRE CVE
* NIST National Vulnerability Database (NVD)
* Metasploitable 2 Documentation
