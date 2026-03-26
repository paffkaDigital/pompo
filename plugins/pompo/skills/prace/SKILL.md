---
name: prace
description: Použij když uživatel chce přidat zahradnickou práci, zobrazit co je potřeba udělat, označit práci jako hotovou, nebo prohlížet deník zahrady.
allowed-tools: Read, Glob, Grep, Write, Edit
user-invocable: true
---

# Práce — zahradnické úkoly a deník zahrady

## Kdy se aktivuje

- "potřebuji ostříhat šeřík", "přidej práci: přeházet kompost"
- "co je potřeba udělat?", "jaké mám práce?"
- "ostříhal jsem šeřík", "kompost přeházen", "hotovo"
- "deník", "co se dělo?", "záznamy"

## Režimy

### 1. Nová práce

Aktivace: "přidej práci", "potřebuji udělat X", "je potřeba X"

Postup:
1. Identifikuj název práce z dotazu
2. Zjisti, zda se práce váže na konkrétní rostlinu:
   - Pokud ano → najdi soubor v `$POMPO_HOME/rostliny/` (Glob), ulož odkaz
   - Pokud ne → prázdné pole (obecná zahradnická práce)
3. Zjisti místo (pokud zmíněno) → odkaz na soubor v `$POMPO_HOME/mista/`
4. Zjisti termín:
   - Konkrétní datum: "15. dubna"
   - Měsíc: "v dubnu"
   - Opakující se: "každý rok v březnu", "každý týden"
   - Pokud neuvedeno → zeptej se
5. Přečti šablonu `${CLAUDE_PLUGIN_ROOT}/sablony/prace.md`
6. Vygeneruj název souboru v kebab-case bez diakritiky
7. Zapiš do `$POMPO_HOME/prace/{{název}}.md`
8. Potvrď v tónu persony

### 2. Přehled prací

Aktivace: "co je potřeba udělat?", "jaké mám práce?", "úkoly"

Postup:
1. Glob všechny soubory v `$POMPO_HOME/prace/`
2. Přečti každý soubor
3. Filtruj: zobraz pouze práce se stavem `plánováno`
4. Seřaď podle termínu (nejbližší první)
5. Zobraz přehlednou tabulku:

| Práce | Rostlina | Místo | Termín |
|-------|----------|-------|--------|
| {{nazev}} | {{rostlina nebo —}} | {{misto nebo —}} | {{termin}} |

6. Zvýrazni práce s blížícím se nebo prošlým termínem

### 3. Splnění práce

Aktivace: "ostříhal jsem šeřík", "kompost přeházen", "udělal jsem X", "hotovo"

Postup:
1. Identifikuj práci z dotazu
2. Najdi odpovídající soubor v `$POMPO_HOME/prace/` (Glob + Grep)
3. Pokud je více kandidátů → zeptej se který
4. Aktualizuj soubor:
   - `stav` → `hotovo`
   - `datum_splneni` → dnešní datum
5. Zapiš do deníku:
   - Zkontroluj, zda existuje `$POMPO_HOME/denik/{{YYYY-MM-DD}}.md` pro dnešek
   - Pokud existuje → přidej práci do sekce "Provedené práce"
   - Pokud neexistuje → vytvoř z šablony `${CLAUDE_PLUGIN_ROOT}/sablony/denik-zaznam.md`
6. Pokud uživatel zmínil počasí nebo podmínky → zapiš do sekce "Počasí / podmínky"
7. Potvrď v tónu persony

### 4. Deník zahrady

Aktivace: "deník", "co se dělo?", "záznamy", "historie prací"

Postup:
1. Glob všechny soubory v `$POMPO_HOME/denik/`
2. Seřaď od nejnovějšího
3. Zobraz přehled posledních záznamů (nebo za období pokud uživatel specifikoval)

### 5. Aktivní doptávání (voláno z session hooku)

Když session hook zjistí nesplněné práce nebo uplynulý čas od posledního deníkového záznamu:
1. Přečti nesplněné práce s termínem ≤ aktuální datum + 7 dní
2. V tónu persony upozorni: "Kamaráde, šeřík by chtěl ostříhat — termín je tento měsíc!"
3. Zeptej se: "Dělo se něco na zahrádce od minula?"
4. Pokud uživatel odpoví co dělal:
   - Zaloguj do deníku (data/denik/YYYY-MM-DD.md)
   - Pokud se odpověď shoduje s plánovanou prací → označ jako hotovou
   - Pokud je to nová aktivita (nebyla v plánu) → pouze deník

## Mapování diakritiky pro názvy souborů

á→a, č→c, ď→d, é→e, ě→e, í→i, ň→n, ó→o, ř→r, š→s, ť→t, ú→u, ů→u, ý→y, ž→z
