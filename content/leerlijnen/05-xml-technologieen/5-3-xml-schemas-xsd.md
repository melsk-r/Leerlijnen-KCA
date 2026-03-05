---
title: "5.3 XML Schema's (XSD)"
date: 2026-03-04
weight: 3
leerlijn: 5
paragraaf: "5.3"
---

<div class="back-link-wrapper">
  <a href="{{< relref "../05-xml-technologieen" >}}" class="back-link">Terug naar leerlijn 5</a>
</div>

## 5.3 XML Schema's (XSD)

Kan XML Schema's (XSD's) lezen, begrijpen en opstellen.

---

### Het probleem: XML is te vrij

In sectie 5.1 heb je geleerd hoe je met XML gegevens structureert. Maar XML zelf legt alleen syntaxregels op — *welgevormdheid*. Het zegt niets over **welke** elementen en attributen zijn toegestaan, in welke volgorde ze mogen voorkomen of welke waarden geldig zijn.

Stel: twee gemeentelijke systemen wisselen persoonsgegevens uit via XML. Systeem A stuurt:

```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <geboortedatum>1985-03-15</geboortedatum>
</persoon>
```

Maar systeem B verwacht:

```xml
<persoon>
  <naam>
    <voornaam>Jan</voornaam>
    <achternaam>de Vries</achternaam>
  </naam>
  <geboortedatum>15-03-1985</geboortedatum>
</persoon>
```

Beide documenten zijn **welgevormd** XML. Maar ze zijn niet compatibel: de structuur verschilt (wel of geen `<naam>`-omhulsel) en het datumformaat is anders. Hoe spreek je af welke structuur en welke waarden toegestaan zijn? Hoe controleer je dat automatisch?

### De oplossing: een schema

Een **schema** is een formele beschrijving van de toegestane structuur en inhoud van een XML-document. Het is als een *blauwdruk* of *contract*: het legt vast welke elementen er mogen zijn, in welke volgorde, hoe vaak, en welk type waarde ze mogen bevatten.

Met een schema kun je automatisch controleren of een XML-document geldig is — dit heet **validatie**. Als een document niet voldoet aan het schema, wordt het afgekeurd met een foutmelding die precies aangeeft wat er mis is.

### Wat is XSD precies?

**XSD** staat voor **XML Schema Definition**. Het is een taal om schema's mee te schrijven — en het bijzondere is: **een XSD-bestand is zelf ook XML**. Je gebruikt dus XML om te beschrijven hoe andere XML-documenten eruit moeten zien.

| Eigenschap | Uitleg |
|---|---|
| **Voluit** | XML Schema Definition |
| **Bestandsextensie** | `.xsd` |
| **Formaat** | XML (een XSD is zelf een geldig XML-document) |
| **Doel** | Formeel beschrijven welke structuur en waarden een XML-document mag hebben |
| **Toepassing** | Validatie: automatisch controleren of een XML-document aan de regels voldoet |
| **Beheerder** | W3C (World Wide Web Consortium), dezelfde organisatie achter XML zelf |

### Een eerste voorbeeld

Stel, je wilt vastleggen dat een `<persoon>` precies een `<voornaam>`, een `<achternaam>` en een `<geboortedatum>` moet bevatten:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <xs:element name="persoon">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="voornaam" type="xs:string"/>
        <xs:element name="achternaam" type="xs:string"/>
        <xs:element name="geboortedatum" type="xs:date"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>

</xs:schema>
```

| Regel | Betekenis |
|---|---|
| `<xs:schema xmlns:xs="...">` | Het root-element van elk XSD-bestand; de prefix `xs` verwijst naar de XML Schema-namespace |
| `<xs:element name="persoon">` | Er moet een element `<persoon>` bestaan |
| `<xs:complexType>` | Het element `<persoon>` heeft een complexe opbouw (het bevat kind-elementen) |
| `<xs:sequence>` | De kind-elementen moeten in de opgegeven volgorde voorkomen |
| `<xs:element name="voornaam" type="xs:string"/>` | Er moet een element `<voornaam>` zijn, met een tekstwaarde |
| `<xs:element name="geboortedatum" type="xs:date"/>` | Er moet een element `<geboortedatum>` zijn, met een datumwaarde (formaat `JJJJ-MM-DD`) |

**Geldig** XML-document:

```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <geboortedatum>1985-03-15</geboortedatum>
</persoon>
```

**Ongeldig** XML-document (drie fouten):

```xml
<persoon>
  <achternaam>de Vries</achternaam>
  <voornaam>Jan</voornaam>
  <geboortedatum>15 maart 1985</geboortedatum>
</persoon>
```

1. `<achternaam>` staat vóór `<voornaam>` — het schema eist een vaste volgorde
2. `<geboortedatum>` bevat `"15 maart 1985"` — het schema eist `xs:date` (formaat `JJJJ-MM-DD`)

### Welgevormd vs. geldig

Dit is een cruciaal onderscheid:

| Begrip | Betekenis | Gecontroleerd door |
|---|---|---|
| **Welgevormd** (well-formed) | Het document voldoet aan de XML-syntaxregels (zie 5.1 en 5.4) | Elke XML-parser |
| **Geldig** (valid) | Het document voldoet aan een specifiek schema (XSD) | Een XML-validator met het bijbehorende schema |

Een document moet **eerst** welgevormd zijn voordat het gevalideerd kan worden.

```
Stap 1: Is het welgevormd?  → Nee  → Afgekeurd (syntaxfout)
                             → Ja   → Stap 2: Is het geldig volgens het schema?
                                              → Nee  → Afgekeurd (validatiefout)
                                              → Ja   → Geaccepteerd ✓
```

### XSD vs. DTD

Voordat XSD bestond (W3C-standaard in 2001), werden structuren beschreven met **DTD's** (Document Type Definitions). XSD is de opvolger:

| Eigenschap | DTD | XSD |
|---|---|---|
| **Formaat** | Eigen, niet-XML syntax | XML |
| **Datatypen** | Nauwelijks (alleen tekst) | Uitgebreid: string, integer, date, boolean, etc. |
| **Namespace-ondersteuning** | Geen | Volledig |
| **Restricties** | Zeer beperkt | Uitgebreid: patronen, min/max, enumeraties, etc. |
| **Hergebruik** | Beperkt | Typen, includes, imports, overerving |

> **StUF-context:** StUF gebruikt uitsluitend XSD. DTD's zijn hier niet relevant, maar het is goed om het verschil te kennen als je oudere documentatie tegenkomt.

### Waarom is XSD belangrijk voor StUF?

StUF-berichten worden gedefinieerd door een set XSD-schema's:

1. **Contract tussen systemen** — Het schema legt exact vast hoe een bericht eruit moet zien. Als een leverancier een systeem bouwt dat StUF-berichten verstuurt, moet elk bericht valideren tegen het schema.
2. **Automatische validatie** — Vóórdat een bericht verwerkt wordt, kan het ontvangend systeem het automatisch valideren.
3. **Documentatie** — De XSD-schema's zijn tegelijk de technische documentatie van de standaard.
4. **Gelaagde opbouw** — StUF-schema's zijn modulair opgebouwd in lagen (basisschema → sectormodel → koppelvlak), mogelijk gemaakt door XSD-mechanismes als `import` en type-overerving.

---

### De structuur van een XSD-bestand

Elk XSD-bestand is zelf een XML-document. Het root-element is altijd `<xs:schema>`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <!-- Hier komen je definities -->

</xs:schema>
```

| Onderdeel | Betekenis |
|---|---|
| `<xs:schema>` | Het root-element van elk XSD-bestand |
| `xmlns:xs="http://www.w3.org/2001/XMLSchema"` | De namespace-declaratie die de prefix `xs` koppelt aan de XML Schema-namespace |

De prefix `xs` is conventie — je kunt ook `xsd` kiezen. In de praktijk zie je zowel `xs:` als `xsd:`.

### Elementen definiëren met `xs:element`

De meest fundamentele bouwsteen van XSD:

```xml
<xs:element name="voornaam" type="xs:string"/>
```

Dit zegt: *"Er mag een element `<voornaam>` bestaan, en de inhoud moet tekst zijn."*

| Attribuut | Betekenis | Voorbeeld |
|---|---|---|
| `name` | De naam van het element in het XML-document | `name="voornaam"` |
| `type` | Het datatype van de inhoud | `type="xs:string"` |

### Simpele vs. complexe typen

Een **type** beschrijft welke waarden een element mag bevatten. XSD kent twee soorten:

1. **Simpele typen** (`simpleType`) — het element bevat alleen een waarde (tekst, getal, datum), geen kind-elementen
2. **Complexe typen** (`complexType`) — het element bevat kind-elementen en/of attributen

### Ingebouwde datatypen

XSD levert een uitgebreide set **ingebouwde datatypen** mee:

**Teksttypen:**

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:string` | Willekeurige tekst | `"Jan de Vries"` |
| `xs:normalizedString` | Tekst zonder tabs en regelovergangen | `"Jan de Vries"` |
| `xs:token` | Tekst zonder overbodige witruimte | `"Jan de Vries"` |

**Numerieke typen:**

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:integer` | Geheel getal | `42`, `-7` |
| `xs:positiveInteger` | Geheel getal > 0 | `1`, `100` |
| `xs:decimal` | Decimaal getal | `3.14`, `-0.5` |

**Datum- en tijdtypen:**

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:date` | Datum (jaar-maand-dag) | `2026-03-04` |
| `xs:time` | Tijd (uur:minuut:seconde) | `14:30:00` |
| `xs:dateTime` | Datum + tijd | `2026-03-04T14:30:00` |
| `xs:gYear` | Alleen een jaar | `2026` |
| `xs:duration` | Tijdsduur | `P1Y2M3D` (1 jaar, 2 maanden, 3 dagen) |

**Overige typen:**

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:boolean` | Waar of onwaar | `true`, `false`, `1`, `0` |
| `xs:anyURI` | Een URI/URL | `http://www.example.nl` |

> **Let op:** Het kiezen van het juiste datatype is belangrijk. Als je `xs:string` gebruikt voor een geboortedatum, accepteert het schema elke tekst — ook "gisteren" of "binnenkort". Met `xs:date` dwing je het formaat `JJJJ-MM-DD` af.

### Simpele typen met restricties (facets)

Vaak wil je de toegestane waarden verder beperken. Bijvoorbeeld: een postcode moet precies 4 cijfers gevolgd door 2 letters zijn. Daarvoor gebruik je **restricties** (ook wel **facets** genoemd) binnen een `<xs:simpleType>`:

```xml
<xs:simpleType name="Postcode">
  <xs:restriction base="xs:string">
    <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
  </xs:restriction>
</xs:simpleType>
```

**Alle beschikbare restricties:**

| Facet | Werkt op | Betekenis | Voorbeeld |
|---|---|---|---|
| `xs:minLength` | Tekst | Minimaal aantal tekens | `<xs:minLength value="1"/>` |
| `xs:maxLength` | Tekst | Maximaal aantal tekens | `<xs:maxLength value="100"/>` |
| `xs:length` | Tekst | Exact aantal tekens | `<xs:length value="6"/>` |
| `xs:pattern` | Tekst | Moet voldoen aan een reguliere expressie | `<xs:pattern value="[0-9]{4}[A-Z]{2}"/>` |
| `xs:enumeration` | Alle typen | Waarde moet uit een vaste lijst komen | `<xs:enumeration value="M"/>` |
| `xs:minInclusive` | Getallen/datums | Minimale waarde (inclusief) | `<xs:minInclusive value="0"/>` |
| `xs:maxInclusive` | Getallen/datums | Maximale waarde (inclusief) | `<xs:maxInclusive value="999"/>` |
| `xs:totalDigits` | Getallen | Maximaal aantal cijfers totaal | `<xs:totalDigits value="5"/>` |
| `xs:fractionDigits` | Decimalen | Maximaal aantal decimalen | `<xs:fractionDigits value="2"/>` |

**Voorbeeld: enumeratie (vaste keuzelijst)**

```xml
<xs:simpleType name="Geslacht">
  <xs:restriction base="xs:string">
    <xs:enumeration value="M"/>
    <xs:enumeration value="V"/>
    <xs:enumeration value="O"/>
  </xs:restriction>
</xs:simpleType>
```

Dit type accepteert alleen `M`, `V` of `O`. Elke andere waarde is ongeldig.

**Voorbeeld: patroon (reguliere expressie)**

```xml
<xs:simpleType name="BSN">
  <xs:restriction base="xs:string">
    <xs:pattern value="[0-9]{9}"/>
  </xs:restriction>
</xs:simpleType>
```

**Restricties combineren:**

```xml
<xs:simpleType name="Postcode">
  <xs:restriction base="xs:string">
    <xs:length value="6"/>
    <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
  </xs:restriction>
</xs:simpleType>
```

> **StUF-context:** In StUF-schema's worden restricties veelvuldig gebruikt. BSN's moeten precies 9 cijfers zijn, postcodes hebben een vast patroon, en geslachtsaanduidingen komen uit een vaste lijst.

### Complexe typen: elementen met structuur

In de praktijk bevatten de meeste elementen **kind-elementen**. Hiervoor heb je een **complex type** nodig.

**Inline complex type:**

```xml
<xs:element name="persoon">
  <xs:complexType>
    <xs:sequence>
      <xs:element name="voornaam" type="xs:string"/>
      <xs:element name="achternaam" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>
</xs:element>
```

**Benoemd complex type** (herbruikbaar):

```xml
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="voornaam" type="xs:string"/>
    <xs:element name="achternaam" type="xs:string"/>
  </xs:sequence>
</xs:complexType>

<xs:element name="persoon" type="PersoonType"/>
<xs:element name="contactpersoon" type="PersoonType"/>
```

**Complex type met simpele inhoud** — een element met tekst én een attribuut:

```xml
<xs:complexType name="BedragType">
  <xs:simpleContent>
    <xs:extension base="xs:decimal">
      <xs:attribute name="valuta" type="xs:string"/>
    </xs:extension>
  </xs:simpleContent>
</xs:complexType>
```

```xml
<bedrag valuta="EUR">1500.00</bedrag>
```

### Nillable: expliciet "geen waarde"

Soms moet een element aanwezig zijn, maar hoeft het geen waarde te hebben. In XSD:

```xml
<xs:element name="overlijdensdatum" type="xs:date" nillable="true"/>
```

In het XML-document:

```xml
<overlijdensdatum xsi:nil="true"/>
```

> **StUF-context:** Het `nillable`-mechanisme wordt in StUF gebruikt om aan te geven dat een gegeven bewust niet gevuld is. Een leeg element kan "nog niet ingevuld" betekenen, terwijl `xsi:nil="true"` betekent "er is vastgesteld dat er geen waarde is."

---

### Compositors: de volgorde van kind-elementen

XSD kent drie compositors die beschrijven hoe kind-elementen zich tot elkaar verhouden:

**`xs:sequence`** — vaste volgorde (veruit het meest gebruikt):

```xml
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="voornaam" type="xs:string"/>
    <xs:element name="achternaam" type="xs:string"/>
    <xs:element name="geboortedatum" type="xs:date"/>
  </xs:sequence>
</xs:complexType>
```

> **StUF-context:** StUF-schema's gebruiken vrijwel uitsluitend `xs:sequence`. De volgorde van elementen in StUF-berichten is altijd vast.

**`xs:choice`** — precies één kiezen:

```xml
<xs:complexType name="ContactType">
  <xs:choice>
    <xs:element name="email" type="xs:string"/>
    <xs:element name="telefoon" type="xs:string"/>
    <xs:element name="postadres" type="xs:string"/>
  </xs:choice>
</xs:complexType>
```

**`xs:all`** — willekeurige volgorde (alle elementen moeten aanwezig zijn, maar de volgorde maakt niet uit):

```xml
<xs:complexType name="AdresType">
  <xs:all>
    <xs:element name="straat" type="xs:string"/>
    <xs:element name="huisnummer" type="xs:positiveInteger"/>
    <xs:element name="postcode" type="xs:string"/>
  </xs:all>
</xs:complexType>
```

**Overzicht:**

| Compositor | Volgorde | Keuze | Gebruik |
|---|---|---|---|
| `xs:sequence` | Vast | Alle elementen | Veruit het meest gebruikt (ook in StUF) |
| `xs:choice` | n.v.t. | Precies één | Wanneer er varianten zijn |
| `xs:all` | Vrij | Alle elementen | Wanneer volgorde niet uitmaakt (zelden in StUF) |

### Kardinaliteit: `minOccurs` en `maxOccurs`

**Kardinaliteit** bepaalt hoe vaak een element mag (of moet) voorkomen:

| Attribuut | Betekenis | Standaard |
|---|---|---|
| `minOccurs` | Minimaal aantal keer | `1` |
| `maxOccurs` | Maximaal aantal keer | `1` |

Veelgebruikte combinaties:

| Combinatie | Betekenis |
|---|---|
| *(standaard)* | Verplicht, precies 1 keer |
| `minOccurs="0"` | Optioneel (0 of 1 keer) |
| `minOccurs="0" maxOccurs="unbounded"` | 0 of meer keer |
| `minOccurs="1" maxOccurs="unbounded"` | 1 of meer keer |

Voorbeeld:

```xml
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="voornaam" type="xs:string"/>                          <!-- Verplicht, 1x -->
    <xs:element name="tussenvoegsel" type="xs:string" minOccurs="0"/>       <!-- Optioneel -->
    <xs:element name="achternaam" type="xs:string"/>                        <!-- Verplicht, 1x -->
    <xs:element name="telefoonnummer" type="xs:string"
                minOccurs="0" maxOccurs="unbounded"/>                       <!-- 0 of meer -->
    <xs:element name="adres" type="AdresType"
                minOccurs="1" maxOccurs="3"/>                               <!-- 1 tot 3 -->
  </xs:sequence>
</xs:complexType>
```

> **StUF-context:** In StUF-schema's zijn veel elementen optioneel (`minOccurs="0"`) omdat niet elk bericht alle gegevens bevat. Het begrijpen van `minOccurs` en `maxOccurs` is essentieel voor het lezen van StUF-schema's.

### Attributen definiëren in XSD

Attributen definieer je met `<xs:attribute>`, **na** de compositor:

```xml
<xs:complexType name="AdresType">
  <xs:sequence>
    <xs:element name="straat" type="xs:string"/>
    <xs:element name="huisnummer" type="xs:positiveInteger"/>
  </xs:sequence>
  <xs:attribute name="type" type="xs:string" use="required"/>
</xs:complexType>
```

| `use`-waarde | Betekenis |
|---|---|
| `optional` | Het attribuut is optioneel (standaard) |
| `required` | Het attribuut is verplicht |
| `prohibited` | Het attribuut mag niet voorkomen (gebruikt bij overerving) |

Attributen met een eigen restrictie-type:

```xml
<xs:simpleType name="MutatiesoortType">
  <xs:restriction base="xs:string">
    <xs:enumeration value="T"/>
    <xs:enumeration value="W"/>
    <xs:enumeration value="V"/>
  </xs:restriction>
</xs:simpleType>

<xs:attribute name="mutatiesoort" type="MutatiesoortType"/>
```

> **StUF-context:** Het attribuut `mutatiesoort` is een van de meest kenmerkende attributen in StUF-berichten. Het geeft aan wat voor soort wijziging het bericht bevat (Toevoeging, Wijziging, Verwijdering, etc.).

### Geneste vs. globale definities

| Gebruik **globaal** wanneer... | Gebruik **lokaal** wanneer... |
|---|---|
| Het type op meerdere plekken gebruikt wordt | Het type maar op één plek gebruikt wordt |
| Andere schema's ernaar moeten kunnen verwijzen | Het type specifiek is voor dit ene element |
| Je een duidelijk overzicht wilt van alle typen | Je het schema compact wilt houden |

> **StUF-context:** StUF-schema's werken voornamelijk met **globale definities**. De typen voor personen, adressen, zaken worden centraal gedefinieerd en vervolgens hergebruikt in meerdere berichtdefinities.

---

### Schema's opsplitsen: `xs:include` en `xs:import`

Een groot schema in één bestand wordt al snel onoverzichtelijk. XSD biedt twee mechanismen om schema's over meerdere bestanden te verdelen.

**`xs:include`** — bestanden samenvoegen (zelfde namespace):

```xml
<!-- persoon.xsd -->
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/schema">

  <xs:include schemaLocation="typen-basis.xsd"/>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="naam" type="xs:string"/>
      <xs:element name="adres" type="tns:AdresType"/>  <!-- Uit typen-basis.xsd -->
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

**`xs:import`** — schema's uit een andere namespace:

```xml
<!-- persoon.xsd (namespace: http://www.example.nl/persoon) -->
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon"
           xmlns:adr="http://www.example.nl/adres">

  <xs:import namespace="http://www.example.nl/adres"
             schemaLocation="adres.xsd"/>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="naam" type="xs:string"/>
      <xs:element name="adres" type="adr:AdresType"/>  <!-- Uit adres.xsd -->
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

| Kenmerk | `xs:include` | `xs:import` |
|---|---|---|
| Namespace | Zelfde of geen | Andere |
| Attribuut `namespace` | Niet nodig | Verplicht |
| Analogie | Bestanden samenvoegen | Bibliotheek importeren |
| Gebruik | Opknippen van groot schema | Hergebruik van extern schema |

> **StUF-context:** StUF-schema's zijn zeer modulair. Het basisschema `stuf0302.xsd` definieert fundamentele StUF-typen, en domeinschema's importeren deze om hun eigen berichttypes te definiëren. Je zult tientallen `import`- en `include`-regels tegenkomen.

### Type-afleiding: extension en restriction

Een van de krachtigste features van XSD is **type-afleiding**: een nieuw type baseren op een bestaand type, vergelijkbaar met overerving in objectgeoriënteerd programmeren.

**`xs:extension`** — een type uitbreiden:

```xml
<!-- Basistype -->
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="naam" type="xs:string"/>
    <xs:element name="geboortedatum" type="xs:date"/>
  </xs:sequence>
</xs:complexType>

<!-- Afgeleid type: voegt elementen toe -->
<xs:complexType name="MedewerkerType">
  <xs:complexContent>
    <xs:extension base="PersoonType">
      <xs:sequence>
        <xs:element name="functie" type="xs:string"/>
        <xs:element name="afdeling" type="xs:string"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```

`MedewerkerType` bevat nu alle elementen van `PersoonType` **plus** de extra elementen:

```
PersoonType          (basistype)
  ├── naam
  └── geboortedatum
       │
       ▼
MedewerkerType       (afgeleid = extends PersoonType)
  ├── naam            ← geërfd
  ├── geboortedatum   ← geërfd
  ├── functie         ← toegevoegd
  └── afdeling        ← toegevoegd
```

**`xs:restriction`** — een type beperken:

```xml
<!-- Basistype: alles optioneel -->
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="naam" type="xs:string"/>
    <xs:element name="geboortedatum" type="xs:date" minOccurs="0"/>
    <xs:element name="adres" type="xs:string" minOccurs="0"/>
  </xs:sequence>
</xs:complexType>

<!-- Restrictietype: naam en geboortedatum verplicht, geen adres -->
<xs:complexType name="KernPersoonType">
  <xs:complexContent>
    <xs:restriction base="PersoonType">
      <xs:sequence>
        <xs:element name="naam" type="xs:string"/>
        <xs:element name="geboortedatum" type="xs:date"/>
      </xs:sequence>
    </xs:restriction>
  </xs:complexContent>
</xs:complexType>
```

> **StUF-context:** StUF maakt intensief gebruik van `xs:restriction`. Het basistype bevat alle denkbare elementen (vaak optioneel), en per berichttype wordt een restriction gemaakt die alleen de relevante elementen overhoudt. Dit is het zogenaamde **"Russische poppetjes-model"** (matryoshka-model) van StUF.

| Techniek | Wat doet het? | Richting |
|---|---|---|
| `xs:extension` | Voegt elementen/attributen toe | Basistype → **ruimer** |
| `xs:restriction` | Beperkt of verwijdert optionele delen | Basistype → **strikter** |

### Abstracte typen en substitutiegroepen

Een type kan gemarkeerd worden als `abstract` — het mag **niet direct** in XML-documenten gebruikt worden:

```xml
<xs:complexType name="VoertuigType" abstract="true">
  <xs:sequence>
    <xs:element name="kenteken" type="xs:string"/>
  </xs:sequence>
</xs:complexType>

<xs:complexType name="AutoType">
  <xs:complexContent>
    <xs:extension base="VoertuigType">
      <xs:sequence>
        <xs:element name="aantalDeuren" type="xs:integer"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```

In XML moet je met `xsi:type` aangeven welk concreet type je bedoelt:

```xml
<voertuig xsi:type="AutoType">
  <kenteken>AB-123-CD</kenteken>
  <aantalDeuren>5</aantalDeuren>
</voertuig>
```

### Groepen: `xs:group` en `xs:attributeGroup`

**`xs:group`** — herbruikbare elementgroep:

```xml
<xs:group name="NaamGroep">
  <xs:sequence>
    <xs:element name="voornaam" type="xs:string"/>
    <xs:element name="tussenvoegsel" type="xs:string" minOccurs="0"/>
    <xs:element name="achternaam" type="xs:string"/>
  </xs:sequence>
</xs:group>

<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:group ref="NaamGroep"/>
    <xs:element name="geboortedatum" type="xs:date"/>
  </xs:sequence>
</xs:complexType>
```

**`xs:attributeGroup`** — herbruikbare attribuutgroep:

```xml
<xs:attributeGroup name="StUFMetadata">
  <xs:attribute name="mutatiesoort" type="xs:string"/>
  <xs:attribute name="indicatorOvername" type="xs:string"/>
  <xs:attribute name="noValue" type="xs:string"/>
</xs:attributeGroup>

<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="naam" type="xs:string"/>
  </xs:sequence>
  <xs:attributeGroup ref="StUFMetadata"/>
</xs:complexType>
```

> **StUF-context:** `xs:attributeGroup` wordt in StUF-schema's veelvuldig gebruikt. De basismetadata-attributen die bij elk StUF-element horen (zoals `mutatiesoort`, `noValue`) worden als `attributeGroup` gedefinieerd en overal hergebruikt.

### Modulaire schema-architectuur

In echte projecten (en zeker in StUF) zie je een gelaagde opzet:

```
basis-typen.xsd
├── Simpele typen (Postcode, BSN, Datum, etc.)
└── Attribuutgroepen (metadata)
        │
        ▼
domein-typen.xsd
├── include basis-typen.xsd
├── Complexe typen (PersoonType, AdresType, etc.)
└── Elementgroepen (NaamGroep, etc.)
        │
        ▼
berichten.xsd
├── include domein-typen.xsd
├── Berichttypen (met restriction op domeintypen)
└── Root-elementen
```

In StUF concreet:

```
stuf0302.xsd                 ← Basis StUF-types en attributen
    │
    ├── import ──→ bg0310_stuf_simpleTypes.xsd
    ├── import ──→ bg0310_ent_basis.xsd      ← Entiteittypen (persoon, adres, etc.)
    │                 │
    │                 └── import ──→ gml.xsd  ← Geometrie
    │
    └── bg0310_msg_vraagAntwoord.xsd         ← Berichtstructuren
```

---

### Namespaces in XSD

In sectie 5.2 leer je wat XML-namespaces zijn. Hier zie je hoe namespaces werken **aan de schema-kant**.

**`targetNamespace`** — de namespace van je schema:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon"
           xmlns:per="http://www.example.nl/persoon">

  <xs:element name="persoon" type="per:PersoonType"/>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="naam" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>
</xs:schema>
```

Alle globale definities (`persoon`, `PersoonType`) behoren tot de `targetNamespace`. Als je ernaar verwijst, heb je de prefix nodig: `type="per:PersoonType"`.

**`elementFormDefault`** — bepaalt of lokale elementen een namespace-prefix nodig hebben:

| Waarde | Betekenis |
|---|---|
| `unqualified` (standaard) | Lokale elementen hebben **geen** prefix in XML |
| `qualified` | Lokale elementen **moeten** in de namespace staan |

> **StUF-context:** StUF-schema's gebruiken `elementFormDefault="qualified"`. In de praktijk wordt vaak de default namespace gebruikt zodat niet alle elementen een prefix nodig hebben.

**Schema koppelen aan een XML-document:**

Met `xsi:schemaLocation` (paren van namespace + bestandslocatie):

```xml
<persoon xmlns="http://www.example.nl/persoon"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.example.nl/persoon persoon.xsd">
  <naam>Jan</naam>
</persoon>
```

Met meerdere schema's:

```xml
<bericht xmlns="http://www.example.nl/bericht"
         xmlns:per="http://www.example.nl/persoon"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
           http://www.example.nl/bericht  bericht.xsd
           http://www.example.nl/persoon  persoon.xsd">
  <!-- ... -->
</bericht>
```

> **Let op:** `xsi:schemaLocation` is een **hint** voor de validator, geen verplichting. De validator mág een ander schema gebruiken.

---

### Compleet voorbeeld: alles samen

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <!-- Simpele typen met restricties -->
  <xs:simpleType name="BSN">
    <xs:restriction base="xs:string">
      <xs:pattern value="[0-9]{9}"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:simpleType name="Postcode">
    <xs:restriction base="xs:string">
      <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:simpleType name="Geslacht">
    <xs:restriction base="xs:string">
      <xs:enumeration value="M"/>
      <xs:enumeration value="V"/>
      <xs:enumeration value="O"/>
    </xs:restriction>
  </xs:simpleType>

  <!-- Complexe typen -->
  <xs:complexType name="AdresType">
    <xs:sequence>
      <xs:element name="straat" type="xs:string"/>
      <xs:element name="huisnummer" type="xs:positiveInteger"/>
      <xs:element name="huisletter" type="xs:string" minOccurs="0"/>
      <xs:element name="postcode" type="Postcode"/>
      <xs:element name="woonplaats" type="xs:string"/>
    </xs:sequence>
    <xs:attribute name="type" type="xs:string" use="required"/>
  </xs:complexType>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="voornaam" type="xs:string" maxOccurs="unbounded"/>
      <xs:element name="tussenvoegsel" type="xs:string" minOccurs="0"/>
      <xs:element name="achternaam" type="xs:string"/>
      <xs:element name="geslacht" type="Geslacht"/>
      <xs:element name="geboortedatum" type="xs:date"/>
      <xs:element name="overlijdensdatum" type="xs:date" nillable="true" minOccurs="0"/>
      <xs:element name="adres" type="AdresType" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="bsn" type="BSN" use="required"/>
  </xs:complexType>

  <!-- Root-element -->
  <xs:element name="persoon" type="PersoonType"/>

</xs:schema>
```

Een geldig XML-document:

```xml
<persoon bsn="123456789">
  <voornaam>Jan</voornaam>
  <voornaam>Pieter</voornaam>
  <tussenvoegsel>de</tussenvoegsel>
  <achternaam>Vries</achternaam>
  <geslacht>M</geslacht>
  <geboortedatum>1985-03-15</geboortedatum>
  <overlijdensdatum xsi:nil="true"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"/>
  <adres type="woonadres">
    <straat>Kerkstraat</straat>
    <huisnummer>12</huisnummer>
    <postcode>3511AB</postcode>
    <woonplaats>Utrecht</woonplaats>
  </adres>
</persoon>
```

In dit ene voorbeeld komen alle behandelde concepten samen: simpele typen met restricties (BSN, Postcode, Geslacht), complexe typen (AdresType, PersoonType), kardinaliteit (`minOccurs`, `maxOccurs`), verplichte attributen (`use="required"`), nillable elementen, en de boomstructuur van geneste elementen.

### Samenvatting

| Concept | Uitleg |
|---|---|
| **XSD** | XML Schema Definition — beschrijft de toegestane structuur van XML in XML-formaat |
| **Welgevormd vs. geldig** | Welgevormd = syntax OK; geldig = voldoet aan schema |
| **Ingebouwde datatypen** | `xs:string`, `xs:integer`, `xs:date`, `xs:boolean`, etc. |
| **Restricties (facets)** | `pattern`, `enumeration`, `minLength`, `maxInclusive`, etc. |
| **Compositors** | `xs:sequence` (volgorde), `xs:choice` (keuze), `xs:all` (vrij) |
| **Kardinaliteit** | `minOccurs` / `maxOccurs` — hoe vaak een element mag voorkomen |
| **`xs:include` / `xs:import`** | Schema's samenvoegen (zelfde ns) / importeren (andere ns) |
| **Extension / restriction** | Type uitbreiden of beperken (overerving) |
| **`xs:group` / `xs:attributeGroup`** | Herbruikbare groepen elementen of attributen |
| **`targetNamespace`** | Namespace waaraan het schema zijn definities toekent |
| **`elementFormDefault`** | Bepaalt of lokale elementen gekwalificeerd moeten zijn |
| **`xsi:schemaLocation`** | Koppelt namespace aan schema-bestand in XML |

> **Kernpunt:** XSD is een taal (zelf ook XML) waarmee je exact vastlegt hoe een XML-document eruit moet zien. StUF is volledig gedefinieerd in XSD-schema's — het begrijpen van XSD is daarom essentieel voor het werken met StUF. De belangrijkste concepten zijn: datatypen met restricties, compositors voor structuur, kardinaliteit voor optionaliteit, en modulaire schema-opzet met include/import en type-afleiding.
