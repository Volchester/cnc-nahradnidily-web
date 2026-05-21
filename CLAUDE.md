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

## Vícejazyčné verze (EN/DE/PL) — živý checklist

> Plán: `CNC_Web_Vicejazycne_Verze.md`. Realizace EN jako první. Slugy: hlavní stránky lokalizované (co-dodavame→what-we-supply…), detaily lozisko→bearing, remen→belt, davkovac→lubrication-restrictor, repasovani→…-refurbishment, kryci-plechy→telescopic-covers-and-bellows; značky/modely (indramat, yaskawa, euchner, smc) beze změny. Terminologie: „řemen" = **belt** (ne timing belt), „dávkovač mazání" = **lubrication restrictor**. Ceny CZK→EUR kurz 25, zaokrouhlit na desítky, „from €X". Telefon +420, e-mail info@.

**EN verze (`HTML_V1/en/` + `en/dily/`, 24 stránek):**
- [x] Část 1 — struktura, EN slugy, přepis cest (en/→`../`, en/dily/→`../../`) — commit f3ff4dd
- [x] Část 2 — hreflang (cs/en/x-default→en) na všech 48 stránkách, EN absolutní URL na /en/, og:locale en_US, html lang en — commit 183f380
- [x] Část 3 — language picker CZ|EN se SVG vlajkami (symbol/use), localStorage, bez auto-redirectu — commit e4c1f26
- [~] Část 4 — překlad obsahu 24 EN stránek. Ceny CZK→EUR kurz 25 **dolů na desítky** (např. 1480→€50, 19650→€780; sub-€10 case 240→€9). HOTOVO: base kit chrome (vše), **7/7 hlavních stránek** (index, what-we-supply, how-to-inquire, parts-examples, contact, inquiry vč. ?dil mapy+JS, privacy), sdílený chrome 17 detailů + **bearing-20** (vzor). ZBÝVÁ: 16 detailních stránek (bearing-30/35/zarn, belt-400/420/450, rotary-joint, lubrication-restrictor, pneumatic-cylinder, indramat, yaskawa, euchner, smc, spindle-refurbishment, ballscrew-refurbishment, telescopic-covers-and-bellows). Pozn.: dily JSON-LD má `"name": "X"`/`"price"`/`"priceCurrency"` (1 mezera) — přepsat na EUR; mailto subject přepsat na "Inquiry - ...". Bare-replace pozor na JSON-LD/komentáře. HTML komentáře česky = OK (neviditelné).
- [ ] Část 5 — EN URL do sitemap.xml, ověřit robots.txt /en/, validace hreflang
- [ ] Vizuálně ověřit picker na Netlify preview (layout hlavičky, mobilní menu, render SVG vlajek) — Claude nevidí render

**DE a PL verze:** až po vyhodnocení EN (viz plán fáze 3–4).

## Stav nasazení — živý checklist

> **Claude: tenhle seznam udržuj aktuální.** Když nějaký bod dokončíme, přepiš `[ ]` na `[x]` a doplň datum/poznámku. Při startu sezení podle něj uživateli připomeň, co zbývá. Detailní kontext je v projektové paměti (`web-cnc-nahradnidily-projekt.md`).

**Hotovo:**
- [x] Web nasazen na Netlify — `https://magenta-sorbet-f1a326.netlify.app/` (2026-05-20)
- [x] Migrace URL staging→produkce, přesun `Obrázky/` do `HTML_V1/`, `robots.txt` na `Allow: /` (2026-05-20)
- [x] Poptávkový formulář ověřeně funguje (deploy „without cache" → Netlify detekoval `poptavka`, odeslání projde) (2026-05-20)
- [x] Netlify Forms notifikace nastavena na `cncnahradnidily@gmail.com`, notifikační e-mail dorazil (2026-05-20)

**Zbývá (vše manuální, mimo kód):**
- [ ] **OPRAVA DNS DELEGACE (blokátor webu i pošty) — probíhá od 2026-05-20:** Doména je registrovaná u WEDOS (registrátor REG-WEDOS), ale nameservery (`ns1/ns2.register.it`) i DNSSEC keyset zůstaly u Webnode → Webnode NS odmítají zónu + starý DNSSEC klíč (KeyTag 4789) → doména je SERVFAIL/nedostupná (proto web jede jen přes `magenta-sorbet-f1a326.netlify.app` a `info@` nefunguje). Provedeno ve WEDOS 2026-05-20: [x] NSSET → „Výchozí NSSET WEDOS", [x] DNSSEC → „použít DNSSEC WEDOS" + automatická správa. **Čeká se na propagaci u CZ.NIC (až 6 h).** Ověřit: `nslookup -type=NS cnc-nahradnidily.cz a.ns.nic.cz` (má ukázat WEDOS NS místo register.it) + `Resolve-DnsName cnc-nahradnidily.cz -Server 8.8.8.8` (nesmí být SERVFAIL).
- [ ] Připojit doménu `cnc-nahradnidily.cz` v Netlify dashboardu (DNS / nameservery) — A/CNAME se přidávají ve WEDOS DNS až po propagaci výše
- [ ] **E-mail domény u WEDOS** (info@ nefunguje od přechodu z Webnode):
  - [ ] zřídit u Vedos schránku/přesměrování `info@cnc-nahradnidily.cz` → `cncnahradnidily@gmail.com` + správné MX záznamy
  - [ ] Gmail „Odesílat jako" `info@` přes SMTP Vedos (port 587 TLS; ověřovací mail dorazí přes forwarding)
  - [ ] SPF / DKIM / DMARC v DNS, ať pošta nepadá do spamu
  - [ ] (volitelně) přepnout Netlify Forms notifikaci z gmailu zpět na `info@`, až bude funkční
- [ ] Google Search Console: přidat property pro `cnc-nahradnidily.cz`, ověřit, submit `sitemap.xml`
- [ ] Vypnout starý GitHub Pages staging (Settings → Pages → Source: None)
- [ ] Ověřit placeholdery v `ochrana-osobnich-udaju.html` (doba uchování 24 měsíců, datum účinnosti 11.5.2026)

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
