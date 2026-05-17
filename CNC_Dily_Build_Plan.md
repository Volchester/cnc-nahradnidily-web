# Build Plan: CNC díly produktové stránky

> **Tento soubor používá Claude Code jako pracovní seznam.** Postupně zpracovává úkoly označené `[ ]` zhora dolů. Po dokončení každého úkolu zaškrtne `[x]` a počká na souhlas s pokračováním.

---

## INSTRUKCE PRO CLAUDE CODE

**Spouštěcí povel od uživatele:** *„Přečti CNC_Dily_Build_Plan.md a začni plnit úkoly. Po každém úkolu se zastav."*

**Postup pro každý úkol:**

1. **Najdi první úkol s `[ ]`** (nezaškrtnutý) ve sekci „ÚKOLY" níže.
2. **Přečti specifika úkolu** + odpovídající sekci v `../CNC_Dily_Texty.md` (zdroj textů).
3. **Napiš uživateli plán** — co konkrétně budeš dělat, jaký obrázek použiješ, jaké URL atd. **Počkej na schválení.**
4. **Pokud uživatel řekne „pokračuj"** — implementuj podle plánu.
5. **Commit** s descriptive message (např. `feat: produktová stránka lozisko-20tac47csuhpn7c`).
6. **V tomto souboru** změň `[ ]` na `[x]` u dokončeného úkolu (jedním samostatným commitem `chore: označen úkol N jako hotový` nebo přibal do hlavního commitu).
7. **Pošli uživateli URL na GH Pages s cache-busterem** ve formátu `https://volchester.github.io/cnc-nahradnidily-web/HTML_V1/dily/[slug].html?v=[hash]`
8. **STOP.** Nepřecházej automaticky na další úkol. Počkej, až uživatel řekne *„pokračuj"* nebo *„další"*.

**Pokud narazíš na problém:** chybějící obrázek, nejasnost v textu, technický blok — napiš uživateli a počkej na řešení. Neimprovizuj.

---

## SPOLEČNÉ POŽADAVKY PRO VŠECHNY ÚKOLY

### Zdrojový soubor textů
`../CNC_Dily_Texty.md` (v rootu projektu, ne v `HTML_V1/`). Sekce číslované 1–14, každá odpovídá jedné stránce.

### Cílový adresář
`HTML_V1/dily/` (vytvoř, pokud neexistuje).

### URL slugy (kanonické)

| Sekce | Slug |
|---|---|
| 1 | `lozisko-20tac47csuhpn7c.html` |
| 2 | `lozisko-30tac62csuhpn7c.html` |
| 3 | `lozisko-35tac72bsu-c10.html` |
| 4 | `lozisko-zarn-2557-tn.html` |
| 5 | `rotacni-pruchodka-kjc-6314-20.html` |
| 6 | `davkovac-mazani-cpv2.html` |
| 7 | `indramat-tdm-3-2-030-300-w1.html` |
| 8 | `yaskawa-hv-75ap4.html` |
| 9 | `pneumaticky-valec-sun-3000kg.html` |
| 10 | `remen-400-5gt-40.html` |
| 11 | `remen-420-5gt-40.html` |
| 12 | `remen-450-5gt-40.html` |
| 13 | `repasovani-vretene.html` |
| 14 | `repasovani-kulickoveho-sroubu.html` |

### Struktura HTML stránky DÍLU (sekce 1–12)

1. `<head>` — meta tagy, SEO, JSON-LD (viz níže)
2. Hlavička + navigace (identická s ostatními stránkami webu)
3. Breadcrumb: `Úvod > Ukázky dílů > [Název dílu]`
4. Hero — h1 (název dílu), krátký lead, produktový obrázek vpravo
5. Sekce „Popis produktu" (text z CNC_Dily_Texty.md, někdy 2 odstavce)
6. Sekce „Technické parametry" — tabulka (zebra řádky, brand barvy headeru)
7. Sekce „Použití a kompatibilita"
8. Sekce „Cena a dostupnost" — velká cena + štítek dostupnosti + CTA tlačítko *„Poptat tento díl"* linkující na `../poptavka.html?dil=[slug]` (bez `.html` koncovky v parametru)
9. Patička (identická s ostatními stránkami)

### Struktura HTML stránky SLUŽBY (sekce 13–14)

1. `<head>` — meta tagy, SEO, JSON-LD (Service schema)
2. Hlavička + navigace
3. Breadcrumb: `Úvod > Ukázky dílů > [Název služby]`
4. Hero — h1, krátký lead, velký obrázek
5. Sekce „O službě" (úvodní 1–2 odstavce)
6. Sekce „Kdy zvolit repas a kdy koupi nového [vřetene/šroubu]" — rozhodovací tabulka
7. Sekce „Jak proces probíhá" — číslovaný seznam kroků (pro vřeteno 7 kroků včetně záběhu)
8. Sekce „Co repasujeme" — bullet list + omezení
9. Sekce „Co od vás potřebujeme" — bullet list
10. Sekce „Cena a dodací doba" — velká cena + CTA *„Poptat repas"* linkující na `../poptavka.html?dil=[slug]`
11. Patička

### SEO meta v `<head>` (každá stránka)

```html
<title>[Název dílu] — Náhradní díly CNC | cnc-nahradnidily.cz</title>
<meta name="description" content="[lead text, max 160 znaků]">
<meta property="og:title" content="[Název dílu]">
<meta property="og:description" content="[lead text]">
<meta property="og:image" content="https://volchester.github.io/cnc-nahradnidily-web/HTML_V1/Obrázky/[obrázek]">
<meta property="og:url" content="https://volchester.github.io/cnc-nahradnidily-web/HTML_V1/dily/[slug]">
<meta property="og:type" content="product">
<link rel="canonical" href="https://volchester.github.io/cnc-nahradnidily-web/HTML_V1/dily/[slug]">
<link rel="alternate" hreflang="cs" href="...stejná URL...">
```

### JSON-LD pro DÍLY (sekce 1–12)

```json
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "[Název dílu]",
  "image": "[absolutní URL obrázku]",
  "description": "[lead text]",
  "brand": { "@type": "Brand", "name": "[NSK / INA / Gates / Yaskawa / atd.]" },
  "sku": "[part-number]",
  "offers": {
    "@type": "Offer",
    "price": "[cena číslo bez mezer a Kč]",
    "priceCurrency": "CZK",
    "availability": "https://schema.org/InStock | PreOrder",
    "priceValidUntil": "2026-12-31"
  }
}
```

### JSON-LD pro SLUŽBY (sekce 13–14)

```json
{
  "@context": "https://schema.org",
  "@type": "Service",
  "name": "[Název služby]",
  "description": "[lead text]",
  "provider": {
    "@type": "Organization",
    "name": "Šikl Servis s.r.o.",
    "url": "https://cnc-nahradnidily.cz"
  },
  "areaServed": ["CZ", "SK", "EU"],
  "offers": {
    "@type": "Offer",
    "price": "[cena]",
    "priceCurrency": "CZK"
  }
}
```

### Po každé stránce

1. Aktualizuj `HTML_V1/sitemap.xml` — přidej novou URL s `priority 0.7`, `changefreq monthly`, `lastmod` dnešní datum.
2. Ověř, že stránka má všechny lazy-loading atributy, alt texty, aria-current na navigaci.
3. Zaškrtni úkol v tomto souboru.

### Obrázky — pravidlo

Použij přesný název souboru ze složky `HTML_V1/Obrázky/` (pozor na diakritiku, mezery, velikost písmen). Pokud nenajdeš odpovídající obrázek, **napiš uživateli a počkej**.

### Design konzistence

Drž se brand identity webu: tmavá námořnická, krémová, oranžová akcent. Stejná typografie, mezery, mřížka jako `ukazky-dilu.html`.

---

## ÚKOLY

### [x] ÚKOL 1: Pilot — Ložisko 20TAC47CSUHPN7C

- **URL:** `HTML_V1/dily/lozisko-20tac47csuhpn7c.html`
- **Text:** sekce 1 v `CNC_Dily_Texty.md`
- **Obrázek:** `/Obrázky/Ložiska NSK.*` (najdi přesný název)
- **Cena pro JSON-LD:** `1480` CZK, `InStock`
- **Brand:** NSK
- **POZN:** Tohle je pilot — pečlivě navrhni template stránky, který budeme používat pro všechny další díly. Po schválení ho použijeme jako vzor.

### [x] ÚKOL 2: Ložisko 30TAC62CSUHPN7C

- **URL:** `HTML_V1/dily/lozisko-30tac62csuhpn7c.html`
- **Text:** sekce 2
- **Obrázek:** `/Obrázky/Ložiska NSK.*` (stejný generický NSK obrázek)
- **Cena:** `1950` CZK, `InStock`
- **Brand:** NSK

### [x] ÚKOL 3: Ložisko 35TAC72BSU C-10

- **URL:** `HTML_V1/dily/lozisko-35tac72bsu-c10.html`
- **Text:** sekce 3
- **Obrázek:** `/Obrázky/Ložiska NSK.*`
- **Cena:** `2450` CZK, `InStock`
- **Brand:** NSK

### [x] ÚKOL 4: Ložisko ZARN 2557 TN INA

- **URL:** `HTML_V1/dily/lozisko-zarn-2557-tn.html`
- **Text:** sekce 4
- **Obrázek:** `/Obrázky/ZARN_lozisko.*` (specifický). Pokud neexistuje, použij `Ložiska NSK.*` a napiš mi.
- **Cena:** `7650` CZK, `PreOrder` (objednávkové, 2–3 týdny)
- **Brand:** INA (Schaeffler)

### [x] ÚKOL 5: Rotační průchodka KJC 6314-20

- **URL:** `HTML_V1/dily/rotacni-pruchodka-kjc-6314-20.html`
- **Text:** sekce 5 (POZOR: 2 odstavce popisu — druhý o náhradě za Deublin)
- **Obrázek:** `/Obrázky/Rotary Joint.*`
- **Cena:** `19650` CZK, `InStock`
- **Brand:** KJC

### [x] ÚKOL 6: Dávkovač mazání CPV2

- **URL:** `HTML_V1/dily/davkovac-mazani-cpv2.html`
- **Text:** sekce 6
- **Obrázek:** ověř ve `/Obrázky/`. Pokud chybí, napiš mi a navrhni řešení (placeholder ikona nebo generický obrázek).
- **Cena:** `240` CZK, `InStock`
- **Brand:** ponech v JSON-LD prázdné nebo uveď „výrobce dle specifikace"

### [x] ÚKOL 7: Indramat TDM 3.2-030-300-W1

- **URL:** `HTML_V1/dily/indramat-tdm-3-2-030-300-w1.html`
- **Text:** sekce 7
- **Obrázek:** ověř ve `/Obrázky/`. Pokud chybí, napiš mi.
- **Cena:** `22000` CZK, `PreOrder`
- **Brand:** Indramat (Bosch Rexroth)

### [x] ÚKOL 8: Yaskawa HV-75AP4

- **URL:** `HTML_V1/dily/yaskawa-hv-75ap4.html`
- **Text:** sekce 8 (POZOR: 2 odstavce popisu, druhý je důležitý diagnostický tip o „pop" zvuku)
- **Obrázek:** `/Obrázky/YASKAWA HV*`
- **Cena:** `15000` CZK, `PreOrder`
- **Brand:** Yaskawa
- **POZN:** V description JSON-LD zmiň „Status: OBSOLETE — dostupný jako náhradní díl".

### [ ] ÚKOL 9: Pneumatic boosting cylinder SUN

- **URL:** `HTML_V1/dily/pneumaticky-valec-sun-3000kg.html`
- **Text:** sekce 9
- **Obrázek:** `/Obrázky/Pneumatic boosting cylinder.*`
- **Cena:** `18000` CZK, `PreOrder`
- **Brand:** Chen Sound (SUN)

### [ ] ÚKOL 10: Řemen 400-5GT-40

- **URL:** `HTML_V1/dily/remen-400-5gt-40.html`
- **Text:** sekce 10
- **Obrázek:** ověř ve `/Obrázky/`. Pokud existuje generický obrázek řemenu, použij ho i pro 420 a 450 (jsou vizuálně podobné). Pokud chybí, napiš mi.
- **Cena:** `1050` CZK, `InStock`
- **Brand:** Gates (PowerGrip GT)

### [ ] ÚKOL 11: Řemen 420-5GT-40

- **URL:** `HTML_V1/dily/remen-420-5gt-40.html`
- **Text:** sekce 11
- **Obrázek:** stejný jako u úkolu 10
- **Cena:** `1150` CZK, `InStock`
- **Brand:** Gates

### [ ] ÚKOL 12: Řemen 450-5GT-40

- **URL:** `HTML_V1/dily/remen-450-5gt-40.html`
- **Text:** sekce 12
- **Obrázek:** stejný jako u úkolu 10
- **Cena:** `1250` CZK, `InStock`
- **Brand:** Gates

### [ ] ÚKOL 13: Repasování vřetene (SLUŽBA — JINÁ STRUKTURA)

- **URL:** `HTML_V1/dily/repasovani-vretene.html`
- **Text:** sekce 13
- **Obrázek:** ověř ve `/Obrázky/`. Vhodná je fotka vřetene nebo opravy vřetene. Pokud chybí, napiš mi.
- **Cena:** `110000` CZK
- **Struktura stránky:** **ODLIŠNÁ** — viz sekce „Struktura HTML stránky SLUŽBY" výše. NEPOUŽÍVEJ Product schema, použij **Service schema**.
- **POZN:** Proces má 7 kroků (NE 6) — krok 6 je „Záběh. Vřeteno zabíháme do 4 000 otáček. Zbytek rozsahu otáček je potřeba zaběhnout přímo na stroji."

### [ ] ÚKOL 14: Repasování kuličkového šroubu (SLUŽBA)

- **URL:** `HTML_V1/dily/repasovani-kulickoveho-sroubu.html`
- **Text:** sekce 14
- **Obrázek:** ověř ve `/Obrázky/`. Vhodná je fotka kuličkového šroubu nebo opravy. Pokud chybí, napiš mi.
- **Cena:** `12000` CZK
- **Struktura:** stejná jako u úkolu 13 (Service schema, 5 kroků procesu)

### [ ] ÚKOL 15: FINAL — integrace rozcestníku a kontrola

**ČÁST 1 — Aktualizuj `ukazky-dilu.html` (rozcestník):**
- Každá karta dílu má teď mít odkaz na detail stránku v `/dily/`
- Přidej 2 nové karty pro služby (Repasování vřetene, Repasování kuličkového šroubu) — ideálně v samostatné sekci „Služby" nebo na konci seznamu
- Karty pneumatický válec, Yaskawa, Indramat — pokud na `ukazky-dilu.html` ještě nejsou, přidej je
- Sjednoť ceny ve všech kartách s detail stránkami (zdroj pravdy je `CNC_Dily_Texty.md`)

**ČÁST 2 — Aktualizuj `index.html`:**
- 4 karty v hero sekci (Pneumatic, Yaskawa, Rotary Joint, NSK ložisko) — každá má teď odkaz na detail stránku v `/dily/`
- Sekce „Co jsme nedávno řešili" — propoj s detail stránkami

**ČÁST 3 — Kontrola konzistence:**
- Ověř, že všech 14 nových stránek má identickou hlavičku a patičku
- Ověř, že breadcrumb na všech stránkách funguje (linky)
- Ověř hreflang cs ve `<head>` všech nových stránek
- Ověř, že `sitemap.xml` obsahuje všech 14 nových URL

**ČÁST 4 — Update `MIGRATION.md`:**
- Přidej seznam všech 14 nových URL k přepsání domény při migraci na produkci.

**ČÁST 5 — Vykaž statistiku:**
- Kolik souborů přibylo
- Kolik commitů celkem
- Seznam všech 14 URL na GH Pages s cache-busterem pro finální ověření

---

## PO DOKONČENÍ VŠECH ÚKOLŮ

1. **Validace JSON-LD:** otevři <https://search.google.com/test/rich-results>, otestuj 2–3 náhodné stránky (alespoň 1 díl a 1 službu). Měl bys vidět Product / Service bez chyb.
2. **Visual review:** uživatel otevře všechny stránky v incognito a vizuálně zkontroluje.
3. **Možné iterace:** pokud uživatel najde chybu v textu, oprav ji v `CNC_Dily_Texty.md` (zdroj pravdy) + propaguj do HTML.

---

## POZNÁMKY PRO BUDOUCNO

- **CTA tlačítka** linkují na `poptavka.html?dil=[slug]`. Až bude poptávkový formulář funkční (Netlify Forms po migraci), JS automaticky předvyplní pole „Co potřebujete dodat" hodnotou z URL parametru.
- **Migrace na cnc-nahradnidily.cz:** všechny absolutní URL v meta tagách a JSON-LD bude potřeba přepsat (viz `MIGRATION.md`).
- **Status:** soubor `CNC_Dily_Build_Plan.md` je živý dokument. Pokud potřebuješ změnit zdrojový text, uprav `CNC_Dily_Texty.md` a regeneruj příslušnou stránku.
