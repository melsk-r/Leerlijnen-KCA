---
title: "2.4 Hergebruik en modulariteit"
weight: 4
---

# 2.4 Hergebruik en modulariteit

In grotere projecten – en zeker bij StUF – werk je niet met één enkel schema-bestand. Schema's worden **modulair** opgezet: typen worden hergebruikt, schema's worden gesplitst in bestanden, en nieuwe typen worden afgeleid van bestaande typen. In deze sectie leer je alle technieken die XSD hiervoor biedt.

## Hergebruik via benoemde typen

In sectie 2.3 heb je het verschil gezien tussen globale en lokale definities. Een **benoemd (globaal) type** is de basis van hergebruik:

```xml
<xs:simpleType name="Postcode">
  <xs:restriction base="xs:string">
    <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
  </xs:restriction>
</xs:simpleType>
```

Dit type kun je nu overal gebruiken met het `type`-attribuut:

```xml
<xs:element name="woonpostcode" type="Postcode"/>
<xs:element name="werkpostcode" type="Postcode"/>
<xs:element name="factuurpostcode" type="Postcode"/>
```

Drie elementen, maar de validatieregel (4 cijfers + 2 hoofdletters) is slechts **één keer** gedefinieerd. Als de definitie van een postcode verandert, hoef je dat maar op één plek aan te passen.

Hetzelfde geldt voor complexe typen:

```xml
<xs:complexType name="AdresType">
  <xs:sequence>
    <xs:element name="straat" type="xs:string"/>
    <xs:element name="huisnummer" type="xs:positiveInteger"/>
    <xs:element name="postcode" type="Postcode"/>
    <xs:element name="woonplaats" type="xs:string"/>
  </xs:sequence>
</xs:complexType>

<!-- Hergebruik in meerdere elementen -->
<xs:element name="woonadres" type="AdresType"/>
<xs:element name="postadres" type="AdresType"/>
<xs:element name="factuuradres" type="AdresType"/>
```

### Verwijzingen naar elementen met `ref`

Naast type-hergebruik kun je ook naar **elementen** verwijzen. Een globaal gedefinieerd element kan hergebruikt worden met `ref`:

```xml
<!-- Globale elementdefinitie -->
<xs:element name="postcode" type="Postcode"/>

<!-- Hergebruik in een complex type -->
<xs:complexType name="AdresType">
  <xs:sequence>
    <xs:element name="straat" type="xs:string"/>
    <xs:element ref="postcode"/>    <!-- Verwijzing naar globaal element -->
    <xs:element name="woonplaats" type="xs:string"/>
  </xs:sequence>
</xs:complexType>
```

**Verschil `type` vs. `ref`:**

| Aanpak | Wat hergebruik je? | Elementnaam | Gebruik |
|---|---|---|---|
| `type="..."` | Het **type** (validatieregels) | Geef je zelf op bij het element | Meest flexibel |
| `ref="..."` | Het gehele **element** (naam + type) | Vastgelegd in de globale definitie | Wanneer element overal dezelfde naam moet hebben |

> **StUF-context:** StUF-schema's gebruiken zowel `type`-verwijzingen als `ref`-verwijzingen. Veel voorkomende elementen zoals tijdvakken en metadata-elementen worden als globale elementen gedefinieerd en met `ref` hergebruikt.

## Schema's opsplitsen: `xs:include` en `xs:import`

Een groot schema in één bestand wordt al snel onoverzichtelijk. XSD biedt twee mechanismen om schema's over meerdere bestanden te verdelen.

### `xs:include` – bestanden samenvoegen

Met `xs:include` voeg je een ander schema-bestand **in dezelfde namespace** samen met het huidige schema. Denk aan een `#include` in C of een `import` in Python:

Bestand `typen-basis.xsd`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/schema"
           xmlns:tns="http://www.example.nl/schema">

  <xs:simpleType name="Postcode">
    <xs:restriction base="xs:string">
      <xs:pattern value="[0-9]{4}[A-Z]{2}"/>
    </xs:restriction>
  </xs:simpleType>

  <xs:complexType name="AdresType">
    <xs:sequence>
      <xs:element name="straat" type="xs:string"/>
      <xs:element name="huisnummer" type="xs:positiveInteger"/>
      <xs:element name="postcode" type="tns:Postcode"/>
      <xs:element name="woonplaats" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

Bestand `persoon.xsd`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/schema"
           xmlns:tns="http://www.example.nl/schema">

  <!-- Include: voeg typen-basis.xsd samen in dit schema -->
  <xs:include schemaLocation="typen-basis.xsd"/>

  <xs:complexType name="PersoonType">
    <xs:sequence>
      <xs:element name="naam" type="xs:string"/>
      <xs:element name="adres" type="tns:AdresType"/>   <!-- Uit typen-basis.xsd -->
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

**Belangrijk:** `xs:include` werkt alleen als beide bestanden óf dezelfde `targetNamespace` hebben, óf het geïncludeerde bestand **geen** `targetNamespace` heeft (een zogenaamd *chameleon schema* – het neemt de namespace over van het includerende bestand).

### `xs:import` – schema's uit een andere namespace

Met `xs:import` gebruik je typen uit een schema dat een **andere namespace** heeft:

Bestand `adres.xsd` (namespace: `http://www.example.nl/adres`):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/adres"
           xmlns:adr="http://www.example.nl/adres">

  <xs:complexType name="AdresType">
    <xs:sequence>
      <xs:element name="straat" type="xs:string"/>
      <xs:element name="huisnummer" type="xs:positiveInteger"/>
      <xs:element name="woonplaats" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

</xs:schema>
```

Bestand `persoon.xsd` (namespace: `http://www.example.nl/persoon`):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.nl/persoon"
           xmlns:per="http://www.example.nl/persoon"
           xmlns:adr="http://www.example.nl/adres">

  <!-- Import: gebruik schema uit een andere namespace -->
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

Let op het verschil:
- `xs:include` → **dezelfde** namespace (of geen namespace) → alleen `schemaLocation` nodig
- `xs:import` → **andere** namespace → zowel `namespace` als `schemaLocation` nodig

### Overzicht `include` vs. `import`

| Kenmerk | `xs:include` | `xs:import` |
|---|---|---|
| Namespace | Zelfde of geen | Andere |
| Attribuut `namespace` | Niet nodig | Verplicht |
| Attribuut `schemaLocation` | Verplicht | Optioneel (maar vaak gebruikt) |
| Analogie | Bestanden samenvoegen | Bibliotheek importeren |
| Gebruik | Opknippen van groot schema | Hergebruik van extern schema |

> **StUF-context:** StUF-schema's zijn zeer modulair. De kern-schema's (StUF-BG, StUF-ZKN, etc.) gebruiken intensief `xs:import` en `xs:include`. Het basisschema `stuf0302.xsd` definieert fundamentele StUF-typen, en domeinschema's importeren deze om hun eigen berichttypes te definiëren. Je zult in StUF-schema's tientallen `import`- en `include`-regels tegenkomen.

## Type-afleiding: extension en restriction

Een van de krachtigste features van XSD is **type-afleiding**: je kunt een nieuw type baseren op een bestaand type, door het uit te breiden of te beperken. Dit is vergelijkbaar met overerving (inheritance) in objectgeoriënteerd programmeren.

### `xs:extension` – een type uitbreiden

Met `xs:extension` voeg je elementen of attributen toe aan een bestaand type.

**Bij complex types met complexContent:**

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

`MedewerkerType` bevat nu **alle** elementen van `PersoonType` (naam, geboortedatum) **plus** de extra elementen (functie, afdeling):

```xml
<medewerker>
  <naam>Jan de Vries</naam>
  <geboortedatum>1985-03-15</geboortedatum>
  <functie>Ontwikkelaar</functie>
  <afdeling>ICT</afdeling>
</medewerker>
```

Het verband met objectgeoriënteerd programmeren:

```
PersoonType          (basisklasse)
  ├── naam
  └── geboortedatum
       │
       ▼
MedewerkerType       (afgeleide klasse = extends PersoonType)
  ├── naam            ← geërfd
  ├── geboortedatum   ← geërfd
  ├── functie         ← toegevoegd
  └── afdeling        ← toegevoegd
```

**Bij simpele types met simpleContent:**

Je kunt ook een simpel type uitbreiden met attributen (dit zag je al kort in 2.2):

```xml
<xs:complexType name="Bedrag">
  <xs:simpleContent>
    <xs:extension base="xs:decimal">
      <xs:attribute name="valuta" type="xs:string" use="required"/>
    </xs:extension>
  </xs:simpleContent>
</xs:complexType>
```

```xml
<prijs valuta="EUR">29.95</prijs>
```

### `xs:restriction` – een type beperken

Met `xs:restriction` maak je een type **strenger** dan het basistype. Je hebt dit al gezien bij simpele typen (sectie 2.2). Het kan ook bij complexe typen:

**Restriction op complex type:**

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
        <!-- Adres is hier weggelaten (was optioneel) -->
      </xs:sequence>
    </xs:restriction>
  </xs:complexContent>
</xs:complexType>
```

**Regels voor restriction:**
- Je kunt elementen alleen **strenger** maken, niet ruimer
- Een verplicht element in het basistype moet verplicht blijven
- Een optioneel element mag verplicht gemaakt worden of weggelaten
- Het resultaattype moet een geldige subset zijn van het basistype

> **StUF-context:** StUF maakt intensief gebruik van `xs:restriction`. Het basistype bevat alle mogelijk denkbare elementen (vaak optioneel), en per berichttype wordt een restriction gemaakt die alleen de relevante elementen over houdt en sommige verplicht maakt. Dit is het zogenaamde **"Russisch poppetjes-model"** (of matryoshka-model) van StUF.

### Extension vs. restriction samengevat

| Techniek | Wat doet het? | Richting | Voorbeeld |
|---|---|---|---|
| `xs:extension` | Voegt elementen/attributen toe | Basistype → **ruimer** | PersoonType → MedewerkerType |
| `xs:restriction` | Beperkt of verwijdert optionele delen | Basistype → **strikter** | PersoonType → KernPersoonType |

## Abstracte typen

Een type kan gemarkeerd worden als `abstract`. Dit betekent dat het type **niet direct** in XML-documenten gebruikt mag worden – er moet altijd een afgeleid type gekozen worden:

```xml
<xs:complexType name="VoertuigType" abstract="true">
  <xs:sequence>
    <xs:element name="kenteken" type="xs:string"/>
    <xs:element name="bouwjaar" type="xs:gYear"/>
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

<xs:complexType name="MotorType">
  <xs:complexContent>
    <xs:extension base="VoertuigType">
      <xs:sequence>
        <xs:element name="cilinderinhoud" type="xs:integer"/>
      </xs:sequence>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```

Een element van type `VoertuigType` kan niet direct bestaan. In XML moet je met `xsi:type` aangeven welk concreet type je bedoelt:

```xml
<voertuig xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:type="AutoType">
  <kenteken>AB-123-CD</kenteken>
  <bouwjaar>2020</bouwjaar>
  <aantalDeuren>5</aantalDeuren>
</voertuig>
```

> Abstracte typen dwingen af dat er altijd een **specifiek** afgeleid type wordt gekozen, nooit het generieke basistype zelf.

## Substitutiegroepen

Substitutiegroepen bieden een alternatieve manier om polymorfisme te realiseren. Een **hoofd-element** (head element) kan vervangen worden door elementen die in dezelfde substitutiegroep zitten:

```xml
<!-- Hoofd-element -->
<xs:element name="voertuig" type="VoertuigType"/>

<!-- Substitueerbare elementen -->
<xs:element name="auto" substitutionGroup="voertuig" type="AutoType"/>
<xs:element name="motor" substitutionGroup="voertuig" type="MotorType"/>
```

Nu mag overal waar `<voertuig>` verwacht wordt, ook `<auto>` of `<motor>` staan:

```xml
<parkeerplaats>
  <voertuig>
    <kenteken>AB-123-CD</kenteken>
    <bouwjaar>2020</bouwjaar>
  </voertuig>
  <auto>
    <kenteken>EF-456-GH</kenteken>
    <bouwjaar>2021</bouwjaar>
    <aantalDeuren>3</aantalDeuren>
  </auto>
  <motor>
    <kenteken>IJ-789-KL</kenteken>
    <bouwjaar>2019</bouwjaar>
    <cilinderinhoud>650</cilinderinhoud>
  </motor>
</parkeerplaats>
```

**Verschil abstracte typen vs. substitutiegroepen:**

| Kenmerk | Abstracte typen + `xsi:type` | Substitutiegroepen |
|---|---|---|
| Elementnaam in XML | Altijd hetzelfde (bv. `<voertuig>`) | Wisselt (bv. `<auto>`, `<motor>`) |
| Type-aanduiding | Via `xsi:type` attribuut | Impliciet door elementnaam |
| Schema-aanpassing | Nieuwe typen afleiden | Nieuwe elementen aan groep toevoegen |

> **StUF-context:** StUF maakt beperkt gebruik van substitutiegroepen, maar het concept is waardevol om te begrijpen wanneer je StUF-schema's analyseert of wanneer je berichten ziet waarin elementnamen variëren.

## Groepen: `xs:group` en `xs:attributeGroup`

Voor hergebruik van **groepen elementen** of **groepen attributen** die samen voorkomen:

### `xs:group` – herbruikbare elementgroep

```xml
<xs:group name="NaamGroep">
  <xs:sequence>
    <xs:element name="voornaam" type="xs:string"/>
    <xs:element name="tussenvoegsel" type="xs:string" minOccurs="0"/>
    <xs:element name="achternaam" type="xs:string"/>
  </xs:sequence>
</xs:group>

<!-- Hergebruik -->
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:group ref="NaamGroep"/>
    <xs:element name="geboortedatum" type="xs:date"/>
  </xs:sequence>
</xs:complexType>

<xs:complexType name="ContactpersoonType">
  <xs:sequence>
    <xs:group ref="NaamGroep"/>
    <xs:element name="email" type="xs:string"/>
  </xs:sequence>
</xs:complexType>
```

Beide typen bevatten dezelfde naam-elementen (voornaam, tussenvoegsel, achternaam) zonder duplicatie.

### `xs:attributeGroup` – herbruikbare attribuutgroep

```xml
<xs:attributeGroup name="StUFMetadata">
  <xs:attribute name="mutatiesoort" type="xs:string"/>
  <xs:attribute name="indicatorOvername" type="xs:string"/>
  <xs:attribute name="noValue" type="xs:string"/>
</xs:attributeGroup>

<!-- Hergebruik -->
<xs:complexType name="PersoonType">
  <xs:sequence>
    <xs:element name="naam" type="xs:string"/>
  </xs:sequence>
  <xs:attributeGroup ref="StUFMetadata"/>
</xs:complexType>
```

> **StUF-context:** `xs:attributeGroup` wordt in StUF-schema's veelvuldig gebruikt. De basismetadata-attributen die bij elk StUF-element horen (zoals `mutatiesoort`, `noValue`, etc.) worden als `attributeGroup` gedefinieerd en overal hergebruikt. Dit is een van de meest karakteristieke patronen in StUF-schema's.

## Modulaire schema-architectuur in de praktijk

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

In StUF ziet dit er concreet zo uit:

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

Elk bestand heeft een duidelijke verantwoordelijkheid. Dit maakt het schema onderhoudbaar en herbruikbaar.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **Benoemde typen** | Globale typen die hergebruikt worden via `type="..."` |
| **`ref`** | Verwijzing naar een globaal element |
| **`xs:include`** | Voegt schema samen uit dezelfde namespace |
| **`xs:import`** | Importeert schema uit een andere namespace |
| **`xs:extension`** | Breidt een type uit met extra elementen/attributen |
| **`xs:restriction`** | Beperkt een type (maakt strenger) |
| **Abstracte typen** | Niet direct te gebruiken, moet afgeleid worden |
| **Substitutiegroepen** | Elementen die een ander element mogen vervangen |
| **`xs:group`** | Herbruikbare groep elementen |
| **`xs:attributeGroup`** | Herbruikbare groep attributen |

> **Kernpunt:** XSD biedt rijke mogelijkheden voor hergebruik en modulariteit. Via benoemde typen, element-verwijzingen, include/import, type-afleiding en groepen bouw je schema's die onderhoudbaar, consistent en schaalbaar zijn. StUF-schema's zijn een schoolvoorbeeld van al deze technieken in de praktijk – het begrijpen van deze concepten is essentieel om StUF-schema's te kunnen lezen en aanpassen.
