---
title: "2.2 Basisconcepten van XSD"
weight: 2
---

# 2.2 Basisconcepten van XSD

In sectie 2.1 heb je gezien wat XSD is en waarom het bestaat. Nu gaan we de bouwstenen van XSD leren: hoe definieer je elementen, welke datatypen bestaan er, en hoe beperk je welke waarden zijn toegestaan?

## De structuur van een XSD-bestand

Elk XSD-bestand is zelf een XML-document. Het root-element is altijd `<xs:schema>`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <!-- Hier komen je definities -->

</xs:schema>
```

Laten we dit ontleden:

| Onderdeel | Betekenis |
|---|---|
| `<?xml version="1.0" encoding="UTF-8"?>` | Standaard XML-declaratie |
| `<xs:schema>` | Het root-element van elk XSD-bestand |
| `xmlns:xs="http://www.w3.org/2001/XMLSchema"` | De namespace-declaratie die de prefix `xs` koppelt aan de XML Schema-namespace |

De prefix `xs` is conventie – je kunt ook `xsd` of iets anders kiezen. In de praktijk zie je zowel `xs:` als `xsd:` in gebruik. In deze cursus gebruiken we `xs:`.

> **Herkenning:** Alles in een XSD-bestand dat begint met `xs:` is een element of attribuut uit de XML Schema-standaard zelf. Zo weet je altijd: dit is een XSD-instructie, geen gegeven.

## Elementen definiëren met `xs:element`

De meest fundamentele bouwsteen van XSD is `<xs:element>`: hiermee definieer je welke elementen er in het XML-document mogen voorkomen.

### Een eenvoudig element

```xml
<xs:element name="voornaam" type="xs:string"/>
```

Dit zegt: *"Er mag een element `<voornaam>` bestaan, en de inhoud moet tekst zijn."*

Een XML-document dat hieraan voldoet:

```xml
<voornaam>Jan</voornaam>
```

De twee belangrijkste attributen van `<xs:element>`:

| Attribuut | Betekenis | Voorbeeld |
|---|---|---|
| `name` | De naam van het element in het XML-document | `name="voornaam"` |
| `type` | Het datatype van de inhoud | `type="xs:string"` |

### Wat is een "type"?

Een **type** beschrijft welke waarden een element mag bevatten. XSD kent twee soorten typen:

1. **Simpele typen** (`simpleType`) – het element bevat alleen een waarde (tekst, getal, datum, etc.), geen kind-elementen
2. **Complexe typen** (`complexType`) – het element bevat kind-elementen en/of attributen

In dit hoofdstuk focussen we eerst op simpele typen. Complexe typen komen verderop.

## Ingebouwde datatypen

XSD levert een uitgebreide set **ingebouwde datatypen** mee. Dit zijn de typen die je kunt gebruiken zonder ze zelf te definiëren. Hier zijn de meest gebruikte:

### Teksttypen

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:string` | Willekeurige tekst | `"Jan de Vries"` |
| `xs:normalizedString` | Tekst zonder tabs en regelovergangen | `"Jan de Vries"` |
| `xs:token` | Tekst zonder overbodige witruimte | `"Jan de Vries"` |

### Numerieke typen

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:integer` | Geheel getal (positief of negatief) | `42`, `-7` |
| `xs:positiveInteger` | Geheel getal > 0 | `1`, `100` |
| `xs:nonNegativeInteger` | Geheel getal >= 0 | `0`, `42` |
| `xs:decimal` | Decimaal getal | `3.14`, `-0.5` |
| `xs:float` | Zwevendekommagetal (32-bit) | `3.14E2` |
| `xs:double` | Zwevendekommagetal (64-bit) | `3.14E2` |

### Datum- en tijdtypen

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:date` | Datum (jaar-maand-dag) | `2026-03-04` |
| `xs:time` | Tijd (uur:minuut:seconde) | `14:30:00` |
| `xs:dateTime` | Datum + tijd | `2026-03-04T14:30:00` |
| `xs:gYear` | Alleen een jaar | `2026` |
| `xs:gYearMonth` | Jaar en maand | `2026-03` |
| `xs:duration` | Tijdsduur | `P1Y2M3D` (1 jaar, 2 maanden, 3 dagen) |

### Overige typen

| Type | Beschrijving | Voorbeeldwaarde |
|---|---|---|
| `xs:boolean` | Waar of onwaar | `true`, `false`, `1`, `0` |
| `xs:anyURI` | Een URI/URL | `http://www.example.nl` |
| `xs:base64Binary` | Binaire data in Base64-codering | `SGVsbG8=` |
| `xs:hexBinary` | Binaire data in hexadecimale codering | `48656C6C6F` |

### Voorbeeld: elementen met verschillende typen

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <xs:element name="naam" type="xs:string"/>
  <xs:element name="leeftijd" type="xs:positiveInteger"/>
  <xs:element name="geboortedatum" type="xs:date"/>
  <xs:element name="actief" type="xs:boolean"/>

</xs:schema>
```

Geldige XML-waarden:

```xml
<naam>Jan de Vries</naam>
<leeftijd>41</leeftijd>
<geboortedatum>1985-03-15</geboortedatum>
<actief>true</actief>
```

Ongeldige waarden:

```xml
<leeftijd>veertig</leeftijd>          <!-- "veertig" is geen getal -->
<geboortedatum>15 maart 1985</geboortedatum>  <!-- verkeerd datumformaat -->
<actief>ja</actief>                    <!-- "ja" is geen geldige boolean -->
```

> **Let op:** Het kiezen van het juiste datatype is belangrijk. Als je `xs:string` gebruikt voor een geboortedatum, accepteert het schema elke tekst – ook "gisteren" of "binnenkort". Met `xs:date` dwing je het formaat `JJJJ-MM-DD` af.

## Simpele typen met restricties

De ingebouwde typen zijn een goed begin, maar vaak wil je de toegestane waarden verder beperken. Bijvoorbeeld: een postcode moet precies 4 cijfers gevolgd door 2 letters zijn. Daarvoor gebruik je **restricties** (ook wel **facets** genoemd) binnen een `<xs:simpleType>`.

### Hoe werkt dat?

Je maakt een nieuw simpel type door een bestaand type te **beperken** met `<xs:restriction>`:

```xml
<xs:simpleType name="Postcode">
  <xs:restriction base="xs:string">
    <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
  </xs:restriction>
</xs:simpleType>
```

Dit zegt: *"Een Postcode is een tekst (`xs:string`) die moet voldoen aan het patroon: 4 cijfers gevolgd door 2 hoofdletters."*

### Alle beschikbare restricties (facets)

| Facet | Werkt op | Betekenis | Voorbeeld |
|---|---|---|---|
| `xs:minLength` | Tekst | Minimaal aantal tekens | `<xs:minLength value="1"/>` |
| `xs:maxLength` | Tekst | Maximaal aantal tekens | `<xs:maxLength value="100"/>` |
| `xs:length` | Tekst | Exact aantal tekens | `<xs:length value="6"/>` |
| `xs:pattern` | Tekst | Moet voldoen aan een reguliere expressie | `<xs:pattern value="[0-9]{4}[A-Z]{2}"/>` |
| `xs:enumeration` | Alle typen | Waarde moet uit een vaste lijst komen | `<xs:enumeration value="M"/>` |
| `xs:minInclusive` | Getallen/datums | Minimale waarde (inclusief) | `<xs:minInclusive value="0"/>` |
| `xs:maxInclusive` | Getallen/datums | Maximale waarde (inclusief) | `<xs:maxInclusive value="999"/>` |
| `xs:minExclusive` | Getallen/datums | Minimale waarde (exclusief) | `<xs:minExclusive value="0"/>` |
| `xs:maxExclusive` | Getallen/datums | Maximale waarde (exclusief) | `<xs:maxExclusive value="1000"/>` |
| `xs:totalDigits` | Getallen | Maximaal aantal cijfers totaal | `<xs:totalDigits value="5"/>` |
| `xs:fractionDigits` | Decimalen | Maximaal aantal cijfers na de komma | `<xs:fractionDigits value="2"/>` |
| `xs:whiteSpace` | Tekst | Hoe witruimte behandeld wordt | `<xs:whiteSpace value="collapse"/>` |

### Voorbeeld: enumeratie (vaste keuzelijst)

Een veelgebruikte restrictie is `xs:enumeration` – hiermee beperk je de waarde tot een vaste set opties:

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

Gebruik in een element:

```xml
<xs:element name="geslachtsaanduiding" type="Geslacht"/>
```

Geldig:
```xml
<geslachtsaanduiding>M</geslachtsaanduiding>
```

Ongeldig:
```xml
<geslachtsaanduiding>Man</geslachtsaanduiding>
```

### Voorbeeld: patroon (reguliere expressie)

Met `xs:pattern` kun je een reguliere expressie opgeven:

```xml
<xs:simpleType name="BSN">
  <xs:restriction base="xs:string">
    <xs:pattern value="[0-9]{9}"/>
  </xs:restriction>
</xs:simpleType>
```

Dit accepteert alleen strings van precies 9 cijfers, zoals `"123456789"`.

### Voorbeeld: bereik (min/max)

Voor getallen kun je een bereik opgeven:

```xml
<xs:simpleType name="Huisnummer">
  <xs:restriction base="xs:positiveInteger">
    <xs:minInclusive value="1"/>
    <xs:maxInclusive value="99999"/>
  </xs:restriction>
</xs:simpleType>
```

### Voorbeeld: lengte

```xml
<xs:simpleType name="Woonplaatsnaam">
  <xs:restriction base="xs:string">
    <xs:minLength value="1"/>
    <xs:maxLength value="80"/>
  </xs:restriction>
</xs:simpleType>
```

### Restricties combineren

Je mag meerdere facets combineren in één restrictie:

```xml
<xs:simpleType name="Postcode">
  <xs:restriction base="xs:string">
    <xs:length value="6"/>
    <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
  </xs:restriction>
</xs:simpleType>
```

Hier moet de waarde precies 6 tekens lang zijn **én** voldoen aan het patroon.

> **StUF-context:** In de StUF-schema's worden restricties veelvuldig gebruikt. Denk aan BSN's die precies 9 cijfers moeten zijn, postcodes met een vast patroon, en geslachtsaanduidingen die uit een vaste lijst komen. Het definiëren van zulke typen is een van de kernactiviteiten bij het werken met StUF-schema's.

## Complexe typen: elementen met structuur

Tot nu toe bevatten onze elementen alleen een waarde (tekst, getal, datum). Maar in de praktijk bevatten de meeste elementen **kind-elementen** – denk aan het `<persoon>`-element dat `<voornaam>` en `<achternaam>` bevat. Hiervoor heb je een **complex type** nodig.

### Inline complex type

Het eenvoudigste is om het complexe type direct in het element te definiëren:

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

Dit zegt:
- Er bestaat een element `<persoon>`
- Het is een complex type (het bevat kind-elementen)
- De kind-elementen moeten in volgorde (`<xs:sequence>`) voorkomen: eerst `<voornaam>`, dan `<achternaam>`

### Benoemd complex type

Je kunt een complex type ook een **naam** geven en het apart definiëren. Dat maakt het herbruikbaar:

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

Nu gebruiken zowel `<persoon>` als `<contactpersoon>` dezelfde structuur. Dit is een eerste stap naar hergebruik – een belangrijk principe dat in sectie 2.4 verder wordt uitgewerkt.

### Complex type met simpele inhoud

Soms wil je een element dat tekst bevat **én** een attribuut heeft. Denk aan:

```xml
<bedrag valuta="EUR">1500.00</bedrag>
```

Dit element heeft een tekstwaarde (`1500.00`) en een attribuut (`valuta`). In XSD definieer je dit met `<xs:simpleContent>`:

```xml
<xs:complexType name="BedragType">
  <xs:simpleContent>
    <xs:extension base="xs:decimal">
      <xs:attribute name="valuta" type="xs:string"/>
    </xs:extension>
  </xs:simpleContent>
</xs:complexType>
```

Dit zegt: *"Het type is gebaseerd op `xs:decimal` (de tekstinhoud is een decimaal getal) en wordt uitgebreid met een attribuut `valuta`."*

## Default-waarden en vaste waarden

Je kunt aan elementen en attributen een standaardwaarde of een vaste waarde toekennen:

### Default-waarde

Als het element leeg is of ontbreekt (bij optionele elementen), wordt de default-waarde gebruikt:

```xml
<xs:element name="land" type="xs:string" default="Nederland"/>
```

### Vaste waarde

Het element mag alleen deze waarde bevatten:

```xml
<xs:element name="landcode" type="xs:string" fixed="NL"/>
```

Bij `fixed` is de enige geldige inhoud `"NL"`. Elke andere waarde is ongeldig.

## Nillable: expliciet "geen waarde"

Soms moet een element aanwezig zijn, maar hoeft het geen waarde te hebben. Denk aan een overlijdensdatum bij een levende persoon. In XSD kun je dit toestaan met `nillable`:

```xml
<xs:element name="overlijdensdatum" type="xs:date" nillable="true"/>
```

In het XML-document geef je aan dat er bewust geen waarde is met het `xsi:nil`-attribuut:

```xml
<overlijdensdatum xsi:nil="true"/>
```

Dit is anders dan een leeg element (`<overlijdensdatum/>`) – `xsi:nil="true"` zegt expliciet: *"er is geen waarde, en dat is bewust."*

> **StUF-context:** Het `nillable`-mechanisme wordt in StUF gebruikt om aan te geven dat een gegeven bewust niet gevuld is. Dit is een belangrijk onderscheid: een leeg element kan "nog niet ingevuld" betekenen, terwijl `xsi:nil="true"` betekent "er is vastgesteld dat er geen waarde is."

## Een uitgebreider voorbeeld

Laten we de behandelde concepten combineren:

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
      <xs:element name="postcode" type="Postcode"/>
      <xs:element name="woonplaats" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="voornaam" type="xs:string"/>
      <xs:element name="achternaam" type="xs:string"/>
      <xs:element name="geslacht" type="Geslacht"/>
      <xs:element name="geboortedatum" type="xs:date"/>
      <xs:element name="adres" type="AdresType"/>
    </xs:sequence>
  </xs:complexType>

  <!-- Root-element -->
  <xs:element name="persoon" type="PersoonType"/>

</xs:schema>
```

Een geldig XML-document:

```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <geslacht>M</geslacht>
  <geboortedatum>1985-03-15</geboortedatum>
  <adres>
    <straat>Kerkstraat</straat>
    <huisnummer>12</huisnummer>
    <postcode>3511AB</postcode>
    <woonplaats>Utrecht</woonplaats>
  </adres>
</persoon>
```

Merk op hoe de benoemde typen (`BSN`, `Postcode`, `Geslacht`, `AdresType`, `PersoonType`) van elkaar gescheiden zijn en hergebruikt kunnen worden.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **`xs:element`** | Definieert een element met een naam en een type |
| **Simpel type** | Een type dat alleen een waarde bevat (tekst, getal, datum) |
| **Complex type** | Een type dat kind-elementen en/of attributen bevat |
| **Ingebouwde datatypen** | Kant-en-klare typen: `xs:string`, `xs:integer`, `xs:date`, etc. |
| **Restrictie (facet)** | Beperking op een type: `pattern`, `enumeration`, `minLength`, `maxInclusive`, etc. |
| **`xs:simpleType`** | Definieert een eigen simpel type (meestal met restricties) |
| **`xs:complexType`** | Definieert een eigen complex type (met kind-elementen) |
| **`nillable`** | Staat toe dat een element expliciet "geen waarde" heeft |

> **Kernpunt:** XSD biedt een rijke set ingebouwde datatypen (string, integer, date, etc.) en de mogelijkheid om eigen typen te definiëren met restricties zoals patronen, enumeraties en lengtelimieten. Elementen die kind-elementen bevatten krijgen een complex type. Door typen een naam te geven worden ze herbruikbaar. Dit systeem van typen en restricties vormt het hart van elk XSD-schema.
