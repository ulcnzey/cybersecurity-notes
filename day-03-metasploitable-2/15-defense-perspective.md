# 15. Savunma Perspektifi

## 15.1. Genel Bakış

Güvenlik değerlendirmesinde tespit edilen zafiyetlerin yalnızca saldırgan açısından değil, savunma açısından da değerlendirilmesi gerekir.

Amaç yalnızca bir güvenlik açığını tespit etmek değil; saldırı yüzeyini azaltmak, saldırıların gerçekleşme ihtimalini düşürmek, şüpheli aktiviteleri tespit etmek ve olası bir olay sonrasında sistemi kurtarabilmektir.

---

## 15.2. Gereksiz Servislerin Kapatılması

Bir sistemde kullanılmayan servislerin açık bırakılması saldırı yüzeyini genişletebilir.

Bu nedenle yalnızca ihtiyaç duyulan servislerin çalıştırılması ve gereksiz servislerin kapatılması temel bir güvenlik yaklaşımıdır.

Örneğin bir sunucuda FTP hizmetine ihtiyaç duyulmuyorsa FTP servisinin çalıştırılmaması ve ilgili erişimlerin sınırlandırılması saldırı yüzeyini azaltabilir.

---

## 15.3. Güncelleme ve Patch Management

Eski yazılım sürümleri zaman içerisinde keşfedilmiş güvenlik açıklarına karşı savunmasız kalabilir.

Bu nedenle işletim sistemi, uygulamalar ve servisler için güvenlik güncellemeleri düzenli olarak uygulanmalıdır.

Patch management süreci genel olarak:

```text
Güvenlik açığının tespit edilmesi
        ↓
Güncelleme/yamanın değerlendirilmesi
        ↓
Test edilmesi
        ↓
Sisteme uygulanması
        ↓
Doğrulama ve takip
```

şeklinde düşünülebilir.

Metasploitable 2 üzerinde gözlemlediğimiz eski Apache, PHP, Samba ve diğer servis sürümleri bu konunun laboratuvar ortamındaki örnekleridir.

---

## 15.4. Firewall

Firewall, ağ trafiğini belirlenen güvenlik kurallarına göre kontrol eder.

Amaç, gerekli iletişimlere izin verirken gereksiz veya yetkisiz ağ erişimlerini sınırlandırmaktır.

Örneğin yalnızca web hizmeti sunan bir sunucuda gerekli olmayan servislerin dış ağlardan erişilebilir durumda bırakılmaması saldırı yüzeyini azaltabilir.

---

## 15.5. Network Segmentation

Network segmentation, ağın farklı güvenlik bölgelerine ayrılmasıdır.

Örneğin internetten erişilebilen bir web sunucusu ile kurum içindeki veritabanı veya kullanıcı sistemleri farklı ağ segmentlerinde tutulabilir.

Bu yaklaşım, bir sistem ele geçirildiğinde saldırganın diğer sistemlere doğrudan erişmesini zorlaştırabilir ve lateral movement riskini azaltabilir.

---

## 15.6. Güçlü Kimlik Doğrulama

Sistemlerde güçlü ve benzersiz kimlik bilgileri kullanılmalıdır.

Ayrıca:

* Varsayılan parolalar değiştirilmelidir.
* Kullanılmayan hesaplar devre dışı bırakılmalıdır.
* Güçlü parola politikaları uygulanmalıdır.
* Uygun sistemlerde çok faktörlü kimlik doğrulama kullanılmalıdır.

Laboratuvar ortamında kullanılan `msfadmin / msfadmin` gibi varsayılan kimlik bilgileri gerçek sistemlerde kullanılmamalıdır.

---

## 15.7. Least Privilege

Least Privilege veya En Az Ayrıcalık prensibine göre kullanıcılar ve servisler yalnızca görevlerini gerçekleştirebilmeleri için ihtiyaç duydukları yetkilere sahip olmalıdır.

Örneğin normal bir kullanıcının yönetici yetkilerine ihtiyaç duymadığı durumda administrator/root yetkilerinin verilmemesi gerekir.

Bu yaklaşım, bir hesabın ele geçirilmesi durumunda saldırganın sahip olabileceği yetkileri ve potansiyel etkisini sınırlandırmaya yardımcı olur.

---

## 15.8. Log Monitoring

Sistem ve ağ olaylarının kayıt altına alınması güvenlik olaylarının tespit edilmesine yardımcı olur.

İzlenebilecek olaylara örnek olarak:

* Başarılı ve başarısız girişler
* Yetki değişiklikleri
* Servis bağlantıları
* Sistem hataları
* Şüpheli ağ aktiviteleri
* Kullanıcı hareketleri

verilebilir.

Log monitoring, özellikle SOC ekiplerinin güvenlik olaylarını analiz etmesinde önemli bir role sahiptir.

---

## 15.9. IDS / IPS

### IDS — Intrusion Detection System

IDS, şüpheli veya saldırı niteliğindeki aktiviteleri tespit etmeye yardımcı olur.

### IPS — Intrusion Prevention System

IPS, tespit edilen belirli zararlı veya yetkisiz trafiği güvenlik politikalarına göre engellemeye yardımcı olabilir.

Basit şekilde:

```text
IDS → Tespit
IPS → Tespit + Engelleme
```

---

## 15.10. Multi-Factor Authentication

MFA (Multi-Factor Authentication), kullanıcının kimliğini birden fazla doğrulama faktörüyle doğrulamasıdır.

Örneğin:

```text
Parola
+
İkinci doğrulama faktörü
```

kullanılabilir.

Bu yaklaşım, yalnızca parolanın ele geçirilmesinin hesap erişimi için yeterli olmamasını sağlayarak hesap güvenliğini artırabilir.

---

## 15.11. Backup ve Recovery

Güvenlik yaklaşımı yalnızca saldırıları önlemeye odaklanmamalıdır.

Bir güvenlik olayı sonrasında sistemlerin ve verilerin kurtarılabilmesi için düzenli yedekleme ve kurtarma planlarının bulunması gerekir.

Yedeklerin uygun erişim kontrolleriyle korunması ve gerektiğinde geri yüklenebildiğinin test edilmesi de önemlidir.

---

## 15.12. Savunma Yaklaşımının Genel Yapısı

Güvenlik önlemleri genel olarak dört temel aşamada düşünülebilir:

```text
Önleme
   ↓
Tespit
   ↓
Müdahale
   ↓
Kurtarma
```

Örneğin:

```text
Firewall / Patch / MFA
        ↓
      Önleme

IDS / IPS / Log Monitoring
        ↓
       Tespit

Incident Response
        ↓
      Müdahale

Backup / Recovery
        ↓
       Kurtarma
```

Bu yaklaşım, güvenliğin yalnızca saldırıyı engellemekten ibaret olmadığını; saldırı öncesi, saldırı sırası ve saldırı sonrasındaki süreçlerin birlikte ele alınması gerektiğini gösterir.

## 15.13. Sonuç

Metasploitable 2 üzerinde yapılan analizlerde çok sayıda servis ve eski yazılım sürümü gözlemlenmiştir. Bu durum saldırı yüzeyinin geniş olabileceğini göstermektedir.

Savunma açısından temel yaklaşım; gereksiz servisleri kapatmak, sistemleri güncel tutmak, ağ erişimlerini sınırlandırmak, güçlü kimlik doğrulama uygulamak, en az ayrıcalık prensibini kullanmak, logları izlemek, saldırı tespit ve önleme mekanizmalarından yararlanmak ve güvenilir yedekleme süreçleri oluşturmaktır.

Bu önlemler birlikte uygulandığında güvenlik olaylarının gerçekleşme ihtimalinin ve gerçekleşmeleri durumundaki potansiyel etkinin azaltılmasına yardımcı olabilir.
