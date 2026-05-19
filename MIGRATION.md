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
- 7 HTML stránek v `HTML_V1/` (index, co-dodavame, jak-poptat, ukazky-dilu, kontakty, poptavka, ochrana-osobnich-udaju, 404):
  - `<link rel="canonical" href="...">`
  - `<link rel="alternate" hreflang="cs" href="...">`
  - `<meta property="og:url" content="...">`
  - `<meta property="og:image" content="...">`
  - `<meta name="twitter:image" content="...">`
- **16 detail stránek v `HTML_V1/dily/`** (stejná pole jako výše, plus JSON-LD URL):
  - `dily/lozisko-20tac47csuhpn7c.html`
  - `dily/lozisko-30tac62csuhpn7c.html`
  - `dily/lozisko-35tac72bsu-c10.html`
  - `dily/lozisko-zarn-2557-tn.html`
  - `dily/rotacni-pruchodka-kjc-6314-20.html`
  - `dily/davkovac-mazani-cpv2.html`
  - `dily/indramat-tdm-3-2-030-300-w1.html`
  - `dily/yaskawa-hv-75ap4.html`
  - `dily/pneumaticky-valec-sun-3000kg.html`
  - `dily/remen-400-5gt-40.html`
  - `dily/remen-420-5gt-40.html`
  - `dily/remen-450-5gt-40.html`
  - `dily/repasovani-vretene.html`
  - `dily/repasovani-kulickoveho-sroubu.html`
  - `dily/kryci-plechy-a-mechy-na-miru.html`
  - `dily/euchner-sn02d12-502-mc1688.html`
- **PDF přílohy v `HTML_V1/Dokumenty/`** (interní odkazy v `kryci-plechy-a-mechy-na-miru.html` — relativní `../Dokumenty/...`, žádné absolutní URL k přepsání. Po migraci dostupné na `https://cnc-nahradnidily.cz/Dokumenty/...`):
  - `Dokumenty/Specifikace_krytu_lamelove.pdf`
  - `Dokumenty/Specifikace_krytu_plechove.pdf`
- `HTML_V1/sitemap.xml` — `<loc>` URL všech 23 stránek (7 hlavních + 16 dily)
- `HTML_V1/robots.txt`:
  - `Sitemap:` direktiva
  - **přepnout `Disallow: /` zpět na `Allow: /`** (staging blokuje indexaci, produkce povoluje)
  - smazat komentář o staging na prvních 3 řádcích
- JSON-LD strukturovaná data (ve všech 14 dily/ stránkách):
  - `Organization.url` / `Service.provider.url`
  - `Product.image` / `Service.image`
  - `Product.offers.url` / `Service.offers.url`
  - `BreadcrumbList.itemListElement[*].item` (3 položky × 14 stránek)

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

## 5. 404 stránka

`HTML_V1/404.html` existuje. Na GitHub Pages staging **není auto-discovered** (GitHub hledá `404.html` v rootu repa, ne v `HTML_V1/`). Pro test stránky: navštívit `volchester.github.io/cnc-nahradnidily-web/HTML_V1/404.html` přímo.

Po migraci na **Netlify** (`publish="HTML_V1"`) bude `HTML_V1/404.html` automaticky použito pro neplatné URL — Netlify ho detekuje jako root `/404.html`.

## 6. Co NEMUSÍ řešit migrace

- Texty (CNC_Web_Texty.md, .md soubory) — už v `.gitignore`
- Lokální obrázky `Obrázky/*.jpg|.png|.webp` — přesouvají se s repem
- Netlify Forms — funguje stejně po deploy (form-name `poptavka`)
- Cookie banner — funguje na obou doménách (localStorage je per-doména, takže uživatelé znovu odsouhlasí, to je OK)
