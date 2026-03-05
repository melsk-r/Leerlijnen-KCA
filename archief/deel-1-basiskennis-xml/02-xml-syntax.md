---
title: "1.2 XML-syntax"
weight: 2
---

# 1.2 XML-syntax

In sectie 1.1 heb je kennisgemaakt met het idee achter XML: gegevens structureren met tags. Nu gaan we precies bekijken **welke regels** daarbij gelden. XML is streng: als je je niet aan de regels houdt, weigert een computer het bestand te verwerken. Dit is bewust zo ontworpen – het voorkomt dat er verwarring ontstaat over de betekenis van gegevens.

## Elementen: de bouwstenen van XML

Je hebt in 1.1 al gezien dat een **element** bestaat uit een openingstag, inhoud en een sluitingstag:

```xml
<woonplaats>Utrecht</woonplaats>
```

Laten we nu dieper kijken naar wat er allemaal mogelijk is met elementen.

### Elementen met tekst als inhoud

De eenvoudigste vorm: een element bevat een stukje tekst (een waarde).

```xml
<voornaam>Jan</voornaam>
<huisnummer>12</huisnummer>
<geboortedatum>1985-03-15</geboortedatum>
```

Let op: voor XML is alles tekst. Het getal `12` en de datum `1985-03-15` zijn voor XML gewoon tekenreeksen. Pas later, met XSD (Deel 2), kun je afdwingen dat een waarde een getal of datum moet zijn.

### Elementen met kind-elementen als inhoud

Een element kan andere elementen bevatten. Dit heb je al gezien bij het adres-voorbeeld:

```xml
<adres>
  <straat>Kerkstraat</straat>
  <huisnummer>12</huisnummer>
  <postcode>3511AB</postcode>
  <woonplaats>Utrecht</woonplaats>
</adres>
```

De elementen `<straat>`, `<huisnummer>`, `<postcode>` en `<woonplaats>` zijn **kind-elementen** (of: child elements) van `<adres>`. Het element `<adres>` is het **ouder-element** (parent element).

Je kunt zo diep nesten als nodig:

```xml
<gemeente>
  <naam>Utrecht</naam>
  <inwoner>
    <naam>
      <voornaam>Jan</voornaam>
      <achternaam>de Vries</achternaam>
    </naam>
    <adres>
      <straat>Kerkstraat</straat>
      <huisnummer>12</huisnummer>
    </adres>
  </inwoner>
</gemeente>
```

Hier is `<gemeente>` ouder van `<inwoner>`, die weer ouder is van `<naam>` en `<adres>`, enzovoort. Zo ontstaat een **boomstructuur**.

### Lege elementen

Soms wil je een element opnemen dat (nog) geen inhoud heeft. Dat kan op twee manieren:

```xml
<overlijdensdatum></overlijdensdatum>
```

of de verkorte notatie:

```xml
<overlijdensdatum/>
```

Beide vormen zijn gelijkwaardig. De verkorte notatie met `/>` wordt vaak gebruikt wanneer een element bewust leeg is.

### Belangrijk: tekst en kind-elementen niet mengen

In de praktijk bevat een element óf tekst óf kind-elementen, maar niet allebei door elkaar. Hoewel XML dit technisch toestaat (het heet "mixed content"), wordt het in gegevensuitwisseling zoals StUF vrijwel nooit gebruikt. Houd als vuistregel aan:

- Een element bevat tekst → het is een "bladknooppunt" (uiteinde van de boom)
- Een element bevat kind-elementen → het is een "takknooppunt" (structurerend element)

## Attributen: extra informatie bij een element

Naast kind-elementen is er een tweede manier om informatie aan een element te koppelen: **attributen**. Een attribuut staat in de openingstag en heeft de vorm `naam="waarde"`:

```xml
<inwoner burgerservicenummer="123456789">
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
</inwoner>
```

Hier heeft het element `<inwoner>` een attribuut genaamd `burgerservicenummer` met de waarde `123456789`.

**Regels voor attributen:**
- De waarde staat altijd tussen aanhalingstekens (enkele `'...'` of dubbele `"..."`)
- Een element mag meerdere attributen hebben, gescheiden door spaties
- Elk attribuut mag maar één keer voorkomen op hetzelfde element
- Attributen hebben geen volgorde (in tegenstelling tot kind-elementen)

Voorbeeld met meerdere attributen:

```xml
<adres type="woonadres" begindatum="2020-01-15">
  <straat>Kerkstraat</straat>
  <huisnummer>12</huisnummer>
</adres>
```

### Wanneer een attribuut en wanneer een kind-element?

Dit is een veelgestelde vraag. Er is geen harde regel, maar een handige vuistregel:

| Gebruik een **attribuut** voor... | Gebruik een **kind-element** voor... |
|---|---|
| Metagegevens (informatie *over* het gegeven) | De feitelijke gegevens zelf |
| Korte, enkelvoudige waarden | Waarden die zelf weer structuur kunnen bevatten |
| Identificatoren, typen, datumstempels | Inhoudelijke data |

In het voorbeeld hierboven is `type="woonadres"` metadata (wat voor *soort* adres is het?), terwijl `<straat>Kerkstraat</straat>` het feitelijke gegeven is.

> **Let op:** In StUF-berichten worden attributen veelvuldig gebruikt, onder meer voor het aangeven van het mutatiesoort en de aanduiding van sleutelwaarden. Je zult ze dus vaak tegenkomen.

## Tekstinhoud en speciale tekens

De tekst binnen een element mag bijna alles bevatten, maar er zijn een paar tekens die een speciale betekenis hebben in XML. Deze kun je niet zomaar in je tekst gebruiken:

| Teken | Probleem | Oplossing (entity reference) |
|---|---|---|
| `<` | Wordt gelezen als begin van een tag | `&lt;` |
| `>` | Wordt gelezen als einde van een tag | `&gt;` |
| `&` | Wordt gelezen als begin van een entity | `&amp;` |
| `"` | Conflicteert met attribuutwaarden | `&quot;` |
| `'` | Conflicteert met attribuutwaarden | `&apos;` |

Deze vervangingen heten **entity references**. Een voorbeeld:

```xml
<opmerking>Temperatuur is &lt; 0 graden</opmerking>
```

Dit wordt door een computer gelezen als: *"Temperatuur is < 0 graden"*. Als je `<` letterlijk zou schrijven, zou de computer denken dat er een nieuwe tag begint.

## Welgevormdheid: de basisregels van XML

Een XML-document dat aan alle syntaxregels voldoet, heet **welgevormd** (Engels: *well-formed*). Een document dat niet welgevormd is, wordt door elke XML-verwerker geweigerd – er is geen "beetje goed". Dit is een fundamenteel verschil met bijvoorbeeld HTML, waar browsers fouten proberen te herstellen.

De regels voor welgevormdheid zijn:

### Regel 1: Er is precies één root-element

Alles moet binnen één buitenste element staan.

Fout:
```xml
<voornaam>Jan</voornaam>
<achternaam>de Vries</achternaam>
```

Goed:
```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
</persoon>
```

### Regel 2: Elke openingstag heeft een bijbehorende sluitingstag

Fout:
```xml
<voornaam>Jan
```

Goed:
```xml
<voornaam>Jan</voornaam>
```

(Of bij een leeg element: `<overlijdensdatum/>`)

### Regel 3: Elementen moeten correct genest zijn

Tags mogen niet "over elkaar heen" staan. Denk aan het openen en sluiten van haakjes: wat je het laatst opent, sluit je het eerst.

Fout (kruisende tags):
```xml
<naam><voornaam>Jan</naam></voornaam>
```

Goed (correct genest):
```xml
<naam><voornaam>Jan</voornaam></naam>
```

### Regel 4: Attribuutwaarden staan tussen aanhalingstekens

Fout:
```xml
<inwoner bsn=123456789>
```

Goed:
```xml
<inwoner bsn="123456789">
```

### Regel 5: XML is hoofdlettergevoelig

`<Naam>`, `<naam>` en `<NAAM>` zijn drie *verschillende* elementen. De sluitingstag moet exact overeenkomen met de openingstag.

Fout:
```xml
<Voornaam>Jan</voornaam>
```

Goed:
```xml
<voornaam>Jan</voornaam>
```

## Naamgevingsregels voor elementen en attributen

Niet elke tekenreeks mag als element- of attribuutnaam dienen. De regels:

- Een naam **begint** met een letter of een underscore (`_`)
- Daarna mogen letters, cijfers, underscores, koppeltekens (`-`) en punten (`.`) volgen
- Namen mogen **niet** beginnen met een cijfer of een koppelteken
- Namen mogen **geen spaties** bevatten
- Namen die starten met `xml` (in welke hoofdlettercombinatie ook) zijn gereserveerd

| Geldig | Ongeldig | Reden |
|---|---|---|
| `<voornaam>` | `<1e-naam>` | Begint met een cijfer |
| `<achter_naam>` | `<achter naam>` | Bevat een spatie |
| `<geboorte-datum>` | `<xml-datum>` | Begint met "xml" (gereserveerd) |
| `<_tijdelijk>` | `<-test>` | Begint met een koppelteken |

In de praktijk worden in Nederlandse standaarden als StUF vaak **camelCase** (bijv. `geboortedatum`) of **koppeltekens** (bijv. `geboorte-datum`) gebruikt.

## Een compleet voorbeeld

Laten we alles samenbrengen in een iets uitgebreider voorbeeld:

```xml
<kennisgeving>
  <stuurgegevens>
    <zender>BRP-systeem</zender>
    <ontvanger>Zaaksysteem</ontvanger>
    <tijdstip>2026-03-03T10:30:00</tijdstip>
  </stuurgegevens>
  <inhoud>
    <inwoner burgerservicenummer="123456789">
      <naam>
        <voornaam>Jan</voornaam>
        <voorvoegsel>de</voorvoegsel>
        <achternaam>Vries</achternaam>
      </naam>
      <adres type="woonadres">
        <straat>Kerkstraat</straat>
        <huisnummer>12</huisnummer>
        <postcode>3511AB</postcode>
        <woonplaats>Utrecht</woonplaats>
      </adres>
      <overlijdensdatum/>
    </inwoner>
  </inhoud>
</kennisgeving>
```

In dit voorbeeld zie je alle behandelde concepten terug:
- Één **root-element** (`<kennisgeving>`)
- **Nesting** op meerdere niveaus
- **Tekst-elementen** (`<voornaam>Jan</voornaam>`)
- **Attributen** (`burgerservicenummer="123456789"`, `type="woonadres"`)
- Een **leeg element** (`<overlijdensdatum/>`)
- Correcte **welgevormdheid**: elke tag is gesloten, nesting is correct, aanhalingstekens staan waar nodig

> **Tip:** Dit voorbeeld lijkt al een beetje op een StUF-bericht! De structuur met stuurgegevens en inhoud is kenmerkend. In Deel 3 van deze cursus zul je echte StUF-berichten tegenkomen die op dezelfde principes zijn gebouwd.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **Kind-element** | Een element dat binnen een ander element staat |
| **Ouder-element** | Het omvattende element |
| **Attribuut** | Extra informatie in de openingstag: `naam="waarde"` |
| **Leeg element** | Een element zonder inhoud: `<tag/>` |
| **Entity reference** | Vervanging voor speciale tekens, bijv. `&lt;` voor `<` |
| **Welgevormd** | Een XML-document dat aan alle syntaxregels voldoet |

> **Kernpunt:** XML heeft strikte syntaxregels: precies één root-element, correct geneste en gesloten tags, aanhalingstekens rond attribuutwaarden, en hoofdlettergevoelige namen. Als een document niet welgevormd is, wordt het geweigerd. Deze strengheid garandeert dat gegevens altijd ondubbelzinnig verwerkt kunnen worden.
