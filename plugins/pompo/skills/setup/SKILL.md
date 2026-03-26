---
name: setup
description: Použij při prvním kontaktu s uživatelem, když chybí data/profil.md, nebo když uživatel chce změnit nastavení (lokalita, místa).
allowed-tools: Read, Glob, Write, Edit
user-invocable: true
---

# Setup — nastavení profilu a míst

## Kdy se aktivuje

- První kontakt s Pompem (chybí `${CLAUDE_PLUGIN_ROOT}/data/profil.md`)
- Uživatel chce změnit nastavení ("změň lokalitu", "přidej místo")

## Postup

### 1. Kontrola stavu

Zkontroluj, zda existuje `${CLAUDE_PLUGIN_ROOT}/data/profil.md`:
- Pokud existuje a uživatel chce aktualizovat → nabídni co změnit
- Pokud neexistuje → pokračuj nastavením profilu

### 2. Profil uživatele

Zeptej se na město (např. "Kde zahradničíš?").

Z města odvoď:
- **Oblast:** nížina / pahorkatina / vysočina / hory
- **Nadmořská výška:** přibližný odhad v m n.m.
- **Klimatická zóna:** mírná / chladnější mírná / teplá mírná

Vyplň typické měsíční teploty na základě znalosti české klimatologie pro danou lokalitu. Žádné API — použij své znalosti o průměrných teplotách v ČR.

Zapiš do `${CLAUDE_PLUGIN_ROOT}/data/profil.md`:

```markdown
---
lokalita: "{{město}}"
oblast: "{{oblast}}"
nadmorska_vyska: {{číslo}}
klimaticka_zona: "{{zóna}}"
---

# Profil zahrádkáře

## Lokalita
- **Město:** {{město}}
- **Oblast:** {{oblast}}
- **Nadmořská výška:** {{číslo}} m n.m.
- **Klimatická zóna:** {{zóna}}

## Typické teploty
- **Leden:** {{rozsah}} °C
- **Únor:** {{rozsah}} °C
- **Březen:** {{rozsah}} °C
- **Duben:** {{rozsah}} °C
- **Květen:** {{rozsah}} °C
- **Červen:** {{rozsah}} °C
- **Červenec:** {{rozsah}} °C
- **Srpen:** {{rozsah}} °C
- **Září:** {{rozsah}} °C
- **Říjen:** {{rozsah}} °C
- **Listopad:** {{rozsah}} °C
- **Prosinec:** {{rozsah}} °C

## Poznámky
{{specifika lokality}}
```

### 3. Pěstební místa

Zeptej se: "Jaká máš pěstební místa? (zahrada, skleník, foliovník, parapet...)"

Pro každé místo:
1. Zeptej se na detaily (typ, světlo, vytápění, teplotní rozsah)
2. Přečti šablonu `${CLAUDE_PLUGIN_ROOT}/sablony/misto.md`
3. Vygeneruj název souboru v kebab-case bez diakritiky
4. Zapiš do `${CLAUDE_PLUGIN_ROOT}/data/mista/{{název}}.md`
5. Zeptej se, zda má uživatel další místa

### 4. Shrnutí

Na konci zobraz shrnutí: jaký profil byl vytvořen a jaká místa jsou k dispozici.

## Přidání nového místa

Pokud uživatel říká "přidej místo" a profil už existuje, přeskoč krok 2 a rovnou se zeptej na nové místo.

## Mapování diakritiky pro názvy souborů

á→a, č→c, ď→d, é→e, ě→e, í→i, ň→n, ó→o, ř→r, š→s, ť→t, ú→u, ů→u, ý→y, ž→z
