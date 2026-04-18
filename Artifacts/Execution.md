## 1. REGISTRY ARTIFACT'LARI

```
Artifact Adı → Shimcache (AppCompatCache)
Konum → SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache
Öncelik: Yüksek
Erişim: Her ikisi
Gözlem Notu: Çalıştırılan veya yolu bilinen EXE'lerin son değişiklik zamanı ve dosya yolunu tutar; yeniden başlatmada diske yazılır.
```

```
Artifact Adı → Amcache.hve
Konum → C:\Windows\appcompat\Programs\Amcache.hve
Öncelik: Yüksek
Erişim: Her ikisi
Gözlem Notu: SHA1 hash, tam dosya yolu, boyut, yayıncı ve ilk çalıştırma zamanını içerir.
```

```
Artifact Adı → BAM (Background Activity Moderator)
Konum → SYSTEM\CurrentControlSet\Services\bam\State\UserSettings\{SID}
Öncelik: Yüksek
Erişim: Her ikisi
Gözlem Notu: Her kullanıcı için son çalıştırılan EXE'nin tam yolunu ve UTC zaman damgasını tutar (Win10 1709+).
```

```
Artifact Adı → DAM (Desktop Activity Moderator)
Konum → SYSTEM\CurrentControlSet\Services\dam\State\UserSettings\{SID}
Öncelik: Orta
Erişim: Her ikisi
Gözlem Notu: BAM ile aynı yapıdadır; masaüstü oturumlarındaki çalıştırmaları ayrıca kaydeder.
```
