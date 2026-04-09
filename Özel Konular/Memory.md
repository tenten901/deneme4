### **Müdahale Adımları**
·         **windows.sessions** --> console uygulamalarını tespit et (kullanıcı sayısı tespiti)

·         **windows.pslist** --> console uygulamalarını PPID kontrol et ne tetiklemiş

·        windows.psscan --> pslist ile gizlemiş sürçlerin tespiti

·         **windows.cmdline** --> doğru dizin mi kontrol

·         **windows.netscan** | grep <PID> --> ile süpheli bağlantıyı tespit et

·         windows.malfind --> anomalileri tespit et.
        
·        windows.handles --pid --> sürece ait tüm dosya tanımlayıcılarını (handles) listelemeye yarar.  (sisteme bıraktığı dosyalar incelenebilir.)

·         windows.pslist --dump --pid <PID> --> süpheli uygulamanın dumpını al

·         sha256 <dosya> --> kontrol et veya direk dosyayı ver.

·         strings <dosya> --> okunabilir ne var packlenmiş mi bak.

·         Windows.dlllist --> yapaya zekaya ver?

·         windows.info --> dump ne zaman alınmış onu kontrol eder.

·         windows.hashdump --> kullanıcıların parolalarını hashli verir. (hashcat ile çözülebilir.)

·         windows.svcscan --> Bellek imajındaki servisleri tespit eder

-------------------------------------------------------------------------------------------------------------
.EXE içerisindeki .DLL çekmek için bunları yap

Eğer olayları bir .dll tetikledi ise amaç exe içerisinde çalışan .dll çıkartmak olmalı bu yüzden bu adımlar izlenebilir.

<python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem windows.malfind> 
anomali .dll rastlandı 


<python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem   windows.dumpfiles --pid 1656>
birsürü dll dump edicek içerisinden süpheli olanın hashi alınır ve kontrol edilir.

VEYA

<python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem windows.filescan | egrep -i 'winsrvc|cyberjunkie|downloads'>
0xc885370cebd0	\Users\CyberJunkie\Downloads\Winsrvc.dll (bu şekilde de konum testpiti yap)

<python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem windows.dumpfiles --virtaddr 0xc885370cebd0>
ImageSectionObject	0xc885370cebd0	Winsrvc.dll	file.0xc885370cebd0.0xc88533bbab80.ImageSectionObject.Winsrvc.dll-4.img

<sha256sum file.0xc885370cebd0.0xc88533bbab80.ImageSectionObject.Winsrvc.dll-4.img>
535e76cdb0572d72d717940198e6719bed3a3ad70b30a7056fdfea87ffb2a338

--> virustotal'de kontrol et

-------------------------------------------------------------------------------------------------------------

Örnek Olay

·         İlk önce windows.session ile hangi kullanıcı ne yapmış inceledik 'lsass.exe' dikkat çekiciydi.

·         sonrasında windows.pslist komutu ile hangi uygulama sebep olmuş inceledik ve çif tıklanarak açılmış yani explorer.exe ile açılmış.

·         sonrasında windows.cmdline ile kontrol ettik dizini yanlıştı 'System\lsass.exe' idi olması gereken 'System32\lsass.exe' Everything ile kendimde arayarak doğruladım.

·         sonrasında windows.netscan ile PİD kontrol ettim hemen müdahale etmek için ancak herhangi bir ip adresi yoktu.

·         sonrasında windows.malfind ile kontrol ettim ve lsass.exe PAGE_EXECUTE_READWRITE ile çalışıyodu.

·         son olarak windows.pslist --dumpp --pid 7592 dedim ve dumpını aldım

·         Aldığım dumpın sha256sum ile hashini alıp virüstotalde kontrol ettim ve kesin olarak zararlı aktivite çıktı 'winPEAS.exe' adında.

-------------------------------------------------------------------------------------------------------------

Dikkat Edilecek İşaretler:
•	Şüpheli süreç isimleri: rastgele, hatalı yazılmış, beklenmedik isimler (lsasss.exe, exploerer.exe)
•	Yanlış ebeveyn (parent) ilişkisi: Örneğin lsass.exe’in ebeveyni wininit.exe olmalıdır. Başka bir süreçten başlıyorsa şüphelidir.
•	Şüpheli süreç ilişkileri: winword.exe → powershell.exe gibi
•	Savunmadan kaçış (Defense Evasion): Meşru bir süreç içine zararlı kod enjekte edilir (örnek: process hollowing, DLL injection). Bu durumda yüzeyde her şey normal görünür ama bellek içinde zararlı kod çalışır.

•	MsMpEng.exe görüldü; bu genellikle false positive olabilir. çünkü Defender belleğe EXECUTE_READWRITE izinleriyle erişir. Meşruiyeti doğrulanmalı <prosesin Bağlandığı ip adresileri incelenmeli>

•	Örnek: cmd.exe veya powershell.exe normalde explorer’den açılır ama svchost.exe veya spoolsv.exe gibi sistem süreçlerinin normal olmayan bir PPID’ye sahip olması şüphelidir.

-------------------------------------------------------------------------------------------------------------

Sistem Hive Dump

python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem windows.registry.hivelist
--> görüntüle neler var


python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem windows.filescan | egrep -i ntuser.dat
--> 0xc8853689ec80  \Users\CyberJunkie\NTUSER.DAT

python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem windows.dumpfiles --virtaddr 0xc8853689ec80
--> file.0xc8853689ec80.0xc885351b7950.DataSectionObject.NTUSER.DAT.dat



-------------------------------------------------------------------------------------------------------------

python3 vol.py -f ../memoryexmpl/DLLInjection-Case3.mem windows.filescan   | grep -i 'config\\'
--> diğer hiveların konumu tespiti (önemli kısımlar 0xc88533ea10c0  dump etmek için konumu)

 | grep -i '\\System32\\config\\'
 | grep -i '\\Programs\\Amcache.hve'
 | grep -i '\\CyberJunkie\\ntuser.dat'
 | grep -i '\\Windows\\UsrClass.dat' 



-------------------------------------------------------------------------------------------------------------

Dikkat Edilecek İşaretler:

•	Şüpheli süreç isimleri: rastgele, hatalı yazılmış, beklenmedik isimler (lsasss.exe, exploerer.exe)
•	Yanlış ebeveyn (parent) ilişkisi: Örneğin lsass.exe’in ebeveyni wininit.exe olmalıdır. Başka bir süreçten başlıyorsa şüphelidir.
•	Şüpheli süreç ilişkileri: winword.exe → powershell.exe gibi
•	Savunmadan kaçış (Defense Evasion): Meşru bir süreç içine zararlı kod enjekte edilir (örnek: process hollowing, DLL injection). Bu durumda yüzeyde her şey normal görünür ama bellek içinde zararlı kod çalışır.

•	MsMpEng.exe görüldü; bu genellikle false positive olabilir. çünkü Defender belleğe EXECUTE_READWRITE izinleriyle erişir. Meşruiyeti doğrulanmalı <prosesin Bağlandığı ip adresileri incelenmeli>

•	Örnek: cmd.exe veya powershell.exe normalde explorer’den açılır ama svchost.exe veya spoolsv.exe gibi sistem süreçlerinin normal olmayan bir PPID’ye sahip olması şüphelidir.



https://github.com/volatilityfoundation/volatility3/releases

pip install volatility3-2.7.0-py3-none-any.whl
vol -h

-------------------------------------------------------------------------------------------------------------

- Sonradan Öğrendiklerim- 

MALFİND komutunda assembly kısımlarda
jmp 0x1c5ab790000 komutu görülmektedir. Bu, sürecin normal akışını değiştirip başka bir bellek adresine atladığı (hijack) anlamına gelir. 

- windows.hashdump --> Kullanıcı parolalarının hashli hallerini verir. 

- Arana yapabilmek için bilgi edinme
python2 vol.py -f ../Snapshot19_1609159453792.vmem imageinfo
Bu komut ile --profile bilgisi edinilir. 

- cmd'de yazılan komutlar.
python2 vol.py -f ../Snapshot19_1609159453792.vmem --profile=Win7SP1x64 consoles
komut satırında en son neler oldu bunlar izlenebilir.

- Cihaz En son ne zaman kapandı 
python2 vol.py -f ../Snapshot19_1609159453792.vmem --profile=Win7SP1x64 shutdowntime
en son ne zaman kapandı bunu gösterir. 

- En son hangi uygulama açıldı
python2 vol.py -f  ../../victim.raw --profile=Win7SP1x64 shellbags
en son açılan uygulamalar görüntülenir. en yüksek tarih tespit edilir sonrasında grep ile o tarih aranır. Aranan tarihte en son hangi uygulama kapatılmış incelenebilir.


 --profile=Win10x64_17134 consoles



