---
title: "4.1 StUF-berichten lezen en schrijven"
weight: 1
---

# 4.1 StUF-berichten lezen en schrijven

In Deel 3 heb je de theorie achter StUF-berichten geleerd: stuurgegevens, berichttypen, het mutatiesoort-attribuut. In dit hoofdstuk gaan we van theorie naar praktijk. Je leert **stap voor stap** hoe je een StUF-bericht ontleedt, hoe je de bijbehorende schema's erbij pakt, en hoe je zelf berichten kunt samenstellen. Aan het einde herken je de patronen die je in de dagelijkse praktijk steeds opnieuw tegenkomt.

## Systematisch een StUF-bericht lezen

Wanneer je een StUF-bericht onder ogen krijgt – uit een logfile, van een leverancier, of uit een testtool – pak het dan systematisch aan. Hier is een stappenplan:

### Stap 1: Identificeer het berichttype

Kijk naar het root-element (binnen de SOAP Body):

```xml
<soap:Body>
  <BG:npsLa01 xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
              xmlns:StUF="http://www.egem.nl/StUF/StUF0302">
```

Uit de naam `npsLa01` lees je:
- **`nps`** → Natuurlijk Persoon
- **`La`** → Antwoordbericht (Lees antwoord)
- **`01`** → Versie 1

Je weet nu: dit is een **antwoord** op een vraag over een **persoon**.

### Stap 2: Lees de stuurgegevens

```xml
<StUF:stuurgegevens>
  <StUF:berichtcode>La01</StUF:berichtcode>
  <StUF:zender>
    <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
    <StUF:applicatie>BRP-koppeling</StUF:applicatie>
  </StUF:zender>
  <StUF:ontvanger>
    <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
    <StUF:applicatie>Burgerzaken-front</StUF:applicatie>
  </StUF:ontvanger>
  <StUF:referentienummer>BRP-2024-RES-00456</StUF:referentienummer>
  <StUF:crossRefnummer>BZF-2024-REQ-00123</StUF:crossRefnummer>
  <StUF:tijdstipBericht>20240315141532</StUF:tijdstipBericht>
  <StUF:entiteittype>NPS</StUF:entiteittype>
</StUF:stuurgegevens>
```

Noteer:
| Gegeven | Waarde | Betekenis |
|---|---|---|
| Berichtcode | La01 | Antwoordbericht |
| Zender | BRP-koppeling | Het BRP-systeem antwoordt |
| Ontvanger| Burgerzaken-front | De burgerzaken-applicatie ontvangt |
| Referentienummer | BRP-2024-RES-00456 | ID van dit antwoord |
| CrossRefnummer | BZF-2024-REQ-00123 | Verwijst naar het oorspronkelijke vraagbericht |
| Tijdstip | 20240315141532 | 15 maart 2024, 14:15:32 |

### Stap 3: Analyseer de body

Bij een antwoordbericht bevat de body de gevonden objecten:

```xml
<BG:antwoord>
  <BG:object StUF:entiteittype="NPS">
    <BG:inp.bsn>123456789</BG:inp.bsn>
    <BG:voornamen>Jan Pieter</BG:voornamen>
    <BG:voorvoegselGeslachtsnaam>de</BG:voorvoegselGeslachtsnaam>
    <BG:geslachtsnaam>Vries</BG:geslachtsnaam>
    <BG:geboortedatum>19850315</BG:geboortedatum>
    <BG:geslachtsaanduiding>M</BG:geslachtsaanduiding>
    <BG:verblijfsadres>
      <BG:gor.straatnaam>Kerkstraat</BG:gor.straatnaam>
      <BG:aoa.huisnummer>12</BG:aoa.huisnummer>
      <BG:aoa.huisletter xsi:nil="true" StUF:noValue="geenWaarde"/>
      <BG:aoa.postcode>3511AB</BG:aoa.postcode>
      <BG:wpl.woonplaatsNaam>Utrecht</BG:wpl.woonplaatsNaam>
    </BG:verblijfsadres>
  </BG:object>
</BG:antwoord>
```

### Stap 4: Let op bijzonderheden

Loop het bericht door en noteer opvallende zaken:

| Observatie | Betekenis |
|---|---|
| `xsi:nil="true" StUF:noValue="geenWaarde"` op `huisletter` | Er is geen huisletter – bewust leeg |
| `voorvoegselGeslachtsnaam` heeft een waarde | "de" – tussenvoegsel aanwezig |
| Geboortedatum `19850315` | Formaat jjjjMMdd, dus 15 maart 1985 |
| Eén `<BG:object>` in het antwoord | Er is precies 1 persoon gevonden |

## Het bijbehorende schema erbij pakken

Nu je het bericht hebt gelezen, wil je controleren: klopt het met het schema? En welke elementen had ik nog meer kunnen verwachten?

### De juiste schema's vinden

Vanuit het bericht kun je de schema's traceren:

1. **Namespace** `http://www.stufstandaarden.nl/koppelvlak/bg0310` → dit is het koppelvlak-schema voor StUF-BG 3.10
2. **Namespace** `http://www.egem.nl/StUF/StUF0302` → dit is de StUF-kern 3.02

De bijbehorende bestanden zijn typisch:
- `bg0310_msg_vraagAntwoord.xsd` → definieert `npsLa01`
- `bg0310_ent_basis.xsd` → definieert het NPS-type
- `stuf0302.xsd` → definieert stuurgegevens

### Elementen matchen met het schema

Open `bg0310_ent_basis.xsd` en zoek het type `NPS-basis`:

```xml
<xs:complexType name="NPS-basis">
  <xs:sequence>
    <xs:element name="inp.bsn" type="bg:BSN-e" minOccurs="0"/>
    <xs:element name="authentiek" type="bg:Authentiek-e" minOccurs="0"/>
    <xs:element name="geslachtsnaam" type="bg:Geslachtsnaam-e" minOccurs="0"/>
    <xs:element name="voorvoegselGeslachtsnaam" type="bg:Voorvoegsel-e"
                minOccurs="0" nillable="true"/>
    <xs:element name="voornamen" type="bg:Voornamen-e" minOccurs="0"/>
    <xs:element name="geboortedatum" type="bg:Datum-e" minOccurs="0"/>
    <xs:element name="geslachtsaanduiding" type="bg:Geslachtsaanduiding-e"
                minOccurs="0"/>
    <!-- ... nog veel meer elementen ... -->
    <xs:element name="verblijfsadres" type="bg:VerblijfsAdres-basis"
                minOccurs="0"/>
    <!-- ... relaties, historie, etc. ... -->
  </xs:sequence>
  <xs:attributeGroup ref="StUF:entiteitAttributen"/>
</xs:complexType>
```

Nu kun je elk element uit het bericht matchen:

| Element in bericht | Element in schema | Type | Verplicht? |
|---|---|---|---|
| `inp.bsn` = 123456789 | `inp.bsn` | `BSN-e` (9 cijfers) | Nee (minOccurs=0) |
| `voornamen` = Jan Pieter | `voornamen` | `Voornamen-e` | Nee |
| `geslachtsnaam` = Vries | `geslachtsnaam` | `Geslachtsnaam-e` | Nee |
| `geboortedatum` = 19850315 | `geboortedatum` | `Datum-e` | Nee |
| `verblijfsadres` = {...} | `verblijfsadres` | `VerblijfsAdres-basis` | Nee |

Je ziet ook dat het schema nog veel meer elementen kent (bijv. `authentiek`) die in dit bericht niet voorkomen – dat mag, want alles is optioneel in het basistype.

## Veelvoorkomende patronen in StUF-berichten

Na het lezen van meerdere StUF-berichten ga je patronen herkennen. Hier zijn de belangrijkste:

### Patroon 1: Het vraag-scope-patroon

In een vraagbericht (Lv) definieert de **scope** welke velden je terugkrijgt:

```xml
<BG:npsLv01>
  <StUF:stuurgegevens>...</StUF:stuurgegevens>

  <!-- WAT zoek ik? -->
  <BG:gelijk>
    <BG:inp.bsn>123456789</BG:inp.bsn>
  </BG:gelijk>

  <!-- WAT wil ik terugkrijgen? -->
  <BG:scope>
    <BG:object StUF:entiteittype="NPS">
      <BG:inp.bsn xsi:nil="true"/>
      <BG:voornamen xsi:nil="true"/>
      <BG:geslachtsnaam xsi:nil="true"/>
      <BG:geboortedatum xsi:nil="true"/>
    </BG:object>
  </BG:scope>
</BG:npsLv01>
```

**Vuistregel:** de scope bepaalt de "breedte" van het antwoord. Hoe meer velden in de scope, hoe meer gegevens je terugkrijgt.

### Patroon 2: Het oud-nieuw-patroon bij kennisgevingen

Bij een wijziging (`mutatiesoort="W"`) bevat het bericht twee objectblokken:

```xml
<BG:body>
  <!-- NIEUW (de huidige situatie) -->
  <BG:object StUF:entiteittype="NPS" StUF:sleutelVerzwordendeObjectSleutel="123456789">
    <BG:verblijfsadres>
      <BG:gor.straatnaam>Nieuwstraat</BG:gor.straatnaam>
      <BG:aoa.huisnummer>42</BG:aoa.huisnummer>
    </BG:verblijfsadres>
    <StUF:tijdvakGeldigheid>
      <StUF:beginGeldigheid>20240315</StUF:beginGeldigheid>
    </StUF:tijdvakGeldigheid>
  </BG:object>

  <!-- OUD (de vorige situatie) -->
  <BG:object StUF:entiteittype="NPS" StUF:sleutelVerzwordendeObjectSleutel="123456789">
    <BG:verblijfsadres>
      <BG:gor.straatnaam>Kerkstraat</BG:gor.straatnaam>
      <BG:aoa.huisnummer>12</BG:aoa.huisnummer>
    </BG:verblijfsadres>
    <StUF:tijdvakGeldigheid>
      <StUF:beginGeldigheid>20100601</StUF:beginGeldigheid>
      <StUF:eindGeldigheid>20240315</StUF:eindGeldigheid>
    </StUF:tijdvakGeldigheid>
  </BG:object>
</BG:body>
```

**Vuistregel:** het eerste object is de **nieuwe** situatie, het tweede de **oude**.  De `eindGeldigheid` van het oude object = de `beginGeldigheid` van het nieuwe.

### Patroon 3: Geneste entiteiten

StUF-berichten bevatten vaak geneste objecten – een persoon met een adres, of een zaak met een betrokkene:

```xml
<BG:object StUF:entiteittype="NPS">
  <BG:inp.bsn>123456789</BG:inp.bsn>
  <BG:geslachtsnaam>Vries</BG:geslachtsnaam>

  <!-- Genest: verblijfsadres -->
  <BG:verblijfsadres>
    <BG:aoa.identificatie>0344200000012345</BG:aoa.identificatie>
    <BG:gor.straatnaam>Kerkstraat</BG:gor.straatnaam>
    <BG:aoa.huisnummer>12</BG:aoa.huisnummer>

    <!-- Genest in adres: woonplaats -->
    <BG:wpl.woonplaatsNaam>Utrecht</BG:wpl.woonplaatsNaam>
  </BG:verblijfsadres>

  <!-- Genest: partnerentiteit -->
  <BG:inp.heeftAlsEchtgenootPartner StUF:entiteittype="NPS">
    <BG:geslachtsnaam>Bakker</BG:geslachtsnaam>
    <BG:voornamen>Maria</BG:voornamen>
  </BG:inp.heeftAlsEchtgenootPartner>

</BG:object>
```

**Vuistregel:** geneste entiteiten vertegenwoordigen **relaties**. De naamgeving (bijv. `inp.heeftAlsEchtgenootPartner`) beschrijft de aard van de relatie.

### Patroon 4: Gebruik van `noValue`

Wanneer een element bewust geen waarde heeft, zie je het `noValue`-patroon:

```xml
<BG:voorvoegselGeslachtsnaam xsi:nil="true" StUF:noValue="geenWaarde"/>
```

Varianten die je tegenkomt:

| XML | Betekenis |
|---|---|
| `<element>waarde</element>` | Element heeft een waarde |
| `<element xsi:nil="true" StUF:noValue="geenWaarde"/>` | Bewust leeg |
| `<element xsi:nil="true" StUF:noValue="waardeOnbekend"/>` | Waarde onbekend |
| Element afwezig | Niet opgenomen in dit bericht (niet gevraagd of niet beschikbaar) |

### Patroon 5: Sleutels voor objectidentificatie

Elk object heeft een sleutel waarmee het uniek geïdentificeerd kan worden:

```xml
<BG:object StUF:entiteittype="NPS"
           StUF:sleutelVerzwordendeObjectSleutel="123456789">
```

Bij personen is dit typisch het BSN. Bij adressen de BAG-identificatie. Bij zaken het zaakidentificatienummer.

| Entiteit | Sleutelveld | Voorbeeld |
|---|---|---|
| Natuurlijk Persoon (NPS) | BSN | 123456789 |
| Adres (ADR) | BAG-identificatie | 0344200000012345 |
| Zaak (ZAK) | Zaakidentificatie | ZAAK-2024-00001 |
| Document (EDC) | Documentidentificatie | DOC-2024-00001 |

## Zelf een StUF-bericht samenstellen

Laten we stap voor stap een vraagbericht samenstellen waarmee we een persoon opvragen op BSN.

### Stap 1: Kies het berichttype

We willen gegevens **opvragen** → dit is een **Lv01** (vraagbericht).
De entiteit is een **Natuurlijk Persoon** → `nps`.
Het bericht heet dus: `npsLv01`.

### Stap 2: Stel de stuurgegevens samen

```xml
<StUF:stuurgegevens>
  <StUF:berichtcode>Lv01</StUF:berichtcode>
  <StUF:zender>
    <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
    <StUF:applicatie>MijnApplicatie</StUF:applicatie>
  </StUF:zender>
  <StUF:ontvanger>
    <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
    <StUF:applicatie>BRP-koppeling</StUF:applicatie>
  </StUF:ontvanger>
  <StUF:referentienummer>MA-2024-00001</StUF:referentienummer>
  <StUF:tijdstipBericht>20240315150000</StUF:tijdstipBericht>
  <StUF:entiteittype>NPS</StUF:entiteittype>
</StUF:stuurgegevens>
```

### Stap 3: Definieer de zoekcriteria

We zoeken op BSN:

```xml
<BG:gelijk>
  <BG:inp.bsn>987654321</BG:inp.bsn>
</BG:gelijk>
```

### Stap 4: Definieer de scope

We willen naam, geboortedatum en adres:

```xml
<BG:scope>
  <BG:object StUF:entiteittype="NPS">
    <BG:inp.bsn xsi:nil="true"/>
    <BG:voornamen xsi:nil="true"/>
    <BG:voorvoegselGeslachtsnaam xsi:nil="true"/>
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
```

### Stap 5: Verpak in SOAP

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <BG:npsLv01
        xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
        xmlns:StUF="http://www.egem.nl/StUF/StUF0302"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

      <StUF:stuurgegevens>
        <StUF:berichtcode>Lv01</StUF:berichtcode>
        <StUF:zender>
          <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
          <StUF:applicatie>MijnApplicatie</StUF:applicatie>
        </StUF:zender>
        <StUF:ontvanger>
          <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
          <StUF:applicatie>BRP-koppeling</StUF:applicatie>
        </StUF:ontvanger>
        <StUF:referentienummer>MA-2024-00001</StUF:referentienummer>
        <StUF:tijdstipBericht>20240315150000</StUF:tijdstipBericht>
        <StUF:entiteittype>NPS</StUF:entiteittype>
      </StUF:stuurgegevens>

      <BG:gelijk>
        <BG:inp.bsn>987654321</BG:inp.bsn>
      </BG:gelijk>

      <BG:scope>
        <BG:object StUF:entiteittype="NPS">
          <BG:inp.bsn xsi:nil="true"/>
          <BG:voornamen xsi:nil="true"/>
          <BG:voorvoegselGeslachtsnaam xsi:nil="true"/>
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
  </soap:Body>
</soap:Envelope>
```

### Checklist voor het samenstellen

Voordat je een bericht verstuurt, controleer:

- [ ] Juiste berichtcode in stuurgegevens?
- [ ] Zender en ontvanger correct ingevuld?
- [ ] Uniek referentienummer?
- [ ] Tijdstip actueel?
- [ ] Namespace-declaraties compleet?
- [ ] Zoekcriteria logisch (bij Lv)?
- [ ] Scope volledig (bij Lv)?
- [ ] Mutatiesoort correct (bij Lk)?
- [ ] Oud én nieuw object aanwezig (bij Lk met W)?
- [ ] SOAP-envelop eromheen?

## Oefening: lees dit bericht

Bekijk het volgende bericht en beantwoord de vragen:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <BG:npsLk01
        xmlns:BG="http://www.stufstandaarden.nl/koppelvlak/bg0310"
        xmlns:StUF="http://www.egem.nl/StUF/StUF0302"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

      <StUF:stuurgegevens>
        <StUF:berichtcode>Lk01</StUF:berichtcode>
        <StUF:zender>
          <StUF:organisatie>RvIG</StUF:organisatie>
          <StUF:applicatie>BRP-distributie</StUF:applicatie>
        </StUF:zender>
        <StUF:ontvanger>
          <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
          <StUF:applicatie>Burgerzaken</StUF:applicatie>
        </StUF:ontvanger>
        <StUF:referentienummer>RVIG-2024-0088721</StUF:referentienummer>
        <StUF:tijdstipBericht>20240320093045</StUF:tijdstipBericht>
        <StUF:entiteittype>NPS</StUF:entiteittype>
      </StUF:stuurgegevens>

      <BG:parameters>
        <StUF:mutatiesoort>T</StUF:mutatiesoort>
      </BG:parameters>

      <BG:body>
        <BG:object StUF:entiteittype="NPS"
                   StUF:sleutelVerzwordendeObjectSleutel="555666777">
          <BG:inp.bsn>555666777</BG:inp.bsn>
          <BG:voornamen>Sophie Anna</BG:voornamen>
          <BG:geslachtsnaam>Mulder</BG:geslachtsnaam>
          <BG:geboortedatum>20240318</BG:geboortedatum>
          <BG:geslachtsaanduiding>V</BG:geslachtsaanduiding>
        </BG:object>
      </BG:body>
    </BG:npsLk01>
  </soap:Body>
</soap:Envelope>
```

**Vragen:**

1. Wat voor type bericht is dit?
2. Wie stuurt het en aan wie?
3. Wat is de mutatiesoort en wat betekent dat?
4. Over wie gaat dit bericht?
5. Waarom is er maar één `<object>` (en geen oud/nieuw paar)?

**Antwoorden:**

1. `npsLk01` → Een **kennisgevingsbericht** voor een Natuurlijk Persoon
2. **RvIG (BRP-distributie)** stuurt aan **Gemeente Utrecht (Burgerzaken)** – de rijksoverheid distribueert een BRP-mutatie naar de gemeente
3. Mutatiesoort **T** = **Toevoeging** – er is een nieuw persoon toegevoegd
4. **Sophie Anna Mulder**, geboren 18 maart 2024, BSN 555666777, vrouw
5. Bij een **toevoeging** is er geen "oude" situatie – het object bestond nog niet. Daarom is er slechts één objectblok.

## Samenvatting

| Vaardigheid | Aanpak |
|---|---|
| **Bericht lezen** | Berichttype → stuurgegevens → body → bijzonderheden |
| **Schema matchen** | Namespace → schemabestand → complexType → elementen vergelijken |
| **Patronen herkennen** | Vraag-scope, oud-nieuw, geneste entiteiten, noValue, sleutels |
| **Bericht schrijven** | Berichttype kiezen → stuurgegevens → criteria/data → SOAP verpakken → checklist |

> **Kernpunt:** Het lezen en schrijven van StUF-berichten is een vaardigheid die je opbouwt door oefening. De systematische aanpak – berichttype, stuurgegevens, body, schema-matching – zorgt ervoor dat je elk bericht kunt doorgronden. De patronen die je leert herkennen (oud/nieuw, scope, noValue) zijn de bouwstenen van je dagelijks werk met StUF.
