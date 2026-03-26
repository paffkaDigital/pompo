---
rostlina: "{{soubor-rostliny-bez-pripony}}"
misto: "{{soubor-mista-bez-pripony}}"
datum_vysevu: {{YYYY-MM-DD}}
stav: "zaseto"
---

# {{nazev rostliny}} — {{nazev místa}}

- **Zaseto:** {{datum}}
- **Stav:** zaseto
- **Přesazeno kam:** {{volitelné — místo, kam bylo přesazeno}}
- **Poznámky:** {{volitelné}}

## Stavy a přechody

Platné přechody stavů:
- `zaseto` → `klíčí` | `uhynulo`
- `klíčí` → `roste` | `přesazeno` | `uhynulo`
- `roste` → `sklizeno` | `uhynulo`
- `přesazeno` → `roste` | `uhynulo`
