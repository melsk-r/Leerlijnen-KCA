---
title: "4.3 StUF in de praktijk"
weight: 3
---

# 4.3 StUF in de praktijk

StUF is niet alleen een technische standaard op papier – het is de lijm die honderden applicaties bij honderden gemeenten met elkaar verbindt. In dit afsluitende hoofdstuk bekijken we hoe StUF in de echte wereld wordt ingezet, welke architectuur erachter zit, en hoe de overheid op dit moment bezig is met de overgang van StUF naar moderne API's.

## Het applicatielandschap van een gemeente

### Wat is een applicatielandschap?

Een gemeente gebruikt tientallen tot honderden software-applicaties voor het uitvoeren van haar taken. Denk aan:

| Domein | Voorbeelden van applicaties |
|---|---|
| **Burgerzaken** | Civision Burgerzaken, PinkRoccade Cipers |
| **Basisregistratie Personen (BRP)** | GBA-V, Procura |
| **Basisregistratie Adressen en Gebouwen (BAG)** | Neuron, Key2Gebouwen |
| **Sociaal domein** | Suite4Sociaal, GWS4all |
| **Belastingen** | Tribuut, Key2Belastingen |
| **Vergunningen (VTH)** | SquitXO, RxMission |
| **Zaakgericht werken** | OpenZaak, ZGW, Decos JOIN |
| **Documentbeheer (DMS)** | Corsa, Alfresco |
| **Gegevensmagazijn** | RSGB-datawarehouse |

Elk van deze applicaties beheert een deel van de gemeentelijke gegevens. Maar die gegevens zijn **niet geïsoleerd**: als iemand verhuist, moet die wijziging doorgegeven worden van de BRP naar belastingen, vergunningen, het sociaal domein, en meer.

### De rol van StUF in dit landschap

StUF is het **gestandaardiseerde protocol** waarmee deze applicaties gegevens uitwisselen. Zonder StUF zou elke leverancier een eigen berichtenformaat gebruiken, en zouden gemeenten voor elke combinatie van applicaties een maatwerkkoppeling moeten bouwen.

Met StUF geldt:
- Alle leveranciers implementeren **dezelfde berichtstandaard**
- Een wijziging in het BRP-systeem levert een StUF-kennisgevingsbericht op
- Alle ontvangende systemen begrijpen dat bericht – ongeacht de leverancier

```
┌──────────┐     StUF      ┌──────────────┐     StUF      ┌──────────┐
│   BRP    │──────────────→│  Gegevens-   │──────────────→│ Belasting│
│ systeem  │               │  distributie │               │ systeem  │
└──────────┘               │  (ESB/broker)│               └──────────┘
                           │              │     StUF      ┌──────────┐
                           │              │──────────────→│ Sociaal  │
                           │              │               │ domein   │
                           └──────────────┘               └──────────┘
                                  │
                                  │ StUF
                                  ▼
                           ┌──────────────┐
                           │    Zaak-     │
                           │   systeem    │
                           └──────────────┘
```

### Gegevensdistributie: het hart van StUF

Het meest voorkomende gebruik van StUF is **gegevensdistributie**: het automatisch doorsturen van wijzigingen van een bronregistratie naar afnemende systemen.

**Voorbeeld: een verhuizing**

1. Een medewerker burgerzaken registreert de verhuizing in het BRP-systeem
2. Het BRP-systeem genereert een StUF-kennisgeving (`npsLk01`) met:
   - Het **oude** adres (in `<oud>`)
   - Het **nieuwe** adres (in `<nieuw>`)
3. De gegevensdistributiecomponent (ESB/broker) ontvangt het bericht
4. De broker stuurt het bericht door naar alle geabonneerde systemen:
   - Belastingsysteem (past WOZ-aanslag aan)
   - Sociaal domein (past dossier aan)
   - Zaaksysteem (koppelt lopende zaken aan nieuw adres)
5. Elk ontvangend systeem stuurt een `Bv03` (bevestiging) of `Fo03` (fout) terug

Dit alles gebeurt automatisch, zonder handmatige data-invoer in elk systeem.

### Kernregistraties en abonnementen

De distributie werkt op basis van **abonnementen**. Elk afnemend systeem meldt aan de distributiecomponent welke berichten het wil ontvangen:

| Bronregistratie | Koppelvlak | Voorbeelden van geabonneerde systemen |
|---|---|---|
| BRP (personen) | StUF-BG | Belastingen, sociaal domein, vergunningen |
| BAG (adressen/gebouwen) | StUF-BG | BRP, belastingen, WOZ |
| BRK (kadaster) | StUF-BG | WOZ, belastingen |
| Zaken | StUF-ZKN | DMS, archief, portaal |

## Koppelvlakken en adapters

### Wat is een koppelvlak?

Een **koppelvlak** is een afgesproken set StUF-berichten voor communicatie tussen twee soorten systemen. Het koppelvlak definieert:

- Welke **berichten** worden uitgewisseld (Lk01, Lk02, La01, etc.)
- Welke **entiteiten** erin zitten (NPS, ADR, etc.)
- Welke **elementen** verplicht of optioneel zijn
- Welke **richting** de berichten opgaan (wie stuurt, wie ontvangt)

De belangrijkste koppelvlakken:

| Koppelvlak | Omschrijving | Berichtstandaard |
|---|---|---|
| **StUF-BG** | Basisgegevens (personen, adressen, gebouwen) | StUF 0301/0302 |
| **StUF-ZKN** | Zaak- en Documentgegevens | StUF 0310 |
| **StUF-EF** | Elektronische formulieren | StUF 0302 |
| **StUF-GEO** | Geo-informatie (vlakken, punten) | StUF 0302 |
| **Sectormodel e-Formulieren** | Koppeling e-formulieren met zaaksysteem | StUF 0302 |

### Wat is een adapter?

Een **adapter** (ook wel: connector of koppelcomponent) is een stukje software dat de vertaling doet tussen het interne datamodel van een applicatie en het StUF-berichtenformaat.

```
┌───────────────┐     intern      ┌──────────┐     StUF      ┌──────────┐
│  Applicatie A │───────────────→│ Adapter A│──────────────→│   ESB    │
│  (eigen model)│     formaat     └──────────┘               │          │
└───────────────┘                                            │          │
                                                             │          │
┌───────────────┐     intern      ┌──────────┐     StUF      │          │
│  Applicatie B │←───────────────│ Adapter B│←──────────────│          │
│  (eigen model)│     formaat     └──────────┘               └──────────┘
└───────────────┘
```

Elke softwareleverancier bouwt een adapter voor zijn eigen applicatie. De adapter:
1. **Ontvangt** StUF-berichten via SOAP
2. **Vertaalt** de StUF-gegevens naar het interne datamodel
3. **Verwerkt** de gegevens in de applicatie
4. **Stuurt** een StUF-bevestiging of foutmelding terug

### Enterprise Service Bus (ESB)

In veel gemeenten zit er een **ESB** (Enterprise Service Bus) of **messagebroker** tussen de systemen. De ESB:

| Functie | Toelichting |
|---|---|
| **Routering** | Stuurt berichten naar de juiste ontvangers op basis van abonnementen |
| **Transformatie** | Kan berichten omzetten tussen StUF-versies of -koppelvlakken |
| **Logging** | Houdt een logboek bij van alle berichten |
| **Foutafhandeling** | Vangt fouten op en biedt retry-mechanismen |
| **Monitoring** | Biedt inzicht in berichtenstromen en -volumes |

Bekende ESB-producten in de gemeentemarkt:

| Product | Leverancier |
|---|---|
| MSB (MSb Integratie) | MSb |
| MSb eBMS | MSb |
| Mule ESB | MuleSoft |
| WSO2 | WSO2 |

## Van StUF naar API's: de transitie

### Waarom verandert het landschap?

StUF is ontwikkeld in een tijd (begin jaren 2000) waarin SOAP-webservices de standaard waren voor systeem-naar-systeemcommunicatie. Sindsdien is de technologie sterk geëvolueerd:

| Aspect | StUF/SOAP (oud) | REST/API's (nieuw) |
|---|---|---|
| **Protocol** | SOAP over HTTP | REST over HTTP |
| **Berichtformaat** | XML | JSON (leesbaar, compact) |
| **Documentatie** | WSDL + XSD | OpenAPI / Swagger |
| **Complexiteit** | Hoog (namespaces, SOAP-envelop, etc.) | Laag (URL + JSON) |
| **Tooling** | Gespecialiseerd (SoapUI, oXygen) | Breed beschikbaar (curl, Postman, browsers) |
| **Standaardisatie** | KING/VNG Realisatie | VNG/Common Ground |
| **Geschikt voor** | Batch-synchronisatie, distributie | Real-time opvragen, event-driven |

### Common Ground

**Common Ground** is de visie van de VNG (Vereniging van Nederlandse Gemeenten) op de informatievoorziening van de toekomst. De kernprincipes:

| Principe | Toelichting |
|---|---|
| **Gegevens bij de bron** | Geen kopieën meer; haal gegevens op bij de bronregistratie wanneer je ze nodig hebt |
| **Componentgebaseerd** | Kleine, onafhankelijke componenten in plaats van grote monolithische systemen |
| **Open standaarden** | REST-API's volgens de API-strategie van de overheid |
| **Open source** | Voorkeur voor open-source componenten |

Het verschil met de StUF-wereld:

```
StUF-model (kopieën):                    Common Ground (bij de bron):

┌─────┐  kopie  ┌──────────┐             ┌─────┐
│ BRP │────────→│ Belasting│             │ BRP │←──── API-call ────┐
│     │  kopie  │          │             │     │                   │
│     │────────→│          │             └─────┘             ┌──────────┐
└─────┘         └──────────┘                                 │ Belasting│
                                                             └──────────┘
In StUF: Belasting heeft           In Common Ground: Belasting
een KOPIE van persoonsgegevens     BEVRAAGT de BRP wanneer nodig
```

### Haal Centraal

**Haal Centraal** is het programma dat de concrete API's ontwikkelt die StUF-koppelvlakken moeten vervangen. Elke API is het REST-equivalent van een StUF-koppelvlak:

| StUF-koppelvlak | Haal Centraal API | Status |
|---|---|---|
| StUF-BG (personen) | BRP Personen Bevragen API | In productie |
| StUF-BG (adressen) | BAG Bevragen API | In productie |
| StUF-BG (kadaster) | BRK Bevragen API | In productie |
| StUF-BG (WOZ) | WOZ Bevragen API | In ontwikkeling |
| StUF-ZKN (zaken) | Zaak API's (ZGW API's) | In productie |
| StUF-EF (formulieren) | Formulieren API | In ontwikkeling |

**Voorbeeld:** een persoon opvragen.

Met StUF:
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
               xmlns:StUF="http://www.egem.nl/StUF/StUF0302"
               xmlns:BG="http://www.stufstandaarden.nl/sector/bg/0310">
  <soap:Body>
    <BG:npsLv01>
      <BG:stuurgegevens>
        <StUF:berichtcode>Lv01</StUF:berichtcode>
        <StUF:zender><StUF:applicatie>BelApp</StUF:applicatie></StUF:zender>
        <StUF:ontvanger><StUF:applicatie>BRP</StUF:applicatie></StUF:ontvanger>
        <StUF:referentienummer>REF-001</StUF:referentienummer>
        <StUF:tijdstipBericht>20240115120000</StUF:tijdstipBericht>
        <StUF:entiteittype>NPS</StUF:entiteittype>
      </BG:stuurgegevens>
      <BG:gpiParameters>
        <StUF:sortering>1</StUF:sortering>
        <StUF:indicatorVervolgvraag>false</StUF:indicatorVervolgvraag>
      </BG:gpiParameters>
      <BG:object StUF:entiteittype="NPS">
        <BG:inp.bsn>123456789</BG:inp.bsn>
      </BG:object>
    </BG:npsLv01>
  </soap:Body>
</soap:Envelope>
```

Met Haal Centraal (REST API):
```bash
curl -X GET "https://api.brp.nl/personen/123456789" \
  -H "Authorization: Bearer eyJ..." \
  -H "Accept: application/json"
```

Antwoord:
```json
{
  "burgerservicenummer": "123456789",
  "naam": {
    "voornamen": "Jan",
    "geslachtsnaam": "Vries"
  },
  "geboorte": {
    "datum": "1985-03-15"
  }
}
```

Het verschil in complexiteit en leesbaarheid is duidelijk.

### Het migratiepad

De overgang van StUF naar API's gaat **geleidelijk**. De meeste gemeenten zitten nu in een hybride situatie:

| Fase | Kenmerk |
|---|---|
| **Fase 1: Volledig StUF** | Alle koppelingen verlopen via StUF. Dit was de situatie tot ~2018. |
| **Fase 2: Hybride** | Nieuwe koppelingen worden als API gebouwd; bestaande StUF-koppelingen blijven draaien. Dit is waar de meeste gemeenten nu zitten. |
| **Fase 3: API-first** | StUF wordt alleen nog onderhouden voor legacy-systemen die niet gemigreerd zijn. |
| **Fase 4: Volledig API** | Alle koppelingen zijn API-gebaseerd. StUF is uitgefaseerd. (Toekomst) |

**In de praktijk:** als je nu met StUF werkt, werk je waarschijnlijk in fase 2. Je beheert bestaande StUF-koppelingen en bouwt mogelijk nieuwe koppelingen op API-basis. Kennis van StUF is daarom nog jaren essentieel:

- Bestaande koppelingen moeten onderhouden worden
- Migratie vereist begrip van zowel StUF als API's
- Niet alle leveranciers migreren even snel
- Sommige gemeentelijke processen draaien nog volledig op StUF

### Translatie-laag: StUF ↔ API

Sommige gemeenten gebruiken een **translatielaag** die StUF-berichten omzet naar API-calls en vice versa. Dit maakt het mogelijk om nieuwe API-bronnen te gebruiken zonder alle bestaande StUF-afnemers te migreren:

```
┌──────────────┐     StUF      ┌──────────────┐     REST     ┌──────────┐
│  Legacy      │──────────────→│  Translatie- │──────────────→│  Haal    │
│  applicatie  │               │  laag        │               │  Centraal│
│  (StUF)      │←──────────────│              │←──────────────│  API     │
└──────────────┘     StUF      └──────────────┘     JSON      └──────────┘
```

## Praktijkscenario's

### Scenario 1: Gegevensdistributie na verhuizing

**Context:** Een burger verhuist van Amsterdam naar Utrecht.

**Stappen:**
1. Utrecht registreert de inschrijving in het BRP-systeem
2. Het BRP-systeem stuurt een `npsLk01` (kennisgeving) via StUF-BG
3. De ESB routeert het bericht naar:
   - Belastingsysteem (mutatie WOZ)
   - Sociaal domein (overdracht dossier)
   - Schuldhulpverlening (geen mutatie nodig → negeert bericht)
4. Elk systeem verwerkt de mutatie en stuurt een bevestiging

### Scenario 2: Zaak aanmaken vanuit e-formulier

**Context:** Een burger vraagt online een kapvergunning aan.

**Stappen:**
1. Het e-formulier genereert een StUF-EF bericht
2. Het bericht wordt omgezet naar een StUF-ZKN `zakLk01` (zaak-kennisgeving)
3. Het zaaksysteem maakt een zaak aan
4. Het zaaksysteem stuurt een `Bv03` bevestiging terug
5. Het bijgevoegde document wordt via een apart StUF-bericht aan de zaak gekoppeld

### Scenario 3: Hybride koppeling met API

**Context:** Een nieuw zaaksysteem gebruikt de Haal Centraal API, maar het belastingsysteem verwacht nog StUF.

**Stappen:**
1. Het zaaksysteem bevraagt de BRP via de Haal Centraal API (REST/JSON)
2. Wanneer de zaak afgerond is, moet het belastingsysteem geïnformeerd worden
3. De translatielaag zet de REST-notificatie om naar een StUF-kennisgeving
4. Het belastingsysteem ontvangt het StUF-bericht en verwerkt het

## Samenvatting

| Onderwerp | Kernpunt |
|---|---|
| **Applicatielandschap** | Gemeenten gebruiken tientallen applicaties die via StUF gegevens uitwisselen |
| **Gegevensdistributie** | Kernfunctie van StUF: wijzigingen automatisch doorsturen via kennisgevingen |
| **Koppelvlakken** | Gestandaardiseerde sets berichten per domein (StUF-BG, StUF-ZKN, etc.) |
| **Adapters** | Vertaallaag tussen applicatie-intern formaat en StUF |
| **ESB** | Centrale routering, transformatie, logging en monitoring |
| **Common Ground** | VNG-visie: gegevens bij de bron, REST-API's, open source |
| **Haal Centraal** | Concrete API's die StUF-koppelvlakken vervangen |
| **Migratiepad** | Geleidelijke overgang; hybride fase kan nog jaren duren |
| **StUF-kennis blijft nodig** | Onderhoud van bestaande koppelingen, migratie, en niet-gemigreerde systemen |

> **Kernpunt:** StUF is diep verweven met het applicatielandschap van Nederlandse gemeenten. Hoewel de transitie naar REST-API's (Common Ground / Haal Centraal) in volle gang is, blijft StUF-kennis essentieel voor het onderhouden van bestaande koppelingen en het begeleiden van migraties. Een goed begrip van zowel StUF als de nieuwe API-standaarden maakt je als informatieprofessional extra waardevol.
