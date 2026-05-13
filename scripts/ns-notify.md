# ns-notify

> Western-temalı, standalone bildirim sistemi — RedM/FiveM uyumlu.

Parşömen kağıdı dokusu, Rye + IM Fell DW Pica fontları, pirinç kaplama gibi gold detaylar. Wanted poster estetiği.

## Özellikler

- **7 notif tipi:** `info`, `warning`, `error`, `job`, `sheriff`, `doctor`, `bandit` — her birine özgü renk, ikon, ses ve western etiket (TELGRAF, İKAZ, TEHLİKE, GÖREV, KANUN, ŞİFACI, HAYDUT)
- **9 pozisyon** (3x3 grid) — oyuncu seçer, **FiveM KVP** ile yerel kaydedilir
- **3 animasyon:** `slide` / `fade` / `pop` — oyuncu seçer
- **Progress bar** (timeout indicator) ve **per-type sound effect**
- **Stack davranışı** — birden çok notif üst üste birikir
- **Standalone** — framework bağımsız (ns-lib dependency YOK)
- **Hibrit API:** `show()` (kısa) + `advanced()` (detaylı)

## Kurulum

1. `ns-notify` klasörünü sunucuya kopyala (`resources/[mr_prof]/ns-notify`).
2. `server.cfg`'ye ekle: `ensure ns-notify`.
3. (Opsiyonel) `config.lua`'dan tip renklerini, ikonları, sesleri özelleştir.
4. **İkonlar:** `html/icons/<type>.png` — kendi western temalı PNG'lerini ekle (info, warning, error, job, sheriff, doctor, bandit). İkon eksikse otomatik fallback (tip etiketinin baş harfi) gösterilir.

## Komutlar

| Komut | Açıklama |
|---|---|
| `/notifsettings` | Pozisyon ve animasyon ayar paneli (NUI) |
| `/notiftest <tip> [süre]` | Test notif gönder. Örn: `/notiftest sheriff 5000` |

## Kullanım

### Client
```lua
-- Kısa
exports['ns-notify']:show('info', 'Para alındı')
exports['ns-notify']:show('warning', 'Düşük can', 6000)

-- Detaylı
exports['ns-notify']:advanced({
    type     = 'sheriff',
    title    = 'Yeni Görev',
    message  = "Valentine'da soygun var, partner.",
    duration = 5000,
})
```

### Server
```lua
-- Tek oyuncu
exports['ns-notify']:show(source, 'warning', 'Düşük can')

exports['ns-notify']:advanced(source, {
    type    = 'sheriff',
    title   = 'Görev',
    message = '...',
})

-- Tüm oyunculara
exports['ns-notify']:showAll('info', 'Sunucu yeniden başlıyor')

-- Veya net event
TriggerClientEvent('ns-notify:show', source, {
    type = 'info', message = 'Hoş geldin partner',
})
```

## Tipler & Renkler

| Tip | Etiket | Renk |
|---|---|---|
| `info`    | TELGRAF   | Aged gold (#C9A961) |
| `warning` | İKAZ      | Rust amber (#D97706) |
| `error`   | TEHLİKE   | Barn red (#991B1B) |
| `job`     | GÖREV     | Forest green (#15803D) |
| `sheriff` | KANUN     | Sheriff blue (#1E3A8A) |
| `doctor`  | ŞİFACI    | Cyan (#0E7490) |
| `bandit`  | HAYDUT    | Coal (#1F1B16) |

## Konfigürasyon

`config.lua` içinde:

| Anahtar | Açıklama |
|---|---|
| `Config.DefaultDuration` | Varsayılan süre (ms) |
| `Config.DefaultPosition` | Yeni oyuncu için varsayılan pozisyon |
| `Config.DefaultAnimation` | Varsayılan animasyon |
| `Config.Types[<type>].color` | Tip rengi (hex) |
| `Config.Types[<type>].icon` | İkon yolu (`html/...`) |
| `Config.Types[<type>].sound` | RDR `HUD_DEAD_HORSE_SOUNDSET` ses adı |
| `Config.Types[<type>].label` | Notif başında gösterilen etiket |
| `Config.ProgressBar` | Timeout barı açık/kapalı |
| `Config.PlaySound` | Ses açık/kapalı |

## Mimari

```
ns-notify/
├── fxmanifest.lua
├── config.lua              # Tüm ayarlar tek dosya
├── exports.lua             # Public API (client+server)
├── client/
│   ├── main.lua            # NUI bridge, Show/Advanced
│   ├── settings.lua        # KVP + /notifsettings
│   └── commands.lua        # /notiftest
├── server/
│   └── main.lua            # Server-side relay
└── html/
    ├── index.html          # Settings panel + container
    ├── style.css           # Western parchment theme
    ├── script.js           # NUI message handler
    └── icons/              # PNG ikonlar (kullanıcı eklemeli)
```

## Notlar

- **Standalone** — VORP/RSG/ESX gibi framework gerekmez.
- **Sound** RDR'nin `HUD_DEAD_HORSE_SOUNDSET` paletinden gelir; özel ses isimleri `config.lua`'da değiştirilebilir.
- **Fontlar** Google Fonts'tan yüklenir (Rye + IM Fell DW Pica). NUI internet erişimi olmayan ortamlarda fallback `Georgia` serif kullanılır.
- **NUI focus** sadece `/notifsettings` açıkken aktif olur — notif gösterimi sırasında oyuncu hareketi kısıtlanmaz.
