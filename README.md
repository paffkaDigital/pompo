# Pompo — zahradní asistent

Claude Code plugin pro plánování výsevů, evidenci rostlin a míst.

## Co Pompo umí

- **Nastavení profilu** — lokalita, klimatická zóna, typické teploty
- **Správa pěstebních míst** — zahrada, skleník, foliovník, parapet...
- **Databáze rostlin** — vyhledání info na webu, uložení do .md souborů
- **Doporučení co zasít** — na základě sezóny, lokality a parametrů míst
- **Evidence výsevů** — sledování stavu od zasetí po sklizeň
- **Ganttův diagram** — vizuální přehled výsevů v .md formátu

## Instalace

```bash
claude plugin marketplace add paffkaDigital/pompo
claude plugin install pompo
```

## Použití

Pompo reaguje na přirozený jazyk. Příklady:

```
Nastav mi profil, jsem z Pardubic.
Přišlo mi osivo z permaseminka.cz: paprika měňavka, rajče tyčkové, bazalka.
Co mám teď zasít?
Kam dát rajčata?
Co do foliovníku?
Zasel jsem rajčata do foliovníku.
Rajčata klíčí.
Stav výsevů.
Vygeneruj gantt.
```

## Struktura dat

Uživatelská data se ukládají do `plugins/pompo/data/`:

```
data/
  profil.md          # lokalita, klimatická zóna, typické teploty
  mista/             # pěstební místa (.md)
  rostliny/          # databáze rostlin (.md)
  vysevy/            # evidence výsevů (.md)
  gantt.md           # generovaný Ganttův diagram
```

## Licence

MIT
