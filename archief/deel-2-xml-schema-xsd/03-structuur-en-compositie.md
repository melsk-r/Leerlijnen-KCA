---
title: "2.3 Structuur en compositie"
weight: 3
---

# 2.3 Structuur en compositie

In sectie 2.2 heb je geleerd hoe je elementen definieert en welke datatypen beschikbaar zijn. Maar een echt XML-document is meer dan een lijst losse elementen – elementen staan in een bepaalde **volgorde**, sommige zijn **optioneel**, andere mogen **herhaald** worden, en elementen kunnen **attributen** hebben. In deze sectie leer je hoe je dat allemaal vastlegt in XSD.

## Compositors: de volgorde van kind-elementen

Wanneer een element kind-elementen bevat (een complex type), moet je aangeven hoe die kind-elementen zich tot elkaar verhouden. Dit doe je met een **compositor** – een XSD-element dat de structuur beschrijft.

XSD kent drie compositors:

### `xs:sequence` – vaste volgorde

De meest gebruikte compositor. Alle kind-elementen moeten in **precies de opgegeven volgorde** voorkomen:

```xml
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="voornaam" type="xs:string"/>
    <xs:element name="achternaam" type="xs:string"/>
    <xs:element name="geboortedatum" type="xs:date"/>
  </xs:sequence>
</xs:complexType>
```

Geldig:
```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <geboortedatum>1985-03-15</geboortedatum>
</persoon>
```

Ongeldig (verkeerde volgorde):
```xml
<persoon>
  <achternaam>de Vries</achternaam>
  <voornaam>Jan</voornaam>
  <geboortedatum>1985-03-15</geboortedatum>
</persoon>
```

> **StUF-context:** StUF-schema's gebruiken vrijwel uitsluitend `xs:sequence`. De volgorde van elementen in StUF-berichten is dus altijd vast. Dit maakt verwerking voorspelbaarder.

### `xs:choice` – precies één kiezen

Met `xs:choice` mag precies **één** van de opgegeven kind-elementen voorkomen:

```xml
<xs:complexType name="ContactType">
  <xs:choice>
    <xs:element name="email" type="xs:string"/>
    <xs:element name="telefoon" type="xs:string"/>
    <xs:element name="postadres" type="xs:string"/>
  </xs:choice>
</xs:complexType>
```

Geldig (één gekozen):
```xml
<contact>
  <email>jan@example.nl</email>
</contact>
```

Ongeldig (twee gekozen):
```xml
<contact>
  <email>jan@example.nl</email>
  <telefoon>030-1234567</telefoon>
</contact>
```

`xs:choice` is handig wanneer een element verschillende vormen kan aannemen, maar er altijd precies één variant aanwezig is.

### `xs:all` – willekeurige volgorde

Met `xs:all` moeten alle opgegeven kind-elementen voorkomen, maar de **volgorde maakt niet uit**:

```xml
<xs:complexType name="AdresType">
  <xs:all>
    <xs:element name="straat" type="xs:string"/>
    <xs:element name="huisnummer" type="xs:positiveInteger"/>
    <xs:element name="postcode" type="xs:string"/>
    <xs:element name="woonplaats" type="xs:string"/>
  </xs:all>
</xs:complexType>
```

Beide geldige:
```xml
<adres>
  <straat>Kerkstraat</straat>
  <huisnummer>12</huisnummer>
  <postcode>3511AB</postcode>
  <woonplaats>Utrecht</woonplaats>
</adres>
```

```xml
<adres>
  <woonplaats>Utrecht</woonplaats>
  <postcode>3511AB</postcode>
  <straat>Kerkstraat</straat>
  <huisnummer>12</huisnummer>
</adres>
```

**Beperkingen van `xs:all`:**
- Elk kind-element mag maximaal 1 keer voorkomen (`maxOccurs` mag alleen `0` of `1` zijn)
- Je kunt `xs:all` niet nesten in andere compositors
- `xs:all` kan niet gecombineerd worden met `xs:sequence` of `xs:choice` als directe kinderen

> In de praktijk van StUF wordt `xs:all` zelden gebruikt – `xs:sequence` is de norm. Maar het is goed om te weten dat het bestaat.

### Overzicht compositors

| Compositor | Volgorde | Keuze | Gebruik |
|---|---|---|---|
| `xs:sequence` | Vast | Alle elementen | Veruit het meest gebruikt (ook in StUF) |
| `xs:choice` | n.v.t. | Precies één | Wanneer er varianten zijn |
| `xs:all` | Vrij | Alle elementen | Wanneer volgorde niet uitmaakt |

## Kardinaliteit: `minOccurs` en `maxOccurs`

**Kardinaliteit** bepaalt hoe vaak een element mag (of moet) voorkomen. Dit regel je met twee attributen:

| Attribuut | Betekenis | Standaard (als je het weglaat) |
|---|---|---|
| `minOccurs` | Minimaal aantal keer dat het element moet voorkomen | `1` |
| `maxOccurs` | Maximaal aantal keer dat het element mag voorkomen | `1` |

### Standaard: precies 1 keer

Als je niets opgeeft, is een element **verplicht en mag het precies 1 keer voorkomen**:

```xml
<xs:element name="voornaam" type="xs:string"/>
<!-- Equivalent aan: -->
<xs:element name="voornaam" type="xs:string" minOccurs="1" maxOccurs="1"/>
```

### Optioneel element (0 of 1 keer)

```xml
<xs:element name="tussenvoegsel" type="xs:string" minOccurs="0"/>
```

Het element mag voorkomen, maar het hoeft niet. Als het voorkomt, mag het maximaal 1 keer (want `maxOccurs` is standaard `1`).

### Herhalend element (0 of meer keer)

```xml
<xs:element name="telefoonnummer" type="xs:string" minOccurs="0" maxOccurs="unbounded"/>
```

Het element mag willekeurig vaak voorkomen (inclusief 0 keer). Het sleutelwoord `"unbounded"` betekent "geen bovengrens".

### Verplicht en herhalend (1 of meer keer)

```xml
<xs:element name="voornaam" type="xs:string" minOccurs="1" maxOccurs="unbounded"/>
```

Er moet minstens 1 voornaam zijn, maar er mogen er meer zijn.

### Een exact aantal

```xml
<xs:element name="coordinaat" type="xs:decimal" minOccurs="2" maxOccurs="2"/>
```

Er moeten precies 2 coordinaten zijn – niet meer, niet minder.

### Praktijkvoorbeelden

```xml
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="voornaam" type="xs:string"/>                          <!-- Verplicht, 1x -->
    <xs:element name="tussenvoegsel" type="xs:string" minOccurs="0"/>       <!-- Optioneel -->
    <xs:element name="achternaam" type="xs:string"/>                        <!-- Verplicht, 1x -->
    <xs:element name="telefoonnummer" type="xs:string"
                minOccurs="0" maxOccurs="unbounded"/>                       <!-- 0 of meer -->
    <xs:element name="adres" type="AdresType"
                minOccurs="1" maxOccurs="3"/>                               <!-- 1 tot 3 adressen -->
  </xs:sequence>
</xs:complexType>
```

Geldig XML:
```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>Vries</achternaam>
  <telefoonnummer>030-1234567</telefoonnummer>
  <telefoonnummer>06-12345678</telefoonnummer>
  <adres><!-- ... --></adres>
</persoon>
```

Ook geldig (tussenvoegsel en telefoonnummers weggelaten):
```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>Vries</achternaam>
  <adres><!-- ... --></adres>
</persoon>
```

> **StUF-context:** In StUF-schema's wordt kardinaliteit intensief gebruikt. Veel elementen zijn optioneel (`minOccurs="0"`) omdat niet elk bericht alle gegevens bevat. Het begrijpen van `minOccurs` en `maxOccurs` is essentieel voor het lezen van StUF-schema's.

## Kardinaliteit op compositors

`minOccurs` en `maxOccurs` kunnen ook op compositors zelf staan. Dit is minder bekend maar krachtig:

```xml
<xs:complexType name="BerichtType">
  <xs:sequence>
    <xs:element name="kop" type="xs:string"/>
    <xs:sequence minOccurs="0" maxOccurs="unbounded">
      <xs:element name="regelNummer" type="xs:integer"/>
      <xs:element name="regelInhoud" type="xs:string"/>
    </xs:sequence>
  </xs:sequence>
</xs:complexType>
```

Hier mag het blok `regelNummer` + `regelInhoud` als geheel herhaald worden:

```xml
<bericht>
  <kop>Overzicht</kop>
  <regelNummer>1</regelNummer>
  <regelInhoud>Eerste regel</regelInhoud>
  <regelNummer>2</regelNummer>
  <regelInhoud>Tweede regel</regelInhoud>
</bericht>
```

## Attributen definiëren

In Deel 1 (sectie 1.2) heb je geleerd wat attributen zijn in XML. In XSD definieer je attributen met `<xs:attribute>`.

### Een attribuut toevoegen aan een complex type

```xml
<xs:complexType name="AdresType">
  <xs:sequence>
    <xs:element name="straat" type="xs:string"/>
    <xs:element name="huisnummer" type="xs:positiveInteger"/>
  </xs:sequence>
  <xs:attribute name="type" type="xs:string"/>
</xs:complexType>
```

**Let op:** attributen staan **na** de compositor (`xs:sequence`, `xs:choice` of `xs:all`), niet erin.

Geldig XML:
```xml
<adres type="woonadres">
  <straat>Kerkstraat</straat>
  <huisnummer>12</huisnummer>
</adres>
```

### Verplicht vs. optioneel

Standaard zijn attributen **optioneel**. Om een attribuut verplicht te maken, gebruik je `use="required"`:

```xml
<xs:attribute name="burgerservicenummer" type="xs:string" use="required"/>
```

De mogelijke waarden voor `use`:

| Waarde | Betekenis |
|---|---|
| `optional` | Het attribuut is optioneel (standaard) |
| `required` | Het attribuut is verplicht |
| `prohibited` | Het attribuut mag niet voorkomen (gebruikt bij overerving) |

### Default en fixed waarden

Net als bij elementen kun je ook voor attributen default- en fixed-waarden opgeven:

```xml
<xs:attribute name="land" type="xs:string" default="Nederland"/>
<xs:attribute name="landcode" type="xs:string" fixed="NL"/>
```

### Attribuut met een eigen type

Je kunt een attribuut ook een restrictie-type geven:

```xml
<xs:simpleType name="MutatiesoortType">
  <xs:restriction base="xs:string">
    <xs:enumeration value="T"/>
    <xs:enumeration value="W"/>
    <xs:enumeration value="V"/>
    <xs:enumeration value="E"/>
    <xs:enumeration value="I"/>
    <xs:enumeration value="S"/>
  </xs:restriction>
</xs:simpleType>

<xs:attribute name="mutatiesoort" type="MutatiesoortType"/>
```

> **StUF-context:** Het attribuut `mutatiesoort` is een van de meest kenmerkende attributen in StUF-berichten. Het geeft aan wat voor soort wijziging het bericht bevat (Toevoeging, Wijziging, Verwijdering, etc.). Je ziet hier hoe het in XSD gedefinieerd wordt.

## Geneste vs. globale definities

Er zijn twee plekken waar je elementen, typen en attributen kunt definiëren:

### Globale definities

Staan direct onder `<xs:schema>` en zijn overal in het schema beschikbaar:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <!-- Globaal type -->
  <xs:simpleType name="Postcode">
    <xs:restriction base="xs:string">
      <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
    </xs:restriction>
  </xs:simpleType>

  <!-- Globaal element -->
  <xs:element name="persoon" type="PersoonType"/>

  <!-- Globaal complex type -->
  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="naam" type="xs:string"/>
      <xs:element name="postcode" type="Postcode"/>
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

Voordelen van globale definities:
- **Herbruikbaar** – je kunt ernaar verwijzen vanuit meerdere plekken
- **Importeerbaar** – andere schema's kunnen ze importeren (zie sectie 2.4)
- **Overzichtelijk** – alle definities op het hoogste niveau

### Lokale (geneste) definities

Staan binnen een ander element en zijn alleen daar beschikbaar:

```xml
<xs:element name="persoon">
  <xs:complexType>                        <!-- Lokaal complex type (geen naam) -->
    <xs:sequence>
      <xs:element name="naam">            <!-- Lokaal element -->
        <xs:simpleType>                   <!-- Lokaal simpel type -->
          <xs:restriction base="xs:string">
            <xs:maxLength value="100"/>
          </xs:restriction>
        </xs:simpleType>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
</xs:element>
```

Lokale definities hebben **geen naam** – ze zijn anoniem en alleen bruikbaar op de plek waar ze staan.

### Wanneer globaal, wanneer lokaal?

| Gebruik **globaal** wanneer... | Gebruik **lokaal** wanneer... |
|---|---|
| Het type op meerdere plekken gebruikt wordt | Het type maar op één plek gebruikt wordt |
| Andere schema's ernaar moeten kunnen verwijzen | Het type specifiek is voor dit ene element |
| Je een duidelijk overzicht wilt van alle typen | Je het schema compact wilt houden |

> **StUF-context:** StUF-schema's werken voornamelijk met **globale definities**. De typen voor personen, adressen, zaken, etc. worden centraal gedefinieerd en vervolgens hergebruikt in meerdere berichtdefinities. Dit maakt de schema's modulair en consistent.

## Compositors nesten

Compositors kunnen in elkaar genest worden voor complexere structuren:

### Sequence met een choice erin

```xml
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="naam" type="xs:string"/>
    <xs:choice>
      <xs:element name="bsn" type="xs:string"/>
      <xs:element name="vreemdelingennummer" type="xs:string"/>
    </xs:choice>
    <xs:element name="geboortedatum" type="xs:date"/>
  </xs:sequence>
</xs:complexType>
```

Dit zegt: een persoon heeft een naam, dan óf een bsn óf een vreemdelingennummer (niet beide), en dan een geboortedatum.

### Choice tussen sequences

```xml
<xs:complexType name="ZoekType">
  <xs:choice>
    <xs:sequence>
      <xs:element name="voornaam" type="xs:string"/>
      <xs:element name="achternaam" type="xs:string"/>
    </xs:sequence>
    <xs:sequence>
      <xs:element name="bsn" type="xs:string"/>
    </xs:sequence>
  </xs:choice>
</xs:complexType>
```

Dit zegt: je zoekt óf op voornaam + achternaam óf op bsn.

## Een compleet voorbeeld

Laten we alles samenbrengen in een schema dat een vereenvoudigd bericht beschrijft:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <!-- Simpele typen -->
  <xs:simpleType name="BSN">
    <xs:restriction base="xs:string">
      <xs:pattern value="[0-9]{9}"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:simpleType name="Geslacht">
    <xs:restriction base="xs:string">
      <xs:enumeration value="M"/>
      <xs:enumeration value="V"/>
      <xs:enumeration value="O"/>
    </xs:restriction>
  </xs:simpleType>

  <!-- Complex type: Adres -->
  <xs:complexType name="AdresType">
    <xs:sequence>
      <xs:element name="straat" type="xs:string"/>
      <xs:element name="huisnummer" type="xs:positiveInteger"/>
      <xs:element name="huisletter" type="xs:string" minOccurs="0"/>
      <xs:element name="postcode" type="xs:string"/>
      <xs:element name="woonplaats" type="xs:string"/>
    </xs:sequence>
    <xs:attribute name="type" type="xs:string" use="required"/>
  </xs:complexType>

  <!-- Complex type: Persoon -->
  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="voornaam" type="xs:string" maxOccurs="unbounded"/>
      <xs:element name="tussenvoegsel" type="xs:string" minOccurs="0"/>
      <xs:element name="achternaam" type="xs:string"/>
      <xs:element name="geslacht" type="Geslacht"/>
      <xs:element name="geboortedatum" type="xs:date"/>
      <xs:element name="adres" type="AdresType"
                  minOccurs="0" maxOccurs="unbounded"/>
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
  <adres type="woonadres">
    <straat>Kerkstraat</straat>
    <huisnummer>12</huisnummer>
    <postcode>3511AB</postcode>
    <woonplaats>Utrecht</woonplaats>
  </adres>
  <adres type="postadres">
    <straat>Postbus</straat>
    <huisnummer>100</huisnummer>
    <postcode>3500AA</postcode>
    <woonplaats>Utrecht</woonplaats>
  </adres>
</persoon>
```

Merk op:
- Het attribuut `bsn` is verplicht (`use="required"`) en moet 9 cijfers zijn
- Er zijn twee voornamen (toegestaan: `maxOccurs="unbounded"`)
- `tussenvoegsel` is optioneel en aanwezig
- `huisletter` is optioneel en weggelaten
- Er zijn twee adressen, elk met een verplicht `type`-attribuut
- De volgorde van elementen volgt exact wat het schema voorschrijft

## Samenvatting

| Concept | Uitleg |
|---|---|
| **`xs:sequence`** | Kind-elementen in vaste volgorde |
| **`xs:choice`** | Precies één van de opgegeven kind-elementen |
| **`xs:all`** | Alle kind-elementen in willekeurige volgorde |
| **`minOccurs`** | Minimaal aantal keer dat een element moet voorkomen (standaard: 1) |
| **`maxOccurs`** | Maximaal aantal keer (standaard: 1, of `"unbounded"` voor onbeperkt) |
| **`xs:attribute`** | Definieert een attribuut op een element |
| **`use="required"`** | Maakt een attribuut verplicht |
| **Globale definitie** | Op schema-niveau, herbruikbaar en importeerbaar |
| **Lokale definitie** | Genest in een ander element, anoniem en eenmalig |

> **Kernpunt:** Met compositors (`xs:sequence`, `xs:choice`, `xs:all`) bepaal je de structuur van kind-elementen. Met `minOccurs` en `maxOccurs` bepaal je hoe vaak elk element mag of moet voorkomen. Attributen definieer je met `xs:attribute`. Door globale definities te gebruiken maak je typen herbruikbaar – een belangrijk principe dat in de volgende sectie verder wordt uitgewerkt.
