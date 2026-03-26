---
name: co-zasit
description: Použij když se uživatel ptá co zasít, kam dát konkrétní rostlinu, co je vhodné pro dané místo, nebo co je v sezóně.
allowed-tools: Read, Glob, Grep
user-invocable: true
---

# Co zasít — doporučení co, kam a kdy

## Kdy se aktivuje

- "co mám teď zasít?"
- "kam dát rajčata?"
- "co do foliovníku?"
- "co je v sezóně?"
- "co můžu vysít venku?"

## Předpoklady

Před doporučením zkontroluj:
1. Existuje `$POMPO_HOME/profil.md`? Pokud ne → "Nejdřív spusť nastavení."
2. Jsou soubory v `$POMPO_HOME/mista/`? Pokud ne → "Nemáš nastavená žádná místa."
3. Jsou soubory v `$POMPO_HOME/rostliny/`? Pokud ne → "Nemáš žádné rostliny v databázi."

## Postup

### 1. Načtení dat

- Přečti `$POMPO_HOME/profil.md` — extrahuj typické teploty pro aktuální měsíc
- Přečti všechny soubory v `$POMPO_HOME/mista/`
- Přečti všechny soubory v `$POMPO_HOME/rostliny/`
- Přečti všechny soubory v `$POMPO_HOME/vysevy/` (pro kontrolu co už je zaseto)
- Přečti všechny soubory v `$POMPO_HOME/inventar/` (pro kontrolu co je na skladě)

### 2. Odvození efektivní teploty míst

Pro každé místo odvoď efektivní teplotu na základě jeho vlastností a venkovní teploty z profilu:

- **Venku:** odpovídá typické venkovní teplotě pro aktuální měsíc z profilu
- **Zakryté bez vytápění (skleník, foliovník):** venkovní teplota + bonus cca 5–15°C přes den (v noci blíže venkovní teplotě). Odhadni bonus podle popisu místa.
- **Zakryté s vytápěním:** teplotní rozsah uvedený v parametrech místa
- **Vnitřní:** stabilní teplota dle parametrů místa (typicky 18–24°C)

Toto je heuristický odhad — použij své znalosti o tom, jak se chová teplota ve skleníku vs. venku.

### 3. Porovnání a doporučení

Pro každou rostlinu v databázi:

1. **Termín výsevu:** Je aktuální měsíc v rozmezí "termín výsevu"?
2. **Teplota klíčení:** Splňuje efektivní teplota místa minimální teplotu klíčení?
3. **Světlo:** Odpovídá světlo v místě požadavkům rostliny?
4. **Už zaseto?** Existuje aktivní výsev (stav jiný než `sklizeno` / `uhynulo`)?
5. **Na skladě?** Existuje inventární záznam s množstvím jiným než "vyčerpáno"?

### 4. Zohlednění existujících výsevů

- Nedoporučuj rostliny, které už mají aktivní výsev
- Upozorni na případné konflikty kapacity místa
- Pokud je některá rostlina ve stavu `klíčí` nebo `roste` v zakrytém místě a venkovní teploty už dovolují přesazení ven → doporuč přesazení

### 5. Formát výstupu

#### Pokud je otázka obecná ("co zasít?")

Seskup doporučení podle míst:

**{{název místa}}** ({{efektivní teplota}} °C)
- {{rostlina}} — {{způsob výsevu}}, vhodná díky {{důvod}}
- ...

**Zatím počkat:**
- {{rostlina}} — vhodná od {{měsíc}}, potřebuje {{důvod}}

**Pozdě:**
- {{rostlina}} — termín výsevu byl {{termín}}

#### Pokud je otázka konkrétní ("kam dát rajčata?")

Odpověz přímo na konkrétní otázku, pak přidej kontext.

#### Pokud je otázka na místo ("co do foliovníku?")

Zobraz jen rostliny vhodné pro dané místo.

## Důležité

- Tento skill je čistě poradní — nic nezapisuje, pouze čte a doporučuje.
- Pokud má rostlina pole "nezjištěno", upozorni na to a doporuč aktualizaci dat.
- Doporučuj primárně rostliny, které má uživatel na skladě (v inventáři).
- Rostliny bez inventáře zobraz v samostatné sekci s poznámkou "nemáš na skladě".
- Rostliny s vyčerpaným inventářem nezobrazuj (nebo jen s poznámkou "semena vyčerpána").
- Vždy uveď aktuální datum a měsíc, ze kterého vycházíš.
