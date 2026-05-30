# nuha-data

Nuha uygulamasının runtime veri kaynağı. jsdelivr CDN üzerinden
sunulur; uygulama bundle'ında yer almaz, kullanıcı ihtiyaç duydukça lazy
indirilir.

## Erişim

```
https://cdn.jsdelivr.net/gh/tnc0-dev/nuha-data@main/{path}
```

Production'da `@main` yerine sabit bir tag/sha kullanılması önerilir
(uzun cache busting). Uygulamada şu an `@main` referansı var
(`lib/data-host.ts` → `GIT_REF`).

## Klasör yapısı

```
nuha-data/
├── words/
│   └── 001.json..114.json              # Arapça + transliterasyon + EN + TR
├── words-wbw/
│   └── {lang}/001.json..114.json       # 16 dilde kelime-kelime overlay
│                                       # diller: ur bn id fa hi ta fr de
│                                       #         ml dv sd sq zh ru es ms
├── morphology/
│   └── 001.json..114.json              # Kelime morfolojisi
├── tafsir/
│   ├── json/{slug}/{1..114}.json       # Per-sure JSON tefsir
│   └── sqlite/{file}.{db|zip}          # SQLite (.db) veya zipli SQLite
└── translations/
    └── {edition-id}.min.json
```

## Veri formatları

### `words/{NNN}.json`

```json
{
  "surah": 1,
  "ayahs": {
    "1": [
      {
        "position": 1,
        "text_arabic": "بِسْمِ",
        "transliteration": "bismi",
        "translation": "In the name",
        "translation_tr": "adıyla"
      }
    ]
  }
}
```

### `words-wbw/{lang}/{NNN}.json`

Sadece çeviri dizisi — `words/{NNN}.json`'daki `position` ile eşleşir.

```json
{
  "1": ["birinci kelime", "ikinci kelime"],
  "2": ["..."]
}
```

### `morphology/{NNN}.json`

```json
{
  "surah": 1,
  "ayahs": {
    "1": [
      { "p": 1, "text": "بِسْمِ", "pos": "N", "root": "س م و" }
    ]
  }
}
```

### `tafsir/json/{slug}/{N}.json`

Per-sure JSON: tek dosya = bir sure.

```json
{
  "ayahs": [
    { "ayah": 1, "text": "..." }
  ]
}
```

### `tafsir/sqlite/{file}.db|.zip`

SQLite virtual tablo `verses(sura, ayah, text)`. Tek dosya = tüm Kur'an.
Zipli sürümler `.zip` içinde bir `.db` taşır.

### `translations/{edition-id}.min.json`

```json
{
  "quran": [
    { "chapter": 1, "verse": 1, "text": "..." }
  ]
}
```

## Uygulama URL'leri

Tüm yollar `lib/data-host.ts` üzerinden tek noktadan üretilir. Yüklenmemiş
bir yol istenirse uygulama 404 alır ve kullanıcı dostu bir hata mesajı
gösterilir.
