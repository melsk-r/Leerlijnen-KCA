---
title: "3.2 StUF in context"
weight: 2
---

# 3.2 StUF in context

StUF staat niet op zichzelf. Het maakt deel uit van een breder ecosysteem van standaarden, informatiemodellen en technologieën. In dit hoofdstuk leer je hoe StUF zich verhoudt tot andere gemeentelijke standaarden, welke sectormodellen er zijn, hoe StUF technisch wordt getransporteerd, en wat het verschil is tussen synchrone en asynchrone communicatie.

## Het gemeentelijk standaardenlandschap

Om StUF te begrijpen, moet je weten welke andere standaarden en modellen er in het gemeentelijk domein bestaan. Ze vullen elkaar aan als puzzelstukjes:

```
┌──────────────────────────────────────────────────────────┐
│                   GEMMA-architectuur                     │
│  (referentiearchitectuur voor gemeenten)                 │
├──────────────────┬───────────────────────────────────────┤
│                  │                                       │
│  Informatie-     │  Uitwisselingsstandaarden             │
│  modellen        │                                       │
│                  │                                       │
│  ┌──────────┐    │  ┌──────────┐  ┌──────────────────┐  │
│  │  RSGB    │    │  │  StUF    │  │  API-standaarden │  │
│  │  (basis- │    │  │  (XML/   │  │  (REST/JSON)     │  │
│  │  geg.)   │    │  │   SOAP)  │  │                  │  │
│  └──────────┘    │  └──────────┘  └──────────────────┘  │
│  ┌──────────┐    │  ┌──────────┐                        │
│  │  RGBZ    │    │  │  ZDS/ZGW │                        │
│  │  (zaken) │    │  │  (zaken- │                        │
│  └──────────┘    │  │   API's) │                        │
│  ┌──────────┐    │  └──────────┘                        │
│  │  ImZTC   │    │                                      │
│  │  (zaak-  │    │                                      │
│  │   typen) │    │                                      │
│  └──────────┘    │                                      │
└──────────────────┴───────────────────────────────────────┘
```

### GEMMA

**GEMMA** (GEMeentelijke Model Architectuur) is de referentiearchitectuur voor gemeenten. Het beschrijft hoe de informatievoorziening van een gemeente idealiter is ingericht. StUF is een van de bouwstenen binnen GEMMA.

### Informatiemodellen

Informatiemodellen beschrijven **welke gegevens** er zijn en hoe ze zich tot elkaar verhouden. Ze zijn het fundament waarop StUF-berichten worden gebaseerd:

| Model | Voluit | Beschrijft |
|---|---|---|
| **RSGB** | Referentiemodel Stelsel van Gemeentelijke Basisgegevens | Gegevens uit basisregistraties: personen, adressen, kadaster, etc. |
| **RGBZ** | Referentiemodel Gemeentelijke Basisgegevens Zaken | Gegevens rondom zaken: zaaktypen, statussen, documenten, betrokkenen |
| **ImZTC** | Informatiemodel Zaaktypecatalogus | De catalogus van zaaktypen die een gemeente afhandelt |

De relatie tussen informatiemodellen en StUF:

```
RSGB (informatiemodel)          ──→  StUF-BG (berichtenstandaard)
  "Een persoon heeft een naam,        "Dit StUF-bericht bevat element
   geboortedatum en adres"             <voornaam>, <geboortedatum>, <adres>"

RGBZ (informatiemodel)          ──→  StUF-ZKN (berichtenstandaard)
  "Een zaak heeft een zaaktype,        "Dit StUF-bericht bevat element
   status en betrokkene"                <zaaktype>, <status>, <betrokkene>"
```

Het informatiemodel zegt **wat** er is; StUF zegt **hoe** je het uitwisselt.

### MIM

Het **Metamodel Informatie Modellering (MIM)** is de standaard die beschrijft hóé informatiemodellen (zoals RSGB en RGBZ) moeten worden opgesteld. Het definieert de modelleerconventies en -niveaus.

## Sectormodellen

StUF kent meerdere **sectormodellen** – elk gericht op een specifiek domein. Elk sectormodel:
- Definieert de entiteiten en attributen voor dat domein
- Levert de XSD-schema's voor berichtvalidatie
- Beschrijft de berichten die voor dat domein beschikbaar zijn

### Overzicht van de belangrijkste sectormodellen

| Sectormodel | Voluit | Domein | Gebaseerd op |
|---|---|---|---|
| **StUF-BG** | StUF-Basisgegevens | Persoonsgegevens, adressen, kadaster | RSGB |
| **StUF-ZKN** | StUF-Zaken | Zaken, documenten, statussen | RGBZ |
| **StUF-EF** | StUF-Erfpacht | Erfpacht en grondzaken | Domein-specifiek |
| **StUF-ZTC** | StUF-Zaaktypecatalogus | Zaaktypen en configuratie | ImZTC |

### StUF-BG: Basisgegevens

Dit is het meest gebruikte sectormodel. StUF-BG richt zich op gegevens uit de basisregistraties:

**Entiteiten in StUF-BG (selectie):**

| Entiteit | Voorbeeldattributen |
|---|---|
| Natuurlijk Persoon (NPS) | BSN, voornamen, achternaam, geboortedatum, geslachtsaanduiding |
| Niet-Natuurlijk Persoon (NNP) | RSIN, statutaire naam, rechtsvorm |
| Vestiging (VES) | Vestigingsnummer, handelsnaam |
| Adres (ADR) | Straatnaam, huisnummer, postcode, woonplaats |
| Gemeentelijke Openbare Ruimte (GOR) | Naam, type (weg, water, terrein, etc.) |
| Woonplaats (WPL) | Woonplaatsnaam |
| Kadastrale Onroerende Zaak (KOZ) | Kadastrale aanduiding, koopsom |
| Maatschappelijke Activiteit (MAC) | KvK-nummer, naam |

StUF-BG-berichten worden gebruikt voor:
- Het opvragen van persoonsgegevens uit de BRP
- Het doorgeven van verhuizingen
- Het synchroniseren van adresgegevens uit de BAG
- Het ophalen van kadastergegevens

### StUF-ZKN: Zaken

StUF-ZKN richt zich op het zaakgericht werken – het afhandelen van aanvragen, vergunningen, meldingen, etc.:

**Entiteiten in StUF-ZKN (selectie):**

| Entiteit | Voorbeeldattributen |
|---|---|
| Zaak (ZAK) | Zaakidentificatie, omschrijving, startdatum, einddatum, status |
| Document (EDC) | Identificatie, titel, auteur, creatiedatum |
| Status (STA) | Statustype, datumgezet |
| Betrokkene (BTR) | Rol, naam (koppeling naar NPS of NNP) |
| Zaaktype (ZKT) | Zaaktypeomschrijving, doorlooptijd |

### Relatie tussen sectormodellen

Sectormodellen staan niet los van elkaar. Een zaak (StUF-ZKN) kan betrekking hebben op een persoon (StUF-BG). De berichten verwijzen naar entiteiten uit andere sectormodellen:

```
StUF-ZKN: Zaak
  ├── heeft betrokkene ──→ StUF-BG: Natuurlijk Persoon (NPS)
  ├── heeft object     ──→ StUF-BG: Adres (ADR)
  └── heeft document   ──→ StUF-ZKN: Document (EDC)
```

## SOAP en WSDL: de transportlaag

StUF-berichten worden niet "los" verstuurd. Ze worden verpakt in **SOAP-berichten** en aangeboden via **webervices** die beschreven worden met **WSDL**.

### SOAP (Simple Object Access Protocol)

SOAP is een protocol voor het uitwisselen van gestructureerde berichten via HTTP (of andere transportprotocollen). Een SOAP-bericht heeft een vaste structuur:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header>
    <!-- Optionele headers (bijv. authenticatie) -->
  </soap:Header>
  <soap:Body>
    <!-- Hier komt het StUF-bericht -->
    <stuf:kennisgevingsBericht xmlns:stuf="http://www.egem.nl/StUF/StUF0302">
      <!-- ... StUF-inhoud ... -->
    </stuf:kennisgevingsBericht>
  </soap:Body>
</soap:Envelope>
```

De structuur in lagen:

```
┌─────────────────────────────────┐
│  HTTP POST                      │  Transportlaag
├─────────────────────────────────┤
│  SOAP Envelope                  │  Berichtenvelop
│  ├── SOAP Header                │  Metadata (optioneel)
│  └── SOAP Body                  │  
│      └── StUF-bericht           │  De feitelijke inhoud
│          ├── Stuurgegevens      │  Wie, wat, wanneer
│          └── Body (gegevens)    │  De data
└─────────────────────────────────┘
```

> **Belangrijke observatie:** SOAP is puur de "envelop" – het definieert niet wát er verstuurd wordt, alleen hóé. StUF definieert de inhoud.

### WSDL (Web Services Description Language)

WSDL is een XML-gebaseerde taal die beschrijft welke **operaties** (diensten) een webservice aanbiedt. Een WSDL-document bevat:

| Onderdeel | Beschrijft |
|---|---|
| **Types** | De XSD-schema's voor de berichten |
| **Messages** | De berichttypes (input en output) |
| **PortType** | De beschikbare operaties |
| **Binding** | Hoe de operaties technisch worden aangeboden (SOAP) |
| **Service** | Het adres (URL) van de webservice |

Een vereenvoudigd voorbeeld:

```xml
<wsdl:definitions xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
                  xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/">

  <wsdl:types>
    <!-- XSD-schema's voor StUF-berichten -->
  </wsdl:types>

  <wsdl:message name="opvragenPersoonRequest">
    <!-- Input-bericht -->
  </wsdl:message>

  <wsdl:message name="opvragenPersoonResponse">
    <!-- Output-bericht -->
  </wsdl:message>

  <wsdl:portType name="StUF_BG_PortType">
    <wsdl:operation name="opvragenPersoon">
      <wsdl:input message="opvragenPersoonRequest"/>
      <wsdl:output message="opvragenPersoonResponse"/>
    </wsdl:operation>
  </wsdl:portType>

  <wsdl:binding name="StUF_BG_Binding" type="StUF_BG_PortType">
    <soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
    <!-- ... -->
  </wsdl:binding>

  <wsdl:service name="StUF_BG_Service">
    <wsdl:port name="StUF_BG_Port" binding="StUF_BG_Binding">
      <soap:address location="https://gemeente.nl/services/stuf-bg"/>
    </wsdl:port>
  </wsdl:service>

</wsdl:definitions>
```

In de praktijk hoef je WSDL-bestanden zelden handmatig te schrijven – ze worden vaak gegenereerd. Maar je moet ze kunnen **lezen** om te begrijpen welke operaties een StUF-service aanbiedt.

## Synchrone vs. asynchrone communicatie

StUF ondersteunt twee communicatiepatronen:

### Synchrone communicatie

Bij synchrone communicatie stuurt systeem A een verzoek en **wacht** op het antwoord van systeem B:

```
Systeem A                          Systeem B
    │                                  │
    │── Vraagbericht (Lv01) ─────────→│
    │                                  │── verwerkt vraag
    │←── Antwoordbericht (La01) ──────│
    │                                  │
```

**Kenmerken:**
- Direct antwoord
- De verbinding blijft open totdat het antwoord er is
- Geschikt voor bevraging (bijv. persoonsgegevens opvragen)
- Timeout als het antwoord te lang duurt

**Voorbeeld:** een burgerzaken-applicatie vraagt persoonsgegevens op bij de BRP-koppeling en ontvangt direct het antwoord.

### Asynchrone communicatie

Bij asynchrone communicatie stuurt systeem A een bericht en gaat **verder met zijn werk**. Het antwoord komt later (of er is geen antwoord):

```
Systeem A                          Systeem B
    │                                  │
    │── Kennisgeving (Lk01) ─────────→│
    │                                  │── bevestiging (Bv01)
    │←── Ontvangstbevestiging ────────│
    │                                  │
    │    (A gaat verder)               │── verwerkt kennisgeving
    │                                  │
```

**Kenmerken:**
- Geen wacht op verwerking
- Ontkoppeling: systemen hoeven niet gelijktijdig beschikbaar te zien
- Geschikt voor mutaties (bijv. een verhuizing doorgeven)
- Vaak via een **message broker** of **ESB** (Enterprise Service Bus)

**Voorbeeld:** de BRP geeft een adreswijziging door aan alle afnemers via een kennisgevingsbericht. De afnemers bevestigen de ontvangst maar verwerken het bericht op hun eigen tempo.

### Wanneer synchroon, wanneer asynchroon?

| Criterium | Synchroon | Asynchroon |
|---|---|---|
| **Doel** | Gegevens opvragen | Mutaties doorgeven |
| **Verwachting** | Direct antwoord nodig | Verwerking mag later |
| **Afhankelijkheid** | Beide systemen moeten online zijn | Ontkoppeld mogelijk |
| **Berichttype (StUF)** | Vraag/antwoord (Lv/La) | Kennisgeving (Lk) + bevestiging (Bv) |
| **Voorbeeld** | Persoonskaart ophalen | Verhuizing doorgeven |

## Andere standaarden in het gemeentelijk domein

### ZDS (Zaak- en Documentservices)

ZDS was een set koppelvlakspecificaties bovenop StUF-ZKN voor zaakgericht werken. Het definieerde specifieke services zoals "creëerZaak", "voegZaakdocumentToe", etc. ZDS is inmiddels vervangen door ZGW API's.

### ZGW API's (Zaakgericht Werken API's)

De **ZGW API's** zijn de moderne opvolger van ZDS. Ze zijn REST/JSON-gebaseerd in plaats van StUF/SOAP/XML. De ZGW API's omvatten:

| API | Functie |
|---|---|
| Zaken API | Aanmaken en beheren van zaken |
| Documenten API | Beheren van informatieobjecten |
| Catalogi API | Zaaktypen en besluittypen |
| Besluiten API | Registreren van besluiten |
| Autorisaties API | Autorisatie van applicaties |

### Haal Centraal

**Haal Centraal** is een programma van VNG Realisatie dat REST/JSON API's ontwikkelt voor het bevragen van basisregistraties. Het is het moderne alternatief voor StUF-BG voor bevragingen:

| Haal Centraal API | Vervangt (deels) | Basisregistratie |
|---|---|---|
| BRP Personen Bevragen | StUF-BG opvragen NPS | BRP |
| BAG Bevragen | StUF-BG opvragen ADR | BAG |
| BRK Bevragen | StUF-BG opvragen KOZ | BRK |

> **Let op:** Haal Centraal vervangt alleen de **bevragingen** (opvragen van gegevens). Voor **mutatie-kennisgevingen** (het doorgeven van wijzigingen) is er (nog) geen breed geadopteerd API-alternatief, waardoor StUF daar nog dominant is.

## De positie van StUF: samengevat

```
                INFORMATIEMODELLEN              UITWISSELINGSSTANDAARDEN
                                         
                ┌──────────┐             ┌─────────────┐
                │   RSGB   │────────────→│  StUF-BG    │  (XML/SOAP)
                └──────────┘             └─────────────┘
                                         ┌─────────────┐
                                    ────→│ Haal Centraal│  (REST/JSON)
                                         └─────────────┘
                ┌──────────┐             ┌─────────────┐
                │   RGBZ   │────────────→│  StUF-ZKN   │  (XML/SOAP)
                └──────────┘             └─────────────┘
                                         ┌─────────────┐
                                    ────→│  ZGW API's  │  (REST/JSON)
                                         └─────────────┘
```

Voor elk informatiemodel bestaan er inmiddels twee "routes" voor uitwisseling: de klassieke (StUF/SOAP/XML) en de moderne (API/REST/JSON). Beide zijn relevant:

- **StUF** voor bestaande koppelingen, mutatie-kennisgevingen, en systemen die nog niet gemigreerd zijn
- **API's** voor nieuwe koppelingen en bevragingen

## Samenvatting

| Concept | Uitleg |
|---|---|
| **GEMMA** | Referentiearchitectuur voor gemeenten; StUF is een bouwsteen |
| **RSGB** | Informatiemodel voor basisgegevens → basis voor StUF-BG |
| **RGBZ** | Informatiemodel voor zaken → basis voor StUF-ZKN |
| **Sectormodellen** | Domeinspecifieke StUF-schema's (StUF-BG, StUF-ZKN, StUF-EF, etc.) |
| **SOAP** | Protocol voor berichtuitwisseling; StUF-berichten zitten in SOAP-envelop |
| **WSDL** | Beschrijving van webservice-operaties |
| **Synchroon** | Vraag-en-antwoord; direct resultaat (Lv/La) |
| **Asynchroon** | Kennisgeving-en-bevestiging; ontkoppeld (Lk/Bv) |
| **ZGW API's** | Moderne REST/JSON opvolger voor zaakgericht werken |
| **Haal Centraal** | Moderne REST/JSON API's voor bevragen basisregistraties |

> **Kernpunt:** StUF is onderdeel van een breder ecosysteem. Informatiemodellen (RSGB, RGBZ) beschrijven wát er wordt uitgewisseld; StUF beschrijft hóé. De berichten worden via SOAP/WSDL-webservices verstuurd, zowel synchroon als asynchroon. Naast StUF ontstaan steeds meer REST/JSON-alternatieven, maar StUF blijft een belangrijk onderdeel van het gemeentelijke landschap.
