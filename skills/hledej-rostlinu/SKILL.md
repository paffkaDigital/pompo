---
name: hledej-rostlinu
description: Použij když uživatel chce najít informace o rostlině, přidat novou rostlinu, nebo zpracovat seznam osiv z obchodu.
allowed-tools: Read, Glob, Grep, Write, Edit, WebSearch, WebFetch
user-invocable: true
---

# Hledej rostlinu — vyhledání a uložení informací o rostlině

## Kdy se aktivuje

- "najdi mi info o rajčatech"
- "přidej rostlinu bazalka"
- "přišlo mi osivo z permaseminka.cz: paprika měňavka, rajče, bazalka"
- "aktualizuj info o X"

## Postup — jednotlivá rostlina

### 1. Identifikace

Extrahuj název rostliny z uživatelova dotazu. Vygeneruj kebab-case název souboru bez diakritiky (á→a, č→c, ď→d, é→e, ě→e, í→i, ň→n, ó→o, ř→r, š→s, ť→t, ú→u, ů→u, ý→y, ž→z).

### 2. Kontrola existence

Glob pro `${CLAUDE_PLUGIN_ROOT}/data/rostliny/{{filename}}.md`:
- Pokud existuje a uživatel nežádá aktualizaci → zobraz existující data
- Pokud existuje a uživatel žádá aktualizaci → pokračuj vyhledáváním
- Pokud neexistuje → pokračuj vyhledáváním

### 3. Vyhledání na webu

Priorita zdrojů:
1. Pokud uživatel zmínil konkrétní obchod/URL → WebSearch na daném webu, pak WebFetch produktové stránky
2. WebSearch pro obecné informace o pěstování dané rostliny v českých podmínkách
3. Hledej klíčové parametry: termín výsevu, způsob (přímý/předpěstování), teplota klíčení, doba klíčení, světlo, rozestup, doba do sklizně

### 4. Vytvoření souboru

1. Přečti šablonu `${CLAUDE_PLUGIN_ROOT}/sablony/rostlina.md`
2. Vyplň nalezené hodnoty
3. Co nebylo nalezeno, označ jako "nezjištěno"
4. Vyplň `zdroj` — URL odkud data pochází
5. Vyplň `datum_aktualizace` — dnešní datum
6. Zapiš do `${CLAUDE_PLUGIN_ROOT}/data/rostliny/{{filename}}.md`

### 5. Potvrzení

Zobraz uživateli shrnutí — co bylo nalezeno, co zůstalo jako "nezjištěno".

## Postup — hromadný režim

Rozpoznej vzory jako:
- "přišlo mi 20 druhů osiva z permaseminka.cz: X, Y, Z..."
- "přidej: rajčata, paprika, bazalka, hrášek"

Postup:
1. Parsuj seznam názvů rostlin
2. Pro každou rostlinu proveď stejný postup jako pro jednotlivou rostlinu
3. Ukazuj průběh: "Zpracovávám 3/20: Bazalka..."
4. Na konci zobraz souhrn:
   - Vytvořeno: X nových souborů
   - Už existovalo: Y souborů
   - Nekompletní data: Z souborů (vyjmenuj které)

## Aktualizace existujícího souboru

Pokud uživatel žádá aktualizaci ("aktualizuj info o rajčatech"):
1. Přečti existující soubor
2. Vyhledej na webu aktuální data
3. Aktualizuj pouze pole, která se změnila nebo byla "nezjištěno"
4. Aktualizuj `datum_aktualizace`
5. Zobraz co se změnilo
