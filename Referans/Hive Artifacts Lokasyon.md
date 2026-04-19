## 1. Registry Hive Dosyaları

**`C:\Windows\System32\config\`** → SAM, SYSTEM, SECURITY, SOFTWARE, DEFAULT, COMPONENTS, BBI, \*.LOG1, \*.LOG2, RegBack\

**`C:\Windows\Repair\`** → SAM

**`C:\Users\<kullanıcı adı>\`** → NTUSER.DAT, AppData\Local\Microsoft\Windows\UsrClass.dat

- `C:\System Volume Information\Syscache.hve`
- `C:\Windows\appcompat\Programs\Amcache.hve`

---

## 2. Olay Günlükleri (Event Logs)

- `C:\Windows\System32\winevt\Logs\*.evtx`

---

## 3. NTFS Dosya Sistemi Artifact'ları

**`C:\`** kök dizininde: `$MFT`, `$LogFile`, `$Bitmap`, `$Boot`, `$Secure`

**`C:\$Extend\`** → `$UsnJrnl`, `$UsnJrnl:$J`, `$UsnJrnl:$MAX`

`$I30` → Her NTFS dizininin içinde bulunur, silinmiş dosya isimlerini tespit etmek için incelenir. Örnek: `C:\Users\$I30`, `C:\Windows\$I30`

---

## 4. Prefetch Dosyaları

**`C:\Windows\Prefetch\`** → `*.pf`, `Ag*.db`

Saldırı araçlarına ait prefetch dosyaları da bu dizinde aranır: `mimikatz.pf`, `procdump.pf`, `ntdsutil.pf`, `vssadmin.pf`, `rundll32.pf`, `bcdedit.pf`, `cmdkey.pf`, `mstsc.pf`, `rdpclip.pf`

---

## 5. Kullanıcı Aktivite ve Shell Artifact'ları

**`C:\Users\<kullanıcı adı>\AppData\Roaming\Microsoft\Windows\Recent\`** → `*.lnk`, `AutomaticDestinations\`, `CustomDestinations\`

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Windows\Explorer\`** → `thumbcache_*.db`, `iconcache_*.db`

**`C:\Users\<kullanıcı adı>\AppData\Local\ConnectedDevicesPlatform\`** → `ActivitiesCache.db` (alt klasörlerden birinin içindedir)

- `C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Windows\Notifications\wpndatabase.db`
- `C:\Users\<kullanıcı adı>\AppData\Roaming\Microsoft\Office\Recent\`

---

## 6. PowerShell ve Komut Satırı Geçmişi

**`C:\Users\<kullanıcı adı>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\`** → `ConsoleHost_history.txt`

**`C:\Windows\System32\WindowsPowerShell\v1.0\`** → `profile.ps1`

- `C:\Users\<kullanıcı adı>\Documents\PowerShell\Microsoft.PowerShell_profile.ps1`
- `C:\Users\<kullanıcı adı>\Documents\PowerShell_transcript.*.txt`

---

## 7. Görev Zamanlayıcı

**`C:\Windows\System32\Tasks\`** → `*.xml`, `At*`, `Microsoft\Windows\` (alt görevler)

---

## 8. WMI Repository

**`C:\Windows\System32\wbem\Repository\`** → `OBJECTS.DATA`, `INDEX.BTR`, `MAPPING*.MAP`

---

## 9. Tarayıcı Artifact'ları

**`C:\Users\<kullanıcı adı>\AppData\Local\Google\Chrome\User Data\Default\`** → `History`, `Cache\`, `Login Data`, `Cookies`, `Web Data`, `Extensions\`, `Local State`

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Edge\User Data\Default\`** → `History`, `Login Data`, `Extensions\`, `Local State`

**`C:\Users\<kullanıcı adı>\AppData\Roaming\Mozilla\Firefox\Profiles\<profil adı>\`** → `places.sqlite`, `cookies.sqlite`, `logins.json`, `formhistory.sqlite`, `key4.db`

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Windows\WebCache\`** → `WebCacheV01.dat`

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Windows\INetCache\`** → geçici internet dosyaları

**`C:\Users\<kullanıcı adı>\AppData\LocalLow\Microsoft\CryptnetUrlCache\`** → `Content\`, `MetaData\`

---

## 10. Uzak Erişim Araçları

**`C:\Users\<kullanıcı adı>\AppData\Roaming\TeamViewer\`** → `Connections.txt`

**`C:\Program Files\TeamViewer\`** → `Connections_incoming.txt`

**`C:\Users\<kullanıcı adı>\AppData\Roaming\AnyDesk\`** → `ad.trace`, `system.conf`

**`C:\ProgramData\AnyDesk\`** → `ad_svc.trace`, `system.conf`

**`C:\Users\<kullanıcı adı>\AppData\Roaming\RustDesk\config\`** → `*.toml`

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Terminal Server Client\Cache\`** → `*.bmc`, `bcache*.bmc`

- `C:\Users\<kullanıcı adı>\Documents\Default.rdp`
- `C:\Users\<kullanıcı adı>\.ngrok2\ngrok.yml`

---

## 11. SSH ve Ağ Bağlantıları

**`C:\Users\<kullanıcı adı>\.ssh\`** → `known_hosts`, `config`, `authorized_keys`

**`C:\ProgramData\Microsoft\Wlansvc\Profiles\Interfaces\`** → her ağ adaptörüne ait alt klasörde `*.xml` dosyaları bulunur

**`C:\Windows\System32\drivers\etc\`** → `hosts`, `lmhosts.sam`

- `C:\ProgramData\ssh\administrators_authorized_keys`
- `C:\ProgramData\Microsoft\Network\Connections\Pbk\rasphone.pbk`
- `C:\Users\<kullanıcı adı>\AppData\Roaming\Microsoft\Network\Connections\Pbk\rasphone.pbk`
- `C:\Windows\System32\LogFiles\Firewall\pfirewall.log`

---

## 12. Dosya Transfer Araçları

**`C:\Users\<kullanıcı adı>\AppData\Roaming\FileZilla\`** → `recentservers.xml`, `queue.sqlite3`

- `C:\Users\<kullanıcı adı>\AppData\Roaming\WinSCP.ini`
- `C:\Users\<kullanıcı adı>\AppData\Roaming\WinRAR\`
- `C:\Users\<kullanıcı adı>\AppData\Roaming\7-Zip\`

---

## 13. Kimlik Bilgileri ve Parola Dump Artifact'ları

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Vault\`** → alt klasörlerde `Policy.vpol`, `*.vcrd`

**`C:\Users\<kullanıcı adı>\AppData\Roaming\Microsoft\Protect\`** → kullanıcı SID'ine ait alt klasörde şifrelenmiş DPAPI anahtarları

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Credentials\`** → şifrelenmiş kimlik bilgisi dosyaları

**`C:\Users\<kullanıcı adı>\AppData\Roaming\Microsoft\Credentials\`** → şifrelenmiş kimlik bilgisi dosyaları

- `C:\Windows\NTDS\ntds.dit` (Active Directory veritabanı, domain controller'larda)
- `C:\Windows\Temp\lsass.dmp`
- `C:\Users\<kullanıcı adı>\AppData\Local\Temp\lsass*.dmp`
- `C:\ProgramData\Microsoft\Windows\WER\ReportQueue\` → içinde `lsass.exe*.dmp` aranır
- `*.kirbi` dosyaları → `C:\Windows\Temp\`, `C:\Users\<kullanıcı adı>\AppData\Local\Temp\` ve kullanıcı profili kök dizininde aranır

---

## 14. Başlangıç Noktaları ve Persistence

**Startup klasörleri:**
- `C:\Users\<kullanıcı adı>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\`
- `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\`

**Group Policy betikleri:**

**`C:\Windows\System32\GroupPolicy\Machine\Scripts\`** → `Startup\`, `scripts.ini`, `psscripts.ini`

**`C:\Windows\System32\GroupPolicy\User\Scripts\`** → `Logon\`

**Domain ortamında SYSVOL:**
- `\\<domain>\SYSVOL\<domain>\Policies\` → her politika klasöründe `Machine\Preferences\Groups\Groups.xml` ve `Machine\Preferences\Services\Services.xml`

---

## 15. Geri Dönüşüm Kutusu

**`C:\$Recycle.Bin\`** → her kullanıcıya ait SID isimli alt klasörde `$I*` (metadata) ve `$R*` (asıl dosya) bulunur

Diğer sürücülerde de aynı yapı aranır: `D:\$Recycle.Bin\`, `E:\$Recycle.Bin\`

---

## 16. Volume Shadow Copy

- `\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy<N>\` → gölge kopya numarası N ile temsil edilir, içinde sistemin eski hali bulunur
- `C:\System Volume Information\`

---

## 17. Windows Arama ve İndeks

**`C:\ProgramData\Microsoft\Search\Data\Applications\Windows\`** → `Windows.edb`

- `C:\Users\<kullanıcı adı>\AppData\Local\Packages\Microsoft.Windows.Cortana_...\LocalState\DeviceSearchCache\AppCache.db`

---

## 18. BITS (Arka Plan Aktarım Servisi)

**`C:\ProgramData\Microsoft\Network\Downloader\`** → `qmgr.db`, `qmgr0.dat`, `qmgr1.dat`

---

## 19. Hata Raporları ve Crash Dump

**`C:\ProgramData\Microsoft\Windows\WER\`** → `ReportArchive\`, `ReportQueue\`

**`C:\Users\<kullanıcı adı>\AppData\Local\`** → `CrashDumps\`, `Microsoft\Windows\WER\`

- `C:\Windows\MEMORY.DMP`
- `C:\Windows\Minidump\*.dmp`
- `C:\Windows\LiveKernelReports\`

---

## 20. Kurulum ve Güncelleme Logları

**`C:\Windows\INF\`** → `setupapi.dev.log`, `setupapi.setup.log`, `setupapi.app.log`

**`C:\Windows\Logs\`** → `CBS\CBS.log`, `DISM\dism.log`, `SetupAPI\`, `MeasuredBoot\`

**`C:\Windows\Panther\`** → `Unattend.xml`, `unattend\`

**`C:\Windows\SoftwareDistribution\`** → `DataStore\DataStore.edb`, `Download\`

- `C:\Users\<kullanıcı adı>\AppData\Local\Temp\MSI*.log`

---

## 21. IIS Web Sunucu Logları

**`C:\inetpub\`** → `logs\LogFiles\W3SVC*\*.log`, `wwwroot\`

---

## 22. SRUM (System Resource Usage Monitor)

- `C:\Windows\System32\sru\SRUDB.dat`

---

## 23. Bellek ve Sayfalama Dosyaları

`C:\` kök dizininde: `hiberfil.sys`, `pagefile.sys`, `swapfile.sys`

---

## 24. OneDrive Logları

**`C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\OneDrive\`** → `logs\SyncDiagnostics.log`, `settings\`

---

## 25. Yazıcı Spool

**`C:\Windows\System32\spool\`** → `PRINTERS\`, `drivers\`

---

## 26. Boot ve Sistem Bütünlüğü

**`C:\Windows\System32\`** → `CodeIntegrity\`, `CatRoot\`, `WDI\`, `ELAM\`

**`C:\Windows\Logs\`** → `MeasuredBoot\`

- `C:\Boot\BCD`

---

## 27. WSL (Windows Subsystem for Linux)

- `\\wsl$\<dağıtım adı>\home\<kullanıcı adı>\.bashrc`
- `C:\Users\<kullanıcı adı>\AppData\Local\Packages\` → `*CanonicalGroup*` ile başlayan klasör (Ubuntu, Debian vb.)

---

## 28. Diğer Uygulama Artifact'ları

- `C:\Users\<kullanıcı adı>\AppData\Local\Steam\htmlcache\`
- `C:\Users\<kullanıcı adı>\AppData\Local\Packages\Microsoft.WindowsNotepad_...\LocalState\TabState\`
- `C:\Users\<kullanıcı adı>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_...\LocalState\plum.sqlite`
- `C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Media Player\`
- `C:\Users\<kullanıcı adı>\AppData\Local\Microsoft\Windows\INetCache\Content.Outlook\`
- `C:\ProgramData\Microsoft\Windows\Containers\`
- `C:\Program Files\VMware\VMware Tools\`
- `C:\Program Files\Oracle\VirtualBox Guest Additions\`
