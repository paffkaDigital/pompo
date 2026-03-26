---
name: stav
description: Použij když uživatel chce vidět přehled výsevů, aktualizovat stav rostliny, vytvořit nový záznam o výsevu, nebo vygenerovat Ganttův diagram.
allowed-tools: Read, Glob, Grep, Write, Edit
user-invocable: true
---

# Stav — evidence výsevů a Ganttův diagram

## Kdy se aktivuje

- "stav", "přehled", "co mám zaseto?"
- "zasel jsem rajčata do foliovníku"
- "rajčata klíčí", "hrášek roste"
- "gantt", "diagram", "aktualizuj gantt"
- "co mám na skladě?", "inventář", "kolik mám semen?"

## Režimy

### 1. Přehled výsevů

Aktivace: "stav", "přehled", "co mám zaseto?"

Postup:
1. Glob všechny soubory v `${CLAUDE_PLUGIN_ROOT}/data/vysevy/`
2. Přečti každý soubor
3. Zobraz přehlednou tabulku:

| Rostlina | Místo | Zaseto | Stav |
|----------|-------|--------|------|
| {{nazev}} | {{misto}} | {{datum}} | {{stav}} |

Seskup podle míst. Pokud nejsou žádné výsevy → "Zatím nemáš žádné výsevy evidované."

### 2. Inventář semen

Aktivace: "co mám na skladě?", "inventář", "kolik mám semen?"

Postup:
1. Glob všechny soubory v `${CLAUDE_PLUGIN_ROOT}/data/inventar/`
2. Přečti každý soubor
3. Zobraz přehlednou tabulku:

| Rostlina | Množství | Odkud | Nákup | Expirace |
|----------|----------|-------|-------|----------|
| {{nazev}} | {{mnozstvi}} | {{obchod}} | {{datum}} | {{expirace}} |

Pokud je inventář prázdný → "Nemáš evidovaná žádná semena. Řekni mi co ti přišlo."

### 3. Nový výsev

Aktivace: "zasel jsem X do Y", "zasej X", "založit výsev"

Postup:
1. Identifikuj rostlinu a místo z uživatelova dotazu
2. Ověř, že rostlina existuje v `${CLAUDE_PLUGIN_ROOT}/data/rostliny/` (Glob)
3. Ověř, že místo existuje v `${CLAUDE_PLUGIN_ROOT}/data/mista/` (Glob)
4. Pokud neexistuje → navrhni nejdřív přidat ("Rostlina X není v databázi. Chceš ji nejdřív vyhledat?")
5. Datum: použij datum z dotazu, nebo se zeptej (výchozí: dnes)
6. Přečti šablonu `${CLAUDE_PLUGIN_ROOT}/sablony/vysev.md`
7. Vygeneruj název souboru: `{{rostlina}}-{{misto}}.md` (kebab-case, bez diakritiky)
   - Pokud soubor už existuje (duplicitní výsev), přidej datum: `{{rostlina}}-{{misto}}-{{YYYY-MM-DD}}.md`
8. Vyplň šablonu a zapiš do `${CLAUDE_PLUGIN_ROOT}/data/vysevy/`
9. Aktualizuj inventář:
   - Najdi odpovídající soubor v `${CLAUDE_PLUGIN_ROOT}/data/inventar/` (Glob pro filename rostliny)
   - Pokud inventář existuje:
     - **Přesné množství** (3 sáčky, 100 ks): sniž číslo automaticky, zapiš do Historie
     - **Nepřesné množství** (hrst, hodně, trochu): zapiš výsev do Historie a **zeptej se uživatele** "Zbylo ještě něco v inventáři?" → pokud ne, nastav množství na "vyčerpáno"; pokud ano, aktualizuj dle odpovědi
     - Pokud množství klesne na 0 / vyčerpáno → upozorni "Poslední semena použita"
   - Pokud inventář neexistuje → neřeš, ne každý vede inventář
10. Potvrď uživateli

### 3. Aktualizace stavu

Aktivace: "rajčata klíčí", "hrášek jsem sklidil", "paprika uhynula"

Postup:
1. Identifikuj rostlinu a nový stav z dotazu
2. Najdi odpovídající soubor v `${CLAUDE_PLUGIN_ROOT}/data/vysevy/` (Glob + Grep)
3. Pokud je více výsevů stejné rostliny → zeptej se který
4. Ověř platnost přechodu stavu:
   - `zaseto` → `klíčí` | `uhynulo`
   - `klíčí` → `roste` | `přesazeno` | `uhynulo`
   - `roste` → `sklizeno` | `uhynulo`
   - `přesazeno` → `roste` | `uhynulo`
5. Pokud přechod není platný → vysvětli jaké přechody jsou možné z aktuálního stavu
6. Aktualizuj `stav` ve frontmatter souboru
7. Přidej poznámku s datem změny
8. Potvrď uživateli

### 4. Ganttův diagram

Aktivace: "gantt", "diagram", "aktualizuj gantt", "plán výsevů"

Postup:
1. Přečti všechny výsevy z `${CLAUDE_PLUGIN_ROOT}/data/vysevy/`
2. Pro každý výsev přečti data rostliny z `${CLAUDE_PLUGIN_ROOT}/data/rostliny/` (doba do sklizně)
3. Vygeneruj `${CLAUDE_PLUGIN_ROOT}/data/gantt.md`

Formát Ganttova diagramu:

```markdown
# Plán výsevů {{rok}}

Vygenerováno: {{datum}}

## {{název místa}}

| Rostlina | Led | Úno | Bře | Dub | Kvě | Čvn | Čvc | Srp | Zář | Říj | Lis | Pro |
|----------|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| [{{název}}](../rostliny/{{soubor}}.md) | ... | ... | [V] | [V] | [R] | [R] | [R] | [S] | ... | ... | ... | ... |

Legenda:
- [V] = výsev / klíčení
- [R] = růst
- [P] = přesazeno (pokračuje růst na novém místě)
- [S] = sklizeň
- ... = neaktivní
```

Pravidla:
- Seskup podle míst
- Časová osa po měsících, celý rok
- Výsevní období podle `datum_vysevu`
- Dobu růstu odvoď z `doba do sklizně` v databázi rostliny
- Pokud rostlina byla přesazena, zobraz přesun na nové místo
- Soubor musí být čitelný v čistém markdownu a vhodný k tisku
- Názvy rostlin v tabulce jsou relativní linky na soubory v `data/rostliny/` (formát `[Název](../rostliny/soubor.md)`)

## Mapování diakritiky pro názvy souborů

á→a, č→c, ď→d, é→e, ě→e, í→i, ň→n, ó→o, ř→r, š→s, ť→t, ú→u, ů→u, ý→y, ž→z
