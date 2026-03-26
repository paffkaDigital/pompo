# Pompo — zahradní asistent

Claude Code plugin pro plánování výsevů, evidenci rostlin a míst. Defaultně vystupuje jako strýček Pompo z Arabely.

## Co Pompo umí

- **Persona** — defaultně strýček Pompo z Arabely, měnitelná
- **Onboarding** — při prvním spuštění provede nastavením: persona → lokalita → místa
- **Nastavení profilu** — lokalita, klimatická zóna, typické teploty
- **Správa pěstebních míst** — zahrada, skleník, foliovník, parapet...
- **Databáze rostlin** — vyhledání info na webu, uložení do .md souborů
- **Inventář semen** — evidence co mám na skladě, volný formát množství (sáček, hrst, stovky...)
- **Doporučení co zasít** — na základě sezóny, lokality, parametrů míst a dostupného inventáře
- **Evidence výsevů** — sledování stavu od zasetí po sklizeň
- **Ganttův diagram** — vizuální přehled výsevů v .md formátu s linky na rostliny
- **Zahradnické práce** — plánování úkolů (vázaných na rostlinu i obecných), připomínky
- **Deník zahrady** — automatický log provedených prací
- **Proaktivní plánování** — Pompo při startu session upozorní na blížící se termíny výsevů a nesplněné práce, zeptá se co se na zahradě dělo

## Instalace

```bash
claude plugin marketplace add paffkaDigital/pompo
claude plugin install pompo
```

Aktualizace po nové verzi:

```bash
claude plugin marketplace refresh
```

## Použití

Pompo reaguje na přirozený jazyk. Příklady:

```
# Onboarding a nastavení
Nastav mi profil, jsem z Pardubic.
Změň personu.
Přidej místo.

# Rostliny a inventář
Přišlo mi osivo z permaseminka.cz: paprika měňavka, rajče tyčkové, bazalka.
Najdi info o česneku.
Co mám na skladě?

# Doporučení a výsevy
Co mám teď zasít?
Kam dát rajčata?
Co do foliovníku?
Zasel jsem rajčata do foliovníku.
Rajčata klíčí.

# Práce a deník
Přidej práci: ostříhat šeřík po odkvětu v červnu.
Přidej práci: přeházet kompost.
Co je potřeba udělat?
Ostříhal jsem šeřík.
Deník.

# Přehledy
Stav výsevů.
Vygeneruj gantt.
```

## Struktura dat

Uživatelská data se ukládají do `plugins/pompo/data/`:

```
data/
  persona.md         # persona asistenta (jméno, styl)
  persona-default.md # výchozí persona (strýček Pompo)
  profil.md          # lokalita, klimatická zóna, typické teploty
  mista/             # pěstební místa (.md)
  rostliny/          # databáze rostlin (.md)
  inventar/          # inventář semen (.md)
  vysevy/            # evidence výsevů (.md)
  prace/             # zahradnické práce (.md)
  denik/             # deníkové záznamy (.md)
  gantt.md           # generovaný Ganttův diagram
```

## Licence

MIT
