
19 Nisan 2026

---

## REGISTRY ARTIFACT'LARI

1. Shimcache (AppCompatCache)
Öncelik: Yüksek
Konum → SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache
Dikkat: Çalıştırılan veya yolu bilinen EXE'lerin son değişiklik zamanı ve dosya yolunu tutar; yeniden başlatmada diske yazılır.

2. Amcache.hve
Öncelik: Yüksek
Konum → C:\Windows\appcompat\Programs\Amcache.hve
Dikkat: SHA1 hash, tam dosya yolu, boyut, yayıncı ve ilk çalıştırma zamanını içerir.

3. BAM (Background Activity Moderator)
Öncelik: Yüksek
Konum → SYSTEM\CurrentControlSet\Services\bam\State\UserSettings\{SID}
Dikkat: Her kullanıcı için son çalıştırılan EXE'nin tam yolunu ve UTC zaman damgasını tutar (Win10 1709+).

4. UserAssist
Öncelik: Yüksek
Konum → NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist\{GUID}\Count
Dikkat: GUI üzerinden çalıştırılan programların ROT13 ile kodlanmış adını, çalıştırma sayısını ve son çalıştırma zamanını tutar.

5. Services (Servis Kayıtları)
Öncelik: Yüksek
Konum → SYSTEM\CurrentControlSet\Services\<ServiceName>
Dikkat: ImagePath, Start tipi ve ServiceDll değerlerini inceleyerek yeni eklenen veya değiştirilen servisleri tespit eder. (T1569.002)

6. Scheduled Task Registry Entries
Öncelik: Yüksek
Konum → SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tasks\{GUID}
Dikkat: Zamanlanmış görevlerin Actions, Triggers ve son çalışma durumunu tutar. (T1053.005)

7. DAM (Desktop Activity Moderator)
Öncelik: Orta
Konum → SYSTEM\CurrentControlSet\Services\dam\State\UserSettings\{SID}
Dikkat: BAM ile aynı yapıdadır; masaüstü oturumlarındaki çalıştırmaları ayrıca kaydeder.

8. RecentApps
Öncelik: Orta
Konum → NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Search\RecentApps
Dikkat: Son çalıştırılan uygulamaların AppID, çalıştırma sayısı ve son erişim zamanını içerir (Win10; Win11'de kısıtlı).

9. RunMRU
Öncelik: Orta
Konum → NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU
Dikkat: Win+R (Run) diyaloguna yazılan komutların listesini MRU sırasıyla tutar.

10. AppCompatFlags\Compatibility Assistant (PCA)
Öncelik: Orta
Konum → NTUSER.DAT\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Compatibility Assistant\Store
Dikkat: Program Compatibility Assistant tarafından algılanan çalıştırmaların tam yolunu ve zaman damgasını tutar.

11. Terminal Server Client\Servers (RDP Çalıştırma İzi)
Öncelik: Orta
Konum → NTUSER.DAT\Software\Microsoft\Terminal Server Client\Servers
Dikkat: RDP ile bağlanılan uzak sunucularda çalıştırılan programlara dolaylı kanıt sağlar.

12. SessionManager\AppCertDlls
Öncelik: Orta
Konum → SYSTEM\CurrentControlSet\Control\Session Manager\AppCertDlls
Dikkat: Her CreateProcess çağrısında yüklenen DLL yollarını tutar; kötüye kullanım halinde persistence ve execution kanıtıdır.

13. Image File Execution Options (IFEO)
Öncelik: Orta
Konum → SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\<exe>
Dikkat: Debugger değeri atanmış EXE'ler, çalıştırma sırasında başka bir programın tetiklendiğini gösterir.

14. MUICache
Öncelik: Düşük
Konum → NTUSER.DAT\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\MuiCache
Dikkat: Çalıştırılan uygulamanın dil kaynağından (MUI) çekilen açıklama metnini ve tam yolunu tutar.

15. AppCompatFlags\Layers
Öncelik: Düşük
Konum → NTUSER.DAT\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers
Dikkat: Uyumluluk modunda çalıştırılan programların tam yolunu ve uygulanan flag'leri gösterir.

16. CIDSizeMRU
Öncelik: Düşük
Konum → NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\CIDSizeMRU
Dikkat: Dosya açma/kaydetme diyaloğu kullanan uygulamaların tam yolunu tutar; dolaylı çalıştırma kanıtıdır.

17. FeatureUsage
Öncelik: Düşük
Konum → NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\FeatureUsage
Dikkat: Görev çubuğuna sabitlenen veya oradan başlatılan uygulamaların sayaçlarını tutar (Win10 1903+).

---

## FILE SYSTEM ARTIFACT'LARI

1. Prefetch
Öncelik: Yüksek
Konum → C:\Windows\Prefetch\<EXENAME>-<HASH>.pf
Dikkat: Son 8 çalıştırma zamanı, çalıştırma sayısı ve yüklenen dosya/dizin listesini tutar (Win11 SSD'de varsayılan açık).

2. SRUM (System Resource Usage Monitor)
Öncelik: Yüksek
Konum → C:\Windows\System32\sru\SRUDB.dat
Dikkat: Son 30–60 günde çalışan her programın CPU, ağ, enerji kullanım detayını kullanıcı SID'i ile birlikte tutar.

3. Scheduled Tasks XML
Öncelik: Yüksek
Konum → C:\Windows\System32\Tasks\ ve alt dizinleri
Dikkat: Zamanlanmış görevin XML tanımını (komut, argüman, tetikleyici, oluşturan kullanıcı) içerir. (T1053.005)

4. WMI Repository
Öncelik: Yüksek
Konum → C:\Windows\System32\wbem\Repository\ (OBJECTS.DATA, INDEX.BTR, MAPPING*.MAP)
Dikkat: WMI event subscription ile çalıştırılan komut/script'lerin kalıcı tanımlarını tutar. (T1047)

5. PowerShell ConsoleHost History
Öncelik: Yüksek
Konum → %APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
Dikkat: Kullanıcının PowerShell konsolunda yazdığı tüm komutları düz metin olarak saklar. (T1059.001)

6. PowerShell Transcript Logs (Varsayılan: Kapalı)
Öncelik: Yüksek
Konum → Varsayılan çıktı dizini (GPO ile yapılandırılır, genellikle %USERPROFILE%\Documents)
Dikkat: Oturum boyunca girdi/çıktıyı tam metin olarak kaydeder; GPO ile etkinleştirildiğinde her oturum için ayrı dosya oluşturur.

7. ActivitiesCache.db (Timeline)
Öncelik: Orta
Konum → %LOCALAPPDATA%\ConnectedDevicesPlatform\<ID>\ActivitiesCache.db
Dikkat: Çalıştırılan uygulamaların AppID, başlangıç/bitiş zamanı ve odak süresi kaydını SQLite formatında tutar (Win10; Win11'de kısıtlı).

8. Recent / LNK Dosyaları (Shortcut Execution)
Öncelik: Orta
Konum → %APPDATA%\Microsoft\Windows\Recent\ ve C:\Users\<user>\Desktop\*.lnk
Dikkat: LNK hedef yolu, MAC zaman damgaları ve hedef dosyanın volume bilgisini tutar; dolaylı çalıştırma kanıtıdır.

9. Jump Lists
Öncelik: Orta
Konum → %APPDATA%\Microsoft\Windows\Recent\AutomaticDestinations\ ve CustomDestinations\
Dikkat: Uygulamaların son açtığı dosyaların listesini tutar; uygulamanın çalıştırıldığını dolaylı olarak kanıtlar.

10. $MFT Zaman Damgaları
Öncelik: Orta
Konum → \\.\C: (raw) — $MFT
Dikkat: EXE/DLL/script dosyalarının Created/Modified/Accessed zaman damgalarını ve $SI vs $FN tutarsızlığını inceler (timestomping tespiti).

11. $UsnJrnl (USN Journal)
Öncelik: Orta
Konum → \\.\C: — $Extend\$UsnJrnl:$J
Dikkat: Dosya oluşturma, silme, yeniden adlandırma kayıtlarını tutar; silinmiş EXE veya script dosyalarının varlığını kanıtlar.

12. BITS Jobs Veritabanı
Öncelik: Orta
Konum → C:\ProgramData\Microsoft\Network\Downloader\qmgr.db
Dikkat: BITS ile indirilen ve notify komutuyla çalıştırılan dosyaların URL, hedef yol ve zaman bilgisini tutar.

13. Startup Dizinleri
Öncelik: Orta
Konum → %APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\ ve ProgramData karşılığı
Dikkat: Oturum açılışında otomatik çalıştırılan script veya LNK dosyalarının varlığını ve zaman damgasını kontrol eder.

14. Superfetch / SysMain Veritabanı
Öncelik: Düşük
Konum → C:\Windows\Prefetch\Ag*.db
Dikkat: Uzun süreli çalıştırma geçmişi ve erişim paternlerini tutar; ek bağlam sağlar.

15. Windows Error Reporting (WER)
Öncelik: Düşük
Konum → C:\ProgramData\Microsoft\Windows\WER\ReportArchive\ ve ReportQueue\
Dikkat: Çöken veya hata veren uygulamanın tam yolu, modül listesi ve kilitlenme zamanını tutar.

16. Recycle Bin ($I / $R dosyaları)
Öncelik: Düşük
Konum → C:\$Recycle.Bin\{SID}\
Dikkat: Silinen EXE veya script dosyalarının orijinal yolunu, boyutunu ve silinme zamanını tutar.

17. Thumbcache / IconCache
Öncelik: Düşük
Konum → %LOCALAPPDATA%\Microsoft\Windows\Explorer\iconcache_*.db
Dikkat: Daha önce var olan ancak silinmiş bir EXE'nin simge verisinin hâlâ cachede bulunması dolaylı kanıt oluşturur.

---

## EVENT LOG ARTIFACT'LARI

1. Security.evtx — Event ID 4688 (Process Creation)
Öncelik: Yüksek
Konum → C:\Windows\System32\winevt\Logs\Security.evtx
Dikkat: Oluşturulan sürecin adı, PID, üst süreç PID'i ve kullanıcı bilgisini tutar; komut satırı kaydı için ek GPO gerekir (Varsayılan: Kapalı — komut satırı kısmı).

2. Sysmon Event ID 1 (Process Create) (Varsayılan: Kapalı — Sysmon kurulu değilse)
Öncelik: Yüksek
Konum → C:\Windows\System32\winevt\Logs\Microsoft-Windows-Sysmon%4Operational.evtx
Dikkat: Komut satırı, hash (MD5/SHA256/IMPHASH), üst süreç ilişkisi, oturum ve kullanıcı bilgisini tutar.

3. PowerShell Script Block Logging — Event ID 4104
Öncelik: Yüksek
Konum → Microsoft-Windows-PowerShell%4Operational.evtx
Dikkat: Çalıştırılan PowerShell script bloğunun tam metnini kaydeder; obfuscation sonrası de-obfuscated halini de içerebilir (Win10+, kısmen varsayılan açık). (T1059.001)

4. PowerShell Module Logging — Event ID 4103 (Varsayılan: Kapalı)
Öncelik: Yüksek
Konum → Microsoft-Windows-PowerShell%4Operational.evtx
Dikkat: Çağrılan PowerShell modül ve cmdlet'lerin parametre detaylarını kaydeder. (T1059.001)

5. System.evtx — Event ID 7045 (Yeni Servis Kurulumu)
Öncelik: Yüksek
Konum → C:\Windows\System32\winevt\Logs\System.evtx
Dikkat: Yeni kurulan servisin adı, ImagePath'i, başlatma tipi ve hesap bilgisini kaydeder. (T1569.002)

6. Security.evtx — Event ID 4697 (Servis Kurulumu — Audit) (Varsayılan: Kapalı)
Öncelik: Yüksek
Konum → C:\Windows\System32\winevt\Logs\Security.evtx
Dikkat: 7045'e ek olarak kurulumu yapan kullanıcının SID'ini de içerir; audit policy ile etkinleştirilir. (T1569.002)

7. Task Scheduler Event Log — Event ID 106, 140, 200, 201 (Varsayılan: Kapalı — Win10'da)
Öncelik: Yüksek
Konum → Microsoft-Windows-TaskScheduler%4Operational.evtx
Dikkat: Zamanlanmış görevin kaydedilme, güncelleme, başlatma ve tamamlanma olaylarını kaydeder. (T1053.005)

8. WMI-Activity Operational Log — Event ID 5857, 5858, 5860, 5861
Öncelik: Yüksek
Konum → Microsoft-Windows-WMI-Activity%4Operational.evtx
Dikkat: WMI provider yüklemesi, hata ve kalıcı event subscription oluşturma/tetiklenme olaylarını kaydeder. (T1047)

9. AppLocker Event Logs (Varsayılan: Kapalı — AppLocker etkin değilse)
Öncelik: Yüksek
Konum → Microsoft-Windows-AppLocker%4EXE and DLL.evtx (ve diğer alt kanallar)
Dikkat: İzin verilen veya engellenen EXE/DLL/script çalıştırmalarını dosya yolu, hash ve yayıncı ile kaydeder. (T1059)

10. PowerShell Classic Log — Event ID 400, 403, 600
Öncelik: Orta
Konum → Windows PowerShell.evtx
Dikkat: PowerShell engine başlatma/durdurma olaylarını ve HostApplication alanında çalıştırma komutunu tutar.

11. Windows Defender Operational
Öncelik: Orta
Konum → Microsoft-Windows-Windows Defender%4Operational.evtx
Dikkat: Event ID 1116/1117 ile tespit edilen zararlı dosyanın yolu, hash'i ve alınan aksiyonu kaydeder; dolaylı execution kanıtı.

12. System.evtx — Event ID 7034, 7035, 7036, 7040
Öncelik: Orta
Konum → C:\Windows\System32\winevt\Logs\System.evtx
Dikkat: Servislerin başlatma, durdurma, çökme ve start tipinin değiştirilme olaylarını tutar.

13. Security.evtx — Event ID 4648 (Explicit Logon)
Öncelik: Orta
Konum → C:\Windows\System32\winevt\Logs\Security.evtx
Dikkat: Farklı kimlik bilgileriyle (runas vb.) çalıştırılan sürecin hedef sunucu ve kullanıcı bilgisini tutar.

14. BITS Client Operational — Event ID 3, 59, 60
Öncelik: Orta
Konum → Microsoft-Windows-Bits-Client%4Operational.evtx
Dikkat: BITS iş oluşturma, tamamlama ve notify komut çalıştırma olaylarını kaydeder.

15. WDAC / Code Integrity Log — Event ID 3076, 3077 (Varsayılan: Kapalı — WDAC politikası yoksa)
Öncelik: Orta
Konum → Microsoft-Windows-CodeIntegrity%4Operational.evtx
Dikkat: Windows Defender Application Control tarafından engellenen veya denetlenen binary/script'lerin kaydını tutar.

16. Microsoft Office Alerts Log
Öncelik: Orta
Konum → Microsoft Office Alerts.evtx (OAlerts.evtx)
Dikkat: Makro çalıştırma veya güvenlik uyarı olaylarını kaydeder; Office tabanlı execution kanıtı sağlar. (T1204.002)

17. Application.evtx — Event ID 1000, 1001, 1002
Öncelik: Düşük
Konum → C:\Windows\System32\winevt\Logs\Application.evtx
Dikkat: Uygulama çökmesi veya hatasında sürecin adını, modülünü ve hata zamanını tutar.

18. Windows Script Host (WSH) Logs — Event ID 1, 2, 3
Öncelik: Düşük
Konum → Application.evtx (WSH hataları)
Dikkat: wscript.exe / cscript.exe ile çalıştırılan VBS/JS script'lerinin hata kayıtlarını tutar. (T1059.005 / T1059.007)

19. PrintService Operational — Event ID 316
Öncelik: Düşük
Konum → Microsoft-Windows-PrintService%4Operational.evtx
Dikkat: Spool dizinine yazılan ve çalıştırılan DLL'leri tespit etmek için kullanılır (PrintNightmare gibi exploitation senaryoları).

20. DriverFrameworks-UserMode — Event ID 2004
Öncelik: Düşük
Konum → Microsoft-Windows-DriverFrameworks-UserMode%4Operational.evtx
Dikkat: USB bağlantı olaylarıyla ilişkilendirerek harici medyadan çalıştırma zamanlamasını doğrulamada kullanılır.

21. DNS Client Event Log (Varsayılan: Kapalı — Analytic kanal)
Öncelik: Düşük
Konum → Microsoft-Windows-DNS-Client%4Operational.evtx
Dikkat: Çalıştırılan programın yaptığı DNS sorgularını süreç bazında kaydeder; C2 iletişimini süreçle eşleştirir.

---

## MEMORY ARTIFACT'LARI

1. Aktif Süreç Listesi (Process List)
Öncelik: Yüksek
Erişim: Live System
Konum → RAM Dump — volatility/rekall analizi
Dikkat: Çalışan ve gizlenmiş süreçlerin PID, PPID, komut satırı argümanları ve başlatma zamanını içerir.

2. Loaded DLL Listesi
Öncelik: Yüksek
Erişim: Live System
Konum → RAM Dump — süreç adres alanı
Dikkat: Her sürecin yüklediği DLL'lerin tam yolunu ve yüklenme adresini tutar; DLL injection/side-loading tespiti sağlar.

3. Injected Code Bölgeleri
Öncelik: Yüksek
Erişim: Live System
Konum → RAM Dump — VAD (Virtual Address Descriptor) analizi
Dikkat: RWX izinli, dosyasız (unlinked) bellek bölgelerini tespit ederek code injection kanıtı sağlar.

4. Network Connections (Bellek)
Öncelik: Yüksek
Erişim: Live System
Konum → RAM Dump — netscan / network socket yapıları
Dikkat: Kapanmış olanlar dahil tüm TCP/UDP bağlantılarını sahip süreçle eşleştirir; disk üzerinde izi kalmayan bağlantıları gösterir.

5. Bellekteki PowerShell/.NET Assembly Kalıntıları
Öncelik: Yüksek
Erişim: Live System
Konum → RAM Dump — CLR heap / string taraması
Dikkat: Fileless malware'in bellekte bıraktığı .NET assembly, PowerShell scriptblock ve AMSI buffer kalıntılarını tutar. (T1059.001)

6. Kernel Callback / SSDT Hooking
Öncelik: Orta
Erişim: Live System
Konum → RAM Dump — kernel alanı
Dikkat: Rootkit'lerin SSDT veya callback tablolarına yaptığı değişiklikleri bellek analizi ile ortaya çıkarır.

7. Handle / Mutex Tablosu
Öncelik: Orta
Erişim: Live System
Konum → RAM Dump — süreç handle alanı
Dikkat: Süreçlerin açtığı dosya, registry anahtarı ve named mutex'ler üzerinden malware ailesini teyit eder.

8. Clipboard İçeriği
Öncelik: Düşük
Erişim: Live System
Konum → RAM Dump — Win32 clipboard yapıları
Dikkat: Kopyalanmış komut veya script parçalarını ortaya çıkarabilir.

---

## WMI, NETWORK & THIRD-PARTY ARTIFACT'LARI

1. WMI Persistent Event Subscriptions (MOF/CIM)
Öncelik: Yüksek
Konum → C:\Windows\System32\wbem\Repository\OBJECTS.DATA
Dikkat: __EventFilter, CommandLineEventConsumer ve __FilterToConsumerBinding nesnelerinin varlığını kontrol eder. (T1047)

2. IIS / Web Sunucusu Logları (Webshell Execution)
Öncelik: Yüksek
Konum → C:\inetpub\logs\LogFiles\ (IIS varsayılanı)
Dikkat: Webshell üzerinden çalıştırılan komutların HTTP istek parametrelerinde izini tutar. (T1059)

3. AMSI (Antimalware Scan Interface) Trace (Varsayılan: Kapalı — ETW kaydı gerektirir)
Öncelik: Yüksek
Erişim: Live System
Konum → ETW Provider: Microsoft-Antimalware-Scan-Interface
Dikkat: PowerShell, VBScript, JScript ve .NET çalıştırmalarının AMSI'ye gönderilen ham içeriğini yakalar.

4. WMI CIM Repository — AutoRecover MOFs
Öncelik: Orta
Konum → C:\Windows\System32\wbem\AutoRecover\
Dikkat: Derlenen MOF dosyalarının listesini tutar; yetkisiz MOF derleme girişimlerini tespit eder.

5. NTFS Alternate Data Streams (ADS — Zone.Identifier)
Öncelik: Orta
Konum → <dosya>:Zone.Identifier
Dikkat: İndirilen EXE/script'in kaynak URL'sini ve ZoneId değerini tutar; dosyanın internet kaynaklı olduğunu kanıtlar. (T1204.002)

6. COM Object Hijacking Kayıtları
Öncelik: Orta
Konum → HKCU\Software\Classes\CLSID\ ve HKCR\CLSID\
Dikkat: Kullanıcı hive'ında oluşturulan CLSID InprocServer32/LocalServer32 değerleri meşru COM nesnelerini override ederek execution sağlar.

7. AV / EDR Karantina Dosyaları
Öncelik: Orta
Konum → Ürüne göre değişir (ör. C:\ProgramData\Microsoft\Windows Defender\Quarantine\)
Dikkat: Karantinaya alınan zararlı dosyanın orijinal yolunu, hash'ini ve tespit zamanını içerir; çalıştırma girişimini kanıtlar.

8. Autoruns Çıktısı (SysInternals)
Öncelik: Orta
Erişim: Live System
Konum → Canlı sistemde çalıştırılır; çıktı XML/CSV
Dikkat: Tüm auto-start entry point'lerini (Run keys, services, scheduled tasks, WMI, vb.) tek seferde listeler.

9. Group Policy Scripts (Logon/Logoff/Startup/Shutdown)
Öncelik: Orta
Konum → C:\Windows\System32\GroupPolicy\Machine\Scripts\ ve \User\Scripts\
Dikkat: GPO ile atanan başlangıç/oturum açma script'lerinin dosya varlığını ve içeriğini kontrol eder.

10. ETW Trace Logs (Analytic/Debug Kanalları) (Varsayılan: Kapalı)
Öncelik: Orta
Konum → C:\Windows\System32\winevt\Logs\ — çeşitli Analytic/Debug .etl dosyaları
Dikkat: AMSI, .NET Runtime, WinHTTP gibi kanallar etkinleştirildiğinde script çalıştırma ve ağ çağrılarını detaylı kaydeder.

11. SilkETW / SilkService Logları (Varsayılan: Kapalı — kurulu değilse)
Öncelik: Orta
Konum → Yapılandırmaya bağlı JSON/EVT çıktısı (3rd party)
Dikkat: .NET CLR, LDAP, DNS ve WinHTTP sağlayıcılarından detaylı ETW verisini JSON formatında kaydeder.

12. Microsoft-Windows-Shell-Core Operational
Öncelik: Düşük
Konum → Microsoft-Windows-Shell-Core%4Operational.evtx
Dikkat: Event ID 9707/9708 ile uygulama başlatma ve Explorer shell üzerinden çalıştırma olaylarını tutar.

13. CryptnetUrlCache
Öncelik: Düşük
Konum → %LOCALAPPDATA%\Microsoft\Windows\INetCache\IE\ ve Content.IE5\
Dikkat: İmzalı binary'lerin CRL/OCSP kontrolü sırasında erişilen URL'leri cacheler; indirilen ve çalıştırılan imzalı dosyaların dolaylı kanıtıdır.

14. Tracing / Debug Logları (.log / .etl)
Öncelik: Düşük
Konum → C:\Windows\Tracing\, C:\Windows\debug\, %TEMP%\
Dikkat: Bazı Windows bileşenleri ve üçüncü parti yazılımlar çalıştırma detaylarını düz metin loglara yazar; nadir ama değerli kanıt içerebilir.

---

> **Not:** "(Varsayılan: Kapalı)" ibaresi taşıyan artifact'lar, olay öncesinde etkinleştirilmemişse mevcut olmayabilir.  
> "Erişim: Live System" yalnızca canlı sistemden elde edilebilen artifact'larda belirtilmiştir; belirtilmeyenler hem canlı sistem hem disk imajından erişilebilir.  
> Öncelik değerleri, saldırganların bu artifact'ı ne sıklıkla tetiklediği ve execution kanıtı sağlama güvenilirliği baz alınarak belirlenmiştir.
