---
title: "2.5 Namespaces in XSD"
weight: 5
---

# 2.5 Namespaces in XSD

In Deel 1 (sectie 1.3) heb je geleerd wat XML-namespaces zijn en hoe ze werken in XML-documenten. In deze sectie leer je hoe namespaces werken **aan de schema-kant**: hoe je een schema koppelt aan een namespace, hoe je elementen kwalificeert, en hoe je een XML-document aan het juiste schema koppelt.

## `targetNamespace` – de namespace van je schema

Het attribuut `targetNamespace` op het `<xs:schema>`-element bepaalt in welke namespace alle globale elementen, typen en attributen van dit schema terechtkomen:

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

Wat gebeurt hier?

1. **`targetNamespace="http://www.example.nl/persoon"`** – alle globale definities (element `persoon`, type `PersoonType`) behoren tot deze namespace.

2. **`xmlns:per="http://www.example.nl/persoon"`** – we declareren de prefix `per` voor onze eigen namespace, zodat we ernaar kunnen verwijzen bínnen het schema.

3. **`type="per:PersoonType"`** – wanneer we verwijzen naar `PersoonType` (dat in onze eigen namespace leeft), moeten we de prefix gebruiken.

### Verwijzen naar eigen typen

Dit is een veelgemaakte fout bij beginners: vergeten dat je eigen typen in de `targetNamespace` zitten en dus een prefix nodig hebben:

```xml
<!-- FOUT: PersoonType is niet in de default namespace -->
<xs:element name="persoon" type="PersoonType"/>

<!-- GOED: PersoonType zit in de target namespace -->
<xs:element name="persoon" type="per:PersoonType"/>
```

Er is een alternatief: je kunt je eigen namespace als **default namespace** declareren:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon"
           xmlns="http://www.example.nl/persoon">

  <!-- Nu hoeft geen prefix: -->
  <xs:element name="persoon" type="PersoonType"/>
</xs:schema>
```

> Let op: de `xs:`-prefix is dan nog steeds nodig voor XSD-elementen zelf (`xs:schema`, `xs:element`, etc.) omdat die in de XSD-namespace zitten.

## `elementFormDefault` – kwalificatie van lokale elementen

Dit is een cruciaal maar vaak verwarrend concept. Het bepaalt of **lokale elementen** (kind-elementen binnen een complex type) in het XML-document een namespace-prefix nodig hebben.

### Twee opties

| Waarde | Betekenis |
|---|---|
| `unqualified` (standaard) | Lokale elementen hebben **geen** namespace-prefix in XML |
| `qualified` | Lokale elementen **moeten** een namespace-prefix hebben (of de default namespace) |

### Voorbeeld met `elementFormDefault="unqualified"` (standaard)

Schema:
```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon"
           xmlns:per="http://www.example.nl/persoon"
           elementFormDefault="unqualified">

  <xs:element name="persoon" type="per:PersoonType"/>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="naam" type="xs:string"/>
      <xs:element name="leeftijd" type="xs:integer"/>
    </xs:sequence>
  </xs:complexType>
</xs:schema>
```

Geldig XML:
```xml
<per:persoon xmlns:per="http://www.example.nl/persoon">
  <naam>Jan</naam>
  <leeftijd>35</leeftijd>
</per:persoon>
```

Merk op:
- Het **root-element** `persoon` is een globaal element en heeft de prefix `per:`
- De **kind-elementen** `naam` en `leeftijd` zijn lokaal en hebben **geen** prefix

### Voorbeeld met `elementFormDefault="qualified"`

Zelfde schema maar met `elementFormDefault="qualified"`:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon"
           xmlns:per="http://www.example.nl/persoon"
           elementFormDefault="qualified">
  <!-- ... zelfde inhoud ... -->
</xs:schema>
```

Geldig XML (optie 1 – met prefix):
```xml
<per:persoon xmlns:per="http://www.example.nl/persoon">
  <per:naam>Jan</per:naam>
  <per:leeftijd>35</per:leeftijd>
</per:persoon>
```

Geldig XML (optie 2 – met default namespace):
```xml
<persoon xmlns="http://www.example.nl/persoon">
  <naam>Jan</naam>
  <leeftijd>35</leeftijd>
</persoon>
```

In de tweede optie zit alles in de default namespace, waardoor geen prefixen nodig zijn.

### Wat is het verschil in de praktijk?

| `elementFormDefault` | Voordeel | Nadeel |
|---|---|---|
| `unqualified` | XML-documenten zijn compacter | Verwarrend: kind-elementen zitten technisch in "geen namespace" |
| `qualified` | Alle elementen expliciet in de namespace | XML-documenten worden langer (meer prefixen) |

> **StUF-context:** StUF-schema's gebruiken `elementFormDefault="qualified"`. Dit betekent dat in StUF-berichten alle elementen gekwalificeerd moeten zijn – ze moeten in de juiste namespace staan. In de praktijk wordt vaak de default namespace gebruikt zodat niet alle elementen een prefix nodig hebben.

### `attributeFormDefault`

Er bestaat ook `attributeFormDefault`, die hetzelfde doet voor attributen. Standaard is dit `unqualified`, wat betekent dat attributen geen namespace-prefix nodig hebben. Dit is in de meeste gevallen (ook in StUF) het gewenste gedrag.

## Schema koppelen aan een XML-document

Nu het belangrijke praktische brug: hoe vertel je een XML-validator **welk schema** hoort bij een XML-document?

### `xsi:schemaLocation`

Het attribuut `xsi:schemaLocation` koppelt een **namespace** aan een **schema-bestand**:

```xml
<persoon xmlns="http://www.example.nl/persoon"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.example.nl/persoon persoon.xsd">
  <naam>Jan</naam>
</persoon>
```

De waarde van `xsi:schemaLocation` bestaat uit **paren**: `namespace schemaLocatie namespace schemaLocatie ...`

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

### `xsi:noNamespaceSchemaLocation`

Voor XML-documenten **zonder** namespace:

```xml
<persoon xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="persoon.xsd">
  <naam>Jan</naam>
</persoon>
```

### Belangrijk: `xsi:schemaLocation` is een *hint*

`xsi:schemaLocation` is een **hint** voor de validator, geen verplichting. De validator mág het negeren en een ander schema gebruiken. In de praktijk respecteren de meeste tools de hint, maar het schema kan ook op een andere manier worden geconfigureerd (bijvoorbeeldeld via command-line opties of catalogus-bestanden).

> De namespace `http://www.w3.org/2001/XMLSchema-instance` met prefix `xsi` is een speciale namespace die attributen bevat voor schema-instantiatie, zoals `xsi:schemaLocation`, `xsi:noNamespaceSchemaLocation`, `xsi:type` en `xsi:nil`.

## Meerdere namespaces in een schema-set

In complexe projecten (zoals StUF) werken meerdere schema's samen, elk met hun eigen namespace. Laten we een realistisch voorbeeld bekijken:

### Scenario: drie samenwerkende schema's

**1. Basistypen** (`http://www.example.nl/basis`):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/basis"
           xmlns:bas="http://www.example.nl/basis"
           elementFormDefault="qualified">

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

</xs:schema>
```

**2. Persoonsgegevens** (`http://www.example.nl/persoon`):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon"
           xmlns:per="http://www.example.nl/persoon"
           xmlns:bas="http://www.example.nl/basis"
           elementFormDefault="qualified">

  <xs:import namespace="http://www.example.nl/basis"
             schemaLocation="basis.xsd"/>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="bsn" type="bas:BSN"/>       <!-- Uit basis-schema -->
      <xs:element name="naam" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

  <xs:element name="persoon" type="per:PersoonType"/>

</xs:schema>
```

**3. Berichten** (`http://www.example.nl/bericht`):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/bericht"
           xmlns:ber="http://www.example.nl/bericht"
           xmlns:per="http://www.example.nl/persoon"
           elementFormDefault="qualified">

  <xs:import namespace="http://www.example.nl/persoon"
             schemaLocation="persoon.xsd"/>

  <xs:complexType name="RegistratieBerichtType">
    <xs:sequence>
      <xs:element name="tijdstip" type="xs:dateTime"/>
      <xs:element ref="per:persoon"/>    <!-- Element uit persoon-schema -->
    </xs:sequence>
  </xs:complexType>

  <xs:element name="registratieBericht" type="ber:RegistratieBerichtType"/>

</xs:schema>
```

### Het XML-document

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ber:registratieBericht
    xmlns:ber="http://www.example.nl/bericht"
    xmlns:per="http://www.example.nl/persoon"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
      http://www.example.nl/bericht  bericht.xsd
      http://www.example.nl/persoon  persoon.xsd
      http://www.example.nl/basis    basis.xsd">

  <ber:tijdstip>2024-01-15T10:30:00</ber:tijdstip>
  <per:persoon>
    <per:bsn>123456789</per:bsn>
    <per:naam>Jan de Vries</per:naam>
  </per:persoon>
</ber:registratieBericht>
```

Merk op:
- Elk element heeft de prefix van zijn eigen namespace
- `<ber:tijdstip>` hoort bij het bericht-schema
- `<per:persoon>`, `<per:bsn>`, `<per:naam>` horen bij het persoon-schema
- De `xsi:schemaLocation` verwijst naar alle drie de schema's
- In de import-keten trekt het bericht-schema het persoon-schema mee, dat op zijn beurt het basis-schema meetrekt

### Namespace-tabel van dit voorbeeld

| Prefix | Namespace | Schema | Bevat |
|---|---|---|---|
| `xs` | `http://www.w3.org/2001/XMLSchema` | (ingebouwd) | XSD-elementen zelf |
| `xsi` | `http://www.w3.org/2001/XMLSchema-instance` | (ingebouwd) | `schemaLocation`, `type`, `nil` |
| `bas` | `http://www.example.nl/basis` | basis.xsd | BSN, Postcode |
| `per` | `http://www.example.nl/persoon` | persoon.xsd | PersoonType |
| `ber` | `http://www.example.nl/bericht` | bericht.xsd | RegistratieBerichtType |

> **StUF-context:** Een typisch StUF-bericht bevat elementen uit 5-10 verschillende namespaces: de StUF-basisnamespace (`http://www.egem.nl/StUF/StUF0302`), domeinnamespaces (BG, ZKN, etc.), GML voor geometrie, SOAP voor transport, en meer. Het begrijpen van hoe meerdere namespaces samenwerken is essentieel om StUF-berichten te kunnen lezen.

## Schema zonder namespace

Het is ook mogelijk om een schema **zonder** `targetNamespace` te maken:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="persoon">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="naam" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

Het XML-document heeft dan ook geen namespace:
```xml
<persoon>
  <naam>Jan</naam>
</persoon>
```

Dit is eenvoudiger, maar in professionele omgevingen (en zeker bij StUF) werk je altijd met namespaces om conflicten te voorkomen.

## Veelgemaakte fouten

### 1. Vergeten om eigen namespace als prefix te declareren

```xml
<!-- FOUT: geen prefix voor targetNamespace -->
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon">
  <xs:element name="persoon" type="PersoonType"/>  <!-- PersoonType niet gevonden! -->
</xs:schema>
```

Oplossing: voeg `xmlns:per="http://www.example.nl/persoon"` toe en schrijf `type="per:PersoonType"`.

### 2. `xs:include` gebruiken met verschillende namespaces

```xml
<!-- FOUT: include werkt niet met verschillende namespaces -->
<xs:schema targetNamespace="http://www.example.nl/persoon">
  <xs:include schemaLocation="adres.xsd"/>  <!-- adres.xsd heeft ANDERE namespace -->
</xs:schema>
```

Oplossing: gebruik `xs:import` als de namespaces verschillen.

### 3. Namespace-mismatch tussen schema en XML

```xml
<!-- Schema verwacht: http://www.example.nl/persoon -->
<!-- XML gebruikt: http://www.example.nl/Persoon  (hoofdletter!) -->
```

Namespaces zijn **hoofdlettergevoelig** – een kleine typfout maakt het document ongeldig.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **`targetNamespace`** | De namespace waaraan het schema zijn definities toekent |
| **`elementFormDefault`** | `qualified` = lokale elementen moeten in namespace; `unqualified` = niet |
| **`xsi:schemaLocation`** | Koppelt namespace aan schema-bestand in XML (hint voor validator) |
| **`xsi:noNamespaceSchemaLocation`** | Koppelt schema zonder namespace |
| **Meerdere namespaces** | Schema's importeren elkaar; XML-document declareert alle benodigde namespaces |
| **Schema zonder namespace** | Mogelijk maar niet aanbevolen voor professioneel gebruik |

> **Kernpunt:** Namespaces in XSD zorgen ervoor dat schema-definities uniek identificeerbaar zijn en conflictvrij samenwerken. Met `targetNamespace` wijs je je schema aan een namespace toe, met `elementFormDefault` bepaal je hoe elementen in XML gekwalificeerd worden, en met `xsi:schemaLocation` verbind je XML-documenten aan de juiste schema's. In de wereld van StUF – waar tientallen schema's samenwerken – is dit onmisbare kennis.

---

**Einde Deel 2 – XML Schema (XSD)**

Je hebt nu een complete basis in XSD. Je kunt schema's lezen en schrijven, typen definiëren en hergebruiken, schema's modulair opzetten, en begrijpen hoe namespaces in schema's werken. In Deel 3 gaan we deze kennis toepassen op StUF: je zult zien hoe al deze XSD-concepten samenkomen in de StUF-standaard.
