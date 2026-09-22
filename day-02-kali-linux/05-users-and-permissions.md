# 05 — Kullanıcılar ve Linux Yetkileri

## 🎯 Çalışmanın Amacı

Bu çalışmada Linux işletim sistemlerinde kullanıcı, grup ve yetkilendirme yapısı incelenmiştir.

Çalışma kapsamında aşağıdaki komutlar uygulanmıştır:

```bash
whoami
id
groups
sudo -l
```

Bu komutlar kullanılarak mevcut kullanıcının kimliği, kullanıcı ve grup bilgileri ve `sudo` üzerinden sahip olduğu yetkiler incelenmiştir.

> **Not:** Tüm uygulamalar kendi Kali Linux sanal makinem üzerinde gerçekleştirilmiştir.

---

# 1. Linux Kullanıcıları

Linux çok kullanıcılı bir işletim sistemidir. Sistemde farklı kullanıcı hesapları bulunabilir ve her kullanıcıya farklı erişim yetkileri verilebilir.

Kullanıcıların sahip olduğu yetkiler sayesinde hangi dosyalara, klasörlere veya sistem kaynaklarına erişebilecekleri kontrol edilir.

Bu yapı siber güvenlik açısından önemlidir çünkü bir kullanıcının sisteme erişebilmesi, otomatik olarak sistemdeki bütün kaynaklara erişebileceği anlamına gelmez.

---

# 2. `whoami` — Mevcut Kullanıcıyı Görüntüleme

İlk olarak hangi kullanıcı hesabıyla işlem yaptığımı kontrol etmek için:

```bash
whoami
```

komutu kullanılmıştır.

Çalışma sırasında alınan çıktı:

```text
zeynep
```

Bu sonuç, terminal üzerinde gerçekleştirilen işlemlerin `zeynep` kullanıcısı üzerinden yapıldığını göstermektedir.

### 📸 `whoami` çıktısı

<img width="183" height="55" alt="image" src="https://github.com/user-attachments/assets/0edbdfc0-4ee4-4560-8b57-ca25f3202f2d" />


---

# 3. `id` — Kullanıcı ve Grup Bilgileri

Kullanıcının UID, GID ve grup bilgilerini görmek için:

```bash
id
```

komutu kullanılmıştır.

Bu komutun çıktısında temel olarak aşağıdaki bilgiler bulunur:

* `uid` → Kullanıcı kimliği
* `gid` → Kullanıcının ana grup kimliği
* `groups` → Kullanıcının dahil olduğu gruplar

Örnek bir çıktı yapısı:

```text
uid=1000(user) gid=1000(user) groups=1000(user),...
```

Gerçek değerler kullanılan Linux sistemine göre değişebilir.

### 📸 `id` çıktısı

<img width="633" height="107" alt="image" src="https://github.com/user-attachments/assets/ae85d8b2-4b7c-4c88-bcf4-ca9a5c336578" />


---

# 4. UID — User ID

`UID`, Linux sistemindeki kullanıcı hesabını tanımlayan sayısal kimliktir.

Örneğin:

```text
uid=1000(zeynep)
```

ifadesinde:

```text
1000
```

kullanıcının UID değeridir.

Linux sistemlerinde kullanıcı isimleri yerine bazı işlemlerde bu sayısal kimlikler kullanılır.

Bu nedenle bir dosyanın sahibini veya bir işlemin hangi kullanıcı tarafından gerçekleştirildiğini incelerken UID bilgisi önem taşıyabilir.

---

# 5. GID — Group ID

`GID`, kullanıcının bağlı olduğu ana grubun sayısal kimliğidir.

Örneğin:

```text
gid=1000(zeynep)
```

ifadesindeki `1000`, grubun GID değeridir.

Linux'ta dosya ve kaynak erişimlerinin yönetiminde kullanıcılar kadar gruplar da önemlidir.

---

# 6. `groups` — Kullanıcının Grupları

Mevcut kullanıcının dahil olduğu grupları görmek için:

```bash
groups
```

komutu kullanılmıştır.

Bu komut kullanıcının hangi Linux gruplarına dahil olduğunu gösterir.

### 📸 `groups` çıktısı

<img width="626" height="74" alt="image" src="https://github.com/user-attachments/assets/70aee95a-d868-4a8f-92e7-144acbb98090" />


Gruplar, ortak yetkilere ihtiyaç duyan kullanıcıların yönetilmesini kolaylaştırır.

Örneğin bir sistem yöneticisi belirli bir gruba belirli bir kaynağa erişim yetkisi verebilir. O gruba dahil olan kullanıcılar da ilgili yetkilere sahip olabilir.

---

# 7. `sudo` Nedir?

`sudo`, Linux'ta kullanıcının yetkili bir işlem gerçekleştirmesine olanak sağlayan mekanizmalardan biridir.

`sudo` kullanıldığında kullanıcı, sistem yapılandırmasına bağlı olarak kendi hesabından daha yüksek yetki gerektiren belirli bir komutu çalıştırabilir.

Örneğin:

```bash
sudo komut
```

şeklinde kullanılabilir.

Burada önemli bir ayrım vardır:

```text
root → yüksek yetkili kullanıcı hesabı
sudo → yetkili işlemleri gerçekleştirmek için kullanılan mekanizma
```

Dolayısıyla `root` ile `sudo` aynı şey değildir.

---

# 8. `sudo -l` — Sudo Yetkilerini Görüntüleme

Mevcut kullanıcının `sudo` üzerinden hangi komutları çalıştırabileceğini görmek için:

```bash
sudo -l
```

komutu kullanılmıştır.

Bu komut sistem yapılandırmasına bağlı olarak mevcut kullanıcının sahip olduğu `sudo` yetkileri hakkında bilgi verir.

Komut çalıştırıldığında sistem kullanıcı şifresi isteyebilir.

### 📸 `sudo -l` çıktısı

<img width="583" height="219" alt="image" src="https://github.com/user-attachments/assets/3081e57d-b1bb-4029-87a0-d1c44ad3e7b2" />


> **Güvenlik notu:** GitHub'a yüklenen ekran görüntülerinde gereksiz kişisel veya sistem bilgileri paylaşılmamalıdır.

---

# 9. Root Kullanıcısı

Linux'ta `root`, sistem üzerinde en yüksek yetkilere sahip kullanıcı hesabıdır.

Root kullanıcısı sistem dosyalarına ve kritik yapılandırmalara erişebilir.

Bu nedenle root yetkisinin gereksiz yere kullanılması güvenlik açısından risk oluşturabilir.

Örneğin bir saldırgan düşük yetkili bir kullanıcı hesabına eriştiğinde, doğrudan root yetkisine sahip olmayabilir.

Bu durumda saldırganın daha yüksek yetkilere ulaşmaya çalışması **privilege escalation (yetki yükseltme)** olarak adlandırılır.

---

# 10. En Az Ayrıcalık İlkesi

Sistem güvenliğinde önemli prensiplerden biri **Least Privilege**, yani **En Az Ayrıcalık İlkesi**dir.

Bu prensibe göre kullanıcılar ve uygulamalar yalnızca görevlerini gerçekleştirmek için ihtiyaç duydukları yetkilere sahip olmalıdır.

Örneğin bir kullanıcının belirli bir dosyayı okumaya ihtiyacı varsa, yalnızca gerekli okuma yetkisine sahip olması yeterli olabilir.

Her kullanıcıya gereksiz şekilde yönetici yetkisi verilmesi saldırıların etkisini artırabilir.

---

# 11. Siber Güvenlik Açısından Değerlendirme

Kullanıcı ve yetki yönetimi siber güvenliğin temel konularından biridir.

Bir saldırganın sisteme erişim sağlaması tek başına sistem üzerinde tam kontrol sahibi olduğu anlamına gelmez.

Örneğin:

```text
Saldırgan
    ↓
Düşük yetkili kullanıcı hesabı
    ↓
Sınırlı dosya ve kaynak erişimi
    ↓
Yetki yükseltme ihtiyacı
    ↓
Root / yönetici yetkisi
```

Bu nedenle savunma açısından:

* Gereksiz yönetici hesapları sınırlandırılmalıdır.
* Kullanıcılara ihtiyaçlarından fazla yetki verilmemelidir.
* Sudo yetkileri kontrol edilmelidir.
* Kullanıcı ve grup üyelikleri düzenli olarak incelenmelidir.
* Kritik işlemler yetkili kullanıcılarla sınırlandırılmalıdır.

---

# 12. Kullanılan Komutların Özeti

| Komut     | Görevi                                                            |
| --------- | ----------------------------------------------------------------- |
| `whoami`  | Mevcut kullanıcıyı gösterir.                                      |
| `id`      | UID, GID ve grup bilgilerini gösterir.                            |
| `groups`  | Kullanıcının dahil olduğu grupları gösterir.                      |
| `sudo -l` | Kullanıcının sudo üzerinden çalıştırabileceği komutları gösterir. |

---

# 13. Öğrenilenler

Bu çalışma sonunda:

* Linux'ta kullanıcı kavramı öğrenildi.
* `whoami` ile mevcut kullanıcı kontrol edildi.
* `id` komutu ile UID, GID ve grup bilgileri incelendi.
* `groups` komutu ile kullanıcı grup üyelikleri görüldü.
* `sudo` mekanizmasının amacı öğrenildi.
* `sudo -l` ile kullanıcının sudo yetkileri incelendi.
* `root` kullanıcısının Linux'taki rolü öğrenildi.
* Kullanıcı yetkileri ile sistem güvenliği arasındaki ilişki incelendi.
* Least Privilege / En Az Ayrıcalık İlkesi öğrenildi.
* Düşük yetkili bir kullanıcı hesabına erişimin otomatik olarak tam sistem kontrolü anlamına gelmediği öğrenildi.

---

# 📝 Genel Değerlendirme

Bu çalışma ile Linux'ta kullanıcıların ve yetkilerin nasıl yapılandırıldığı hakkında temel bilgi edinilmiştir.

Siber güvenlik açısından kullanıcı yetkilerinin sınırlandırılmasının önemli olduğu görülmüştür. Özellikle gereksiz yönetici yetkilerinin azaltılması ve en az ayrıcalık ilkesinin uygulanması sistem güvenliğinin temel unsurlarından biridir.

Bir sonraki aşamada Linux ağ yapılandırması incelenecek ve `ip addr`, `ip route` ve `ip neigh` komutları kullanılarak sistemin ağ bağlantısı analiz edilecektir.
