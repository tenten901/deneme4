## Prefetch

**Ne söyler:**
Bir binary'nin çalıştığını kanıtlar. Çalışma sayısı,
son çalışma zamanı ve binary'nin hangi dosyalara
eriştiğini gösterir.

**Konum:**
C:\Windows\Prefetch\*.pf

**Dikkat edilecek kısıtlamalar:**
- Server'larda varsayılan kapalı
- SSD sistemlerde kapalı olabilir
- Max 128 dosya (Win7), 1024 (Win8+), dolunca eskiler silinir

**Nasıl parse edilir:**
PECmd.exe -f "C:\Windows\Prefetch\powershell.exe-ABC12345.pf"
PECmd.exe -d "C:\Windows\Prefetch" --csv output

**Çıktıda nelere bakılır:**
- Executable name + hash
- Run count (kaç kez çalıştı)
- Last run time (en son çalışma zamanı)
- Previous run times (önceki 7 çalışma)
- Loaded files (hangi dosyalara erişti — DLL, config, veri)

**IOC / Anomali işaretleri:**
- Temp veya AppData'dan çalışan binary
- Yüksek run count ama son kullanıcı bilmiyor
- Prefetch dosyası var ama binary artık sistemde yok (silinmiş)
- Binary ismi meşru araca benzetilmiş

**İlgili teknikler:**
→ Saldırı Zinciri: Execution → PowerShell
→ Saldırı Zinciri: Execution → LOLBIN Abuse