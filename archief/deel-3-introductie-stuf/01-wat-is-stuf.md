---
title: "3.1 Wat is StUF?"
weight: 1
---

# 3.1 Wat is StUF?

In Deel 1 en 2 heb je geleerd hoe XML en XSD werken. Nu gaan we die kennis toepassen op **StUF**: de standaard die al meer dan twee decennia het fundament vormt voor gegevensuitwisseling tussen gemeentelijke systemen in Nederland. In dit eerste hoofdstuk leer je wat StUF is, waarom het bestaat, en welke rol het speelt in het gemeentelijk ICT-landschap.

## De naam: Standaard Uitwisseling Formaat

**StUF** staat voor **St**andaard **U**itwisseling **F**ormaat. De naam zegt precies wat het is:

- **Standaard** – het is een afspraak die voor iedereen geldt
- **Uitwisseling** – het gaat om het versturen en ontvangen van gegevens
- **Formaat** – het beschrijft de structuur en het formaat van berichten

In de kern is StUF een set **afspraken** over hoe XML-berichten eruit moeten zien wanneer gemeentelijke applicaties gegevens met elkaar uitwisselen. Het is geen softwarepakket, geen server, geen protocol – het is een **berichtenstandaard**.

### Een analogie

Stel: alle gemeenten in Nederland moeten persoonsinformatie uitwisselen. Zonder standaard zou elke gemeente en elke softwareleverancier zijn eigen berichtformaat bedenken:

| Gemeente A | Gemeente B | Leverancier C |
|---|---|---|
| `<persoon><nm>Jan</nm></persoon>` | `<burger naam="Jan"/>` | `<person><first>Jan</first></person>` |

Dit werkt niet. Elk systeem dat met een ander systeem wil communiceren, zou een aparte vertaalslag nodig hebben. Bij 350+ gemeenten en tientallen leveranciers wordt dat onbeheersbaar.

StUF lost dit op door **één formaat** af te spreken:

```xml
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <geboortedatum>19850315</geboortedatum>
</persoon>
```

Iedereen die StUF "spreekt", begrijpt dit bericht. Dat is de kracht van standaardisatie.

## Waarom bestaat StUF?

### Het probleem: een wirwar aan koppelingen

Nederlandse gemeenten gebruiken tientallen applicaties voor verschillende taken: burgerzaken, belastingen, vergunningen, sociale zaken, ruimtelijke ordening, en nog veel meer. Al die applicaties moeten gegevens met elkaar delen:

- Het burgerzaken-systeem moet persoonsgegevens doorgeven aan het belastingsysteem
- Het vergunningensysteem moet adresgegevens ophalen uit de basisregistratie
- Het zaaksysteem moet meldingen doorgeven aan het archiefsysteem

Zonder standaard ontstaat dit patroon:

```
Systeem A ←→ eigen koppeling ←→ Systeem B
Systeem A ←→ eigen koppeling ←→ Systeem C
Systeem B ←→ eigen koppeling ←→ Systeem C
Systeem B ←→ eigen koppeling ←→ Systeem D
...
```

Elke koppeling is maatwerk. Elke koppeling moet apart onderhouden worden. Bij $n$ systemen heb je potentieel $\frac{n(n-1)}{2}$ koppelingen nodig – dat groeit kwadratisch.

### De oplossing: één gemeenschappelijke taal

Met StUF spreken alle systemen dezelfde taal:

```
Systeem A ──┐
Systeem B ──┤── StUF-berichten ──┤── Systeem E
Systeem C ──┤                    ├── Systeem F
Systeem D ──┘                    └── Systeem G
```

Elk systeem hoeft maar **één formaat** te ondersteunen. Een leverancier die StUF implementeert, kan communiceren met álle andere StUF-systemen.

## Oorsprong en geschiedenis

### Tijdlijn

| Periode | Gebeurtenis |
|---|---|
| Eind jaren '90 | Gemeenten worstelen met integratie van applicaties. De behoefte aan standaardisatie groeit. |
| 2001 | De eerste versie van StUF wordt ontwikkeld onder regie van EGEM (Elektronische Gemeenten). |
| 2004 | StUF 2.04 wordt breed geadopteerd door gemeenten en leveranciers. |
| 2007 | StUF 3.01 verschijnt – een ingrijpende herziening met betere XSD-ondersteuning. |
| 2012 | StUF 3.02 – verfijningen en uitbreidingen op 3.01. |
| 2014–heden | Het beheer verschuift naar VNG Realisatie (onderdeel van de VNG – Vereniging van Nederlandse Gemeenten). |
| 2019–heden | Naast StUF worden ook API-gebaseerde standaarden ontwikkeld (zoals de Haal Centraal API's). StUF blijft in gebruik voor bestaande koppelingen. |

### De belangrijkste versies

| Versie | Kenmerken |
|---|---|
| **StUF 2.04** | Vroege versie, eenvoudiger opzet, nog steeds in gebruik bij sommige legacy-koppelingen |
| **StUF 3.01** | Grote herziening: sterker gebruik van XSD, betere ondersteuning voor mutatieberichten, geneste structuren |
| **StUF 3.02** | Verfijning van 3.01 met aanvullingen, huidige gangbare versie |

> In deze cursus richten we ons op **StUF 3.01/3.02**, aangezien dit de versies zijn die je in de praktijk het meest tegenkomt.

### Beheer: VNG Realisatie

StUF wordt beheerd door **VNG Realisatie**, een onderdeel van de Vereniging van Nederlandse Gemeenten (VNG). VNG Realisatie:

- Beheert en publiceert de StUF-standaard en sectormodellen
- Beheert de bijbehorende XSD-schema's
- Ondersteunt gemeenten en leveranciers bij implementatie
- Organiseert werkgroepen en expertgroepen
- Publiceert documentatie op [gemmaonline.nl](https://www.gemmaonline.nl/)

## Wat regelt StUF precies?

StUF is meer dan "een XML-formaat". Het beschrijft:

### 1. De structuur van berichten

Hoe ziet een bericht eruit? Welke elementen bevat het? In welke volgorde? StUF definieert dit tot in detail via XSD-schema's.

### 2. De typen berichten

Welke soorten berichten bestaan er? Er zijn berichten om gegevens op te vragen, om wijzigingen door te geven, om fouten te melden, en meer. Elk type heeft zijn eigen structuur en regels.

### 3. De inhoud: entiteiten en attributen

Welke gegevens worden uitgewisseld? StUF definieert entiteiten zoals **Natuurlijk Persoon**, **Adres**, **Zaak**, **Document**, etc. – inclusief hun attributen en onderlinge relaties.

### 4. De metadata

Elk bericht bevat metadata: wie is de zender? Wie is de ontvanger? Wanneer is het bericht verstuurd? Wat voor soort mutatie betreft het? Deze metadata maakt berichten traceerbaar en verwerkbaar.

### 5. De spelregels

StUF beschrijft ook de regels rondom berichtverkeer: hoe reageer je op een fout? Hoe bevestig je ontvangst? Hoe ga je om met gelijktijdige wijzigingen?

## Core-schema en sectormodellen

StUF heeft een **gelaagde structuur**:

```
┌─────────────────────────────────┐
│  Koppelvlakspecificatie         │  Bijv. "StUF-BG: opvragenPersoon"
│  (specifiek bericht/dienst)     │
├─────────────────────────────────┤
│  Sectormodel                    │  Bijv. StUF-BG, StUF-ZKN, StUF-EF
│  (domein-specifieke schema's)   │
├─────────────────────────────────┤
│  StUF-kern (stuf0302.xsd)       │  Basistypen, stuurgegevens, 
│  (basis voor alles)             │  berichtstructuur
└─────────────────────────────────┘
```

| Laag | Wat bevat het? | Voorbeeld |
|---|---|---|
| **StUF-kern** | Generieke berichtstructuur, stuurgegevens, basistypen, mutatiesoorten | `stuf0302.xsd` |
| **Sectormodel** | Domein-specifieke entiteiten en berichten | StUF-BG (personen, adressen) |
| **Koppelvlakspecificatie** | Concrete beschrijving van een dienst/koppelvlak | "Opvragen persoonsgegevens via StUF-BG" |

We gaan hier in latere hoofdstukken dieper op in.

## Relatie met het Stelsel van Basisregistraties

Nederland kent een **Stelsel van Basisregistraties**: een verzameling registraties die als gezaghebbende bron gelden voor bepaalde gegevens:

| Basisregistratie | Afkorting | Gegevens |
|---|---|---|
| Basisregistratie Personen | BRP | Persoonsgegevens (naam, geboortedatum, adres, etc.) |
| Basisregistratie Adressen en Gebouwen | BAG | Adressen, panden, verblijfsobjecten |
| Basisregistratie Kadaster | BRK | Eigendom, percelen |
| Basisregistratie Handelsregister | HR/NHR | Bedrijven, rechtspersonen |
| Basisregistratie Voertuigen | BRV | Kentekenregistratie |
| Basisregistratie Waardering Onroerende Zaken | WOZ | WOZ-waarden |

Het stelsel is gebaseerd op het principe: **eenmalig vastleggen, meervoudig gebruiken**. Gegevens worden bij de bron bijgehouden en door andere organisaties hergebruikt.

StUF speelt hierin een belangrijke rol:

- **StUF-BG** (Basisgegevens) is het sectormodel dat zich richt op het uitwisselen van gegevens uit basisregistraties – met name BRP- en BAG-gegevens
- Gemeentelijke applicaties ontvangen mutaties uit de BRP via StUF-berichten
- Adresgegevens uit de BAG worden via StUF-berichten gesynchroniseerd

> Het RSGB (Referentiemodel Stelsel van Gemeentelijke Basisgegevens) beschrijft het informatiemodel van de gegevens uit basisregistraties, en StUF-BG is de berichtenstandaard die dit informatiemodel implementeert.

## StUF in het dagelijks werk

Wanneer je in de praktijk met StUF werkt, doe je typisch dit soort taken:

| Taak | Wat je doet |
|---|---|
| **Schema's lezen** | XSD-bestanden openen en begrijpen welke elementen en typen worden gebruikt |
| **Berichten analyseren** | Concrete XML-berichten (bijv. uit logfiles) lezen en interpreteren |
| **Koppelvlakken specificeren** | Beschrijven welke berichten een systeem moet kunnen versturen/ontvangen |
| **Valideren** | Controleren of een bericht voldoet aan het StUF-schema |
| **Problemen oplossen** | Fouten in koppelingen debuggen (verkeerde namespace, ontbrekend element, etc.) |
| **Impact analyseren** | Bepalen wat er moet veranderen als het informatiemodel wijzigt |

De kennis van XML en XSD uit Deel 1 en 2 is hierbij onmisbaar – StUF is gebouwd op die technologieën.

## StUF en de toekomst

StUF is een bewezen standaard die al meer dan 20 jaar in gebruik is. Tegelijkertijd beweegt de wereld richting **API's** (REST, JSON):

| Kenmerk | StUF (klassiek) | Moderne API's |
|---|---|---|
| Formaat | XML | JSON (vaak) |
| Protocol | SOAP over HTTP | REST over HTTP |
| Beschrijving | XSD/WSDL | OpenAPI/JSON Schema |
| Koppelingsstijl | Berichtenverkeer (push) | Bevraging (pull) |
| Complexiteit | Hoog (rijke standaard) | Lager (eenvoudiger opzet) |

VNG Realisatie ontwikkelt inmiddels ook API-standaarden (zoals de **Haal Centraal API's** voor BRP-bevraging). Maar StUF verdwijnt niet op korte termijn:

- Honderden bestaande koppelingen draaien op StUF
- Niet alle domeinen hebben al een API-alternatief
- Migratie van StUF naar API's is een geleidelijk proces
- Kennis van StUF blijft nodig voor beheer, onderhoud en transitie

> **Kernpunt:** StUF is de berichtenstandaard voor gegevensuitwisseling tussen gemeentelijke applicaties. Het is gebouwd op XML/XSD, wordt beheerd door VNG Realisatie, en speelt een centrale rol in het Stelsel van Basisregistraties. Hoewel de wereld richting API's beweegt, blijft StUF-kennis essentieel voor iedereen die werkt met gemeentelijke informatievoorziening.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **StUF** | Standaard Uitwisseling Formaat – berichtenstandaard voor gemeentelijke gegevensuitwisseling |
| **Doel** | Uniforme communicatie tussen gemeentelijke applicaties |
| **Technologie** | Gebouwd op XML en XSD |
| **Beheerder** | VNG Realisatie (onderdeel van VNG) |
| **Versies** | 2.04 (legacy), 3.01, 3.02 (actueel) |
| **Gelaagdheid** | Kern-schema → sectormodel → koppelvlakspecificatie |
| **Stelsel** | Sluit aan op Stelsel van Basisregistraties (BRP, BAG, etc.) |
| **Toekomst** | Naast StUF worden API-standaarden ontwikkeld; StUF blijft voorlopig in gebruik |
