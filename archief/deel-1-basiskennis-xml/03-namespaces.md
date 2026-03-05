---
title: "1.3 Namespaces"
weight: 3
---

# 1.3 Namespaces

In de vorige secties heb je geleerd hoe je met XML gegevens structureert en welke syntaxregels daarbij gelden. Een krachtige eigenschap van XML is dat je je eigen tagnamen mag bedenken. Maar wat als twee partijen toevallig dezelfde naam kiezen voor iets heel anders? Daar zijn **namespaces** voor.

## Het probleem: naamconflicten

Stel je voor: een gemeente gebruikt twee systemen die allebei XML-berichten versturen. Het ene systeem gaat over **personen** en heeft een element `<naam>`:

```xml
<naam>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
</naam>
```

Het andere systeem gaat over **straten** en heeft ook een element `<naam>`:

```xml
<naam>Kerkstraat</naam>
```

Wanneer je deze gegevens in één XML-document wilt combineren, ontstaat er een probleem:

```xml
<registratie>
  <naam>
    <voornaam>Jan</voornaam>
    <achternaam>de Vries</achternaam>
  </naam>
  <naam>Kerkstraat</naam>
</registratie>
```

Beide elementen heten `<naam>`, maar ze betekenen iets compleet anders. Een computer kan niet zien welke `<naam>` bij het persoonsdomein hoort en welke bij het stratendomein. Voor een mens is het misschien duidelijk uit de context, maar een programma heeft die context niet.

Dit probleem wordt alleen maar groter wanneer je – zoals bij StUF – berichten samengesteld uit meerdere standaarden: een basisstandaard, een sectormodel en een koppelvlakdefinitie, elk met hun eigen elementen.

## De oplossing: namespaces

Een **namespace** (naamruimte) is een label dat je aan elementen en attributen toekent om aan te geven bij welk "domein" ze horen. Hierdoor kunnen twee elementen dezelfde naam hebben, maar dankzij hun verschillende namespace toch ondubbelzinnig onderscheiden worden.

Een namespace wordt geïdentificeerd door een **URI** (Uniform Resource Identifier). Dit is meestal een URL-achtige tekst, maar het hoeft *geen* echte webpagina te zijn – het is puur een unieke naamstring.

Voorbeelden van namespace-URI's:
- `http://www.example.nl/personen`
- `http://www.example.nl/straten`
- `http://www.egem.nl/StUF/StUF0301` *(de echte StUF-namespace)*

> **Belangrijk:** Een namespace-URI is géén webadres dat je kunt bezoeken. Het is een afspraak – een unieke tekst die twee partijen gebruiken om hetzelfde domein mee aan te duiden. Dat het eruitziet als een URL is puur conventie.

## Namespace-declaratie met `xmlns`

Om een namespace te gebruiken in een XML-document, moet je deze **declareren**. Dat doe je met het speciale attribuut `xmlns` (wat staat voor "XML Namespace").

Er zijn twee manieren om een namespace te declareren:

### Methode 1: Default namespace

De eenvoudigste vorm. Je verklaart een namespace als **standaard** (default) voor een element en al zijn nakomelingen:

```xml
<naam xmlns="http://www.example.nl/personen">
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
</naam>
```

Hier geldt:
- `xmlns="http://www.example.nl/personen"` zegt: *"Dit element en alles erin hoort bij de namespace `http://www.example.nl/personen`"*
- De elementen `<voornaam>` en `<achternaam>` erven deze namespace automatisch
- Er is geen prefix nodig – vandaar "default"

### Methode 2: Prefixed namespace

Als je meerdere namespaces in hetzelfde document wilt gebruiken, geef je elke namespace een korte **prefix** – een afkorting die je vóór de elementnaam plaatst, gescheiden door een dubbele punt (`:`):

```xml
<registratie xmlns:per="http://www.example.nl/personen"
             xmlns:str="http://www.example.nl/straten">
  <per:naam>
    <per:voornaam>Jan</per:voornaam>
    <per:achternaam>de Vries</per:achternaam>
  </per:naam>
  <str:naam>Kerkstraat</str:naam>
</registratie>
```

Laten we dit stap voor stap ontleden:

| Onderdeel | Betekenis |
|---|---|
| `xmlns:per="http://www.example.nl/personen"` | Declareer prefix `per` als afkorting voor de personen-namespace |
| `xmlns:str="http://www.example.nl/straten"` | Declareer prefix `str` als afkorting voor de straten-namespace |
| `<per:naam>` | Het element `naam` in de personen-namespace |
| `<str:naam>` | Het element `naam` in de straten-namespace |

Nu is het voor een computer volkomen helder: `<per:naam>` en `<str:naam>` zijn **twee verschillende elementen**, ook al is de lokale naam (`naam`) hetzelfde. Het verschil zit in de namespace.

### De prefix is willkeurig

Een veelvoorkomend misverstand: de prefix zelf heeft **geen vaste betekenis**. Het is slechts een korte verwijzing naar de namespace-URI. De volgende twee fragmenten zijn volledig gelijkwaardig:

```xml
<per:naam xmlns:per="http://www.example.nl/personen">Jan</per:naam>
```

```xml
<p:naam xmlns:p="http://www.example.nl/personen">Jan</p:naam>
```

```xml
<xyz:naam xmlns:xyz="http://www.example.nl/personen">Jan</xyz:naam>
```

In alle drie de gevallen hoort het element `naam` bij de namespace `http://www.example.nl/personen`. De prefix (`per`, `p`, `xyz`) is slechts een lokale afkorting. Wat telt is de URI waarnaar de prefix verwijst.

> **Let op:** Hoewel de prefix willekeurig is, kiezen standaarden zoals StUF vaste prefixen om de leesbaarheid te bevorderen. Zo wordt voor StUF-elementen conventioneel de prefix `StUF` of `stuf` gebruikt.

## Default namespace vs. prefixed namespace

Wanneer gebruik je welke methode? Hier een overzicht:

| Situatie | Aanbevolen methode |
|---|---|
| Alle elementen horen bij **één** namespace | Default namespace (`xmlns="..."`) |
| Elementen uit **meerdere** namespaces in één document | Prefixed namespaces (`xmlns:prefix="..."`) |
| Eén dominante namespace met incidenteel een andere | Default voor de dominante, prefix voor de rest |

### Combineren van default en prefixed

In de praktijk worden beide methoden vaak gecombineerd. Dit is zeer gebruikelijk in StUF-berichten:

```xml
<kennisgeving xmlns="http://www.egem.nl/StUF/StUF0301"
              xmlns:bg="http://www.egem.nl/StUF/sector/bg/0310"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <stuurgegevens>
    <zender>BRP-systeem</zender>
  </stuurgegevens>
  <bg:natuurlijkPersoon>
    <bg:voornamen>Jan</bg:voornamen>
    <bg:geslachtsnaam>Vries</bg:geslachtsnaam>
  </bg:natuurlijkPersoon>
</kennisgeving>
```

Hier is:
- De **default namespace** (`xmlns="..."`) van toepassing op `<kennisgeving>` en `<stuurgegevens>` – deze horen bij de StUF-basisstandaard
- De **prefix `bg`** wordt gebruikt voor elementen uit het sectormodel Basisgegevens
- De **prefix `xsi`** verwijst naar de XML Schema Instance-namespace (een standaard van W3C)

## Scope: waar geldt een namespace-declaratie?

Een namespace-declaratie geldt voor het element waarop het staat en **alle elementen daarbinnen** (de nakomelingen). Dit heet de **scope** van de declaratie.

```xml
<bericht>
  <deel1 xmlns:a="http://www.example.nl/alpha">
    <a:item>Dit element is in de alpha-namespace</a:item>
  </deel1>
  <deel2>
    <a:item>FOUT: prefix 'a' is hier niet meer gedeclareerd!</a:item>
  </deel2>
</bericht>
```

In dit voorbeeld is de prefix `a` alleen geldig binnen `<deel1>`, want daar is de declaratie gedaan. In `<deel2>` is `a:item` ongeldig – het document is niet welgevormd.

**Praktische vuistregel:** declareer namespaces op het root-element, dan zijn ze overal in het document beschikbaar:

```xml
<bericht xmlns:a="http://www.example.nl/alpha"
         xmlns:b="http://www.example.nl/beta">
  <deel1>
    <a:item>Geldig</a:item>
  </deel1>
  <deel2>
    <b:item>Ook geldig</b:item>
  </deel2>
</bericht>
```

## Namespaces en attributen

Attributen gedragen zich iets anders dan elementen bij namespaces:

- Een attribuut **zonder prefix** hoort bij **geen** namespace (niet bij de default namespace!)
- Een attribuut **met prefix** hoort bij de namespace van die prefix

```xml
<persoon xmlns="http://www.example.nl/personen"
         xmlns:stuf="http://www.egem.nl/StUF/StUF0301">
  <naam stuf:sleutelVeld="true">Jan de Vries</naam>
</persoon>
```

Hier geldt:
- `<persoon>` en `<naam>` zitten in de default namespace `http://www.example.nl/personen`
- Het attribuut `stuf:sleutelVeld` zit in de StUF-namespace
- Als er een attribuut `type="..."` zou staan (zonder prefix), hoort dat bij géén namespace

> **Dit is een subtiliteit die vaak verwarring veroorzaakt.** De regel is: default namespaces gelden alleen voor elementen, niet voor attributen. Attributen moeten een prefix hebben om in een namespace te zitten.

## Veelgebruikte standaard-namespaces

Sommige namespaces kom je in vrijwel elk XML-document tegen:

| Prefix | Namespace-URI | Betekenis |
|---|---|---|
| `xs` of `xsd` | `http://www.w3.org/2001/XMLSchema` | XML Schema-definities (typen, elementen) |
| `xsi` | `http://www.w3.org/2001/XMLSchema-instance` | Schema-gerelateerde attributen in XML-documenten |
| `xml` | `http://www.w3.org/XML/1998/namespace` | Ingebouwde XML-namespace (bijv. `xml:lang`) |
| `soap` | `http://schemas.xmlsoap.org/soap/envelope/` | SOAP-envelopstructuur |

In de context van StUF komen daar specifieke namespaces bij, zoals:

| Prefix (conventie) | Namespace-URI | Betekenis |
|---|---|---|
| `StUF` | `http://www.egem.nl/StUF/StUF0301` | De StUF 03.01 basisstandaard |
| `bg` | `http://www.egem.nl/StUF/sector/bg/0310` | StUF-BG (Basisgegevens) |
| `zkn` | `http://www.egem.nl/StUF/sector/zkn/0310` | StUF-ZKN (Zaken) |

Je hoeft deze niet uit je hoofd te kennen – in Deel 3 komen ze uitgebreid aan bod. Maar het is goed om alvast te weten dat StUF zwaar leunt op namespaces.

## Belang van namespaces voor StUF

StUF-berichten zijn opgebouwd uit **meerdere lagen**, elk met een eigen namespace:

1. **SOAP-laag** – de envelopstructuur voor webservices (`soap:Envelope`, `soap:Body`)
2. **StUF-basislaag** – stuurgegevens en berichtstructuur (`StUF:stuurgegevens`, etc.)
3. **Sectormodel-laag** – de daadwerkelijke gegevens (`bg:natuurlijkPersoon`, `zkn:zaak`, etc.)
4. **Koppelvlak-laag** – specifieke berichtdefinities per koppelvlak

Elke laag heeft een eigen namespace, en door prefixen kun je in één bericht naadloos elementen uit al deze lagen combineren. Zonder namespaces zou dit onmogelijk zijn – er zouden overal naamconflicten ontstaan.

Een vereenvoudigd voorbeeld van hoe deze lagen samen komen:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
               xmlns:stuf="http://www.egem.nl/StUF/StUF0301"
               xmlns:bg="http://www.egem.nl/StUF/sector/bg/0310">
  <soap:Body>
    <bg:npsLk01>
      <stuf:stuurgegevens>
        <stuf:berichtcode>Lk01</stuf:berichtcode>
        <stuf:zender>
          <stuf:applicatie>BRP</stuf:applicatie>
        </stuf:zender>
      </stuf:stuurgegevens>
      <bg:object>
        <bg:voornamen>Jan</bg:voornamen>
        <bg:geslachtsnaam>Vries</bg:geslachtsnaam>
      </bg:object>
    </bg:npsLk01>
  </soap:Body>
</soap:Envelope>
```

Je hoeft dit voorbeeld nu nog niet volledig te begrijpen – dat komt in Deel 3. Het belangrijke inzicht is: **dankzij namespaces en prefixen is voor elk element ondubbelzinnig duidelijk bij welke standaard het hoort**, ook al staan ze allemaal in hetzelfde XML-document.

## Veelgemaakte fouten met namespaces

| Fout | Uitleg |
|---|---|
| Prefix gebruiken zonder declaratie | `<stuf:element>` schrijven zonder ergens `xmlns:stuf="..."` te hebben gedeclareerd |
| Aannemen dat de prefix de namespace *is* | De prefix is een afkorting; de URI is de echte identifier |
| Default namespace vergeten bij genest element | Als een kind-element in een andere namespace moet zitten, moet je dat expliciet aangeven |
| Namespace-URI verkeerd overnemen | Eén teken verschil in de URI = een andere namespace. Let op exacte schrijfwijze |
| Vergeten dat attributen geen default namespace erven | Attributen zonder prefix zitten in geen namespace |

## Samenvatting

| Concept | Uitleg |
|---|---|
| **Namespace** | Een uniek label (URI) dat aangeeft bij welk domein een element hoort |
| **Namespace-URI** | De identificerende tekst, bijv. `http://www.egem.nl/StUF/StUF0301` |
| **Prefix** | Een korte afkorting voor een namespace, bijv. `stuf:` of `bg:` |
| **Default namespace** | Gedeclareerd met `xmlns="..."`, geldt voor het element en nakomelingen |
| **Prefixed namespace** | Gedeclareerd met `xmlns:prefix="..."`, gebruikt als `prefix:element` |
| **Scope** | Een namespace-declaratie geldt voor het element en alles erin |

> **Kernpunt:** Namespaces zijn XML's manier om naamconflicten op te lossen wanneer elementen uit verschillende standaarden in één document samenkomen. Ze zijn essentieel voor StUF, dat berichten opbouwt uit meerdere lagen (SOAP, StUF-basis, sectormodel) die elk hun eigen namespace hebben. Een namespace wordt geïdentificeerd door een URI en in het document aangeduid met een prefix of als default.
