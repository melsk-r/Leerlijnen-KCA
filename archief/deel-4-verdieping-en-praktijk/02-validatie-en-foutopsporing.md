---
title: "4.2 Validatie en foutopsporing"
weight: 2
---

# 4.2 Validatie en foutopsporing

In de praktijk besteed je als informatieanalist of gegevensarchitect een significant deel van je tijd aan het **oplossen van problemen** met StUF-berichten. Een bericht dat niet geaccepteerd wordt, een koppeling die foutmeldingen geeft, een schema-validatiefout – dit zijn dagelijkse scenario's. In dit hoofdstuk leer je hoe je StUF-berichten valideert, welke fouten je het vaakst tegenkomt, en welke tools je daarbij helpen.

## Wat is validatie?

**Validatie** is het controleren of een XML-document voldoet aan de regels van een XSD-schema. Je hebt dit concept in Deel 2 (sectie 2.1) geleerd. Bij StUF betekent dit concreet:

| Niveau | Wat wordt gecontroleerd? |
|---|---|
| **Well-formed** | Is het geldige XML? (tags gesloten, nesting correct, etc.) |
| **Schema-geldig** | Voldoet het aan het StUF-XSD-schema? (juiste elementen, typen, volgorde) |
| **Inhoudelijk geldig** | Kloppen de gegevens inhoudelijk? (geldig BSN, bestaand adres, etc.) |

XSD-validatie dekt de eerste twee niveaus. Inhoudelijke validatie is applicatielogica en valt buiten het schema.

## Validatie met de command line

### `xmllint` – snel valideren

Het commando `xmllint` (onderdeel van `libxml2`) is de snelste manier om een bericht te valideren:

```bash
# Alleen well-formedness controleren
xmllint --noout bericht.xml

# Valideren tegen een schema
xmllint --schema bg0310_msg_kennisgeving.xsd bericht.xml --noout
```

Succesvol:
```
bericht.xml validates
```

Mislukt:
```
bericht.xml:23: element geslachtsnaam: Schemas validity error :
  Element '{http://www.stufstandaarden.nl/sector/bg/0310}geslachtsnaam':
  This element is not expected. Expected is
  ( {http://www.stufstandaarden.nl/sector/bg/0310}inp.bsn ).
bericht.xml fails to validate
```

### De foutmelding ontleden

Laten we die foutmelding stap voor stap lezen:

| Onderdeel | Betekenis |
|---|---|
| `bericht.xml:23` | De fout zit op regel 23 van het bestand |
| `element geslachtsnaam` | Het element `geslachtsnaam` veroorzaakt de fout |
| `This element is not expected` | Het element mag hier niet staan |
| `Expected is ( inp.bsn )` | Op deze positie verwacht het schema `inp.bsn` |

**Diagnose:** de elementen staan in de verkeerde **volgorde**. Het schema gebruikt `xs:sequence`, dus de volgorde is belangrijk. `inp.bsn` moet vóór `geslachtsnaam` komen.

## Veelvoorkomende validatiefouten

Hier zijn de fouten die je in de praktijk het meest tegenkomt, gerangschikt van meest naar minst frequent:

### 1. Verkeerde volgorde van elementen

**Foutmelding:**
```
Element 'X': This element is not expected. Expected is ( Y ).
```

**Oorzaak:** StUF-schema's gebruiken `xs:sequence` – de volgorde is vast.

**Oplossing:** Controleer de volgorde in het schema en pas het bericht aan. De volgorde in het bericht moet **exact** overeenkomen met de volgorde in het schema.

**Voorbeeld:**
```xml
<!-- FOUT: achternaam voor voornamen -->
<BG:geslachtsnaam>Vries</BG:geslachtsnaam>
<BG:voornamen>Jan</BG:voornamen>

<!-- GOED: volgorde volgens schema -->
<BG:voornamen>Jan</BG:voornamen>
<BG:geslachtsnaam>Vries</BG:geslachtsnaam>
```

### 2. Namespace-fouten

**Foutmelding:**
```
Element '{http://www.stufstandaarden.nl/sector/bg/0310}npsLk01':
  No matching global declaration available for the validation root.
```

Of:
```
Element 'geslachtsnaam': No matching global element declaration available.
```

**Oorzaak:** Het element zit in de verkeerde namespace, of de namespace-declaratie ontbreekt.

**Veelvoorkomende scenario's:**

| Fout | Probleem | Oplossing |
|---|---|---|
| Geen prefix op element | Element zit in "geen namespace" i.p.v. de StUF-namespace | Prefix toevoegen of default namespace declareren |
| Verkeerde prefix | Element verwijst naar verkeerde namespace | `xmlns`-declaratie controleren |
| Typfout in namespace-URI | `http://www.egem.nl/StUF/stuf0302` i.p.v. `StUF0302` (hoofdletter!) | Exact overnemen uit schema |

**Voorbeeld:**
```xml
<!-- FOUT: geen namespace op geslachtsnaam -->
<BG:object StUF:entiteittype="NPS">
  <geslachtsnaam>Vries</geslachtsnaam>    <!-- zit in "geen namespace" -->
</BG:object>

<!-- GOED: met BG-prefix -->
<BG:object StUF:entiteittype="NPS">
  <BG:geslachtsnaam>Vries</BG:geslachtsnaam>
</BG:object>
```

### 3. Ontbrekend verplicht element

**Foutmelding:**
```
Element 'object': Missing child element(s).
  Expected is ( {http://www.egem.nl/StUF/StUF0302}stuurgegevens ).
```

**Oorzaak:** Een element dat verplicht is (`minOccurs="1"` of niet gespecificeerd) ontbreekt in het bericht.

**Oplossing:** Voeg het ontbrekende element toe op de juiste positie.

### 4. Ongeldig datatype

**Foutmelding:**
```
Element 'geboortedatum': 'niet-bekend' is not a valid value of the atomic type
  'Datum-e'.
```

**Oorzaak:** De waarde voldoet niet aan het datatype. Bijvoorbeeld tekst waar een datum verwacht wordt, of een BSN met letters.

**Veelvoorkomende gevallen:**

| Veld | Verwacht | Fout voorbeeld |
|---|---|---|
| Geboortedatum | `jjjjMMdd` (bijv. `19850315`) | `15-03-1985`, `1985-03-15` |
| BSN | 9 cijfers (bijv. `123456789`) | `12345678` (8 cijfers), `12345678A` |
| Geslacht | `M`, `V`, of `O` | `Man`, `m`, `male` |
| Tijdstip | `jjjjMMddHHmmss` | `2024-01-15T10:30:00` |

### 5. Element niet verwacht (bestaat niet in schema)

**Foutmelding:**
```
Element 'emailadres': This element is not expected.
```

**Oorzaak:** Het element bestaat niet in het schema op die positie. Mogelijke redenen:
- Typfout in de elementnaam
- Het element hoort bij een ander type of een andere entiteit
- Het element bestaat alleen in een nieuwere versie van het schema

### 6. `nillable`-fouten

**Foutmelding:**
```
Element 'geslachtsnaam': The element is not nillable.
```

**Oorzaak:** Je gebruikt `xsi:nil="true"` op een element dat niet als `nillable="true"` is gedefinieerd in het schema.

**Oplossing:** Controleer in het schema of het element `nillable="true"` heeft. Zo niet, dan kun je het element weglaten (als het optioneel is) in plaats van nil te maken.

### 7. SOAP-envelop problemen

**Foutmelding (van de webservice, niet van XSD):**
```
Could not parse SOAP message
```

**Oorzaak:** De SOAP-envelop is ongeldig:
- Ontbrekende `<soap:Envelope>` of `<soap:Body>`
- Verkeerde SOAP-namespace (bijv. SOAP 1.2 i.p.v. 1.1)
- Niet-well-formed XML binnen de envelop

## Debuggen van StUF-koppelingen

Wanneer een koppeling in productie problemen geeft, doorloop je deze diagnoseaanpak:

### Stap 1: Verkrijg het werkelijke bericht

Haal het bericht op dat verstuurd of ontvangen is. Bronnen:

| Bron | Hoe? |
|---|---|
| Applicatielogfiles | Zoek in de logs van het zendende/ontvangende systeem |
| ESB/Messagebroker | Bekijk de berichtenqueue of het berichtenlog |
| SoapUI / Postman | Onderschep het bericht via een testtool |
| Netwerkverkeer | Tcpdump of Wireshark (als HTTPS: na TLS-decryptie) |

### Stap 2: Controleer well-formedness

```bash
xmllint --noout bericht.xml
```

Als dit al faalt, is het XML zelf kapot (bijv. niet-gesloten tags, encoding-problemen).

### Stap 3: Valideer tegen het schema

```bash
xmllint --schema bg0310_msg_kennisgeving.xsd bericht.xml --noout
```

Noteer alle gemelde fouten.

### Stap 4: Controleer de stuurgegevens

Ga handmatig na:
- Klopt de berichtcode met het berichtelement?
- Zijn zender en ontvanger correct?
- Is het referentienummer uniek?
- Is het tijdstip in het juiste formaat?

### Stap 5: Controleer de inhoud

Ga de gegevens na:
- Bestaan de entiteiten (BSN geldig, adres-ID correct)?
- Zijn de mutatiesoort en het oud/nieuw-patroon consistent?
- Worden tijdvakken correct gebruikt?

### Stap 6: Vergelijk met een werkend bericht

Als je een bericht hebt dat wél werkt, gebruik `diff` om de verschillen te vinden:

```bash
diff werkend-bericht.xml probleem-bericht.xml
```

Of visueel met een XML-diff tool.

## Tooling voor StUF-werk

### XML-editors en IDE's

| Tool | Sterke punten | Prijs |
|---|---|---|
| **oXygen XML Editor** | Beste XSD-ondersteuning, schema-visualisatie, validatie, XSLT-debugging | Commercieel |
| **XMLSpy (Altova)** | Visuele schema-editor, bestandsvergelijking | Commercieel |
| **VS Code + XML-extensie** | Gratis, lichtgewicht, basisvalidatie met Red Hat XML-extensie | Gratis |
| **Notepad++** | XML-formatting, syntax highlighting | Gratis |

> **Aanbeveling:** Voor serieus StUF-werk is oXygen XML Editor de industriestandaard. Het kan schema's visueel weergeven, berichten valideren met één klik, en type-hiërarchieën navigeren.

### SoapUI

**SoapUI** is een tool specifiek voor het testen van SOAP-webservices:

| Functie | Hoe het helpt bij StUF |
|---|---|
| WSDL importeren | Laadt automatisch alle operaties en schema's |
| Requests genereren | Genereert template-berichten op basis van het schema |
| Requests versturen | Stuurt StUF-berichten naar een webservice |
| Responses inspecteren | Bekijkt het antwoord (La, Fo, Bv) |
| Validatie | Valideert berichten tegen het schema |
| Mock services | Simuleer een StUF-webservice voor tests |

**Werkwijze met SoapUI:**

1. **Nieuw project** → importeer het WSDL-bestand (bijv. `bg0310.wsdl`)
2. SoapUI genereert automatisch template-requests voor alle operaties
3. Vul de template aan met testgegevens
4. Verstuur het bericht en inspecteer het antwoord
5. Gebruik de ingebouwde validator om het antwoord te controleren

### Command-line tools

```bash
# Valideer een bericht
xmllint --schema schema.xsd bericht.xml --noout

# Formatteer XML leesbaar
xmllint --format bericht.xml > bericht-formatted.xml

# Zoek een element in een bericht
grep -n "inp.bsn" bericht.xml

# Haal een waarde op met XPath
xmllint --xpath "//BG:inp.bsn/text()" --shell bericht.xml

# Vergelijk twee berichten
diff <(xmllint --format bericht1.xml) <(xmllint --format bericht2.xml)
```

### Python voor StUF-validatie

Voor geautomatiseerde validatie kun je Python gebruiken met de `lxml`-bibliotheek:

```python
from lxml import etree

# Schema laden
with open('bg0310_msg_kennisgeving.xsd', 'r') as f:
    schema_doc = etree.parse(f)
schema = etree.XMLSchema(schema_doc)

# Bericht laden
with open('bericht.xml', 'r') as f:
    doc = etree.parse(f)

# Valideren
if schema.validate(doc):
    print("Bericht is geldig")
else:
    for error in schema.error_log:
        print(f"Regel {error.line}: {error.message}")
```

Dit kun je inbouwen in geautomatiseerde tests of CI/CD-pipelines.

## Veelgemaakte inhoudelijke fouten

Naast schema-validatiefouten zijn er **inhoudelijke fouten** die niet door het schema worden gevangen maar wel tot verwerkingsproblemen leiden:

### 1. Verkeerde mutatiesoort

| Situatie | Juiste mutatiesoort | Veelgemaakte fout |
|---|---|---|
| Nieuw persoon toevoegen | `T` | `W` (wijziging i.p.v. toevoeging) |
| Adres wijzigen | `W` | `T` (toevoeging i.p.v. wijziging) |
| Persoon verwijderen | `V` | `E` (einddatering i.p.v. verwijdering) |

### 2. Inconsistent oud/nieuw-patroon

Bij `mutatiesoort="W"`:
- **Fout:** alleen het nieuwe object meesturen, zonder het oude
- **Fout:** de sleutels van oud en nieuw object komen niet overeen
- **Fout:** de `eindGeldigheid` van het oude object komt niet overeen met de `beginGeldigheid` van het nieuwe

### 3. Ontbrekende of foutieve stuurgegevens

| Fout | Gevolg |
|---|---|
| Zender/ontvanger omgedraaid | Bericht komt bij het verkeerde systeem aan of wordt geweigerd |
| CrossRefnummer ontbreekt in antwoord | Vraag en antwoord kunnen niet gecorreleerd worden |
| Tijdstip in de toekomst | Sommige systemen weigeren berichten met een toekomstig tijdstip |
| Berichtcode komt niet overeen met berichtelement | Verwarring bij verwerking |

### 4. Encoding-problemen

| Probleem | Symptoom | Oplossing |
|---|---|---|
| UTF-8 BOM (Byte Order Mark) | Parser-fout op eerste regel | Sla bestand op als UTF-8 zonder BOM |
| Latin-1 tekens in UTF-8 document | Ongeldige characters | Zorg voor consistente encoding |
| Speciale tekens niet ge-escaped | Well-formedness fout | Gebruik `&amp;`, `&lt;`, etc. |

## Diagnostisch stroomschema

Gebruik dit stroomschema bij het debuggen van StUF-problemen:

```
Bericht verwerking mislukt
         │
         ▼
Is het well-formed XML?
    │           │
   Nee         Ja
    │           │
    ▼           ▼
Fix XML     Valideert het tegen het schema?
syntax         │           │
              Nee         Ja
               │           │
               ▼           ▼
         Fix schema-    Kloppen de stuurgegevens?
         fouten            │           │
         (volgorde,       Nee         Ja
          namespace,       │           │
          type)            ▼           ▼
                     Fix stuur-    Klopt de inhoud?
                     gegevens       │           │
                                   Nee         Ja
                                    │           │
                                    ▼           ▼
                              Fix inhoud    Probleem ligt bij
                              (mutatie-     de ontvangende
                              soort,        applicatie
                              sleutels,     (applicatielogica,
                              tijdvakken)   autorisatie, etc.)
```

## Samenvatting

| Onderwerp | Aanpak |
|---|---|
| **Schema-validatie** | `xmllint --schema` of oXygen/SoapUI voor directe feedback |
| **Volgordefouten** | Controleer `xs:sequence` in het schema; pas volgorde in bericht aan |
| **Namespace-fouten** | Controleer alle `xmlns`-declaraties; let op hoofdletters |
| **Datatype-fouten** | Controleer formaten (datum, BSN, geslacht, etc.) |
| **Inhoudelijke fouten** | Mutatiesoort, oud/nieuw-patroon, stuurgegevens, encoding |
| **Tooling** | oXygen (IDE), SoapUI (webservice-test), xmllint (CLI), Python/lxml (automatisering) |

> **Kernpunt:** Validatie en foutopsporing zijn essentiële vaardigheden voor iedereen die met StUF werkt. De systematische aanpak – well-formed? → schema-geldig? → stuurgegevens? → inhoud? – helpt je elk probleem gefocust te diagnosticeren. De juiste tooling (oXygen, SoapUI, xmllint) versnelt dit proces aanzienlijk.
