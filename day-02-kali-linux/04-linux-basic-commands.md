# 04 — Temel Linux Komutları

## 🎯 Çalışmanın Amacı

Bu çalışmada Kali Linux üzerinde temel dosya ve klasör yönetimi komutları öğrenilmiştir.

Çalışma kapsamında dosya ve klasör oluşturma, taşıma, kopyalama, silme, dosya içeriğini görüntüleme ve dosya içerisinde belirli ifadeleri arama işlemleri uygulanmıştır.

Ayrıca `grep` ve `find` komutlarının siber güvenlikte log analizi ve dosya araştırması açısından kullanım alanları incelenmiştir.

> **Not:** Tüm uygulamalar kendi Kali Linux sanal makinem üzerinde gerçekleştirilmiştir.

---

# 1. Temel Linux Komutları

| Komut   | Açıklama                                             |
| ------- | ---------------------------------------------------- |
| `ls`    | Bulunulan dizindeki dosya ve klasörleri listeler.    |
| `cd`    | Dizinler arasında geçiş yapmayı sağlar.              |
| `pwd`   | Bulunulan dizinin tam yolunu gösterir.               |
| `mkdir` | Yeni klasör oluşturur.                               |
| `touch` | Yeni dosya oluşturur.                                |
| `cp`    | Dosya veya klasörleri kopyalar.                      |
| `mv`    | Dosya veya klasörleri taşır veya yeniden adlandırır. |
| `rm`    | Dosya veya klasörleri siler.                         |
| `cat`   | Dosya içeriğini terminal üzerinde görüntüler.        |
| `less`  | Dosya içeriğini sayfa şeklinde incelemeyi sağlar.    |
| `head`  | Dosyanın başlangıcındaki satırları gösterir.         |
| `tail`  | Dosyanın sonundaki satırları gösterir.               |
| `grep`  | Dosya içerisinde belirli bir ifade arar.             |
| `find`  | Dosya ve klasörleri belirli kriterlere göre bulur.   |

---

# 2. Çalışma Ortamının Oluşturulması

Çalışmaya home dizinine geçilerek başlanmıştır.

```bash
cd ~
```

Ardından siber güvenlik çalışmalarında kullanılmak üzere `security-lab` isimli bir çalışma klasörü oluşturulmuştur.

```bash
mkdir security-lab
cd security-lab
```

Çalışma klasörü içerisinde üç farklı klasör oluşturulmuştur:

```bash
mkdir notes
mkdir logs
mkdir reports
```

Oluşturulan yapı:

```text
security-lab/
├── notes/
├── logs/
└── reports/
```

### 📸 Çalışma Alanı

<img width="517" height="508" alt="image" src="https://github.com/user-attachments/assets/a17d8de4-ecd3-449e-bfda-015a687d290a" />


---

# 3. `touch` — Dosya Oluşturma

`touch` komutu ile `notes` klasörü içerisinde bir metin dosyası oluşturulmuştur.

```bash
cd notes
touch linux-commands.txt
```

Dosyanın oluşturulduğu `ls` komutu ile kontrol edilmiştir.

```bash
ls
```

### 📸 Dosya oluşturma

<img width="333" height="153" alt="image" src="https://github.com/user-attachments/assets/dd56e093-7e87-49df-92a7-ecab727acea7" />


---

# 4. `cat` — Dosya İçeriğini Görüntüleme

Oluşturulan dosyaya örnek bir bilgi eklenmiştir.

```bash
echo "Linux temel komutları" > linux-commands.txt
```

Ardından dosyanın içeriği `cat` komutu ile görüntülenmiştir.

```bash
cat linux-commands.txt
```

Çıktı:

```text
Linux temel komutları
```

`cat` komutu özellikle küçük dosyaların içeriğini hızlı şekilde kontrol etmek için kullanılabilir.

### 📸 `cat` çıktısı

<img width="434" height="110" alt="image" src="https://github.com/user-attachments/assets/4161b04f-8ca7-47dd-b41d-e6503ea6a63f" />


---

# 5. `cp` — Dosya Kopyalama

Dosyanın bir yedeği oluşturulmuştur.

```bash
cp linux-commands.txt linux-commands-backup.txt
```

Dosyaların mevcut olduğu `ls` komutu ile kontrol edilmiştir.

```bash
ls
```

Bu işlem sonucunda:

```text
linux-commands.txt
linux-commands-backup.txt
```

dosyaları oluşmuştur.

### 📸 Dosya kopyalama

<img width="419" height="104" alt="image" src="https://github.com/user-attachments/assets/b069cf0e-6eeb-4313-9a47-c1a4bee894e8" />


---

# 6. `mv` — Dosya Taşıma

Oluşturulan yedek dosya `reports` klasörüne taşınmıştır.

```bash
mv linux-commands-backup.txt ../reports/
```

Ardından `reports` klasörüne geçilerek dosyanın taşındığı kontrol edilmiştir.

```bash
cd ../reports
ls
```

### 📸 Dosya taşıma

<img width="371" height="158" alt="image" src="https://github.com/user-attachments/assets/8163739b-b7cc-4799-a1de-54923f1c260d" />


---

# 7. `rm` — Dosya Silme

Test amacıyla oluşturulan yedek dosya silinmiştir.

```bash
rm linux-commands-backup.txt
```

Silme işlemi `ls` komutu ile kontrol edilmiştir.

```bash
ls
```

`rm` komutu dosyaları doğrudan silebildiği için kullanılırken dikkatli olunmalıdır.

### 📸 Dosya silme

<img width="353" height="88" alt="image" src="https://github.com/user-attachments/assets/88d70297-43bc-4a04-9cfd-dd5b93db95e6" />


---

# 8. `find` — Dosya ve Klasör Arama

Çalışma dizinine dönülmüştür:

```bash
cd ~/security-lab
```

Çalışma alanındaki tüm dosya ve klasörleri görmek için:

```bash
find .
```

kullanılmıştır.

Daha sonra yalnızca dosyaları listelemek için:

```bash
find . -type f
```

komutu kullanılmıştır.

Belirli bir dosyayı adına göre bulmak için:

```bash
find . -name "linux-commands.txt"
```

komutu uygulanmıştır.

### `find` Komutlarının Farkı

```text
find .
```

Bulunulan dizinin altındaki dosya ve klasörleri listeler.

```text
find . -type f
```

Yalnızca dosyaları listeler.

```text
find . -name "linux-commands.txt"
```

Belirtilen isme sahip dosyayı arar.

### 📸 `find` çıktısı

<img width="305" height="251" alt="image" src="https://github.com/user-attachments/assets/f00f9d99-694c-43a9-b252-6bf6372a0e3f" />


---

# 9. Log Analizi İçin Örnek Dosya Oluşturulması

Siber güvenlik açısından `grep`, `head` ve `tail` komutlarının kullanımını görmek amacıyla `logs` klasörü içerisinde örnek bir log dosyası oluşturulmuştur.

```bash
cd ~/security-lab/logs
touch system.log
```

Örnek log kayıtları oluşturulmuştur:

```bash
echo "INFO User zeynep logged in" > system.log
echo "INFO Network connection established" >> system.log
echo "WARNING Failed login attempt" >> system.log
echo "INFO User zeynep opened terminal" >> system.log
echo "ERROR Authentication service failed" >> system.log
echo "WARNING Failed login attempt" >> system.log
```

Dosyanın tamamı:

```bash
cat system.log
```

komutu ile görüntülenmiştir.

### 📸 Örnek log dosyası

<img width="462" height="361" alt="image" src="https://github.com/user-attachments/assets/849cdfde-5e1f-41e5-941c-e7f344ee1a81" />


---

# 10. `grep` — Log İçerisinde Arama

`grep` komutu kullanılarak log dosyasındaki `WARNING` kayıtları filtrelenmiştir.

```bash
grep "WARNING" system.log
```

Bu işlem sonucunda yalnızca `WARNING` içeren satırlar görüntülenmiştir.

Örneğin:

```text
WARNING Failed login attempt
WARNING Failed login attempt
```

Benzer şekilde hata kayıtlarını bulmak için:

```bash
grep "ERROR" system.log
```

komutu kullanılmıştır.

Bu komut:

```text
ERROR Authentication service failed
```

kaydını göstermektedir.

### 📸 `grep` ile log analizi

<img width="323" height="221" alt="image" src="https://github.com/user-attachments/assets/7ae22de8-e159-4f07-8ddb-a2a5d66392fe" />


---

# 11. `grep -c` — Kayıt Sayısını Bulma

Belirli bir ifadenin kaç satırda bulunduğunu öğrenmek için `grep` komutunun `-c` seçeneği kullanılabilir.

WARNING sayısını bulmak için:

```bash
grep -c "WARNING" system.log
```

ERROR sayısını bulmak için:

```bash
grep -c "ERROR" system.log
```

Bu örnekte iki adet `WARNING` ve bir adet `ERROR` kaydı bulunmaktadır.

### 📸 Kayıt sayıları

<img width="325" height="186" alt="image" src="https://github.com/user-attachments/assets/581640b6-15fb-438d-a2ac-38200945e5ca" />


---

# 12. `head` — Logların Başını İnceleme

Dosyanın ilk satırlarını görmek için:

```bash
head system.log
```

komutu kullanılmıştır.

Belirli sayıda satır görüntülemek için:

```bash
head -n 3 system.log
```

komutu kullanılabilir.

Bu komut özellikle büyük log dosyalarının başlangıç kısmını hızlı şekilde incelemek için kullanılabilir.

### 📸 `head` çıktısı

<img width="335" height="232" alt="image" src="https://github.com/user-attachments/assets/89c02cc3-5a84-44c5-8d78-104cf593b71c" />


---

# 13. `tail` — Logların Sonunu İnceleme

Dosyanın son satırlarını görüntülemek için:

```bash
tail system.log
```

komutu kullanılmıştır.

Örneğin son iki satırı görüntülemek için:

```bash
tail -n 2 system.log
```

komutu kullanılabilir.

Log analizinde `tail`, sistemde gerçekleşen en son olayları incelemek açısından kullanışlıdır.

### 📸 `tail` çıktısı

<img width="325" height="81" alt="image" src="https://github.com/user-attachments/assets/c8e13afe-31c2-469a-8ce9-5c90084e70c3" />


---

# 14. Siber Güvenlik Açısından Değerlendirme

Bu çalışmada kullanılan komutlar yalnızca temel Linux işlemleri için değil, siber güvenlik çalışmalarında da önemli bir temel oluşturmaktadır.

Özellikle:

### `find`

Dosya ve klasör araştırmalarında kullanılabilir.

Örneğin belirli bir dosyanın sistem içerisinde nerede bulunduğu araştırılabilir.

### `grep`

Log dosyaları içerisindeki belirli ifadelerin bulunmasını sağlar.

Örneğin:

```bash
grep "ERROR" system.log
```

ile hata kayıtları filtrelenebilir.

Benzer şekilde başarısız giriş denemeleri gibi kayıtlar da aranabilir:

```bash
grep "Failed" system.log
```

### `head` ve `tail`

Büyük dosyaların tamamını okumak yerine başlangıç veya son bölümlerini hızlı şekilde incelemeye yardımcı olur.

Bu komutlar özellikle log analizi sırasında zaman kazandırabilir.

---

# 15. Öğrenilenler

Bu çalışma sonunda:

* Linux terminalinde temel dosya ve klasör işlemleri öğrenildi.
* Dizinler arasında `cd` ile geçiş yapıldı.
* `mkdir` ile klasör oluşturuldu.
* `touch` ile dosya oluşturuldu.
* `cp` ile dosya kopyalandı.
* `mv` ile dosya taşındı.
* `rm` ile dosya silindi.
* `cat` ile dosya içeriği görüntülendi.
* `find` ile dosya ve klasör araması yapıldı.
* `grep` ile log içerisindeki belirli kayıtlar filtrelendi.
* `head` ve `tail` ile logların başlangıç ve son bölümleri incelendi.
* Linux komutlarının siber güvenlik ve log analizi açısından kullanım alanları görüldü.

---

# 📝 Genel Değerlendirme

Bu çalışma ile Linux terminalinin yalnızca dosya yönetimi için kullanılan bir ortam olmadığı, aynı zamanda sistem ve güvenlik analizlerinde temel bir araç olduğu görülmüştür.

Özellikle `grep` ve `find` komutlarının ileride yapılacak log analizi, sistem inceleme ve adli bilişim çalışmalarında önemli olabileceği anlaşılmıştır.

Bir sonraki aşamada Linux kullanıcıları, gruplar ve yetkilendirme mekanizmaları incelenecektir.
