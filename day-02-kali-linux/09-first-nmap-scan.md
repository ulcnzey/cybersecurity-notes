# 🔎 09 — İlk Nmap Taraması


---

## 🎯 Bölümün Amacı

Bu bölümde Nmap kullanarak kendi Kali Linux bilgisayarımı iki farklı şekilde taradım:

```bash
nmap localhost
```

ve:

```bash
nmap 127.0.0.1
```

Amaç, `localhost` ile `127.0.0.1` arasındaki farkı anlamak ve neden iki taramanın benzer sonuç verdiğini gözlemlemektir.

---

# 🧪 1. Tarama — `localhost`

İlk olarak kendi bilgisayarımı `localhost` kullanarak taradım:

```bash
nmap localhost
```

## 📸 Tarama Sonucu


<img width="568" height="169" alt="image" src="https://github.com/user-attachments/assets/3bbedf54-370a-44de-8de0-bd72b3e3f9a5" />


Tarama sonucunda:

```text
Nmap scan report for localhost (127.0.0.1)
Host is up
```

sonucunu aldım.

Ayrıca:

```text
Not shown: 1000 closed tcp ports (reset)
```

ifadesi görüldü.

Bu sonuç, Nmap'in varsayılan taramasında kontrol ettiği 1000 TCP portunun kapalı olduğunu gösterdi.

---

# 🧪 2. Tarama — `127.0.0.1`

Daha sonra aynı bilgisayarı doğrudan IPv4 loopback adresi ile taradım:

```bash
nmap 127.0.0.1
```

## 📸 Tarama Sonucu


<img width="569" height="149" alt="image" src="https://github.com/user-attachments/assets/9da90157-ff5a-4f07-8ba3-711c7721ec9c" />


Bu taramada da:

```text
Nmap scan report for localhost (127.0.0.1)
Host is up
```

sonucunu aldım.

Ve yine:

```text
Not shown: 1000 closed tcp ports (reset)
```

ifadesini gördüm.

---

# 🔍 İki Taramayı Karşılaştırma

İki komut:

```text
nmap localhost
```

ve:

```text
nmap 127.0.0.1
```

farklı şekilde yazılmış olsa da aynı bilgisayarı hedefledi.

Karşılaştırma:

| Özellik              | `localhost`        | `127.0.0.1`        |
| -------------------- | ------------------ | ------------------ |
| Türü                 | Hostname           | IPv4 adresi        |
| Anlamı               | Kendi bilgisayarım | Kendi bilgisayarım |
| Loopback             | Evet               | Evet               |
| Hedef                | Kali Linux         | Kali Linux         |
| Host durumu          | UP                 | UP                 |
| Taranan TCP portları | 1000               | 1000               |
| Sonuç                | 1000 kapalı        | 1000 kapalı        |

---

# 🧠 `localhost` ve `127.0.0.1` Arasındaki Fark

Burada önemli bir ayrım öğrendim.

### `localhost`

`localhost`, bilgisayarın **kendisini ifade eden bir hostname** olarak kullanılır.

### `127.0.0.1`

`127.0.0.1`, IPv4 içerisindeki **loopback adresidir**.

Basit olarak:

```text
localhost
    ↓
127.0.0.1
    ↓
Kendi bilgisayarım
```

şeklinde düşünebilirim.

Bu nedenle:

```bash
nmap localhost
```

çalıştırıldığında sistem `localhost` adını `127.0.0.1` adresine çözümleyebilir.

---

# 🔄 Neden Sonuçlar Aynı?

İki komut da aynı sistemi hedeflediği için Nmap'in taradığı ağ noktaları aynı sistem üzerinde bulunur.

Bu nedenle iki taramada da:

```text
Host is up
```

ve:

```text
1000 closed tcp ports
```

sonuçlarını gördüm.

Yani:

```text
nmap localhost
       ↓
localhost
       ↓
127.0.0.1
       ↓
Kendi Kali bilgisayarım
```

ve:

```text
nmap 127.0.0.1
       ↓
127.0.0.1
       ↓
Kendi Kali bilgisayarım
```

aynı hedefe ulaşmaktadır.

---

# 🔁 Loopback Nedir?

Loopback, bilgisayarın kendi kendisiyle ağ iletişimi kurmasını sağlayan yapıdır.

IPv4 için:

```text
127.0.0.1
```

adresini kullanıyoruz.

Bu adres üzerinden gönderilen trafik dışarıdaki başka bir bilgisayara gitmez; kendi sistemimize geri döner.

Basit bir gösterim:

```text
┌───────────────────────┐
│       Kali Linux      │
│                       │
│    127.0.0.1          │
│        ↕              │
│    Kendi sistemi      │
└───────────────────────┘
```

Bu nedenle localhost üzerinde yapılan çalışmalar, ağ araçlarını öğrenmek için güvenli bir başlangıç noktasıdır.

---

# 🛡️ Siber Güvenlik Açısından Neden Önemli?

Bir güvenlik uzmanı için hedefin ne olduğunu doğru anlamak önemlidir.

Örneğin:

```text
localhost
```

ile:

```text
127.0.0.1
```

aynı sistemi ifade edebiliyorsa, yapılan taramanın hedefi doğru anlaşılmış olur.

Bu çalışmada dışarıdaki herhangi bir sistem yerine yalnızca kendi bilgisayarımı taradım.

Bu nedenle Nmap'in temel kullanımını güvenli bir laboratuvar ortamında gözlemlemiş oldum.

---

# 📝 Kendi Notum

> `localhost` ile `127.0.0.1` aynı şey gibi görünse de teknik olarak biri hostname, diğeri IPv4 loopback adresidir. Sistem `localhost` adını `127.0.0.1` adresine çözümleyebildiği için Nmap iki komutta da aynı bilgisayarı taramıştır. Bu nedenle sonuçların aynı olması beklenebilir.

---

# 💡 Önemli Öğrenme Noktası

Bu bölümde şu üç kavramı birbirinden ayırmayı öğrendim:

```text
Hostname
    ↓
localhost

IPv4 Loopback
    ↓
127.0.0.1

Hedef Sistem
    ↓
Kendi Kali Linux bilgisayarım
```

---

# ✅ Kontrol Listesi

* [x] `nmap localhost` komutunu çalıştırdım.
* [x] `nmap 127.0.0.1` komutunu çalıştırdım.
* [x] İki taramanın sonuçlarını karşılaştırdım.
* [x] `localhost` kavramını öğrendim.
* [x] `127.0.0.1` loopback adresini öğrendim.
* [x] İki taramanın neden benzer sonuç verdiğini anladım.
* [x] Nmap'i yalnızca kendi sistemimde kullandım.

---

## ➡️ Sonraki Bölüm

Bir sonraki bölümde **port kavramını daha ayrıntılı inceleyeceğim.**

Özellikle şu portları araştıracağım:

```text
21
22
23
25
53
80
110
139
443
445
3389
```

Her portun hangi servisle ilişkili olduğunu ve neden siber güvenlik açısından önemli olabileceğini inceleyeceğim.
