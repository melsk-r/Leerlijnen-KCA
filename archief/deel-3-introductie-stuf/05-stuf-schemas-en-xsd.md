---
title: "3.5 StUF-schema's en XSD"
weight: 5
---

# 3.5 StUF-schema's en XSD

In Deel 2 heb je geleerd hoe XSD werkt: typen definiëren, schema's opsplitsen, namespaces gebruiken, en typen afleiden met extension en restriction. In dit afsluitende hoofdstuk van Deel 3 zien we hoe al die XSD-concepten samenkomen in de **StUF-schema's**. Je leert de gelaagde opbouw kennen, begrijpt hoe namespaces in StUF werken, en kunt een StUF-schema lezen en navigeren.

## De schema-architectuur van StUF

StUF-schema's zijn opgebouwd in **lagen**. Elke laag bouwt voort op de vorige:

```
┌─────────────────────────────────────────────────────────┐
│  Koppelvlak-schema's                                    │
│  (bijv. bg0310_msg_vraagAntwoord.xsd)                   │
│  → Definiëren concrete berichten (npsLv01, npsLa01...)  │
├─────────────────────────────────────────────────────────┤
│  Sectormodel-schema's                                   │
│  (bijv. bg0310_ent_basis.xsd)                           │
│  → Definiëren entiteittypen (NPS, ADR, ZAK...)          │
├─────────────────────────────────────────────────────────┤
│  Sectormodel simpleTypes                                │
│  (bijv. bg0310_stuf_simpleTypes.xsd)                    │
│  → Domeinspecifieke datatypes (BSN, Postcode, etc.)     │
├─────────────────────────────────────────────────────────┤
│  StUF-kern                                              │
│  (stuf0302.xsd)                                         │
│  → Basisstructuur: stuurgegevens, mutatiesoort,         │
│    tijdvakken, berichtbasis, attributeGroups             │
└─────────────────────────────────────────────────────────┘
```

### Vergelijking met Deel 2

In Deel 2 (sectie 2.4) heb je dit patroon al gezien:

| Concept uit Deel 2 | Toepassing in StUF |
|---|---|
| `xs:import` | Sectormodel importeert StUF-kern |
| `xs:include` | Schema samenvoegen binnen zelfde namespace |
| Benoemde complexe typen | Entiteittypen (PersoonType, AdresType) |
| `xs:restriction` | Berichtspecifieke varianten van entiteittypen |
| `xs:extension` | Toevoegen van mutatieattributen aan entiteiten |
| `xs:attributeGroup` | StUF-metadata (mutatiesoort, noValue, etc.) |
| `targetNamespace` | Elke laag heeft zijn eigen namespace |

## De StUF-kern: `stuf0302.xsd`

Het bestand `stuf0302.xsd` is het hart van StUF. Het definieert alles wat **generiek** is aan StUF-berichten – onafhankelijk van het domein.

### Wat zit er in de StUF-kern?

```
stuf0302.xsd
├── Stuurgegevens-type
│   ├── berichtcode
│   ├── zender / ontvanger
│   ├── referentienummer
│   ├── crossRefnummer
│   ├── tijdstipBericht
│   └── entiteittype
│
├── Generieke berichttypen
│   ├── Fo01 (foutbericht)
│   └── Bv01 (bevestigingsbericht)
│
├── Basistypen voor mutaties
│   ├── mutatiesoort (T, W, V, E, I, S)
│   └── noValue (geenWaarde, waardeOnbekend, etc.)
│
├── Tijdvaktypen
│   ├── tijdvakGeldigheid
│   ├── tijdvakRelatie
│   └── tijdstipRegistratie
│
├── Attribuutgroepen
│   ├── StUF-attributen (mutatiesoort, noValue, etc.)
│   └── Entiteitattributen (entiteittype, sleutel)
│
└── Abstracte basistypen
    └── Basis voor kennisgevings- en vraagberichten
```

### Voorbeeld: stuurgegevens in XSD

Hier is een vereenvoudigde versie van hoe de stuurgegevens in de StUF-kern gedefinieerd zijn:

```xml
<xs:complexType name="Stuurgegevens-stuf0302">
  <xs:sequence>
    <xs:element name="berichtcode" type="stuf:Berichtcode"/>
    <xs:element name="zender" type="stuf:Systeem"/>
    <xs:element name="ontvanger" type="stuf:Systeem"/>
    <xs:element name="referentienummer" type="stuf:Refnummer"/>
    <xs:element name="crossRefnummer" type="stuf:Refnummer" minOccurs="0"/>
    <xs:element name="tijdstipBericht" type="stuf:Tijdstip"/>
    <xs:element name="entiteittype" type="stuf:Entiteittype" minOccurs="0"/>
    <xs:element name="functie" type="xs:string" minOccurs="0"/>
  </xs:sequence>
</xs:complexType>

<xs:complexType name="Systeem">
  <xs:sequence>
    <xs:element name="organisatie" type="xs:string"/>
    <xs:element name="applicatie" type="xs:string"/>
    <xs:element name="administratie" type="xs:string" minOccurs="0"/>
    <xs:element name="gebruiker" type="xs:string" minOccurs="0"/>
  </xs:sequence>
</xs:complexType>
```

Herken je de patronen uit Deel 2? Benoemde typen, sequence, minOccurs – precies wat je geleerd hebt.

### Voorbeeld: de mutatiesoort als simpleType

```xml
<xs:simpleType name="Mutatiesoort">
  <xs:restriction base="xs:string">
    <xs:enumeration value="T"/>
    <xs:enumeration value="W"/>
    <xs:enumeration value="V"/>
    <xs:enumeration value="E"/>
    <xs:enumeration value="I"/>
    <xs:enumeration value="S"/>
  </xs:restriction>
</xs:simpleType>
```

Dit is letterlijk het `xs:simpleType` met `xs:enumeration` dat je in sectie 2.2 hebt geleerd.

### Voorbeeld: attribuutgroepen

De StUF-metadata-attributen worden gebundeld in een `attributeGroup`:

```xml
<xs:attributeGroup name="entiteitAttributen">
  <xs:attribute name="entiteittype" type="stuf:Entiteittype" use="required"/>
  <xs:attribute name="sleutelVerzwordendeObjectSleutel" type="xs:string"/>
  <xs:attribute name="mutatiesoort" type="stuf:Mutatiesoort"/>
  <xs:attribute name="noValue" type="stuf:NoValue"/>
  <xs:attribute name="scope" type="xs:boolean"/>
  <xs:attribute name="verwerkingssoort" type="xs:string"/>
</xs:attributeGroup>
```

Deze groep wordt vervolgens hergebruikt in elk entiteittype – precies het `xs:attributeGroup`-patroon uit sectie 2.4.

## Sectormodel-schema's: het voorbeeld StUF-BG

Boven op de StUF-kern bouwen de sectormodellen hun domeinspecifieke schema's. Laten we StUF-BG als voorbeeld nemen.

### Bestandsstructuur van StUF-BG

Een typische StUF-BG schema-set bevat deze bestanden:

```
stuf0302.xsd                           ← StUF-kern
bg0310_stuf_simpleTypes.xsd            ← Domeinspecifieke datatypes
bg0310_ent_basis.xsd                   ← Entiteittypen (NPS, ADR, etc.)
bg0310_ent_gerelateerden.xsd           ← Relatie-entiteiten
bg0310_msg_vraagAntwoord.xsd           ← Vraag/antwoord-berichten
bg0310_msg_kennisgeving.xsd            ← Kennisgevingsberichten
bg0310.wsdl                            ← Webservice-beschrijving
```

### Import-keten

De bestanden hebben een import-hiërarchie:

```
bg0310_msg_vraagAntwoord.xsd
  │
  ├── import ─→ bg0310_ent_basis.xsd
  │                │
  │                ├── import ─→ bg0310_stuf_simpleTypes.xsd
  │                │
  │                └── import ─→ stuf0302.xsd (StUF-kern)
  │
  └── import ─→ stuf0302.xsd
```

Dit is precies het `xs:import`-patroon uit sectie 2.4 – schema's uit verschillende namespaces die naar elkaar verwijzen.

### Voorbeeld: simpleTypes in StUF-BG

Het bestand `bg0310_stuf_simpleTypes.xsd` definieert domeinspecifieke datatypes:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.stufstandaarden.nl/sector/bg/0310">

  <xs:simpleType name="BSN-e">
    <xs:restriction base="xs:string">
      <xs:pattern value="[0-9]{9}"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:simpleType name="Geslachtsaanduiding-e">
    <xs:restriction base="xs:string">
      <xs:enumeration value="M"/>
      <xs:enumeration value="V"/>
      <xs:enumeration value="O"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:simpleType name="AanduidingNaamgebruik-e">
    <xs:restriction base="xs:string">
      <xs:enumeration value="E"/>   <!-- Eigen naam -->
      <xs:enumeration value="P"/>   <!-- Naam partner -->
      <xs:enumeration value="V"/>   <!-- Naam partner voor eigen naam -->
      <xs:enumeration value="N"/>   <!-- Naam partner na eigen naam -->
    </xs:restriction>
  </xs:simpleType>

  <!-- Veel meer simpleTypes... -->

</xs:schema>
```

> De suffix `-e` in de typenamen (bijv. `BSN-e`) is een StUF-conventie die aangeeft dat het een "element-type" is – een type dat bedoeld is voor een XML-element.

### Voorbeeld: entiteittypen in StUF-BG

Het bestand `bg0310_ent_basis.xsd` definieert de complexe typen voor entiteiten:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.stufstandaarden.nl/sector/bg/0310"
           xmlns:bg="http://www.stufstandaarden.nl/sector/bg/0310"
           xmlns:StUF="http://www.egem.nl/StUF/StUF0302">

  <xs:import namespace="http://www.egem.nl/StUF/StUF0302"
             schemaLocation="stuf0302.xsd"/>

  <!-- Basistype voor Natuurlijk Persoon -->
  <xs:complexType name="NPS-basis">
    <xs:sequence>
      <xs:element name="inp.bsn" type="bg:BSN-e" minOccurs="0"/>
      <xs:element name="geslachtsnaam" type="xs:string" minOccurs="0"/>
      <xs:element name="voorvoegselGeslachtsnaam" type="xs:string"
                  minOccurs="0" nillable="true"/>
      <xs:element name="voornamen" type="xs:string" minOccurs="0"/>
      <xs:element name="geboortedatum" type="xs:string" minOccurs="0"/>
      <xs:element name="geslachtsaanduiding" type="bg:Geslachtsaanduiding-e"
                  minOccurs="0"/>
      <!-- Nog veel meer elementen... -->
      <xs:element name="verblijfsadres" type="bg:VerblijfsAdres-basis"
                  minOccurs="0"/>
    </xs:sequence>
    <xs:attributeGroup ref="StUF:entiteitAttributen"/>
  </xs:complexType>

</xs:schema>
```

Merk op:
- **Vrijwel alle elementen zijn optioneel** (`minOccurs="0"`) – dit is het basistype met alle mogelijke velden
- Het type gebruikt `xs:attributeGroup ref` om de StUF-metadata-attributen toe te voegen
- De `imports` halen de StUF-kern erbij
- `nillable="true"` op `voorvoegselGeslachtsnaam` maakt het mogelijk om via `noValue` aan te geven waarom het veld leeg is

### Het restriction-patroon: van basis naar bericht

Nu komt het kenmerkende StUF-patroon: via `xs:restriction` worden er **berichtspecifieke varianten** gemaakt van het basistype. Dit is het "Russisch poppetjes-model":

```
NPS-basis  (alles optioneel)
    │
    ├── restriction ──→ NPS-kennisgeving  (sommige velden verplicht)
    │
    ├── restriction ──→ NPS-vraag         (alleen zoekvelden)
    │
    └── restriction ──→ NPS-antwoord      (alleen scopevelden)
```

Voorbeeld:

```xml
<!-- Basistype: alles optioneel -->
<xs:complexType name="NPS-basis">
  <xs:sequence>
    <xs:element name="inp.bsn" type="bg:BSN-e" minOccurs="0"/>
    <xs:element name="geslachtsnaam" type="xs:string" minOccurs="0"/>
    <xs:element name="voornamen" type="xs:string" minOccurs="0"/>
    <xs:element name="geboortedatum" type="xs:string" minOccurs="0"/>
  </xs:sequence>
  <xs:attributeGroup ref="StUF:entiteitAttributen"/>
</xs:complexType>

<!-- Kennisgevingsvariant: BSN verplicht -->
<xs:complexType name="NPS-kennisgeving">
  <xs:complexContent>
    <xs:restriction base="bg:NPS-basis">
      <xs:sequence>
        <xs:element name="inp.bsn" type="bg:BSN-e"/>          <!-- nu verplicht -->
        <xs:element name="geslachtsnaam" type="xs:string"/>     <!-- nu verplicht -->
        <xs:element name="voornamen" type="xs:string" minOccurs="0"/>
        <xs:element name="geboortedatum" type="xs:string" minOccurs="0"/>
      </xs:sequence>
      <xs:attributeGroup ref="StUF:entiteitAttributen"/>
    </xs:restriction>
  </xs:complexContent>
</xs:complexType>
```

Dit is precies de `xs:restriction` op complexe typen die je in sectie 2.4 hebt geleerd – maar dan consequent en systematisch toegepast op alle entiteiten in StUF.

## Namespaces in StUF

StUF-schema's gebruiken meerdere namespaces. Hier de belangrijkste:

| Prefix | Namespace | Wat bevat het? |
|---|---|---|
| `StUF` | `http://www.egem.nl/StUF/StUF0302` | StUF-kern: stuurgegevens, mutatiesoort, tijdvakken |
| `bg` of `BG` | `http://www.stufstandaarden.nl/sector/bg/0310` | StUF-BG: entiteiten en berichten voor basisgegevens |
| `zkn` of `ZKN` | `http://www.stufstandaarden.nl/sector/zkn/0310` | StUF-ZKN: zaak-gerelateerde entiteiten |
| `xs` | `http://www.w3.org/2001/XMLSchema` | XSD-schema-elementen zelf |
| `xsi` | `http://www.w3.org/2001/XMLSchema-instance` | Schema-instantie attributen (nil, type, schemaLocation) |
| `soap` | `http://schemas.xmlsoap.org/soap/envelope/` | SOAP-envelop |
| `gml` | `http://www.opengis.net/gml` | Geometrie (voor ruimtelijke gegevens) |

### Hoe herken je de laag aan de namespace?

```xml
<BG:npsLk01
    xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
    xmlns:bg="http://www.stufstandaarden.nl/sector/bg/0310"
    xmlns:StUF="http://www.egem.nl/StUF/StUF0302"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <StUF:stuurgegevens>          <!-- StUF-kern -->
    <StUF:berichtcode>Lk01</StUF:berichtcode>
    <!-- ... -->
  </StUF:stuurgegevens>

  <BG:body>                     <!-- Koppelvlak-laag -->
    <BG:object StUF:entiteittype="NPS">   <!-- Attribuut uit StUF-kern -->
      <bg:geslachtsnaam>...</bg:geslachtsnaam>   <!-- Sectormodel -->
    </BG:object>
  </BG:body>

</BG:npsLk01>
```

Aan de prefix kun je direct zien uit welke laag een element komt:
- `StUF:` → StUF-kern (generiek)
- `BG:` → Koppelvlak (berichtstructuur)
- `bg:` → Sectormodel (entiteitgegevens)

> **Opmerking:** De conventie om `BG` (hoofdletters) te gebruiken voor het koppelvlak en `bg` (kleine letters) voor het sectormodel is niet universeel – het kan per implementatie variëren. Controleer altijd de `xmlns`-declaraties om de juiste namespace te verifiëren.

## Een schema lezen: stap voor stap

Als je voor het eerst een StUF-schema opent, kan het overweldigend zijn. Hier is een systematische aanpak:

### Stap 1: Identificeer de namespaces

Open het bestand en kijk naar de `<xs:schema>`-tag:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.stufstandaarden.nl/sector/bg/0310"
           xmlns:bg="http://www.stufstandaarden.nl/sector/bg/0310"
           xmlns:StUF="http://www.egem.nl/StUF/StUF0302"
           elementFormDefault="qualified">
```

Nu weet je:
- Dit schema definieert typen in de BG-namespace
- Het gebruikt de StUF-kern
- Elementen moeten gekwalificeerd zijn

### Stap 2: Bekijk de imports en includes

```xml
<xs:import namespace="http://www.egem.nl/StUF/StUF0302"
           schemaLocation="stuf0302.xsd"/>
<xs:include schemaLocation="bg0310_stuf_simpleTypes.xsd"/>
```

Nu weet je welke andere schema's dit bestand gebruikt.

### Stap 3: Zoek het type dat je interesseert

Zoek naar het complexType met de naam van de entiteit:

```xml
<xs:complexType name="NPS-basis">
```

### Stap 4: Lees de elementen

Loop door de `xs:sequence` en noteer:
- Welke elementen er zijn
- Welke verplicht zijn (`minOccurs="0"` of niet)
- Welke typen ze hebben (ingebouwde XSD-typen of domeinspecifieke typen)
- Of ze `nillable` zijn

### Stap 5: Volg de type-hiërarchie

Zoek naar afgeleide typen met `xs:restriction base="..."` of `xs:extension base="..."` die verwijzen naar het basistype.

## Validatie in de praktijk

StUF-schema's worden gebruikt om berichten te **valideren**. Dit kan op verschillende momenten:

| Moment | Wie valideert? | Waarom? |
|---|---|---|
| **Ontwikkeling** | Ontwikkelaar in IDE | Tijdens het bouwen van de koppeling |
| **Test** | Testomgeving | Automatische controle op geldigheid |
| **Runtime (zender)** | Zendend systeem | Voordat het bericht verstuurd wordt |
| **Runtime (ontvanger)** | Ontvangend systeem | Bij binnenkomst van het bericht |
| **Debugging** | Beheerder | Bij het analyseren van fouten |

### Validatietools

| Tool | Type | Gebruik |
|---|---|---|
| XMLSpy, oXygen | IDE | Visueel schema's bekijken en berichten valideren |
| SoapUI | Testool | SOAP-berichten samenstellen en versturen |
| xmllint | Command-line | Snel valideren op de command line |
| Eigen applicatiecode | Runtime | Validatie in de koppelingsoftware |

### Voorbeeld: validatie met xmllint

```bash
xmllint --schema bg0310_msg_kennisgeving.xsd bericht.xml --noout
```

Als het bericht geldig is:
```
bericht.xml validates
```

Als het bericht ongeldig is:
```
bericht.xml:15: element geslachtsnaam: Schemas validity error:
  Element 'geslachtsnaam': This element is not expected.
  Expected is ( inp.bsn ).
bericht.xml fails to validate
```

De foutmelding vertelt je precies wat er mis is: het element `geslachtsnaam` staat op de verkeerde plek; het schema verwacht `inp.bsn` op die positie (want de volgorde in een `xs:sequence` is vast!).

## De brug naar Deel 4

Met de kennis uit Deel 1 (XML), Deel 2 (XSD) en Deel 3 (StUF) heb je nu de theoretische basis om:

- StUF-schema's te openen en lezen
- Berichten te interpreteren en valideren
- De gelaagde structuur te begrijpen
- Namespaces te herkennen en navigeren

In Deel 4 gaan we deze kennis in de praktijk brengen: je werkt met echte StUF-schema's, stelt berichten samen, debugt problemen, en leert de patterns die je als informatieanalist of gegevensarchitect dagelijks tegenkomt.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **StUF-kern (`stuf0302.xsd`)** | Definieert generieke elementen: stuurgegevens, mutatiesoort, tijdvakken, attribuutgroepen |
| **Sectormodel-schema's** | Domeinspecifieke entiteittypen en datatypes (StUF-BG, StUF-ZKN, etc.) |
| **Koppelvlak-schema's** | Concrete berichtdefinities (npsLk01, npsLv01, etc.) |
| **Import-keten** | Schema's importeren elkaar via `xs:import` en `xs:include` |
| **Restriction-patroon** | Basistype (alles optioneel) → berichtspecifiek type (sommige verplicht) |
| **Namespaces** | StUF-kern, sectormodel en koppelvlak hebben elk hun eigen namespace |
| **Validatie** | Schema's worden gebruikt om berichten te valideren in elke fase |

> **Kernpunt:** StUF-schema's zijn een **praktijktoepassing** van alle XSD-concepten uit Deel 2. De gelaagde opbouw (kern → sectormodel → koppelvlak), het systematische gebruik van restriction voor berichtspecifieke typen, en de rijke namespace-structuur maken StUF-schema's complex maar logisch. Als je XSD beheerst, kun je StUF-schema's lezen – en als je StUF-schema's leest, wordt je XSD-kennis concreet.

---

**Einde Deel 3 – Introductie StUF**

Je hebt nu een compleet beeld van StUF: wat het is, hoe het zich verhoudt tot andere standaarden, hoe berichten zijn opgebouwd, welke berichttypen er bestaan, en hoe de XSD-schema's in elkaar steken. In Deel 4 gaan we van theorie naar praktijk.
