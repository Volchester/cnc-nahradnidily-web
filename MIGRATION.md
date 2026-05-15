# Migrace ze staging GitHub Pages na produkční cnc-nahradnidily.cz

Aktuálně web běží na **https://volchester.github.io/cnc-nahradnidily-web/HTML_V1/** (staging, GitHub Pages).
Cíl: nasadit na **https://cnc-nahradnidily.cz/** (Netlify), vypnout GH Pages.

## 1. Find & Replace URL ve všech souborech

Hromadně nahradit (zachovat URL-encoding tam, kde je):

| Staging | Produkce |
|---|---|
| `https://volchester.github.io/cnc-nahradnidily-web/HTML_V1/` | `https://cnc-nahradnidily.cz/` |
| `https://volchester.github.io/cnc-nahradnidily-web/Obrázky/` | `https://cnc-nahradnidily.cz/Obrázky/` |
| `https://volchester.github.io/cnc-nahradnidily-web/Obr%C3%A1zky/` | `https://cnc-nahradnidily.cz/Obr%C3%A1zky/` |

**Týká se těchto souborů:**
- 7 HTML stránek v `HTML_V1/`:
  - `<link rel="canonical" href="...">`
  - `<meta property="og:url" content="...">`
  - `<meta property="og:image" content="...">`
  - `<meta name="twitter:image" content="...">`
- `HTML_V1/sitemap.xml` (vznikne v úkolu 3) — `<loc>` URL všech stránek
- `HTML_V1/robots.txt` (vznikne v úkolu 3) — `Sitemap:` direktiva
- JSON-LD strukturovaná data (vzniknou v úkolu 4):
  - `Organization.url`
  - `Product.url` / `Product.offers.url`
  - `BreadcrumbList.itemListElement[*].item`

## 2. Netlify: kam s `/Obrázky/`

`netlify.toml` má `publish = "HTML_V1"` → publikuje se jen obsah `HTML_V1/`, nikoli `Obrázky/` z rootu repa.

Po migraci je třeba zvolit:
- **A)** Přesunout `Obrázky/` do `HTML_V1/Obrázky/` a upravit všechny relativní cesty `../Obr%C3%A1zky/` → `Obr%C3%A1zky/` v HTML (nyní v `ukazky-dilu.html` a dalších).
- **B)** Změnit `netlify.toml` na `publish = "."` a do robots.txt přidat `Disallow: /CNC_Web_*` (kvůli interním .md souborům, které už jsou v `.gitignore` ale jen pro git, ne pro Netlify build).
- **C)** Použít Netlify `redirects` / `headers` rule pro `/Obrázky/*` → externí storage (overkill).

Doporučení: **A** (jednodušší, čistší produkce).

## 3. Vypnutí GitHub Pages

- Settings → Pages → Source: None
- Volitelně: smazat repo `Volchester/cnc-nahradnidily-web` nebo nechat jako private mirror

## 4. Po nasazení

- Google Search Console: přidat property pro `cnc-nahradnidily.cz`, ověřit, submit `sitemap.xml`
- Facebook Sharing Debugger: refresh og:image cache (`https://developers.facebook.com/tools/debug/`)
- Twitter Card Validator: ověřit
- Aktualizovat `MEMORY.md` Claude (hosting GH→Netlify, doména změněna)
- Smazat staging URL z případných externích odkazů

## 5. Co NEMUSÍ řešit migrace

- Texty (CNC_Web_Texty.md, .md soubory) — už v `.gitignore`
- Lokální obrázky `Obrázky/*.jpg|.png|.webp` — přesouvají se s repem
- Netlify Forms — funguje stejně po deploy (form-name `poptavka`)
- Cookie banner — funguje na obou doménách (localStorage je per-doména, takže uživatelé znovu odsouhlasí, to je OK)
