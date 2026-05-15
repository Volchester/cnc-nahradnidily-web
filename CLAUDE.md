# Pravidla pro Claude Code v tomto projektu

Tento soubor Claude Code automaticky načítá při každém startu sezení v adresáři projektu. Drž se těchto pravidel.

---

## Workflow

- **Před každým úkolem napiš plán a počkej na výslovný souhlas** uživatele. Nezačínej implementaci, dokud souhlas nezazní (typicky „ano" / „souhlas" / „pokračuj").
- **Po každém schváleném úkolu udělej commit + push na `origin/main` ihned**, nebatchuj víc úkolů do jednoho commitu.
- Commit message piš v HEREDOC formátu, ať se nepokazí CR/LF a české znaky:
  ```bash
  git commit -m "$(cat <<'EOF'
  Krátký popis (1 řádek)

  Detail: co a proč.
  EOF
  )"
  ```
- **Nedělej destruktivní git operace** (`reset --hard`, `push --force`, smazání větve) bez výslovného svolení uživatele.
- **Nezavádět feature flags, fallbacky, „co kdyby"** — řešit jen to, co je v aktuálním zadání. Žádný overengineering.
- Před opravou „bugu" si ověř, že to není záměr (např. anonymita firmy je vědomá, ne náhoda).

## Struktura projektu

- **Veřejné HTML stránky:** `HTML_V1/` (7 hlavních + `404.html`)
- **Obrázky:** `Obrázky/` v rootu repa. Cesty v HTML: `../Obr%C3%A1zky/...` (URL-encoded české znaky)
- **Soubory v `.gitignore`:** `CNC_Web_Prompt_pro_Claude_Design.md`, `CNC_Web_Texty.md` (obsahují interní zadání / jméno firmy), `.claude/`
- **`MIGRATION.md`** v rootu — checklist pro pozdější přechod ze staging na produkční doménu
- Každá stránka má **vlastní inline `<style>`** s base kit kopií (vars, nav, page-hero, footer) + page-specific pravidla

## Tvrdé omezení — anonymita

- **Nikde neuvádět „Šikl Servis s.r.o."** kromě `ochrana-osobnich-udaju.html` (správce osobních údajů — vyplývá z GDPR)
- Žádné jméno týmu, počty lidí, fotky tváří, životopisy
- Hlavička/patička = jen značka „cnc-nahradnidily.cz" a logo wordmark
- **Texty z `CNC_Web_Texty.md` brát doslova, neparafrázovat**

## Brand

| Token | Hodnota | Použití |
|---|---|---|
| `--brand-deep` | `#042C53` | hlavička, footer, hero pozadí |
| `--brand-primary` | `#0C447C` | sekundární tmavá |
| `--brand-accent` | `#185FA5` | linky, pretitle |
| `--accent-warm` | `#EF9F27` | primární CTA tlačítka |
| `--surface-soft` | `#F1EFE8` | alt section background |
| Logo navy | `#1A365D` | favicon, logo dle brand book |
| Logo cream | `#E6EEF8` | "CNC" text v wordmark na tmavém pozadí |
| Logo gray | `#9AA3AE` | "nahradnidily.cz" subtitle v wordmark |

- **Logo wordmark:** „CNC" (Archivo 700, 22 px) + „nahradnidily.cz" (Archivo 500, 10 px, letter-spacing 0.08em, lowercase). Vertikální stack.
- **Favicon:** navy čtverec + bílé „C" (jednopísmenné, ne „CN"). 3 soubory: `favicon.svg`, `favicon-32.png`, `apple-touch-icon.png`.
- `--brand-deep` zůstává `#042C53`. Brand book říká `#1A365D` ale stávající web má `#042C53` — neměnit globálně bez výslovné žádosti uživatele.

## SEO / Accessibility — povinné pro nové stránky

**`<head>`:**
- `<title>`, `<meta name="description">`
- `<link rel="canonical">` na staging URL (po migraci přepsat dle MIGRATION.md)
- og:* (title, description, type, url, image, locale=cs_CZ, site_name)
- `<meta name="twitter:card" content="summary_large_image">` + twitter:title/description/image
- 3× favicon link (SVG + 32 PNG + apple-touch-icon)
- Google Fonts: `Inter` + `Archivo:wght@500;700`
- JSON-LD `<script type="application/ld+json">` s Organization, případně BreadcrumbList / Product

**Images:**
- `loading="lazy"` na všechny `<img>` **kromě hero** (eager pro above-the-fold)
- **Decorative** image: `alt="" + aria-hidden="true"` (na img nebo parent). Žádný popis pro screen reader — to je WCAG best practice.
- **Content** image: meaningful alt (např. „Rotační průchodka KJC 6314-20")

**Navigation:**
- `aria-current="page"` na aktivní položku **v desktop nav-links i mobile-menu**
- Pokud je stránka jen v footer-bottom-links (legal pages), tam je aria-current OK; v hlavní nav nic

**Cookie banner:** na všech HTML stránkách (lišta dole, „Pouze nezbytné" / „Přijmout vše", localStorage `cookies_accepted`). Výjimka: `404.html` (přechod, ne destination).

## Deployment

- **Aktuální staging:** GitHub Pages — `https://volchester.github.io/cnc-nahradnidily-web/HTML_V1/`
- **Produkce (plánovaná):** `cnc-nahradnidily.cz` na Netlify s `publish="HTML_V1"` (viz `netlify.toml`)
- **`robots.txt`** na stagingu má `Disallow: /` (zamezit indexaci staging URL Googlem) — při migraci přepnout na `Allow: /` (viz `MIGRATION.md` sekce 1)
- **Při migraci** hromadný find & replace URL napříč HTML + sitemap.xml + robots.txt + JSON-LD — viz `MIGRATION.md`
- **404.html:** na GH Pages není auto-discovered (GitHub hledá v rootu repa, ne v `HTML_V1/`); po migraci na Netlify s `publish="HTML_V1"` bude `/404.html` aktivní automaticky

## Tooling (Windows + PowerShell + Git Bash)

**Pro batch operace přes víc souborů:**
- **PowerShell 5.1** + `[System.IO.File]::ReadAllText/WriteAllText` s `[System.Text.UTF8Encoding]::new($false)` (bez BOM)
- Před spuštěním PS skriptu `Set-Location $absoluteRoot` (working dir může být zděděný z předchozího `cd` v Bash)

**PowerShell gotchas:**
- `$var:` se parsuje jako drive reference → použij `${var}:` nebo `"$var" + ":"` nebo `("text: " + $var)`
- `New-Object Font 'Name', $size, [FontStyle]::Bold` může selhat na enum binding → použij `[System.Drawing.Font]::new(...)` ([Type]::new() syntax)
- Default file encoding v PS 5.1 je UTF-16 LE s BOM → vždy explicitně `UTF8Encoding::new($false)` pro web soubory
- `ConvertTo-Json` v PS 5.1 dělá weird indentaci s 2-space mezerami po `:` a deepně zarovnanými array. Validní JSON, ale pro readability volitelně post-process

**Pro git:**
- **Bash tool** preferuj pro git operace (HEREDOC funguje, CRLF warnings ignoruj — jsou jen pro line-ending nastavení)
- `git status` před každým commitem, ať vidíš co stage'uješ
- Specifické `git add <files>`, ne `git add .` (nezahrnuj nechtěné soubory typu `logo/`, untracked test files atd.)

**Pro generování PNG z Czech-containing SVG:**
- ImageMagick / Inkscape **nejsou** dostupné. Použij **PowerShell + System.Drawing**:
  - `SmoothingMode::AntiAlias` + `TextRenderingHint::AntiAliasGridFit`
  - `MeasureString` před `DrawString` pro precizní centrování
  - `StringFormat.Alignment = Center, LineAlignment = Center`

## Memory

Memory pro tento projekt je v:
```
C:\Users\franc\.claude\projects\C--Users-franc-Desktop-Claude-Projekty-Web-CNC-d-ly\memory\
```
- `MEMORY.md` — index všech memory souborů
- `web-cnc-nahradnidily-projekt.md` — projektová paměť (stav stránek, rozhodnutí, deployment plán)

**Aktualizuj memory při:**
- Změně deployment strategie nebo domény
- Brand decisions (nová barva, font, logo varianta)
- Nových otevřených TODO nebo dokončených milestones
- Korekci dříve nepravdivých informací

Při čtení memory ber v úvahu její stáří — claims o `file:line` a konkrétních datech mohou být zastaralé. Ověř aktuální stav v kódu.

## Co NEdělat bez explicitní žádosti

- Měnit `--brand-deep` na `#1A365D` napříč webem
- Mazat `.jpg` duplicity v `Obrázky/` (existují vedle `.jpeg` verzí z CDN download)
- Commitovat složku `logo/` (brand book PDF/PNG/HTML jako reference — uživatel se rozhodne sám)
- Měnit `netlify.toml` `publish` setting
- Vypnout GitHub Pages
- Změnit text z `CNC_Web_Texty.md`
