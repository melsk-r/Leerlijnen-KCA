---
title: "3.3 Opbouw van een StUF-bericht"
weight: 3
---

# 3.3 Opbouw van een StUF-bericht

Nu je weet wat StUF is en in welke context het functioneert, gaan we daadwerkelijk een StUF-bericht ontleden. In dit hoofdstuk leer je laag voor laag hoe een StUF-bericht is opgebouwd – van de buitenste SOAP-envelop tot de binnenste gegevenselementen.

## De lagen van een StUF-bericht

Een StUF-bericht bestaat uit meerdere geneste lagen:

```
┌─────────────────────────────────────────────────────────────┐
│ HTTP POST (transport)                                       │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ SOAP Envelope                                           │ │
│ │ ┌─────────────────────────────────────────────────────┐ │ │
│ │ │ SOAP Header (optioneel)                             │ │ │
│ │ │ (bijv. WS-Security, WS-Addressing)                  │ │ │
│ │ └─────────────────────────────────────────────────────┘ │ │
│ │ ┌─────────────────────────────────────────────────────┐ │ │
│ │ │ SOAP Body                                           │ │ │
│ │ │ ┌─────────────────────────────────────────────────┐ │ │ │
│ │ │ │ StUF-bericht                                    │ │ │ │
│ │ │ │ ┌───────────────────────────────────────────┐   │ │ │ │
│ │ │ │ │ Stuurgegevens                             │   │ │ │ │
│ │ │ │ │ (zender, ontvanger, referentie, tijdstip) │   │ │ │ │
│ │ │ │ └───────────────────────────────────────────┘   │ │ │ │
│ │ │ │ ┌───────────────────────────────────────────┐   │ │ │ │
│ │ │ │ │ Body (parameters + entiteiten)            │   │ │ │ │
│ │ │ │ │ (de feitelijke gegevens)                  │   │ │ │ │
│ │ │ │ └───────────────────────────────────────────┘   │ │ │ │
│ │ │ └─────────────────────────────────────────────────┘ │ │ │
│ │ └─────────────────────────────────────────────────────┘ │ │
│ └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

Laten we elke laag van buiten naar binnen bekijken.

## Laag 1: SOAP Envelope

De buitenste laag is de SOAP-envelop. Zoals je in sectie 3.2 hebt gelezen, is SOAP het transportprotocol. Het StUF-bericht is ingepakt in een SOAP Body:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header>
    <!-- Optioneel: WS-Security, WS-Addressing, etc. -->
  </soap:Header>
  <soap:Body>
    <!-- Hier begint het StUF-bericht -->
  </soap:Body>
</soap:Envelope>
```

De SOAP-laag is voor StUF-inhoudelijk niet zo interessant – het is slechts de verpakking. Maar je zult het altijd tegenkomen in logfiles en bij het debuggen van koppelingen.

> **Tip:** Bij het analyseren van StUF-berichten kun je de SOAP-envelop mentaal "weghalen" en je richten op de inhoud van de `<soap:Body>`.

## Laag 2: Het StUF-berichtelement

Binnen de SOAP Body staat het eigenlijke StUF-bericht. De naam van dit element geeft aan welk **type bericht** het is:

```xml
<soap:Body>
  <BG:npsLk01 xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
              xmlns:StUF="http://www.egem.nl/StUF/StUF0302">
    <!-- StUF-bericht: een Lk01 voor Natuurlijk Persoon -->
  </BG:npsLk01>
</soap:Body>
```

De naamgeving volgt een patroon:

```
[entiteit][berichttype][versie]
   nps       Lk          01
```

| Deel | Betekenis | Voorbeeld |
|---|---|---|
| `nps` | De entiteit: Natuurlijk Persoon | `nps`, `adr`, `zak`, `edc` |
| `Lk` | Het berichttype: Kennisgeving | `Lk`, `Lv`, `La`, `Di`, `Du`, `Fo`, `Bv` |
| `01` | Versienummer van het bericht | `01`, `02`, etc. |

Andere voorbeelden:
- `npsLv01` – Vraagbericht voor persoon (opvragen)
- `npsLa01` – Antwoordbericht voor persoon
- `zakLk01` – Kennisgevingsbericht voor zaak
- `adrLv01` – Vraagbericht voor adres

## Laag 3: Stuurgegevens

Elk StUF-bericht begint met **stuurgegevens** – de metadata die beschrijft wie het bericht stuurt, voor wie het is, en wat het bevat:

```xml
<BG:npsLk01 xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
            xmlns:StUF="http://www.egem.nl/StUF/StUF0302">

  <StUF:stuurgegevens>
    <StUF:berichtcode>Lk01</StUF:berichtcode>
    <StUF:zender>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>BRP-systeem</StUF:applicatie>
    </StUF:zender>
    <StUF:ontvanger>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>Belastingsysteem</StUF:applicatie>
    </StUF:ontvanger>
    <StUF:referentienummer>REF-2024-00001234</StUF:referentienummer>
    <StUF:tijdstipBericht>20240115103000</StUF:tijdstipBericht>
    <StUF:entiteittype>NPS</StUF:entiteittype>
  </StUF:stuurgegevens>

  <!-- Body volgt hier -->

</BG:npsLk01>
```

### Elementen in de stuurgegevens

| Element | Verplicht? | Beschrijving |
|---|---|---|
| `berichtcode` | Ja | Type bericht (bijv. `Lk01`, `Lv01`, `La01`) |
| `zender` | Ja | Wie stuurt het bericht |
| `zender/organisatie` | Ja | Naam van de zendende organisatie |
| `zender/applicatie` | Ja | Naam van de zendende applicatie |
| `zender/administratie` | Nee | Optioneel: specifieke administratie |
| `zender/gebruiker` | Nee | Optioneel: specifieke gebruiker |
| `ontvanger` | Ja | Voor wie is het bericht bestemd |
| `ontvanger/organisatie` | Ja | Naam van de ontvangende organisatie |
| `ontvanger/applicatie` | Ja | Naam van de ontvangende applicatie |
| `referentienummer` | Ja | Uniek nummer om het bericht te identificeren |
| `tijdstipBericht` | Ja | Wanneer is het bericht aangemaakt |
| `crossRefnummer` | Nee | Verwijzing naar een eerder bericht (bij antwoorden) |
| `entiteittype` | Soms | De entiteit waar het bericht over gaat (bijv. `NPS`, `ADR`, `ZAK`) |
| `functie` | Nee | Extra aanduiding van de functie van het bericht |

### Stuurgegevens als routeringsinformatie

De stuurgegevens vervullen meerdere rollen:

1. **Routering** – Wie stuurt naar wie? (zender/ontvanger)
2. **Identificatie** – Welk bericht is dit? (referentienummer)
3. **Correlatie** – Hoort dit bij een eerder bericht? (crossRefnummer)
4. **Tijdstempeling** – Wanneer is het bericht gemaakt? (tijdstipBericht)
5. **Context** – Over welke entiteit gaat het? (entiteittype)

### Tijdstipformaat

In StUF worden tijdstippen genoteerd als een compacte string, niet als standaard XML `xs:dateTime`:

```
20240115103000    = 2024-01-15 10:30:00
```

Het formaat is: `jjjjMMddHHmmss` (jaar-maand-dag-uur-minuut-seconde).

## Laag 4: Body – de feitelijke gegevens

Na de stuurgegevens volgt de body van het bericht. De structuur van de body verschilt per berichttype. Laten we de twee meest voorkomende bekijken.

### Body van een kennisgevingsbericht (Lk)

Een kennisgevingsbericht meldt een **wijziging**. De body bevat een of meer **objecten** (entiteiten) met hun gegevens:

```xml
<BG:npsLk01 xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
            xmlns:StUF="http://www.egem.nl/StUF/StUF0302"
            xmlns:bg="http://www.stufstandaarden.nl/sector/bg/0310">

  <StUF:stuurgegevens>
    <!-- ... (zoals hierboven) ... -->
  </StUF:stuurgegevens>

  <BG:body>
    <BG:object StUF:entiteittype="NPS" StUF:sleutelVerzwordendeObjectSleutel="123456789">
      <BG:geslachtsnaam>Vries</BG:geslachtsnaam>
      <BG:voorvoegselGeslachtsnaam>de</BG:voorvoegselGeslachtsnaam>
      <BG:voornamen>Jan Pieter</BG:voornamen>
      <BG:geboortedatum>19850315</BG:geboortedatum>
      <BG:geslachtsaanduiding>M</BG:geslachtsaanduiding>
      <BG:verblijfsadres>
        <BG:aoa.identificatie>0344200000012345</BG:aoa.identificatie>
        <BG:wpl.woonplaatsNaam>Utrecht</BG:wpl.woonplaatsNaam>
        <BG:gor.straatnaam>Kerkstraat</BG:gor.straatnaam>
        <BG:aoa.huisnummer>12</BG:aoa.huisnummer>
        <BG:aoa.postcode>3511AB</BG:aoa.postcode>
      </BG:verblijfsadres>
    </BG:object>
  </BG:body>

</BG:npsLk01>
```

### Body van een vraagbericht (Lv)

Een vraagbericht is een **zoekopdracht**. De body bevat de zoekcriteria:

```xml
<BG:npsLv01 xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
            xmlns:StUF="http://www.egem.nl/StUF/StUF0302">

  <StUF:stuurgegevens>
    <StUF:berichtcode>Lv01</StUF:berichtcode>
    <!-- ... -->
  </StUF:stuurgegevens>

  <BG:gelijk>
    <BG:geslachtsnaam>Vries</BG:geslachtsnaam>
    <BG:voorvoegselGeslachtsnaam>de</BG:voorvoegselGeslachtsnaam>
    <BG:geboortedatum>19850315</BG:geboortedatum>
  </BG:gelijk>

  <BG:scope>
    <BG:object StUF:entiteittype="NPS">
      <BG:voornamen xsi:nil="true"/>
      <BG:geslachtsnaam xsi:nil="true"/>
      <BG:geboortedatum xsi:nil="true"/>
      <BG:verblijfsadres>
        <BG:gor.straatnaam xsi:nil="true"/>
        <BG:aoa.huisnummer xsi:nil="true"/>
        <BG:aoa.postcode xsi:nil="true"/>
        <BG:wpl.woonplaatsNaam xsi:nil="true"/>
      </BG:verblijfsadres>
    </BG:object>
  </BG:scope>

</BG:npsLv01>
```

Hier zien we twee belangrijke onderdelen:

| Onderdeel | Functie |
|---|---|
| `<BG:gelijk>` | De zoekcriteria: zoek personen met achternaam "de Vries" en geboortedatum 1985-03-15 |
| `<BG:scope>` | Welke gegevens wil je terugkrijgen? De elementen met `xsi:nil="true"` zijn de gevraagde velden |

Het antwoordbericht (`La01`) bevat dan de gevonden personen met de gevraagde velden ingevuld.

### Zoekfilters in vraagberichten

Naast `<gelijk>` zijn er meer zoekblokken:

| Element | Zoekoperatie | Voorbeeld |
|---|---|---|
| `<gelijk>` | Exacte match | Achternaam = "Vries" |
| `<vanaf>` | Groter dan of gelijk aan | Geboortedatum >= 19800101 |
| `<totEnMet>` | Kleiner dan of gelijk aan | Geboortedatum <= 19891231 |
| `<vanafTotEnMet>` | Bereik | Gecombineerd: geboortedatum tussen 1980 en 1989 |

## Het `mutatiesoort`-attribuut

Een van de meest kenmerkende kenmerken van StUF is het attribuut `mutatiesoort`. Het geeft aan wat voor soort wijziging een kennisgevingsbericht beschrijft.

### De waarden

| Waarde | Naam | Betekenis |
|---|---|---|
| `T` | Toevoeging | Een nieuw object is aangemaakt |
| `W` | Wijziging | Een bestaand object is gewijzigd |
| `V` | Verwijdering | Een bestaand object is verwijderd |
| `E` | Einddatering | De geldigheid van een object is beëindigd |
| `I` | Indicatief | Ter informatie, geen formele mutatie |
| `S` | Samenstelling | Een samengesteld bericht met meerdere objecten |

### Waar staat het?

Het `mutatiesoort`-attribuut staat op het `<object>`-element in een kennisgevingsbericht:

```xml
<BG:object StUF:entiteittype="NPS" StUF:mutatiesoort="W">
  <!-- De gewijzigde gegevens -->
</BG:object>
```

### Oud en nieuw bij wijzigingen

Bij een wijziging (`mutatiesoort="W"`) bevat het kennisgevingsbericht **twee** representaties van het object: de **oude** waarden en de **nieuwe** waarden:

```xml
<BG:npsLk01>
  <StUF:stuurgegevens>
    <!-- ... -->
  </StUF:stuurgegevens>

  <BG:body>
    <!-- De NIEUWE situatie -->
    <BG:object StUF:entiteittype="NPS" StUF:sleutelVerzwordendeObjectSleutel="123456789">
      <BG:geslachtsnaam>Jansen</BG:geslachtsnaam>
      <BG:voorvoegselGeslachtsnaam xsi:nil="true"
        StUF:noValue="geenWaarde"/>
      <BG:voornamen>Jan Pieter</BG:voornamen>
      <BG:verblijfsadres>
        <BG:gor.straatnaam>Nieuwstraat</BG:gor.straatnaam>
        <BG:aoa.huisnummer>42</BG:aoa.huisnummer>
        <BG:aoa.postcode>3512CD</BG:aoa.postcode>
      </BG:verblijfsadres>

      <!-- Historiepatroon (niet altijd aanwezig): -->
      <StUF:tijdvakGeldigheid>
        <StUF:beginGeldigheid>20240115</StUF:beginGeldigheid>
      </StUF:tijdvakGeldigheid>
    </BG:object>

    <!-- De OUDE situatie (was-situatie) -->
    <BG:object StUF:entiteittype="NPS" StUF:sleutelVerzwordendeObjectSleutel="123456789">
      <BG:geslachtsnaam>Vries</BG:geslachtsnaam>
      <BG:voorvoegselGeslachtsnaam>de</BG:voorvoegselGeslachtsnaam>
      <BG:voornamen>Jan Pieter</BG:voornamen>
      <BG:verblijfsadres>
        <BG:gor.straatnaam>Kerkstraat</BG:gor.straatnaam>
        <BG:aoa.huisnummer>12</BG:aoa.huisnummer>
        <BG:aoa.postcode>3511AB</BG:aoa.postcode>
      </BG:verblijfsadres>

      <StUF:tijdvakGeldigheid>
        <StUF:beginGeldigheid>20100601</StUF:beginGeldigheid>
        <StUF:eindGeldigheid>20240115</StUF:eindGeldigheid>
      </StUF:tijdvakGeldigheid>
    </BG:object>
  </BG:body>
</BG:npsLk01>
```

Dit bericht zegt:
1. Jan Pieter de Vries heette voorheen "de Vries", woonde op Kerkstraat 12
2. Sinds 15 januari 2024 heet hij "Jansen" (voorvoegsel verdwenen) en woont hij op Nieuwstraat 42
3. Het eerste `<object>` is de **nieuwe** situatie, het tweede is de **oude** situatie

Dit patroon (nieuw + oud) is fundamenteel voor StUF-kennisgevingen. Het stelt de ontvanger in staat om:
- Te verifiëren dat de "oude" situatie overeenkomt met de eigen administratie
- De wijziging door te voeren van oud naar nieuw
- Conflicten te detecteren als de eigen gegevens afwijken van de "oude" situatie

## Het `noValue`-attribuut

Naast `mutatiesoort` is `noValue` een belangrijk StUF-attribuut. Het geeft aan waarom een element **geen waarde** heeft:

```xml
<BG:voorvoegselGeslachtsnaam xsi:nil="true" StUF:noValue="geenWaarde"/>
```

De mogelijke waarden:

| Waarde | Betekenis |
|---|---|
| `geenWaarde` | Het element heeft bewust geen waarde (de waarde is leeg gemaakt) |
| `nietGeautoriseerd` | De afnemer is niet geautoriseerd om deze waarde te zien |
| `waardeOnbekend` | De waarde is onbekend |
| `vastgesteldOnbekend` | Het is vastgesteld dat de waarde onbekend is |
| `nietOndersteund` | Het element wordt niet ondersteund door het zendende systeem |

Dit onderscheid is belangrijk. In gewoon XML zou een leeg element (`<voorvoegsel/>`) dubbelzinnig zijn – is het leeg, onbekend, of niet beschikbaar? Met `noValue` maakt StUF dit expliciet.

## Tijdvakken en historie

StUF ondersteunt **historische gegevens** via tijdvakken. Dit is een uniek aspect van de standaard.

### `tijdvakGeldigheid`

Beschrijft wanneer gegevens **inhoudelijk geldig** zijn:

```xml
<StUF:tijdvakGeldigheid>
  <StUF:beginGeldigheid>20240115</StUF:beginGeldigheid>
  <StUF:eindGeldigheid>20240601</StUF:eindGeldigheid>
</StUF:tijdvakGeldigheid>
```

Dit zegt: deze gegevens zijn geldig van 15 januari 2024 tot 1 juni 2024.

### `tijdvakRelatie`

Beschrijft wanneer een **relatie** tussen objecten geldig is:

```xml
<StUF:tijdvakRelatie>
  <StUF:beginRelatie>20100601</StUF:beginRelatie>
</StUF:tijdvakRelatie>
```

### `tijdstipRegistratie`

Beschrijft wanneer de gegevens **administratief zijn vastgelegd**:

```xml
<StUF:tijdstipRegistratie>20240115103000</StUF:tijdstipRegistratie>
```

Het verschil:
- **Materiële historie** (`tijdvakGeldigheid`) = wanneer was het in de werkelijkheid zo?
- **Formele historie** (`tijdstipRegistratie`) = wanneer is het vastgelegd in het systeem?

Voorbeeld: iemand verhuist op 1 januari (materieel) maar meldt het pas op 15 januari (formeel).

## Een compleet StUF-bericht

Laten we alle onderdelen samenvoegen in een compleet voorbeeld – een kennisgevingsbericht voor het toevoegen van een persoon:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>

    <BG:npsLk01
        xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
        xmlns:StUF="http://www.egem.nl/StUF/StUF0302"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

      <!-- 1. STUURGEGEVENS -->
      <StUF:stuurgegevens>
        <StUF:berichtcode>Lk01</StUF:berichtcode>
        <StUF:zender>
          <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
          <StUF:applicatie>BRP-koppeling</StUF:applicatie>
        </StUF:zender>
        <StUF:ontvanger>
          <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
          <StUF:applicatie>Belastingsysteem</StUF:applicatie>
        </StUF:ontvanger>
        <StUF:referentienummer>BRP-2024-00543210</StUF:referentienummer>
        <StUF:tijdstipBericht>20240115103000</StUF:tijdstipBericht>
        <StUF:entiteittype>NPS</StUF:entiteittype>
      </StUF:stuurgegevens>

      <!-- 2. PARAMETERS -->
      <BG:parameters>
        <StUF:mutatiesoort>T</StUF:mutatiesoort>
      </BG:parameters>

      <!-- 3. BODY MET OBJECTGEGEVENS -->
      <BG:body>
        <BG:object StUF:entiteittype="NPS"
                   StUF:sleutelVerzwordendeObjectSleutel="987654321">
          <BG:inp.bsn>987654321</BG:inp.bsn>
          <BG:geslachtsnaam>Bakker</BG:geslachtsnaam>
          <BG:voornamen>Maria Elisabeth</BG:voornamen>
          <BG:geboortedatum>19900720</BG:geboortedatum>
          <BG:geslachtsaanduiding>V</BG:geslachtsaanduiding>
          <BG:verblijfsadres>
            <BG:aoa.identificatie>0344200000098765</BG:aoa.identificatie>
            <BG:wpl.woonplaatsNaam>Utrecht</BG:wpl.woonplaatsNaam>
            <BG:gor.straatnaam>Voorstraat</BG:gor.straatnaam>
            <BG:aoa.huisnummer>88</BG:aoa.huisnummer>
            <BG:aoa.postcode>3512AX</BG:aoa.postcode>
          </BG:verblijfsadres>
          <StUF:tijdvakGeldigheid>
            <StUF:beginGeldigheid>20240115</StUF:beginGeldigheid>
          </StUF:tijdvakGeldigheid>
          <StUF:tijdstipRegistratie>20240115103000</StUF:tijdstipRegistratie>
        </BG:object>
      </BG:body>

    </BG:npsLk01>

  </soap:Body>
</soap:Envelope>
```

### Analyse van dit bericht

| Onderdeel | Waarde | Betekenis |
|---|---|---|
| SOAP Envelope | | Transport-wrapper |
| Berichtelement | `npsLk01` | Kennisgeving voor Natuurlijk Persoon |
| Berichtcode | `Lk01` | Kennisgeving (asynchroon) |
| Zender | BRP-koppeling, Gemeente Utrecht | Het BRP-systeem stuurt |
| Ontvanger | Belastingsysteem, Gemeente Utrecht | Het belastingsysteem ontvangt |
| Referentienummer | BRP-2024-00543210 | Uniek ID van dit bericht |
| Tijdstip | 20240115103000 | 15 januari 2024, 10:30:00 |
| Mutatiesoort | T | Toevoeging (nieuw persoon) |
| BSN | 987654321 | Het BSN van de persoon |
| Naam | Maria Elisabeth Bakker | Voornamen en achternaam |
| Geboortedatum | 19900720 | 20 juli 1990 |
| Adres | Voorstraat 88, 3512AX Utrecht | Verblijfsadres |

## Elementnamen in StUF-BG

Je hebt misschien opgemerkt dat sommige elementnamen in StUF-BG punten bevatten (bijv. `inp.bsn`, `aoa.identificatie`). Dit komt door de naamgevingsconventie in StUF-BG:

| Prefix | Voluit | Betekenis |
|---|---|---|
| `inp.` | Ingeschreven Natuurlijk Persoon | Attribuut specifiek voor ingeschreven personen |
| `aoa.` | Adresseerbaar Object Aanduiding | Attribuut van een adres |
| `gor.` | Gemeentelijke Openbare Ruimte | Attribuut van een straat/weg |
| `wpl.` | Woonplaats | Attribuut van een woonplaats |
| `sub.` | Subject | Algemeen attribuut van een persoon/organisatie |

Deze prefixen komen uit het RSGB-informatiemodel en helpen om de herkomst van een attribuut te herkennen.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **SOAP Envelope** | Buitenste laag; transportverpakking |
| **Berichtelement** | Root van het StUF-bericht; naam bevat entiteit + berichttype |
| **Stuurgegevens** | Metadata: zender, ontvanger, referentie, tijdstip, entiteittype |
| **Body** | De feitelijke gegevens (objecten bij Lk, zoekcriteria bij Lv) |
| **`mutatiesoort`** | Type wijziging: T(oevoeging), W(ijziging), V(erwijdering), E(inddatering), I(ndicatief), S(amenstelling) |
| **Oud/nieuw-patroon** | Bij wijzigingen bevat het bericht zowel de nieuwe als de oude situatie |
| **`noValue`** | Verklaart waarom een element geen waarde heeft |
| **Tijdvakken** | `tijdvakGeldigheid` (materieel) en `tijdstipRegistratie` (formeel) voor historische gegevens |

> **Kernpunt:** Een StUF-bericht is opgebouwd in lagen: SOAP-envelop → StUF-berichtelement → stuurgegevens → body met objectgegevens. De stuurgegevens zorgen voor routering en traceerbaarheid. De body bevat de feitelijke data, verrijkt met attributen als `mutatiesoort` en `noValue` die de betekenis van de gegevens preciseren. Het oud/nieuw-patroon bij wijzigingen maakt StUF uniek ten opzichte van eenvoudiger berichtenstandaarden.
