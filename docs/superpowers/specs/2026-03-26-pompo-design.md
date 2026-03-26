# Pompo — zahradní asistent

## Shrnutí

Pompo je Claude Code plugin pro správu zahrádkářských aktivit. Pomáhá s plánováním výsevů, evidencí rostlin a míst, a rozhodováním co kam a kdy zasít. Komunikace probíhá přirozeným jazykem, vše je česky.

## Typ řešení

Claude Code plugin se skills (inspirováno architekturou Kvido). On-demand interakce — žádný heartbeat, uživatel se ptá a Pompo odpovídá.

## Struktura pluginu

```
pompo/
  PLUGIN.md                    # definice pluginu, metadata
  hooks/
    context-session.md         # kontextové instrukce injektované do session
  skills/
    setup/SKILL.md             # nastavení profilu a míst
    hledej-rostlinu/SKILL.md   # web → .md soubor rostliny
    co-zasit/SKILL.md          # doporučení co/kam/kdy
    stav/SKILL.md              # evidence výsevů + Gantt diagram
  sablony/
    rostlina.md                # template pro .md soubor rostliny
    misto.md                   # template pro .md soubor místa
    vysev.md                   # template pro .md soubor výsevu
  data/
    profil.md                  # lokalita uživatele, klimatická zóna
    mista/                     # .md soubory míst
    rostliny/                  # .md soubory rostlin
    vysevy/                    # .md soubory jednotlivých výsevů
    gantt.md                   # generovaný Ganttův diagram
```

## Datové modely

### Rostlina (`sablony/rostlina.md`)

```markdown
---
nazev: "{{nazev}}"
latinsky_nazev: "{{latinsky_nazev}}"
zdroj: "{{url_odkud_byla_data_získána}}"
datum_aktualizace: "{{datum}}"
---

# {{nazev}}

## Výsev
- **Termín výsevu:** {{od}} – {{do}}
- **Způsob:** {{přímý výsev / předpěstování}}
- **Teplota klíčení:** {{min}}–{{optimální}} °C
- **Doba klíčení:** {{dny}} dní

## Pěstování
- **Požadavky na světlo:** {{plné slunce / polostín / stín}}
- **Rozestup:** {{cm}} cm
- **Doba do sklizně:** {{dny}} dní od výsevu

## Poznámky
{{speciální požadavky, tipy, kompanionské rostliny apod.}}
```

### Místo (`sablony/misto.md`)

```markdown
---
nazev: "{{nazev}}"
typ: "{{venku / zakryté / vnitřní}}"
---

# {{nazev}}

## Parametry
- **Typ:** {{venku / zakryté / vnitřní}}
- **Světlo:** {{přirozené / umělé / kombinace}}
- **Vytápění:** {{ano / ne}}
- **Teplotní rozsah:** {{popis, např. "odpovídá venkovní teplotě" nebo "udržováno 18-25°C"}}

## Kapacita
{{volitelný popis — kolik truhlíků, záhonů, metrů čtverečních}}

## Poznámky
{{cokoliv specifického — např. "přístup k vodě", "stíněno od 14h"}}
```

### Profil uživatele (`data/profil.md`)

```markdown
---
lokalita: "{{město}}"
oblast: "{{nížina / pahorkatina / vysočina / hory}}"
nadmorska_vyska: {{m n.m.}}
klimaticka_zona: "{{mírná / chladnější mírná / teplá mírná}}"
---

# Profil zahrádkáře

## Lokalita
- **Město:** {{město}}
- **Oblast:** {{popis}}
- **Nadmořská výška:** {{m}} m n.m.
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
{{specifika lokality — např. "větrná poloha", "jižní svah"}}
```

Typické teploty Pompo vyplní na základě znalosti české klimatologie pro danou lokalitu. Žádné meteo API.

### Výsev (`sablony/vysev.md`)

```markdown
---
rostlina: "{{soubor-rostliny-bez-pripony}}"
misto: "{{soubor-mista-bez-pripony}}"
datum_vysevu: {{YYYY-MM-DD}}
stav: "{{zaseto / klíčí / roste / přesazeno / sklizeno / uhynulo}}"
---

# {{nazev rostliny}} — {{nazev místa}}

- **Zaseto:** {{datum}}
- **Stav:** {{stav}}
- **Přesazeno kam:** {{volitelné — místo, kam bylo přesazeno}}
- **Poznámky:** {{volitelné}}

## Stavy a přechody

Platné přechody stavů:
- `zaseto` → `klíčí` | `uhynulo`
- `klíčí` → `roste` | `přesazeno` | `uhynulo`
- `roste` → `sklizeno` | `uhynulo`
- `přesazeno` → `roste` | `uhynulo`
```

### Ganttův diagram (`data/gantt.md`)

Generovaný .md soubor s vizuálním přehledem výsevů v čase, seskupených podle míst:

```markdown
# Plán výsevů 2026

## Foliovník
| Rostlina        | Bře | Dub | Kvě | Čvn | Čvc | Srp | Zář |
|-----------------|-----|-----|-----|-----|-----|-----|-----|
| Rajčata měňavka | [===výsev===]   | [========pěstování========] |
| Paprika         | [===výsev===]   | [========pěstování========] |

## Zahrada
| Rostlina        | Bře | Dub | Kvě | Čvn | Čvc | Srp | Zář |
|-----------------|-----|-----|-----|-----|-----|-----|-----|
| Hrášek          |     | [===přímý výsev========================] |
```

Přesný formát Ganttova diagramu bude určen při implementaci — klíčové je, aby byl čitelný v .md a vhodný k tisku.

## Skills — chování

### setup

- **Aktivace:** první kontakt s Pompem, nebo žádost o změnu nastavení
- **Chování:**
  1. Zeptá se na lokalitu (město)
  2. Z lokality odvodí oblast, nadmořskou výšku, klimatickou zónu, typické teploty
  3. Vytvoří `data/profil.md`
  4. Zeptá se na dostupná místa (jedno po druhém)
  5. Pro každé místo vytvoří .md v `data/mista/`
- **Styl:** konverzační, jedno po druhém, přirozený jazyk

### hledej-rostlinu

- **Aktivace:** "najdi mi info o X", "přišlo mi osivo X z Y", "přidej rostlinu X"
- **Chování:**
  1. Zkontroluje, zda `data/rostliny/{{nazev}}.md` už existuje
  2. Pokud ne: vyhledá na webu (WebSearch), prioritně na stránce prodejce pokud je uvedena, doplní obecnými zdroji
  3. Vytvoří .md ze šablony `sablony/rostlina.md`, vyplní co najde, co nenajde označí jako "nezjištěno"
  4. Pokud existuje: zobrazí existující data, nabídne aktualizaci
- **Hromadný režim:** uživatel může zadat více rostlin najednou ("přišlo mi 20 druhů osiva z permaseminka.cz: X, Y, Z...") a Pompo je zpracuje postupně

### co-zasit

- **Aktivace:** "co mám teď zasít?", "kam dát rajčata?", "co do foliovníku?"
- **Chování:**
  1. Přečte `data/profil.md` — lokalita, typické teploty pro aktuální měsíc
  2. Přečte všechna místa z `data/mista/`
  3. Přečte rostliny z `data/rostliny/`
  4. Porovná parametry rostlin s aktuálními podmínkami míst
  5. Doporučí:
     - Co je aktuálně vhodné k výsevu
     - Kam to dát (které místo nejlépe odpovídá potřebám)
     - Co ještě počká a kdy bude vhodný čas
- **Rozhodovací logika:** kombinuje teplotu klíčení rostliny + typ světla + termín výsevu vs. typ místa + aktuální sezónní teploty odvozené z profilu

### stav

- **Aktivace:** "co mám zaseto?", "přehled", "stav výsevů", "gantt", "aktualizuj gantt"
- **Chování:**
  1. Přečte všechny soubory z `data/vysevy/`
  2. Zobrazí přehled — co, kde, kdy, v jakém stavu
  3. Na žádost aktualizuje stav výsevu (např. "rajčata klíčí")
  4. Generuje/aktualizuje `data/gantt.md` s Ganttovým diagramem
- **Gantt:** seskupený podle míst, časová osa po měsících, vizuálně čitelný v .md

## Kontextové instrukce (`hooks/context-session.md`)

Krátký soubor injektovaný do každé Claude Code session. Obsahuje:

- Kdo je Pompo (zahradní asistent)
- Jazyk komunikace: čeština
- Kde jsou data: `data/` adresář
- Kde jsou šablony: `sablony/` adresář
- Jak aktivovat skills na základě přirozeného jazyka
- Základní pravidla (např. "vždy zkontroluj existující .md před vytvořením nového")

## Konvence názvů souborů

Názvy souborů v `data/` adresářích používají kebab-case bez diakritiky:
- Mezery → pomlčka
- Diakritika → základní znak (č→c, ř→r, ž→z, ...)
- Vše malými písmeny

Příklady:
- "Rajčata měňavka" → `rajcata-menavka.md`
- "Foliovník s umělým světlem" → `foliovnik-s-umelym-svetlem.md`

Frontmatter pole `rostlina` a `misto` ve výsevech odkazují na **název souboru bez přípony** (tj. `rajcata-menavka`, ne "Rajčata měňavka"). Tím je zajištěno spolehlivé křížové odkazování.

## Rozhodovací logika — jak Pompo přemýšlí

Pompo kombinuje tři vrstvy informací:

1. **Profil** — lokalita → klimatická zóna → typické sezónní teploty pro aktuální měsíc
2. **Místo** — typ (venku/zakryté/vnitřní), světlo, vytápění → reálné podmínky v daném období
3. **Rostlina** — min. teplota klíčení, požadavky na světlo, termín výsevu

### Odvození efektivní teploty místa

Pompo nepoužívá meteo API. Efektivní teplotu místa odvozuje heuristicky na základě vlastností místa a venkovní teploty z profilu:

- **Venku:** odpovídá typické venkovní teplotě pro daný měsíc z profilu
- **Zakryté (skleník, foliovník bez vytápění):** Pompo odhadne teplotní bonus oproti venku na základě typu zakrytí (typicky +5-15°C přes den, v noci blíže venkovní teplotě)
- **Zakryté s vytápěním:** teplotní rozsah uvedený v parametrech místa (např. "udržováno 18-25°C")
- **Vnitřní:** stabilní teplota dle parametrů místa

Toto je záměrně heuristický přístup — Claude má dostatečné znalosti o tom, jak se chová teplota ve skleníku vs. venku, a dokáže z vlastností místa odvodit rozumný odhad.

### Příklad rozhodování (březen, Pardubice)

- Venkovní teplota: ~5-10°C
- Foliovník s umělým světlem a vytápěním: ~20-25°C
- Rajčata: klíčení min. 20°C, předpěstování únor-březen
- **Doporučení:** rajčata do foliovníku, venku ještě ne

### Zohlednění existujících výsevů

Skill `co-zasit` přečte i `data/vysevy/`, aby:
- Nedoporučoval zasít to, co už je zaseto
- Upozornil na konflikty (např. kapacita místa)
- Mohl doporučit přesazení rostlin, které jsou připravené jít ven

## Budoucí rozšíření (mimo scope první verze)

- Připomínky (proaktivní režim)
- Fotodokumentace
- Meteo API napojení
- Sdílení dat mezi uživateli
- Export/tisk plánu výsevů
