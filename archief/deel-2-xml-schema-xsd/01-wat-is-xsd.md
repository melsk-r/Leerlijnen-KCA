---
title: "2.1 Wat is XSD?"
weight: 1
---

# 2.1 Wat is XSD?

In Deel 1 heb je geleerd hoe je met XML gegevens structureert: elementen, attributen, namespaces en speciale constructies. Maar XML zelf legt alleen syntaxregels op – *welgevormdheid*. Het zegt niets over **welke** elementen en attributen zijn toegestaan, in welke volgorde ze mogen voorkomen of welke waarden geldig zijn. Daar komt XSD om de hoek kijken.

## Het probleem: XML is te vrij

Stel je voor: twee gemeentelijke systemen wisselen persoonsgegevens uit via XML. Systeem A stuurt:

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

Beide documenten zijn **welgevormd** XML – ze voldoen aan alle syntaxregels uit Deel 1. Maar ze zijn niet compatibel: de structuur verschilt (wel of geen `<naam>`-omhulsel) en het datumformaat is anders (`1985-03-15` vs. `15-03-1985`).

Hoe spreek je af welke structuur en welke waarden toegestaan zijn? Hoe controleer je automatisch of een bericht aan die afspraken voldoet?

## De oplossing: een schema

Een **schema** is een formele beschrijving van de toegestane structuur en inhoud van een XML-document. Het is als een *blauwdruk* of *contract*: het legt vast welke elementen er mogen zijn, in welke volgorde, hoe vaak, en welk type waarde ze mogen bevatten.

Met een schema kun je automatisch controleren of een XML-document geldig is – dit heet **validatie**. Als een document niet voldoet aan het schema, wordt het afgekeurd met een foutmelding die precies aangeeft wat er mis is.

## Wat is XSD precies?

**XSD** staat voor **XML Schema Definition**. Het is een taal om schema's mee te schrijven – en het bijzondere is: **een XSD-bestand is zelf ook XML**. Je gebruikt dus XML om te beschrijven hoe andere XML-documenten eruit moeten zien.

| Eigenschap | Uitleg |
|---|---|
| **Voluit** | XML Schema Definition |
| **Bestandsextensie** | `.xsd` |
| **Formaat** | XML (een XSD is zelf een geldig XML-document) |
| **Doel** | Formeel beschrijven welke structuur en waarden een XML-document mag hebben |
| **Toepassing** | Validatie: automatisch controleren of een XML-document aan de regels voldoet |
| **Beheerder** | W3C (World Wide Web Consortium), dezelfde organisatie achter XML zelf |

## Een eerste voorbeeld

Laten we het concreet maken. Stel, je wilt vastleggen dat een `<persoon>` precies een `<voornaam>`, een `<achternaam>` en een `<geboortedatum>` moet bevatten. In XSD ziet dat er zo uit:

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

Schrik niet van alle nieuwe termen – die worden in de volgende secties stap voor stap uitgelegd. Laten we dit voorbeeld eerst op hoofdlijnen doorlopen:

| Regel | Betekenis |
|---|---|
| `<xs:schema xmlns:xs="...">` | Het root-element van elk XSD-bestand; de prefix `xs` verwijst naar de XML Schema-namespace |
| `<xs:element name="persoon">` | Er moet een element `<persoon>` bestaan |
| `<xs:complexType>` | Het element `<persoon>` heeft een complexe opbouw (het bevat kind-elementen) |
| `<xs:sequence>` | De kind-elementen moeten in de opgegeven volgorde voorkomen |
| `<xs:element name="voornaam" type="xs:string"/>` | Er moet een element `<voornaam>` zijn, met een tekstwaarde |
| `<xs:element name="geboortedatum" type="xs:date"/>` | Er moet een element `<geboortedatum>` zijn, met een datumwaarde (formaat `JJJJ-MM-DD`) |

### Geldig XML-document

Dit document voldoet aan bovenstaand schema:

```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <geboortedatum>1985-03-15</geboortedatum>
</persoon>
```

### Ongeldig XML-document

Dit document is welgevormd XML, maar **ongeldig** volgens het schema:

```xml
<persoon>
  <achternaam>de Vries</achternaam>
  <voornaam>Jan</voornaam>
  <geboortedatum>15 maart 1985</geboortedatum>
</persoon>
```

Drie fouten:
1. `<achternaam>` staat vóór `<voornaam>` – het schema eist een vaste volgorde
2. `<geboortedatum>` bevat `"15 maart 1985"` – het schema eist het datumtype `xs:date` (formaat `JJJJ-MM-DD`)
3. De volgorde is omgekeerd

## Welgevormd vs. geldig

Dit is een cruciaal onderscheid:

| Begrip | Betekenis | Gecontroleerd door |
|---|---|---|
| **Welgevormd** (well-formed) | Het document voldoet aan de XML-syntaxregels (Deel 1) | Elke XML-parser |
| **Geldig** (valid) | Het document voldoet aan een specifiek schema (XSD) | Een XML-validator met het bijbehorende schema |

Een document moet **eerst** welgevormd zijn voordat het gevalideerd kan worden. Een document dat niet welgevormd is, kan niet eens gelezen worden door een parser.

```
Stap 1: Is het welgevormd?  → Nee  → Afgekeurd (syntaxfout)
                             → Ja   → Stap 2: Is het geldig volgens het schema?
                                              → Nee  → Afgekeurd (validatiefout)
                                              → Ja   → Geaccepteerd ✓
```

## XSD vs. DTD

Voordat XSD bestond (het werd een W3C-standaard in 2001), werden XML-structuren beschreven met **DTD's** (Document Type Definitions). Je hebt DTD's kort gezien in sectie 1.4. XSD is de opvolger van DTD en biedt veel meer mogelijkheden:

| Eigenschap | DTD | XSD |
|---|---|---|
| **Formaat** | Eigen, niet-XML syntax | XML |
| **Datatypen** | Nauwelijks (alleen tekst) | Uitgebreid: string, integer, date, boolean, etc. |
| **Namespace-ondersteuning** | Geen | Volledig |
| **Restricties** | Zeer beperkt | Uitgebreid: patronen, min/max, enumeraties, etc. |
| **Hergebruik** | Beperkt | Typen, includes, imports, overerving |
| **Leesbaarheid** | Compact maar cryptisch | Uitgebreider maar logisch gestructureerd |

Voor StUF is de keuze eenvoudig: **StUF gebruikt uitsluitend XSD**. DTD's zijn hier niet relevant, maar het is goed om het verschil te kennen als je oudere documentatie tegenkomt.

## Waarom is XSD belangrijk voor StUF?

StUF-berichten worden gedefinieerd door een set XSD-schema's. Deze schema's zijn het fundament van de standaard:

1. **Contract tussen systemen** – Het schema legt exact vast hoe een bericht eruit moet zien. Als een leverancier een systeem bouwt dat StUF-berichten verstuurt, moet elk bericht valideren tegen het schema.

2. **Automatische validatie** – Vóórdat een bericht verwerkt wordt, kan het ontvangend systeem het automatisch valideren. Ongeldig? Dan wordt het geweigerd met een foutmelding, nog voordat er iets in een database terechtkomt.

3. **Documentatie** – De XSD-schema's zijn tegelijk de technische documentatie van de standaard. Ze beschrijven precies welke elementen, attributen en waarden zijn toegestaan.

4. **Gelaagde opbouw** – StUF-schema's zijn modulair opgebouwd in lagen (basisschema → sectormodel → koppelvlak), waarbij elke laag voortbouwt op de vorige. Dit wordt mogelijk gemaakt door XSD-mechanismes als `import` en type-overerving (die je leert in secties 2.4 en 2.5).

## Het validatieproces in de praktijk

Het valideren van een XML-document tegen een XSD verloopt als volgt:

```
XML-document  ─┐
                ├─→  XML-validator  ─→  Geldig ✓  of  Ongeldig ✗ (met foutmeldingen)
XSD-schema    ─┘
```

Een **XML-validator** is een programma (of onderdeel van een programma) dat beide bestanden inleest en controleert of het XML-document voldoet aan alle regels in het schema. Voorbeelden van validators:

- **Ingebouwd in programmeertalen** – Java, C#, Python en andere talen hebben standaardbibliotheken voor XML-validatie
- **XML-editors** – Tools als Oxygen XML Editor, XMLSpy of Visual Studio Code (met plugins) kunnen valideren
- **SoapUI** – Een veelgebruikt tool voor het testen van SOAP-webservices, inclusief StUF
- **Commandoregeltools** – Zoals `xmllint` op Linux

## Hoe koppel je een XML-document aan een schema?

Er zijn twee manieren om aan te geven welk schema bij een XML-document hoort:

### Methode 1: In het XML-document zelf

Met de attributen `xsi:schemaLocation` of `xsi:noNamespaceSchemaLocation`:

```xml
<persoon xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="persoon.xsd">
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <geboortedatum>1985-03-15</geboortedatum>
</persoon>
```

### Methode 2: Bij de validator

Je geeft het schema mee als parameter aan de validator. Dit is flexibeler en wordt bij StUF vaak gebruikt – het ontvangstsysteem weet welk schema hoort bij welk berichttype.

> De details van `xsi:schemaLocation` komen uitgebreid aan bod in sectie 2.5.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **Schema** | Een formele beschrijving van de toegestane structuur en waarden van een XML-document |
| **XSD** | XML Schema Definition – de standaardtaal voor het schrijven van schema's |
| **Validatie** | Het automatisch controleren of een XML-document voldoet aan een schema |
| **Welgevormd** | Voldoet aan XML-syntaxregels (noodzakelijke eerste stap) |
| **Geldig** | Voldoet aan een specifiek schema (de tweede stap) |
| **DTD** | De oudere, beperktere voorganger van XSD |

> **Kernpunt:** XSD is een taal (zelf ook XML) waarmee je exact vastlegt hoe een XML-document eruit moet zien: welke elementen en attributen zijn toegestaan, in welke volgorde, en met welke waarden. Door validatie kun je automatisch controleren of een document aan die regels voldoet. StUF is volledig gedefinieerd in XSD-schema's – het begrijpen van XSD is daarom essentieel voor het werken met StUF.
