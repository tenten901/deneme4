# Initial Access

2024-2025 verilerine göre kimlik tabanlı saldırılar %850 artış gösterdi
phishing artık sadece dosya değil, kimlik çalmaya odaklanıyor.

---

## Phishing — Spearphishing Attachment — T1566.001

Hala en yaygın ilk erişim vektörü. Zararlı ek içeren email.
Klasik .exe yerine artık .pdf, .one, .lnk, .iso, .html ekler kullanılıyor
çünkü bunlar AV tespitinden daha kolay kaçıyor.

**Yaygın ek türleri:**
- .lnk → PowerShell veya cmd çalıştırır
- .one (OneNote) → gömülü script veya HTA
- .iso / .img → içinde .lnk veya .exe, MOTW'yi bypass eder
- .html → HTML Smuggling ile base64 payload
- .pdf → link içerir, direkt zararlı kod taşımaz

**Nereye bakarsın:**
- Browser download geçmişi → ek indirilmiş mi?
- Zone.Identifier (ADS) → dosya internetten mi geldi?
  `ZoneId=2` → internet, `ZoneId=3` → güvenilmeyen
- LNK dosyası → metadata'da kaynak makine, volume serial, orijinal path
- Security 4688 → email client'tan spawn olan process zinciri
- Prefetch → outlook.exe, thunderbird.exe ardından powershell.exe, cmd.exe
- $UsnJrnl → Temp veya Downloads klasörüne yeni dosya
- Sysmon ID 11 → Temp/Downloads'a dosya oluşturuldu
- Sysmon ID 1 → email client'ın child process'i

**IOC:**
- Email client (outlook.exe) → wscript.exe / powershell.exe / mshta.exe
- Temp veya Downloads klasöründen çalışan binary
- Zone.Identifier'sız dosya sisteme kopyalanmışsa bypass denenmiş olabilir
- .lnk dosyasının target path'i uzun ve obfuscated komut içeriyor

**Dikkat:**
.iso ve .img mount edilince içindeki dosyalar Zone.Identifier almaz.
Bu MOTW bypass yöntemi 2022'den bu yana yaygın.
LNK metadata'sı silinmiş olsa bile $MFT ve $UsnJrnl timestamp kalır.

---

## Phishing — Spearphishing Link — T1566.002

Email içindeki link zararlı siteye veya payload'a yönlendirir.
2024-2025'te ClickFix ve fakeCAPTCHA bu kategoride patlama yaptı.

**ClickFix / Paste-and-Run (T1204.004 — yeni teknik):**
Sahte hata mesajı veya CAPTCHA gösterip kullanıcıyı
Win+R → Ctrl+V → Enter yaptırıyor.
Kopyaladığı şey zararlı PowerShell komutu.
Çok etkili çünkü kullanıcı kendisi çalıştırıyor — AV bypass.

**Drive-by Download varyantı:**
SEO poisoning veya malvertising ile kurban meşru gibi görünen siteye çekiliyor.
Oyster kampanyası: puttysystems[.]com gibi sahte IT tool sitesi → zararlı DLL.

**Nereye bakarsın:**
- Browser history → hangi URL ziyaret edildi?
- Browser downloads → hangi dosya indirildi?
- PSReadline history → PowerShell komut geçmişi
  `AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`
- Security 4688 → powershell.exe, mshta.exe, wscript.exe komut satırı
- Sysmon ID 1 → browser'dan spawn olan process
- Zone.Identifier → indirilen dosyanın referrer URL'i

**IOC:**
- PowerShell komutunda base64 encoded string veya IEX (Invoke-Expression)
- Browser → powershell.exe veya cmd.exe doğrudan spawn
- ClickFix: `cmd.exe /c powershell -w hidden -enc [base64]`
- Sahte site URL'i gerçek yazılım sitesine çok benziyor

**Dikkat:**
ClickFix saldırısında kullanıcı kendisi çalıştırdığı için
email filtreleri ve sandbox'lar genellikle yakalayamıyor.
PSReadline history kritik kanıt noktası oluyor.

---

## Valid Accounts — T1078

Çalınmış veya tahmin edilmiş gerçek kimlik bilgileriyle giriş.
Red Canary 2025 raporunda kimlik saldırıları bir önceki yıla göre
%850 artış gösterdi. Infostealer malware'lerin yaygınlaşması
bu tekniği çok daha kolay hale getirdi.

**Alt teknikler:**
- **T1078.001** Default Accounts — varsayılan admin/şifre
- **T1078.002** Domain Accounts — ele geçirilmiş AD hesabı
- **T1078.003** Local Accounts — yerel hesap, özellikle RDP için
- **T1078.004** Cloud Accounts — O365, Azure AD, AWS hesabı

**Kimlikler nasıl elde ediliyor:**
- Infostealer malware (LummaC2, Vidar, Stealc, Acreed) browser kayıtlı şifreler
- Phishing ile kimlik avı
- Credential stuffing — sızdırılmış listelerden deneme
- Dark web'de satın alma

**Nereye bakarsın:**
- Security 4624 → logon type ve kaynak IP
  Alışılmadık saat, yabancı IP, anormal logon type şüpheli
- Security 4625 → başarısız giriş denemeleri, sonra başarılı
- Security 4648 → explicit credential kullanımı
- WLAN/VPN logları → alışılmadık konum veya IP
- Security 4768/4769 → Kerberos ticket talepleri (domain hesabı)
- Microsoft-Windows-TerminalServices → RDP ile giriş

**IOC:**
- Çalışma saati dışında giriş (gece 3, hafta sonu)
- Daha önce hiç görülmemiş ülke/IP'den giriş
- Başarısız girişler ardından başarılı giriş (credential stuffing)
- Aynı hesap farklı IP'lerden eş zamanlı giriş (impossible travel)
- Giriş yapıldıktan hemen sonra keşif komutları (whoami, ipconfig, net user)

**Dikkat:**
Bu tekniğin iz bırakması için audit policy açık olmalı.
Logon type 3 (network) ile giriş çok yaygın ve meşru trafikle karışabilir.
Kaynak IP ve zaman damgasıyla korelasyon kritik.

---

## External Remote Services — T1133

İnternete açık servislerin (VPN, RDP, Citrix, web portal) istismarı.
2024-2025'te özellikle CVE bazlı VPN saldırıları öne çıktı.

**Yaygın hedefler:**
- RDP (3389/TCP) — brute force veya çalınmış credential
- VPN — SonicWall CVE-2024-40766, Fortinet CVE-2024-55591/CVE-2024-21762
- Citrix / NetScaler
- Exchange / Outlook Web Access

**Gerçek vaka — 2024-2025:**
Akira ransomware → SonicWall VPN exploit → 6 dakikada lateral movement
Qilin → Fortinet FortiGate → 26 kamu kuruluşu

**Nereye bakarsın:**
- VPN/firewall logları → başarısız giriş denemeleri + başarılı giriş
- Security 4624 Type 3 veya 10 → remote logon, kaynak IP
- Security 1149 → RDP bağlantı kaynağı (pre-auth, username + IP)
- System/Application logları → servis crash veya exploit sonrası hata
- Prefetch → exploit aracı çalıştı mı?
- $UsnJrnl → exploit sonrası dosya bırakılmış mı?

**IOC:**
- Kısa sürede çok sayıda başarısız giriş → başarılı (brute force)
- Alışılmadık saatte veya IP'den servis erişimi
- RDP ile giriş yapıldıktan hemen sonra keşif komutları
- Patch durumu: ilgili CVE için patch uygulandı mı?

**Dikkat:**
Bu teknik genellikle log olmayan katmanlarda gerçekleşir.
Firewall ve VPN logları olmadan tespit çok zorlaşır.
Patch yönetimi en kritik önlem — CVE açıklandıktan sonra
saldırganlar saatler içinde istismar başlatıyor.

---

## Drive-by Compromise — T1189

Kullanıcı zararlı veya ele geçirilmiş bir siteyi ziyaret ediyor,
kod otomatik çalışıyor. 2025 verilerine göre vakaların %34'ünde
görülen en yaygın ilk erişim vektörü.

**Nasıl çalışıyor:**
- SEO poisoning → arama sonuçlarında sahte yazılım sitesi üste çıkıyor
- Malvertising → meşru reklam ağı üzerinden zararlı reklam
- Watering hole → hedef grubun ziyaret ettiği site ele geçiriliyor

**Oyster/Broomstick kampanyası (2025):**
SEO poisoning ile putty, 7zip gibi araçların sahte sitelerine yönlendirme.
İndirilen installer aslında zararlı DLL (twain_96.dll) içeriyor.
Rundll32.exe ile çalıştırılıyor.

**Nereye bakarsın:**
- Browser history → hangi site ziyaret edildi, timestamp
- Browser downloads → ne indirildi?
- Zone.Identifier → indirilen dosyanın kaynak URL'i
- Sysmon ID 1 → browser'dan spawn olan process
- Prefetch → rundll32.exe, mshta.exe, wscript.exe var mı?
- Amcache → yeni binary'nin hash'i

**IOC:**
- Meşru yazılım adına çok benzeyen domain (puttysystems.com vs putty.org)
- Browser → rundll32.exe / regsvr32.exe doğrudan spawn
- Temp klasöründen çalışan binary
- İmzasız veya beklenmedik yayıncı imzalı installer

**Dikkat:**
SEO poisoning saldırılarında kullanıcı farkında olmadan zararlı siteye gidiyor.
Domain benzerliğini kontrol etmek için browser history'deki URL'leri
tam olarak oku — tek harf farkı gözden kaçabilir.

---

## Public-Facing Application Exploitation — T1190

İnternete açık uygulamalardaki (web app, VPN, backup yazılımı)
güvenlik açıklarının istismarı. CVE bazlı, hızlı hareket gerektirir.

**2024-2025'te öne çıkan CVE'ler:**
- CVE-2024-40766 — SonicWall VPN
- CVE-2024-55591 / CVE-2024-21762 — Fortinet FortiGate
- CVE-2023-27532 — Veeam Backup & Replication
- CVE-2021-34527 — PrintNightmare (hala kullanılıyor)
- Exchange Server CVE'leri — ProxyShell ailesi

**Nereye bakarsın:**
- Web/uygulama logları → anormal istek, exploit pattern
- System/Application logları → uygulama crash, hata
- Prefetch → exploit sonrası çalışan araçlar
- Security 4688 → web servisi veya backup servisinden spawn olan process
- $UsnJrnl → web root veya sistem dizinine yeni dosya (web shell)
- IIS logları → `w3wp.exe` → cmd.exe zinciri

**IOC:**
- Web servisinden (IIS, Apache) spawn olan cmd.exe veya powershell.exe
- Web root'ta yeni .aspx, .php, .jsp dosyası (web shell)
- Backup servisinden beklenmedik network bağlantısı
- Exploit sonrası hızlı keşif komutları

**Dikkat:**
Patch uygulandıktan sonra bile saldırganlar
daha önce bıraktıkları web shell veya persistence ile geri gelebilir.
Patch = initial access kapatıldı ama ortam temiz değil demek değil.