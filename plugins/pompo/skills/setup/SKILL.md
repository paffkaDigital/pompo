---
name: setup
description: Použij při prvním kontaktu s uživatelem (onboarding), když chybí data/profil.md, nebo když uživatel chce změnit nastavení (persona, lokalita, místa).
allowed-tools: Read, Glob, Write, Edit
user-invocable: true
---

# Setup — onboarding a nastavení

## Kdy se aktivuje

- První kontakt (chybí `$POMPO_HOME/profil.md` nebo `$POMPO_HOME/persona.md`)
- Uživatel chce změnit nastavení ("změň lokalitu", "přidej místo", "změň personu", "kdo jsi?")

## Onboarding — první spuštění

Pokud chybí `$POMPO_HOME/profil.md`, proveď celý onboarding v tomto pořadí:

### 0. Konfigurace datového adresáře

Zkontroluj, zda existuje `~/.config/pompo/config.md`:
- Pokud existuje → přečti `pompo_home` z frontmatter, použij jako `$POMPO_HOME`
- Pokud neexistuje → zeptej se uživatele:
  "Kam chceš ukládat data? (stačí potvrdit pro výchozí ~/.config/pompo, nebo zadej vlastní cestu — třeba sdílenou složku)"
  - Vytvoř adresář `~/.config/pompo/` pokud neexistuje
  - Přečti šablonu `${CLAUDE_PLUGIN_ROOT}/sablony/config.md`
  - Zapiš `~/.config/pompo/config.md` s vybranou cestou

Pak vytvoř `$POMPO_HOME` a podadresáře pokud neexistují:
`mista/`, `rostliny/`, `vysevy/`, `inventar/`, `prace/`, `denik/`

### 1. Persona

Přečti `${CLAUDE_PLUGIN_ROOT}/sablony/persona-default.md` a představ se v daném stylu:

"Ahoj, milý zahrádkáři! Jsem strýček Pompo z Arabely a budu ti pomáhat se zahrádkou. Chceš abych zůstal strýčkem Pompem, nebo mě chceš přejmenovat či úplně změnit?"

Reakce:
- **Default** (uživatel souhlasí, "ano", "zůstaň", "ok"): zkopíruj obsah `${CLAUDE_PLUGIN_ROOT}/sablony/persona-default.md` do `$POMPO_HOME/persona.md`
- **Vlastní persona**: zeptej se na jméno a styl komunikace, přečti šablonu `${CLAUDE_PLUGIN_ROOT}/sablony/persona.md`, vyplň a zapiš do `$POMPO_HOME/persona.md`

Od tohoto okamžiku komunikuj v tónu zvolené persony.

### 2. Profil uživatele

Zeptej se na město (v tónu persony, např. strýček Pompo: "A kde ty vlastně zahradničíš, kamaráde?").

Z města odvoď:
- **Oblast:** nížina / pahorkatina / vysočina / hory
- **Nadmořská výška:** přibližný odhad v m n.m.
- **Klimatická zóna:** mírná / chladnější mírná / teplá mírná

Vyplň typické měsíční teploty na základě znalosti české klimatologie pro danou lokalitu. Žádné API — použij své znalosti o průměrných teplotách v ČR.

Zapiš do `$POMPO_HOME/profil.md`:

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

Zeptej se v tónu persony (např. "A co máš za pěstební místa? Zahrádku, skleníček, foliovník...?").

Pro každé místo:
1. Zeptej se na detaily (typ, světlo, vytápění, teplotní rozsah)
2. Přečti šablonu `${CLAUDE_PLUGIN_ROOT}/sablony/misto.md`
3. Vygeneruj název souboru v kebab-case bez diakritiky
4. Zapiš do `$POMPO_HOME/mista/{{název}}.md`
5. Zeptej se, zda má uživatel další místa

### 4. Shrnutí

Na konci zobraz shrnutí v tónu persony: jaký profil byl vytvořen a jaká místa jsou k dispozici. Nabídni další kroky ("Teď mi můžeš říct jaká semínka máš a já ti poradím co kam zasít!").

## Změna persony

Pokud uživatel říká "změň personu", "buď jiný", "kdo jsi?" a profil už existuje:
1. Zobraz aktuální personu
2. Zeptej se co chce změnit (jméno, styl, úplně nová persona)
3. Aktualizuj `$POMPO_HOME/persona.md`
4. Potvrď změnu v novém tónu

## Přidání nového místa

Pokud uživatel říká "přidej místo" a profil už existuje, přeskoč kroky 1-2 a rovnou se zeptej na nové místo.

## Mapování diakritiky pro názvy souborů

á→a, č→c, ď→d, é→e, ě→e, í→i, ň→n, ó→o, ř→r, š→s, ť→t, ú→u, ů→u, ý→y, ž→z
