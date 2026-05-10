zeek -r trafik.pcap

## `conn` — Kim, kiminle, hangi porttan, ne kadar süre konuştu?

| Alan | Açıklama |
|---|---|
| `orig_h` | Bağlantıyı başlatan IP |
| `resp_h` | Hedef IP |
| `orig_p` | Kaynak port |
| `resp_p` | Hedef port |
| `duration` | Bağlantı süresi |
| `service` | Zeek'in tahmin ettiği protokol |
| `conn_state` | Bağlantı başarılı mı, yarım mı, başarısız mı? |

---

## `http` — Bir HTTP isteği ve cevabını tek kayıtta özetler

| Alan | Açıklama |
|---|---|
| `host` | Gidilen domain |
| `uri` | İstenen dosya / yol |
| `user_agent` | İsteği yapan araç |
| `status_code` | HTTP sonucu (200, 404 vb.) |

---

## `files` — Ağ üzerinden taşınan dosya

| Alan | Açıklama |
|---|---|
| `fuid` | Dosya için benzersiz ID |
| `mime_type` | Dosya tipi |
| `seen_bytes` | Görülen boyut |

---

## `software` — Hostların kullandığı uygulamalar

- Browser adı
- Yazılım adı

---

## `known_hosts` — Bu ağda hangi makineler görüldü?

---

## `weird` — Protokolde beklenmeyen / bozuk davranışlar

---

## `stats` — Zeek'in çalışma istatistikleri

- `capture_loss` → PCAP'te eksik trafik var mı?

---

## `alert` — Suricata şüpheli davranışları
