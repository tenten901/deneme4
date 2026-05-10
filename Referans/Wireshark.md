# 🦈 Wireshark Ağ Analiz Rehberi

> Olay müdahalesi (IR), tehdit avı ve adli analiz için sistematik bir referans.
> Bir PCAP açtığında "nereden başlayacağım?" diye düşünmemen için adım adım iş akışı.

---

## 1 — Analiz İş Akışı (Nereye Bakacağını Bil)

Bir PCAP dosyasını açtığında rastgele filtrelere basmak yerine şu sırayı takip et:

```
ADIM 1 ──► Genel Resmi Gör
           Statistics → Capture File Properties  (süre, paket sayısı, boyut)
           Statistics → Protocol Hierarchy       (hangi protokoller ne kadar var?)
           Statistics → Conversations → IPv4     (kim kimle konuşuyor?)
           Statistics → Endpoints → IPv4          (en çok trafik üreten IP'ler)

ADIM 2 ──► Anomaliyi Tespit Et
           Statistics → I/O Graphs               (trafik spike'ları, beaconing deseni)
           Statistics → DNS                       (NXDOMAIN oranı, sorgu uzunlukları)
           Expert Information (sol alt sarı üçgen) (hata ve uyarıları listeler)

ADIM 3 ──► Filtrele ve Derine In
           Display filter ile şüpheli trafiği izole et
           Sağ tık → Follow → TCP/UDP/HTTP Stream  (konuşmayı oku)
           File → Export Objects → HTTP/SMB         (aktarılan dosyaları çıkar)

ADIM 4 ──► Kanıtla ve Raporla
           File → Export Specified Packets          (ilgili paketleri ayrı kaydet)
           İlgili IOC'leri (IP, domain, hash) not et
```

---

## 2 — Temel Display Filtreleri

### 2.1 IP ve MAC Filtreleri

| Amaç | Filtre |
|---|---|
| Belirli IP'ye gelen/giden tüm trafik | `ip.addr == 192.168.1.5` |
| Sadece kaynak IP | `ip.src == 10.0.0.1` |
| Sadece hedef IP | `ip.dst == 10.0.0.1` |
| Belirli subnet | `ip.addr == 192.168.1.0/24` |
| İç ağ dışına çıkan trafik | `!(ip.dst == 10.0.0.0/8 or ip.dst == 172.16.0.0/12 or ip.dst == 192.168.0.0/16)` |
| Kaynak MAC adresi | `eth.src == 00:11:22:33:44:55` |
| Hedef MAC adresi | `eth.dst == aa:bb:cc:dd:ee:ff` |
| İki IP arası konuşma | `ip.addr == 10.0.0.5 && ip.addr == 203.0.113.10` |

### 2.2 Port ve Protokol Filtreleri

| Amaç | Filtre |
|---|---|
| Belirli TCP portu | `tcp.port == 443` |
| Belirli UDP portu | `udp.port == 53` |
| Port aralığı | `tcp.port >= 1024 && tcp.port <= 65535` |
| Sadece DNS | `dns` |
| Sadece HTTP | `http` |
| Sadece TLS/SSL | `tls` |
| Sadece ARP | `arp` |
| Sadece ICMP | `icmp` |
| Sadece DHCP | `dhcp` |
| Sadece SMB | `smb2` |
| Sadece FTP | `ftp` |
| Sadece SSH | `ssh` |
| NTLMSSP (domain bilgisi) | `ntlmssp` |
| Kerberos | `kerberos` |

### 2.3 HTTP Analiz Filtreleri

| Amaç | Filtre |
|---|---|
| Tüm HTTP istekleri | `http.request` |
| Tüm HTTP yanıtları | `http.response` |
| Sadece POST istekleri | `http.request.method == "POST"` |
| Sadece GET istekleri | `http.request.method == "GET"` |
| Belirli bir host | `http.host contains "example.com"` |
| Belirli URL yolu | `http.request.uri contains "/login"` |
| Başarılı yanıtlar | `http.response.code == 200` |
| Yönlendirmeler | `http.response.code >= 300 && http.response.code < 400` |
| Hata yanıtları | `http.response.code >= 400` |
| Sunucu hataları | `http.response.code >= 500` |
| Dosya türüne göre | `http.content_type contains "application/zip"` |
| Resim dosyaları | `http.content_type contains "image"` |
| User-Agent ile | `http.user_agent contains "Firefox"` |

### 2.4 TCP Bayrak ve Durum Filtreleri

| Amaç | Filtre |
|---|---|
| SYN paketleri (bağlantı başlangıcı) | `tcp.flags.syn == 1 && tcp.flags.ack == 0` |
| SYN-ACK paketleri | `tcp.flags.syn == 1 && tcp.flags.ack == 1` |
| RST paketleri (bağlantı reddi) | `tcp.flags.reset == 1` |
| FIN paketleri (bağlantı sonu) | `tcp.flags.fin == 1` |
| Belirli TCP stream | `tcp.stream eq 5` |
| TCP hata bayrakları | `tcp.analysis.flags && !tcp.analysis.window_update` |
| Yeniden iletim | `tcp.analysis.retransmission` |
| Sıfır pencere | `tcp.analysis.zero_window` |
| Duplicate ACK | `tcp.analysis.duplicate_ack` |

### 2.5 Genel Operatörler

| Operatör | Açıklama | Örnek |
|---|---|---|
| `==` | Eşittir | `ip.addr == 10.0.0.1` |
| `!=` | Eşit değil | `ip.addr != 10.0.0.1` |
| `>` `<` `>=` `<=` | Karşılaştırma | `frame.len > 1500` |
| `contains` | İçerir (metin) | `http.host contains "evil"` |
| `matches` | Regex eşleşme | `dns.qry.name matches "^[a-z0-9]{20,}"` |
| `&&` veya `and` | VE | `ip.src == 10.0.0.1 && tcp.port == 80` |
| `\|\|` veya `or` | VEYA | `dns \|\| http` |
| `!` veya `not` | DEĞİL | `!arp && !dns` |
| `in` | Liste içinde | `tcp.port in {80, 443, 8080}` |
| `frame contains` | Tüm pakette metin ara | `frame contains "password"` |

---

## 3 — Tehdit Avı Filtreleri (Threat Hunting)

### 3.1 🔴 C2 (Komuta-Kontrol) Tespiti

**Beaconing Tespiti:**
C2 yazılımları sunucuya düzenli aralıklarla sinyal gönderir. Bunu tespit etmek için:

```
Statistics → Conversations → IPv4 → sütuna göre sırala
  ► En çok paket gönderen iç ağ IP'si hangisi?
  ► Tek bir dış IP ile sürekli konuşan iç IP var mı?

Statistics → I/O Graphs
  ► Belirli bir IP için grafik çiz, düzenli aralıklı spike'lar beacon'dır
  ► Filtre: ip.addr == <şüpheli_ip>
```

**C2 İşaret Filtreleri:**
```
# Küçük pencere boyutlu SYN paketleri (C2 beacon kalıbı)
tcp.flags.syn == 1 && tcp.flags.ack == 0 && tcp.window_size <= 1024

# Standart dışı portlarda HTTP
http && !(tcp.port == 80 || tcp.port == 443 || tcp.port == 8080)

# TLS handshake'leri dış IP'lere (iç ağ hariç)
tls.handshake.type == 1 && !(ip.dst == 10.0.0.0/8 || ip.dst == 172.16.0.0/12 || ip.dst == 192.168.0.0/16)

# Uzun süreli bağlantılar (persist C2)
tcp.time_delta > 3600
```

### 3.2 🟠 DNS Anomali ve Tünelleme Tespiti

```
# Tüm DNS trafiğini gör
dns

# Başarısız DNS sorguları (DGA belirtisi)
dns.flags.rcode == 3

# NXDOMAIN yığını (Domain Generation Algorithm işareti)
dns.flags.rcode != 0

# Uzun DNS sorguları (tünelleme belirtisi, normal DNS < 60 byte)
dns.qry.name.len > 50

# TXT kayıt sorguları (veri taşıma amaçlı kullanılır)
dns.qry.type == 16

# NULL kayıt sorguları (tünelleme aracı iodine kullanır)
dns.qry.type == 10

# Şüpheli TLD'ler
dns.qry.name contains ".xyz"
dns.qry.name contains ".top"
dns.qry.name contains ".tk"
dns.qry.name contains ".cc"
dns.qry.name contains ".pw"

# Şüpheli anahtar kelimeler
dns.qry.name contains "update"
dns.qry.name contains "cdn"
dns.qry.name contains "api"

# Base32/Base64 görünümlü subdomain'ler (tünelleme)
dns.qry.name matches "[A-Za-z0-9]{30,}"

# Büyük DNS paketleri
dns.len > 200

# Belirli bir dış DNS sunucusuna giden sorgular (DNS hijack kontrolü)
dns && ip.dst != <iç_dns_sunucusu>
```

**DNS Analiz İpucu:** `Statistics → DNS` menüsünden sorgu istatistiklerine bak. Ortalama sorgu uzunluğu 20-30 karakter olmalı; 50+ karakter tünelleme belirtisi. Ayrıca aynı domain'e çok sayıda alt alan sorgusu varsa exfiltration olabilir.

### 3.3 🟡 Veri Sızdırma (Data Exfiltration) Tespiti

```
# Büyük HTTP POST istekleri (veri yükleme)
http.request.method == "POST" && http.content_length > 5000

# Büyük DNS paketleri (DNS üzerinden veri kaçırma)
dns.len > 200

# Büyük ICMP paketleri (ICMP tünelleme)
icmp && data.len > 64

# Dış IP'lere giden FTP veri bağlantıları
ftp-data && !(ip.dst == 10.0.0.0/8 || ip.dst == 172.16.0.0/12 || ip.dst == 192.168.0.0/16)

# Standart dışı portlarda şifreli trafik
tls && !(tcp.port in {443, 993, 995, 587, 465})

# SMB üzerinden dosya transferi (lateral movement sonrası)
smb2.cmd == 5
```

### 3.4 🟣 Şüpheli User-Agent Tespiti

```
# Script tabanlı erişimler (saldırgan araçları)
http.user_agent contains "curl"
http.user_agent contains "wget"
http.user_agent contains "python"
http.user_agent contains "powershell"
http.user_agent contains "Go-http-client"
http.user_agent contains "Java/"
http.user_agent contains "Wget"

# Bot/crawler belirtileri
http.user_agent contains "scanner"
http.user_agent contains "nikto"
http.user_agent contains "sqlmap"
http.user_agent contains "nmap"
http.user_agent contains "masscan"
http.user_agent contains "dirbuster"

# Boş User-Agent (çoğu malware)
http.user_agent == ""
```

### 3.5 🔵 Port Tarama ve Keşif Tespiti

```
# SYN taraması (çok sayıda SYN, az SYN-ACK)
tcp.flags.syn == 1 && tcp.flags.ack == 0

# Hızlı RST yanıtları (kapalı porta isabet)
tcp.flags.reset == 1 && tcp.flags.ack == 1

# Çok sayıda farklı porta bağlantı denemesi (tek kaynaktan)
# → Statistics → Conversations → TCP → Sort by "Port B" ile kontrol et

# ARP taraması (ağ keşfi)
arp.opcode == 1

# ICMP ping sweep
icmp.type == 8
```

### 3.6 🟤 SQL Injection / Web Saldırı Tespiti

```
# URL'de SQL anahtar kelimeleri
http.request.uri contains "SELECT"
http.request.uri contains "UNION"
http.request.uri contains "DROP"
http.request.uri contains "' OR "
http.request.uri contains "1=1"

# XSS denemeleri
http.request.uri contains "<script>"
http.request.uri contains "alert("

# Dizin gezinme denemeleri
http.request.uri contains "../"
http.request.uri contains "/etc/passwd"
```

---

## 4 — TLS/SSL Şifre Çözme

HTTPS trafiğini okuyabilmek için tarayıcının oturum anahtarlarını kaydetmesi gerekir.

### 4.1 SSLKEYLOGFILE Kurulumu

**Windows:**
```
# Ortam değişkeni oluştur:
# Sistem Özellikleri → Gelişmiş → Ortam Değişkenleri → Yeni
# Değişken adı:   SSLKEYLOGFILE
# Değişken değeri: C:\Users\KullanıcıAdı\sslkeys.log

# veya CMD ile geçici:
set SSLKEYLOGFILE=C:\temp\sslkeys.log
start chrome.exe
```

**Linux / macOS:**
```bash
export SSLKEYLOGFILE=~/sslkeys.log
# Tarayıcıyı aynı terminalden başlat:
google-chrome &
# veya
firefox &
```

### 4.2 Wireshark Ayarı

```
Edit → Preferences → Protocols → TLS
  → (Pre)-Master-Secret log filename: <sslkeys.log dosyasının yolu>
  → OK
```

### 4.3 Doğrulama Filtreleri

```
# Şifre çözme başarılıysa HTTP/2 çerçeveleri görünür
http2

# Veya klasik HTTP
http

# TLS handshake'leri
tls.handshake

# Belirli bir host'un çözülmüş trafiği
http.host == "api.example.com"
```

### 4.4 tshark ile Komut Satırından Çözme

```bash
tshark -r capture.pcap \
  -o "tls.keylog_file:/home/user/sslkeys.log" \
  -Y "http" \
  -T fields -e http.request.uri
```

> ⚠️ Anahtar dosyası oturum sırlarını içerir; güvenli sakla, işin bitince sil. Üretim ortamında asla etkinleştirme.

---

## 5 — İstatistik Araçları (Statistics Menüsü)

| Menü Yolu | Ne Gösterir | Ne Zaman Kullanılır |
|---|---|---|
| Capture File Properties | Dosya süresi, toplam paket/bayt | İlk bakış, analizin kapsamını anlamak |
| Protocol Hierarchy | Protokol dağılımı (% olarak) | Alışılmadık protokol tespiti |
| Conversations → IPv4 | IP çiftleri arası trafik hacmi | En çok konuşan çifti bulmak, C2 şüphelisi |
| Endpoints → IPv4 | Her IP'nin gönderdiği/aldığı bayt | En aktif host'u bulmak |
| I/O Graphs | Zaman-paket grafiği, filtre uygulayarak | Beacon tespiti, trafik spike analizi |
| DNS | Sorgu istatistikleri, yanıt süreleri | DNS tünelleme, DGA tespiti |
| HTTP → Requests | Ziyaret edilen URL'ler | Zararlı indirme tespiti |
| HTTP → Request Sequences | HTTP istek sırası | Saldırı zincirini görselleştirme |
| Flow Graph | Paket akış diyagramı | TCP handshake ve oturum analizi |
| Resolved Addresses | IP → hostname eşlemeleri | Hızlı referans |

### I/O Graph ile Beacon Tespiti

```
Statistics → I/O Graphs
  → "+" ile yeni grafik ekle
  → Display Filter: ip.addr == <şüpheli_ip>
  → Interval: 1 saniye veya 10 saniye

Grafik düzenli periyodik spike'lar gösteriyorsa → C2 beaconing olabilir
```

---

## 6 — Dosya Çıkarma (Export Objects)

| Menü | Çıkan Dosya Türleri |
|---|---|
| File → Export Objects → HTTP | Web üzerinden indirilen dosyalar (exe, zip, js, html, resim) |
| File → Export Objects → SMB | Ağ paylaşımı üzerinden aktarılan dosyalar |
| File → Export Objects → TFTP | TFTP üzerinden aktarılan dosyalar |
| File → Export Objects → IMF | E-posta ekleri |
| File → Export Objects → DICOM | Tıbbi görüntüler |

> Çıkarılan dosyaları VirusTotal veya sandbox'ta analiz et. Doğrudan ana sistende açma.

---

## 7 — Stream Takibi (Follow Stream)

Bir paketin üzerine sağ tıkla:

| Seçenek | Kullanım |
|---|---|
| Follow → TCP Stream | TCP konuşmasını kronolojik oku (HTTP içerik, login bilgileri) |
| Follow → UDP Stream | UDP konuşmasını oku (DNS, TFTP) |
| Follow → TLS Stream | Şifresi çözülmüş TLS oturumunu oku |
| Follow → HTTP Stream | HTTP istek-yanıt çiftini oku |
| Follow → HTTP/2 Stream | HTTP/2 çerçevelerini oku |

> Stream penceresinde kırmızı = istemciden sunucuya, mavi = sunucudan istemciye.

---

## 8 — Capture Filtreleri (Yakalama Öncesi)

Display filtrelerinden farklıdır; yakalama başlamadan önce uygulanır ve gereksiz trafiği diske yazmaz.

| Amaç | Capture Filter |
|---|---|
| Belirli host | `host 192.168.1.5` |
| Belirli ağ | `net 192.168.1.0/24` |
| Belirli port | `port 80` |
| Port aralığı | `portrange 1-1024` |
| Sadece TCP | `tcp` |
| Sadece UDP | `udp` |
| DNS hariç | `not port 53` |
| ARP hariç | `not arp` |
| İki host arası | `host 10.0.0.1 and host 10.0.0.2` |
| Broadcast hariç | `not broadcast` |

> Display filter söz dizimi (ip.addr) ile capture filter söz dizimi (host) farklıdır, karıştırma.

---

## 9 — tshark Komut Satırı Referansı

```bash
# Canlı yakalama
tshark -i eth0 -w output.pcap

# Belirli filtre ile yakalama
tshark -i eth0 -f "port 80" -w http_traffic.pcap

# PCAP okuma ve display filter uygulama
tshark -r capture.pcap -Y "http.request"

# Belirli alanları çıkarma
tshark -r capture.pcap -Y "dns" -T fields -e dns.qry.name -e ip.src

# HTTP host'larını listeleme
tshark -r capture.pcap -Y "http.request" -T fields -e http.host | sort -u

# DNS sorgularını listeleme
tshark -r capture.pcap -Y "dns.qry.name" -T fields -e dns.qry.name | sort | uniq -c | sort -rn

# Conversations (en çok konuşan IP çiftleri)
tshark -r capture.pcap -q -z conv,ip

# Protocol hierarchy
tshark -r capture.pcap -q -z io,phs

# Endpoint istatistikleri
tshark -r capture.pcap -q -z endpoints,ip

# Belirli paketleri ayrı dosyaya kaydetme
tshark -r capture.pcap -Y "ip.addr == 10.0.0.5" -w filtered.pcap

# TLS çözümlü okuma
tshark -r capture.pcap -o "tls.keylog_file:sslkeys.log" -Y "http"

# User-Agent listeleme
tshark -r capture.pcap -Y "http.user_agent" -T fields -e http.user_agent | sort -u
```

---

## 10 — Yararlı Ayarlar

### İsim Çözümleme
```
Edit → Preferences → Name Resolution
  ☑ Resolve network (IP) addresses   → IP'leri hostname olarak gösterir
  ☑ Resolve transport names          → Port numaralarını servis adı olarak gösterir
```

### Sütun Özelleştirme (Önerilen Ek Sütunlar)
```
Edit → Preferences → Columns → "+" ile ekle:

Başlık              Tür              Alan
─────────────────────────────────────────────
Source Port         Custom           tcp.srcport
Dest Port           Custom           tcp.dstport
HTTP Host           Custom           http.host
Server Name (SNI)   Custom           tls.handshake.extensions_server_name
DNS Query           Custom           dns.qry.name
```

### Renklendirme Kuralları
```
View → Coloring Rules
  → Özel kurallar ekleyerek şüpheli trafiği renklendir
  Örnek: "Suspicious DNS" → dns.qry.name.len > 50 → Arka plan: kırmızı
```

---

## 11 — Kısayol Tuşları

| Tuş | İşlev |
|---|---|
| `Ctrl+E` | Yakalamayı başlat/durdur |
| `Ctrl+F` | Paket içinde arama |
| `Ctrl+G` | Belirli paket numarasına git |
| `Ctrl+M` | Paketi işaretle |
| `Ctrl+Shift+M` | Sonraki işaretli pakete git |
| `Ctrl+→` / `Ctrl+←` | Sonraki/önceki pakete |
| `Ctrl+.` / `Ctrl+,` | Sonraki/önceki display filter sonucu |
| `Ctrl+Shift+E` | Expert Information paneli |

---

## 12 — Analiz Senaryoları (Hızlı Başvuru)

### Senaryo A: "Bu makine enfekte mi?"

```
1. ip.addr == <şüpheli_IP> ile filtrele
2. Statistics → Protocol Hierarchy → alışılmadık protokoller var mı?
3. Statistics → Conversations → bu IP en çok kimle konuşuyor?
4. dns.qry.name ile bu IP'den çıkan DNS sorgularına bak
5. http.request ile HTTP isteklerini incele
   → Garip URL'ler, şüpheli User-Agent, encoded payload
6. Statistics → I/O Graphs → düzenli beaconing deseni var mı?
7. File → Export Objects → HTTP → indirilen dosyaları çıkar, hash'le, VirusTotal'e gönder
```

### Senaryo B: "Veri sızdırılmış mı?"

```
1. Statistics → Endpoints → en çok veri gönderen iç IP hangisi?
2. Statistics → Conversations → dışarıya en çok bayt gönderen konuşma?
3. http.request.method == "POST" && http.content_length > 5000
4. dns.qry.name.len > 50  (DNS exfiltration kontrolü)
5. ftp-data || smb2.cmd == 5  (dosya transfer protokolleri)
6. Follow TCP Stream → gönderilen verinin içeriğini oku
```

### Senaryo C: "Ağda port taraması yapan var mı?"

```
1. tcp.flags.syn == 1 && tcp.flags.ack == 0 ile SYN'leri filtrele
2. Statistics → Endpoints → en çok SYN gönderen IP?
3. Statistics → Conversations → aynı kaynak IP'den kaç farklı hedef port'a SYN var?
4. tcp.flags.reset == 1 ile RST yanıtlarını kontrol et (kapalı portlar)
5. icmp.type == 3 && icmp.code == 3 → port unreachable mesajları
```

### Senaryo D: "Kimlik bilgisi çalınmış mı?"

```
1. frame contains "password"
2. frame contains "login"
3. frame contains "user"
4. http.request.method == "POST" → Follow HTTP Stream
5. ftp.request.command == "USER" || ftp.request.command == "PASS"
6. smtp.req.command == "AUTH"
7. http.authbasic  (Basic auth Header bilgisi)
```

---

## 13 — Sık Kullanılan Birleşik Filtreler

```
# Gürültüyü temizle (analiz başlangıcı için)
!(arp || dns || stp || cdp || lldp || igmp || icmpv6)

# Sadece web trafiği
http || tls

# İç ağdan dışarı çıkan HTTP olmayan trafik (gizli kanal tespiti)
ip.src == 192.168.0.0/16 && !(ip.dst == 192.168.0.0/16) && !dns && !http && !tls

# Başarısız bağlantı denemeleri
tcp.flags.syn == 1 && tcp.flags.ack == 0 && tcp.analysis.retransmission

# Büyük paketler (exfiltration veya dosya transferi)
frame.len > 1500

# Küçük ve sık paketler (C2 beacon)
frame.len < 100 && tcp

# Şifreli olmayan kimlik bilgisi protokolleri
ftp || telnet || http.authbasic || smtp
```

---

## 14 — Wireshark Expert Information Yorumlama

Sol alt köşedeki renkli daire (sarı/kırmızı) Expert Information'a erişim sağlar: `Analyze → Expert Information`

| Seviye | Renk | Anlam |
|---|---|---|
| Chat | Mavi | Bilgilendirme (normal akış) |
| Note | Açık mavi | Dikkat çekici ama muhtemelen normal |
| Warning | Sarı | Potansiyel sorun (retransmission, out-of-order) |
| Error | Kırmızı | Kesin hata (malformed paket, checksum hatası) |

**Sık karşılaşılan uyarılar:**

| Uyarı | Ne Anlama Gelir |
|---|---|
| TCP Retransmission | Paket kayboldu, yeniden gönderildi → ağ sorunu veya tıkanıklık |
| TCP Out-Of-Order | Paketler sırasız geldi |
| TCP Duplicate ACK | Kayıp paket için tekrar ACK → hızlı yeniden iletim tetikleyebilir |
| TCP Zero Window | Alıcı buffer doldu, veri kabul edemez |
| TCP RST | Bağlantı zorla kesildi |
| TCP Previous Segment Not Captured | Yakalama sırasında paket kaçırıldı |

---

> **Son Not:** Analiz yaparken her zaman bir temel çizgi (baseline) bilgisine sahip ol. "Normal" trafiğin nasıl göründüğünü bilmeden anomali tespit edemezsin. Mümkünse temiz bir ortamın PCAP'ini referans olarak sakla.
