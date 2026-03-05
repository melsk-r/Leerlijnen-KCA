# Leerroute StUF – Van XML tot Gemeentelijke Uitwisselstandaard

> Dit document schetst de hoofdlijnen van een cursus die stapsgewijs toewerkt naar begrip van StUF (Standaard Uitwisseling Formaat). Het wordt gaandeweg aangevuld en verbeterd.

---

## Deel 1 – Basiskennis XML

### 1.1 Wat is XML?

#### Het probleem: hoe wissel je gegevens uit?

Stel je voor: twee gemeentelijke systemen moeten gegevens over een inwoner uitwisselen. Het ene systeem slaat gegevens op in een database, het andere in een heel ander formaat. Hoe zorg je ervoor dat beide systemen elkaars gegevens begrijpen?

Je zou de gegevens in platte tekst kunnen sturen, bijvoorbeeld:

```
Jan de Vries, Kerkstraat 12, 3511AB, Utrecht
```

Maar dit levert direct problemen op:
- Wat als iemand twee achternamen heeft? Waar begint het adres dan?
- Hoe weet het ontvangende systeem welk veld wat is?
- Wat als er een komma in een adres staat?

Er is dus een manier nodig om gegevens **gestructureerd** vast te leggen, zodat zowel mensen als computers ondubbelzinnig kunnen begrijpen wat elk gegeven betekent.

#### Markup: tekst met betekenis

De oplossing heet **markup** (letterlijk: "opmaak" of "annotatie"). Het idee is simpel: je plaatst **labels** (ook wel "tags" genoemd) om stukjes tekst heen, zodat duidelijk is wat elk stukje betekent.

Een **tag** herken je aan de punthaken `<` en `>`. Een tag heeft altijd een naam die beschrijft wat de inhoud voorstelt. Tags komen in paren: een **openingstag** en een **sluitingstag**. De sluitingstag begint met `</`.

Voorbeeld:

```xml
<voornaam>Jan</voornaam>
```

Hier is:
- `<voornaam>` de **openingstag** – deze markeert het begin van het gegeven
- `Jan` de **inhoud** (of: tekstinhoud) – het feitelijke gegeven
- `</voornaam>` de **sluitingstag** – deze markeert het einde van het gegeven

Het geheel – openingstag + inhoud + sluitingstag – noemen we een **element**. Het element hierboven heet "voornaam" en bevat de waarde "Jan".

#### Wat is XML precies?

**XML** staat voor **eXtensible Markup Language** – vrij vertaald: een *uitbreidbare opmaaktaal*. Laten we die naam ontleden:

| Begrip | Betekenis |
|---|---|
| **eXtensible** (uitbreidbaar) | Je mag zelf tagnamen bedenken. Er is geen vaste lijst met toegestane tags – je kiest namen die passen bij jouw gegevens. |
| **Markup** (opmaak/annotatie) | Je voegt labels (tags) toe aan tekst om er structuur en betekenis aan te geven. |
| **Language** (taal) | Er zijn vaste regels waaraan je je moet houden, zodat computers de tekst betrouwbaar kunnen verwerken. |

XML is dus een **set afspraken** over hoe je gegevens in tekstformaat vastlegt, zodat:
1. Een mens kan lezen en begrijpen wat er staat
2. Een computer de gegevens automatisch kan verwerken
3. Verschillende systemen (ongeacht programmeertaal of platform) de gegevens kunnen uitwisselen

#### Een eerste volledig voorbeeld

Laten we het inwoner-voorbeeld van hierboven nu in XML vastleggen:

```xml
<inwoner>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <adres>
    <straat>Kerkstraat</straat>
    <huisnummer>12</huisnummer>
    <postcode>3511AB</postcode>
    <woonplaats>Utrecht</woonplaats>
  </adres>
</inwoner>
```

Merk op:
- Elk gegeven zit in een eigen element met een beschrijvende naam
- Elementen kunnen **genest** worden: het element `<adres>` bevat zelf weer elementen (`<straat>`, `<huisnummer>`, etc.)
- Door de nesting ontstaat een **boomstructuur** (hiërarchie): `inwoner` is de "stam", met `voornaam`, `achternaam` en `adres` als "takken"
- Het buitenste element (`<inwoner>`) heet het **root-element** (wortel-element) – elk XML-document heeft er precies één
- De **inspringen** (witruimte) zijn niet verplicht, maar maken het voor mensen veel leesbaarder

#### Vergelijking met HTML

Misschien heb je weleens gehoord van **HTML** – de taal waarmee webpagina's worden gemaakt. HTML gebruikt ook tags, maar er is een belangrijk verschil:

| | HTML | XML |
|---|---|---|
| **Doel** | Beschrijven hoe tekst er *uitziet* (presentatie) | Beschrijven wat gegevens *betekenen* (data) |
| **Tagnamen** | Vast: `<h1>`, `<p>`, `<div>`, etc. | Vrij te kiezen: `<inwoner>`, `<postcode>`, etc. |
| **Strengheid** | Vergevingsgezind: een browser probeert fouten te repareren | Streng: bij een fout stopt de verwerking |

HTML zegt: *"Dit is een koptekst, dit is een alinea."*
XML zegt: *"Dit is een voornaam, dit is een postcode."*

Voor gegevensuitwisseling tussen systemen is XML daarom veel geschikter dan HTML. Het gaat immers niet om hoe iets eruitziet, maar om wat de gegevens **betekenen**.

#### Waarom XML? De voordelen

1. **Leesbaar voor mens én machine**
   XML is gewone tekst. Je kunt het openen in Kladblok/Notepad en direct zien wat erin staat. Tegelijkertijd kunnen programma's het betrouwbaar verwerken.

2. **Platform- en taalonafhankelijk**
   Of een systeem nu in Java, C#, Python of iets anders is geschreven – XML kan overal gelezen en geschreven worden. Het is gewoon tekst.

3. **Zelfbeschrijvend**
   Doordat de tagnamen beschrijven wat de inhoud is, heb je geen apart document nodig om te begrijpen wat `<postcode>3511AB</postcode>` betekent.

4. **Uitbreidbaar**
   Je kunt altijd nieuwe elementen toevoegen zonder bestaande structuren te breken. Dit maakt XML geschikt voor standaarden die in de loop der tijd groeien.

5. **Valideerbaar**
   Met schema's (zoals XSD, dat later in deze cursus aan bod komt) kun je formeel vastleggen welke elementen en waarden zijn toegestaan, en automatisch controleren of een document aan de regels voldoet.

#### Waar wordt XML in de praktijk gebruikt?

XML wordt breed ingezet, onder meer voor:

- **Gegevensuitwisseling tussen organisaties** – Denk aan gemeenten die inwonersgegevens uitwisselen (precies waar StUF voor is ontworpen)
- **Configuratiebestanden** – Veel softwaresystemen gebruiken XML om instellingen vast te leggen
- **Documentformaten** – Microsoft Office-bestanden (.docx, .xlsx) zijn intern ZIP-bestanden gevuld met XML
- **Webservices (SOAP)** – Een veelgebruikt protocol voor communicatie tussen systemen, gebaseerd op XML-berichten
- **RSS-feeds** – Nieuwsfeeds op websites worden in XML uitgedrukt

In de context van deze cursus is het eerste punt het meest relevant: XML als drager voor gestructureerde gegevensuitwisseling tussen gemeentelijke systemen.

#### Samenvatting

| Concept | Uitleg |
|---|---|
| **Tag** | Een label tussen punthaken, bijv. `<voornaam>` |
| **Element** | Een openingstag + inhoud + sluitingstag, bijv. `<voornaam>Jan</voornaam>` |
| **Root-element** | Het buitenste element dat alle andere elementen omvat |
| **Nesting** | Elementen binnen elementen, waardoor een boomstructuur ontstaat |
| **XML** | Een uitbreidbare opmaaktaal voor het gestructureerd vastleggen van gegevens in tekstvorm |

> **Kernpunt:** XML is een manier om gegevens zo op te schrijven dat zowel mensen als computers ze ondubbelzinnig kunnen lezen. Je gebruikt zelfgekozen tags om betekenis te geven aan stukjes tekst, en je houdt je aan vaste regels zodat alles machinaal verwerkbaar blijft.

### 1.2 XML-syntax

In sectie 1.1 heb je kennisgemaakt met het idee achter XML: gegevens structureren met tags. Nu gaan we precies bekijken **welke regels** daarbij gelden. XML is streng: als je je niet aan de regels houdt, weigert een computer het bestand te verwerken. Dit is bewust zo ontworpen – het voorkomt dat er verwarring ontstaat over de betekenis van gegevens.

#### Elementen: de bouwstenen van XML

Je hebt in 1.1 al gezien dat een **element** bestaat uit een openingstag, inhoud en een sluitingstag:

```xml
<woonplaats>Utrecht</woonplaats>
```

Laten we nu dieper kijken naar wat er allemaal mogelijk is met elementen.

**Elementen met tekst als inhoud**

De eenvoudigste vorm: een element bevat een stukje tekst (een waarde).

```xml
<voornaam>Jan</voornaam>
<huisnummer>12</huisnummer>
<geboortedatum>1985-03-15</geboortedatum>
```

Let op: voor XML is alles tekst. Het getal `12` en de datum `1985-03-15` zijn voor XML gewoon tekenreeksen. Pas later, met XSD (Deel 2), kun je afdwingen dat een waarde een getal of datum moet zijn.

**Elementen met kind-elementen als inhoud**

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

**Lege elementen**

Soms wil je een element opnemen dat (nog) geen inhoud heeft. Dat kan op twee manieren:

```xml
<overlijdensdatum></overlijdensdatum>
```

of de verkorte notatie:

```xml
<overlijdensdatum/>
```

Beide vormen zijn gelijkwaardig. De verkorte notatie met `/>` wordt vaak gebruikt wanneer een element bewust leeg is.

**Belangrijk: tekst en kind-elementen niet mengen**

In de praktijk bevat een element óf tekst óf kind-elementen, maar niet allebei door elkaar. Hoewel XML dit technisch toestaat (het heet "mixed content"), wordt het in gegevensuitwisseling zoals StUF vrijwel nooit gebruikt. Houd als vuistregel aan:

- Een element bevat tekst → het is een "bladknooppunt" (uiteinde van de boom)
- Een element bevat kind-elementen → het is een "takknooppunt" (structurerend element)

#### Attributen: extra informatie bij een element

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

**Wanneer een attribuut en wanneer een kind-element?**

Dit is een veelgestelde vraag. Er is geen harde regel, maar een handige vuistregel:

| Gebruik een **attribuut** voor... | Gebruik een **kind-element** voor... |
|---|---|
| Metagegevens (informatie *over* het gegeven) | De feitelijke gegevens zelf |
| Korte, enkelvoudige waarden | Waarden die zelf weer structuur kunnen bevatten |
| Identificatoren, typen, datumstempels | Inhoudelijke data |

In het voorbeeld hierboven is `type="woonadres"` metadata (wat voor *soort* adres is het?), terwijl `<straat>Kerkstraat</straat>` het feitelijke gegeven is.

> **Let op:** In StUF-berichten worden attributen veelvuldig gebruikt, onder meer voor het aangeven van het mutatiesoort en de aanduiding van sleutelwaarden. Je zult ze dus vaak tegenkomen.

#### Tekstinhoud en speciale tekens

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

#### Welgevormdheid: de basisregels van XML

Een XML-document dat aan alle syntaxregels voldoet, heet **welgevormd** (Engels: *well-formed*). Een document dat niet welgevormd is, wordt door elke XML-verwerker geweigerd – er is geen "beetje goed". Dit is een fundamenteel verschil met bijvoorbeeld HTML, waar browsers fouten proberen te herstellen.

De regels voor welgevormdheid zijn:

**Regel 1: Er is precies één root-element**

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

**Regel 2: Elke openingstag heeft een bijbehorende sluitingstag**

Fout:
```xml
<voornaam>Jan
```

Goed:
```xml
<voornaam>Jan</voornaam>
```

(Of bij een leeg element: `<overlijdensdatum/>`)

**Regel 3: Elementen moeten correct genest zijn**

Tags mogen niet "over elkaar heen" staan. Denk aan het openen en sluiten van haakjes: wat je het laatst opent, sluit je het eerst.

Fout (kruisende tags):
```xml
<naam><voornaam>Jan</naam></voornaam>
```

Goed (correct genest):
```xml
<naam><voornaam>Jan</voornaam></naam>
```

**Regel 4: Attribuutwaarden staan tussen aanhalingstekens**

Fout:
```xml
<inwoner bsn=123456789>
```

Goed:
```xml
<inwoner bsn="123456789">
```

**Regel 5: XML is hoofdlettergevoelig**

`<Naam>`, `<naam>` en `<NAAM>` zijn drie *verschillende* elementen. De sluitingstag moet exact overeenkomen met de openingstag.

Fout:
```xml
<Voornaam>Jan</voornaam>
```

Goed:
```xml
<voornaam>Jan</voornaam>
```

#### Naamgevingsregels voor elementen en attributen

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

#### Een compleet voorbeeld

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

#### Samenvatting

| Concept | Uitleg |
|---|---|
| **Kind-element** | Een element dat binnen een ander element staat |
| **Ouder-element** | Het omvattende element |
| **Attribuut** | Extra informatie in de openingstag: `naam="waarde"` |
| **Leeg element** | Een element zonder inhoud: `<tag/>` |
| **Entity reference** | Vervanging voor speciale tekens, bijv. `&lt;` voor `<` |
| **Welgevormd** | Een XML-document dat aan alle syntaxregels voldoet |

> **Kernpunt:** XML heeft strikte syntaxregels: precies één root-element, correct geneste en gesloten tags, aanhalingstekens rond attribuutwaarden, en hoofdlettergevoelige namen. Als een document niet welgevormd is, wordt het geweigerd. Deze strengheid garandeert dat gegevens altijd ondubbelzinnig verwerkt kunnen worden.

### 1.3 Namespaces
- Waarom namespaces? Voorkomen van naamconflicten
- Declaratie met `xmlns` en prefixen
- Default namespace vs. prefixed namespace
- Belang van namespaces voor StUF (vooruitblik)

### 1.4 Speciale XML-constructies
- XML-declaratie (`<?xml version="1.0" encoding="UTF-8"?>`)
- CDATA-secties
- Commentaar
- Processing instructions

---

## Deel 2 – XML Schema (XSD)

### 2.1 Wat is XSD?
- XML Schema Definition als formele beschrijving van de structuur van XML-documenten
- Verschil met DTD: meer expressiviteit, zelf ook XML
- Rol van XSD bij validatie

### 2.2 Basisconcepten van XSD
- Elementen definiëren (`xs:element`)
- Simpele typen (`xs:simpleType`) en complexe typen (`xs:complexType`)
- Ingebouwde datatypen: `xs:string`, `xs:integer`, `xs:date`, `xs:boolean`, etc.
- Restricties (facets): `minLength`, `maxLength`, `pattern`, `enumeration`

### 2.3 Structuur en compositie
- Sequenties (`xs:sequence`), keuzes (`xs:choice`) en `xs:all`
- Kardinaliteit: `minOccurs` en `maxOccurs`
- Attributen definiëren (`xs:attribute`)
- Geneste vs. globale type-definities

### 2.4 Hergebruik en modulariteit
- Naamgeving van typen en hergebruik via `type`-verwijzingen
- `xs:include` en `xs:import` voor modulaire schema's
- Abstracte typen en `xs:extension` / `xs:restriction`
- Substitutiegroepen

### 2.5 Namespaces in XSD
- `targetNamespace` en `elementFormDefault`
- Koppeling tussen XML-document en schema (`xsi:schemaLocation`)
- Meerdere namespaces binnen één schema-set

---

## Deel 3 – Introductie StUF

### 3.1 Wat is StUF?
- StUF als landelijke berichtenstandaard voor gemeentelijke gegevensuitwisseling
- Oorsprong en beheer (VNG Realisatie / KING)
- Doel: uniforme, gestandaardiseerde communicatie tussen gemeentelijke applicaties
- Relatie met het Stelsel van Basisregistraties

### 3.2 StUF in context
- Positie ten opzichte van andere standaarden (bijv. RSGB, RGBZ, ZDS, ZGW)
- Sectormodellen: StUF-BG (Basisgegevens), StUF-ZKN (Zaken), StUF-EF (Erfpacht), etc.
- Synchrone vs. asynchrone berichten
- Relatie met SOAP/WSDL-webservices

### 3.3 Opbouw van een StUF-bericht
- Envelopstructuur: SOAP-envelope → StUF-header → StUF-body
- Stuurgegevens: zender, ontvanger, referentienummer, tijdstip, entiteittype
- Kerngegevens (de feitelijke data)
- Functie van het `mutatiesoort`-attribuut (T, W, V, E, I, S)

### 3.4 Berichttypen in StUF
- Kennisgevingsberichten (Lk, La) – meldingen van mutaties
- Vraag-/antwoordberichten (Lv, La) – opvragen van gegevens
- Vrije berichten (Di, Du) – specifieke diensten
- Foutberichten (Fo)
- Asynchroon bevestigen (Bv)

### 3.5 StUF-schema's en XSD
- Hoe StUF gebruikmaakt van XSD voor berichtdefinities
- Gelaagdheid: stuf0301.xsd → sectormodel-schema → koppelvlakschema
- Namespaces binnen StUF (`http://www.egem.nl/StUF/StUF0301`, etc.)
- Hergebruik van typen en elementen via imports

---

## Deel 4 – Verdieping en Praktijk

### 4.1 StUF-berichten lezen en schrijven
- Een concreet StUF-bericht ontleden (voorbeeld)
- Elementen matchen met het bijbehorende XSD-schema
- Veelvoorkomende patronen herkennen

### 4.2 Validatie en foutopsporing
- XML-validatie tegen StUF-schema's
- Veelgemaakte fouten en valkuilen
- Tooling: XML-editors, SoapUI, validatiescripts

### 4.3 StUF in de praktijk
- Hoe gemeenten StUF inzetten in hun applicatielandschap
- Koppelvlakken en adapters
- Migratiepaden: van StUF naar API's (Common Ground / Haal Centraal)

---

## Bijlagen (nog aan te vullen)

- Begrippenlijst
- Overzicht van StUF-namespaces
- Voorbeeldberichten
- Verwijzingen naar officiële specificaties en documentatie

---

*Dit document is een levend document en wordt stapsgewijs uitgebreid en verbeterd.*
