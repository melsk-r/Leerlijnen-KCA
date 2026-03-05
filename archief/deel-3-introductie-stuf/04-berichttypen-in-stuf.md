---
title: "3.4 Berichttypen in StUF"
weight: 4
---

# 3.4 Berichttypen in StUF

In het vorige hoofdstuk heb je de opbouw van een StUF-bericht van buiten naar binnen leren kennen. Je hebt gezien dat de naam van het berichtelement aangeeft om welk **type bericht** het gaat (bijv. `npsLk01`, `npsLv01`). In dit hoofdstuk gaan we systematisch alle berichttypen behandelen: wat ze doen, wanneer je ze gebruikt, en hoe ze eruit zien.

## Overzicht van berichttypen

StUF kent de volgende berichttypen:

| Code | Naam | Richting | Doel |
|---|---|---|---|
| **Lk** | Kennisgeving | Asynchroon, push | Wijziging doorgeven |
| **Lv** | Vraagbericht | Synchroon, request | Gegevens opvragen |
| **La** | Antwoordbericht | Synchroon, response | Antwoord op vraag |
| **Di** | Vrij bericht (inkomend) | Asynchroon of synchroon | Specifieke dienst aanroepen |
| **Du** | Vrij bericht (uitgaand) | Asynchroon of synchroon | Antwoord op vrij bericht |
| **Fo** | Foutbericht | Response | Fout melden |
| **Bv** | Bevestigingsbericht | Asynchroon, response | Ontvangst bevestigen |

De berichttypen kunnen we groeperen in drie categorieën:

```
┌────────────────────────────────┐
│ 1. KENNISGEVINGEN (push)       │
│    Lk ──→ Bv                   │
│    "Ik meld je een wijziging"  │
├────────────────────────────────┤
│ 2. VRAAG/ANTWOORD (pull)       │
│    Lv ──→ La (of Fo)           │
│    "Ik vraag, jij antwoordt"   │
├────────────────────────────────┤
│ 3. VRIJE BERICHTEN             │
│    Di ──→ Du (of Fo)           │
│    "Ik vraag een dienst"       │
└────────────────────────────────┘
```

Laten we elk type in detail bekijken.

## Kennisgevingsberichten (Lk)

### Wat is een kennisgeving?

Een **kennisgeving** (Lk = *Lees kennisgeving*) is een bericht waarmee een systeem een wijziging **pusht** naar een ander systeem. Het initiatief ligt bij de zender: "Er is iets veranderd in mijn administratie, en ik laat het je weten."

### Wanneer gebruik je een kennisgeving?

- Een persoon verhuist → het BRP-systeem stuurt een kennisgeving naar alle afnemers
- Een nieuw adres wordt aangemaakt in de BAG → kennisgeving naar gemeentelijke systemen
- Een zaak wordt afgesloten → kennisgeving naar archiefsysteem

### Varianten

| Bericht | Versie | Kenmerken |
|---|---|---|
| `Lk01` | Standaard | Kennisgeving met de gewijzigde gegevens |
| `Lk02` | Uitgebreid | Kennisgeving met meer detail of andere structuur |

### Structuur van een Lk01

```xml
<BG:npsLk01>
  <StUF:stuurgegevens>
    <StUF:berichtcode>Lk01</StUF:berichtcode>
    <StUF:zender>...</StUF:zender>
    <StUF:ontvanger>...</StUF:ontvanger>
    <StUF:referentienummer>REF-001</StUF:referentienummer>
    <StUF:tijdstipBericht>20240115103000</StUF:tijdstipBericht>
    <StUF:entiteittype>NPS</StUF:entiteittype>
  </StUF:stuurgegevens>

  <BG:parameters>
    <StUF:mutatiesoort>W</StUF:mutatiesoort>
  </BG:parameters>

  <BG:body>
    <!-- Nieuw object (de nieuwe situatie) -->
    <BG:object StUF:entiteittype="NPS" StUF:sleutelVerzwordendeObjectSleutel="123456789">
      <BG:geslachtsnaam>Bakker-Jansen</BG:geslachtsnaam>
      <!-- ... meer velden ... -->
    </BG:object>

    <!-- Oud object (de vorige situatie) -->
    <BG:object StUF:entiteittype="NPS" StUF:sleutelVerzwordendeObjectSleutel="123456789">
      <BG:geslachtsnaam>Bakker</BG:geslachtsnaam>
      <!-- ... meer velden ... -->
    </BG:object>
  </BG:body>
</BG:npsLk01>
```

### Communicatiepatroon

```
Zender                              Ontvanger
  │                                     │
  │── npsLk01 (kennisgeving) ─────────→ │
  │                                     │── verwerkt
  │←── Bv01 (bevestiging) ────────────  │
  │                                     │
```

De ontvanger bevestigt de ontvangst met een `Bv01`-bericht. De zender weet dan dat het bericht is aangekomen. De bevestiging zegt **niet** dat de verwerking geslaagd is – alleen dat het bericht ontvangen is.

## Vraag- en antwoordberichten (Lv / La)

### Wat is een vraagbericht?

Een **vraagbericht** (Lv = *Lees vraag*) is een synchrone bevraging: "Geef me gegevens die voldoen aan deze criteria." Het antwoord komt direct terug in een **antwoordbericht** (La = *Lees antwoord*).

### Wanneer gebruik je vraag/antwoord?

- Een burgerzaken-medewerker zoekt een persoon op BSN
- Een vergunningensysteem haalt adresgegevens op
- Een callcenter-applicatie zoekt een persoon op naam en geboortedatum

### Het vraagbericht (Lv01)

```xml
<BG:npsLv01>
  <StUF:stuurgegevens>
    <StUF:berichtcode>Lv01</StUF:berichtcode>
    <StUF:zender>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>Vergunningensysteem</StUF:applicatie>
    </StUF:zender>
    <StUF:ontvanger>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>BRP-koppeling</StUF:applicatie>
    </StUF:ontvanger>
    <StUF:referentienummer>VRG-2024-00987</StUF:referentienummer>
    <StUF:tijdstipBericht>20240115140000</StUF:tijdstipBericht>
    <StUF:entiteittype>NPS</StUF:entiteittype>
  </StUF:stuurgegevens>

  <!-- Zoekcriteria -->
  <BG:gelijk>
    <BG:inp.bsn>123456789</BG:inp.bsn>
  </BG:gelijk>

  <!-- Welke gegevens wil ik terug? -->
  <BG:scope>
    <BG:object StUF:entiteittype="NPS">
      <BG:inp.bsn xsi:nil="true"/>
      <BG:voornamen xsi:nil="true"/>
      <BG:geslachtsnaam xsi:nil="true"/>
      <BG:voorvoegselGeslachtsnaam xsi:nil="true"/>
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

### De onderdelen van een vraagbericht

| Onderdeel | Functie |
|---|---|
| **Stuurgegevens** | Wie vraagt, aan wie, wanneer |
| **Zoekcriteria** (`<gelijk>`, `<vanaf>`, etc.) | Waarop wordt gezocht |
| **Scope** | Welke velden (attributen) wil je terugkrijgen |

De **scope** is bijzonder: je definieert een "leeg" object waarbij de gevraagde velden met `xsi:nil="true"` zijn gemarkeerd. Dit vertelt de ontvanger: "Vul deze velden in je antwoord."

### Het antwoordbericht (La01)

```xml
<BG:npsLa01>
  <StUF:stuurgegevens>
    <StUF:berichtcode>La01</StUF:berichtcode>
    <StUF:zender>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>BRP-koppeling</StUF:applicatie>
    </StUF:zender>
    <StUF:ontvanger>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>Vergunningensysteem</StUF:applicatie>
    </StUF:ontvanger>
    <StUF:referentienummer>BRP-2024-00123</StUF:referentienummer>
    <StUF:crossRefnummer>VRG-2024-00987</StUF:crossRefnummer>
    <StUF:tijdstipBericht>20240115140001</StUF:tijdstipBericht>
    <StUF:entiteittype>NPS</StUF:entiteittype>
  </StUF:stuurgegevens>

  <!-- Resultaat -->
  <BG:antwoord>
    <BG:object StUF:entiteittype="NPS">
      <BG:inp.bsn>123456789</BG:inp.bsn>
      <BG:voornamen>Jan Pieter</BG:voornamen>
      <BG:voorvoegselGeslachtsnaam>de</BG:voorvoegselGeslachtsnaam>
      <BG:geslachtsnaam>Vries</BG:geslachtsnaam>
      <BG:geboortedatum>19850315</BG:geboortedatum>
      <BG:verblijfsadres>
        <BG:gor.straatnaam>Kerkstraat</BG:gor.straatnaam>
        <BG:aoa.huisnummer>12</BG:aoa.huisnummer>
        <BG:aoa.postcode>3511AB</BG:aoa.postcode>
        <BG:wpl.woonplaatsNaam>Utrecht</BG:wpl.woonplaatsNaam>
      </BG:verblijfsadres>
    </BG:object>
  </BG:antwoord>

</BG:npsLa01>
```

Merk op:
- De `crossRefnummer` verwijst naar de `referentienummer` van het vraagbericht → zo worden vraag en antwoord aan elkaar gekoppeld
- Het antwoord bevat precies de velden die in de scope waren opgegeven
- Zender en ontvanger zijn omgedraaid ten opzichte van het vraagbericht

### Communicatiepatroon

```
Vrager                              Bevraagde
  │                                     │
  │── npsLv01 (vraag) ────────────────→ │
  │                                     │── zoekt op
  │←── npsLa01 (antwoord) ────────────  │
  │                                     │
```

Bij een fout:

```
Vrager                              Bevraagde
  │                                     │
  │── npsLv01 (vraag) ────────────────→ │
  │                                     │── fout!
  │←── Fo01 (foutbericht) ────────────  │
  │                                     │
```

### Meerdere resultaten

Een vraag kan meerdere resultaten opleveren:

```xml
<BG:npsLa01>
  <StUF:stuurgegevens>...</StUF:stuurgegevens>

  <BG:antwoord>
    <BG:object StUF:entiteittype="NPS">
      <BG:inp.bsn>111111111</BG:inp.bsn>
      <BG:voornamen>Jan</BG:voornamen>
      <BG:geslachtsnaam>Bakker</BG:geslachtsnaam>
      <!-- ... -->
    </BG:object>
    <BG:object StUF:entiteittype="NPS">
      <BG:inp.bsn>222222222</BG:inp.bsn>
      <BG:voornamen>Pieter Jan</BG:voornamen>
      <BG:geslachtsnaam>Bakker</BG:geslachtsnaam>
      <!-- ... -->
    </BG:object>
  </BG:antwoord>
</BG:npsLa01>
```

## Vrije berichten (Di / Du)

### Wat zijn vrije berichten?

**Vrije berichten** zijn berichten voor diensten die niet standaard in het kennisgevings- of vraag/antwoord-patroon passen. Ze bieden flexibiliteit voor specifieke use-cases.

| Code | Naam | Richting |
|---|---|---|
| `Di01` / `Di02` | Vrij bericht (inkomend) | Van aanvrager naar dienstverlener |
| `Du01` / `Du02` | Vrij bericht (uitgaand) | Van dienstverlener naar aanvrager |

### Voorbeelden van vrije berichten

| Dienst | Inkomend (Di) | Uitgaand (Du) |
|---|---|---|
| Selectie van personen op basis van complexe criteria | Di01 met selectiecriteria | Du01 met resultaat |
| Genereren van een overzicht | Di01 met parameters | Du01 met het overzicht |
| Starten van een proces | Di01 met startgegevens | Du01 met bevestiging |

### Structuur

De structuur van vrije berichten is minder vast dan die van Lk/Lv/La – het hangt af van de specifieke dienst. Maar de basisstructuur (stuurgegevens + body) is hetzelfde:

```xml
<BG:genereerOverzichtDi01>
  <StUF:stuurgegevens>
    <StUF:berichtcode>Di01</StUF:berichtcode>
    <!-- ... -->
  </StUF:stuurgegevens>

  <BG:body>
    <!-- Dienstspecifieke inhoud -->
    <BG:peildatum>20240115</BG:peildatum>
    <BG:selectie>
      <BG:woonplaats>Utrecht</BG:woonplaats>
    </BG:selectie>
  </BG:body>
</BG:genereerOverzichtDi01>
```

### Communicatiepatroon

```
Aanvrager                           Dienstverlener
  │                                       │
  │── Di01 (verzoek) ──────────────────→  │
  │                                       │── verwerkt
  │←── Du01 (resultaat) ──────────────    │
  │                                       │
```

Of asynchroon:

```
Aanvrager                           Dienstverlener
  │                                       │
  │── Di01 (verzoek) ──────────────────→  │
  │                                       │── Bv01 (bevestiging)
  │←── Bv01 ──────────────────────────    │
  │                                       │── verwerkt (later)
  │←── Du01 (resultaat) ──────────────    │
  │                                       │
```

## Foutberichten (Fo)

### Wat is een foutbericht?

Wanneer een systeem een bericht **niet kan verwerken**, stuurt het een **foutbericht** (Fo) terug. Dit kan bij elk type inkomend bericht voorkomen.

### Structuur van een foutbericht

```xml
<StUF:Fo01 xmlns:StUF="http://www.egem.nl/StUF/StUF0302">
  <StUF:stuurgegevens>
    <StUF:berichtcode>Fo01</StUF:berichtcode>
    <StUF:zender>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>BRP-koppeling</StUF:applicatie>
    </StUF:zender>
    <StUF:ontvanger>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>Vergunningensysteem</StUF:applicatie>
    </StUF:ontvanger>
    <StUF:referentienummer>BRP-ERR-001</StUF:referentienummer>
    <StUF:crossRefnummer>VRG-2024-00987</StUF:crossRefnummer>
    <StUF:tijdstipBericht>20240115140002</StUF:tijdstipBericht>
  </StUF:stuurgegevens>

  <StUF:body>
    <StUF:code>StUF058</StUF:code>
    <StUF:plek>server</StUF:plek>
    <StUF:omschrijving>Geen resultaat gevonden voor de opgegeven zoekcriteria</StUF:omschrijving>
    <StUF:details>BSN 999999999 niet gevonden in BRP</StUF:details>
  </StUF:body>
</StUF:Fo01>
```

### Elementen in een foutbericht

| Element | Beschrijving |
|---|---|
| `code` | Een foutcode (gestandaardiseerd in StUF) |
| `plek` | Waar de fout is opgetreden: `client` of `server` |
| `omschrijving` | Korte beschrijving van de fout |
| `details` | Gedetailleerde informatie over de fout |

### Veelvoorkomende foutcodes

| Code | Betekenis |
|---|---|
| `StUF001` | Berichtstructuur onjuist |
| `StUF010` | Ongeldige berichtcode |
| `StUF055` | Geen autorisatie |
| `StUF058` | Geen resultaat gevonden |
| `StUF063` | Technische fout bij verwerking |
| `StUF064` | Time-out bij verwerking |

### Wanneer welke `plek`?

| Plek | Betekenis | Voorbeeld |
|---|---|---|
| `client` | De fout zit in het **verzoek** (het inkomende bericht is onjuist) | Ongeldige XML, verplicht veld ontbreekt |
| `server` | De fout zit bij de **verwerking** (het verzoek was correct maar de verwerking faalde) | Database niet bereikbaar, BSN niet gevonden |

## Bevestigingsberichten (Bv)

### Wat is een bevestigingsbericht?

Een **bevestigingsbericht** (Bv = *Bevestiging*) bevestigt de **ontvangst** van een asynchroon bericht. Het wordt teruggestuurd als reactie op een kennisgeving (Lk) of een asynchroon vrij bericht (Di).

### Belangrijk onderscheid

| | Bv (bevestiging) | La (antwoord) |
|---|---|---|
| **Reageert op** | Lk (kennisgeving) | Lv (vraag) |
| **Communicatie** | Asynchroon | Synchroon |
| **Bevat gegevens?** | Nee (alleen bevestiging) | Ja (de gevraagde data) |
| **Betekenis** | "Ik heb je bericht ontvangen" | "Hier is het antwoord op je vraag" |

### Structuur van een bevestigingsbericht

```xml
<StUF:Bv01 xmlns:StUF="http://www.egem.nl/StUF/StUF0302">
  <StUF:stuurgegevens>
    <StUF:berichtcode>Bv01</StUF:berichtcode>
    <StUF:zender>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>Belastingsysteem</StUF:applicatie>
    </StUF:zender>
    <StUF:ontvanger>
      <StUF:organisatie>Gemeente Utrecht</StUF:organisatie>
      <StUF:applicatie>BRP-koppeling</StUF:applicatie>
    </StUF:ontvanger>
    <StUF:referentienummer>BEL-ACK-001</StUF:referentienummer>
    <StUF:crossRefnummer>BRP-2024-00543210</StUF:crossRefnummer>
    <StUF:tijdstipBericht>20240115103005</StUF:tijdstipBericht>
  </StUF:stuurgegevens>
</StUF:Bv01>
```

De `crossRefnummer` verwijst naar de `referentienummer` van het ontvangen kennisgevingsbericht.

## Interactiepatronen samengevat

### Patroon 1: Synchrone bevraging

```
 Vragende applicatie                 Leverende applicatie
       │                                    │
  ┌────┤                                    │
  │    │── [entiteit]Lv01 ───────────────→  │
  │    │              (vraag)               │── zoekt op
  │    │                                    │
  │    │←── [entiteit]La01 ──────────────   │
  │    │              (antwoord)            │
  │    │                                    │
  │    │  OF bij fout:                      │
  │    │←── Fo01 ────────────────────────   │
  └────┤              (foutbericht)         │
       │                                    │
```

### Patroon 2: Asynchrone kennisgeving

```
 Zendende applicatie                 Ontvangende applicatie
       │                                    │
  ┌────┤                                    │
  │    │── [entiteit]Lk01 ───────────────→  │
  │    │          (kennisgeving)            │
  │    │                                    │── ontvangt
  │    │←── Bv01 ────────────────────────   │
  │    │          (bevestiging)             │
  └────┤                                    │── verwerkt (async)
       │                                    │
```

### Patroon 3: Vrij bericht (synchroon)

```
 Aanvragende applicatie              Dienst-applicatie
       │                                    │
  ┌────┤                                    │
  │    │── [dienst]Di01 ─────────────────→  │
  │    │          (verzoek)                 │── verwerkt
  │    │                                    │
  │    │←── [dienst]Du01 ────────────────   │
  │    │          (resultaat)               │
  └────┤                                    │
       │                                    │
```

## Berichttypen en entiteiten gecombineerd

In de praktijk krijg je combinaties van entiteiten en berichttypen. Hier een overzicht voor de meest voorkomende:

| Entiteit | Lk (kennisgeving) | Lv (vraag) | La (antwoord) |
|---|---|---|---|
| Natuurlijk Persoon (NPS) | `npsLk01` | `npsLv01` | `npsLa01` |
| Adres (ADR) | `adrLk01` | `adrLv01` | `adrLa01` |
| Zaak (ZAK) | `zakLk01` | `zakLv01` | `zakLa01` |
| Document (EDC) | `edcLk01` | `edcLv01` | `edcLa01` |
| Maatschappelijke Activiteit (MAC) | `macLk01` | `macLv01` | `macLa01` |

En de generieke berichten:

| Bericht | Entiteit-onafhankelijk? |
|---|---|
| `Fo01` | Ja – kan antwoord zijn op elk type bericht |
| `Bv01` | Ja – bevestigt ontvangst van elk asynchroon bericht |

## Samenvatting

| Berichttype | Code | Patroon | Functie |
|---|---|---|---|
| **Kennisgeving** | Lk01/Lk02 | Asynchroon (push) | Wijziging doorgeven (T/W/V/E) |
| **Vraagbericht** | Lv01 | Synchroon (request) | Gegevens opvragen met zoekcriteria + scope |
| **Antwoordbericht** | La01 | Synchroon (response) | Gevonden gegevens retourneren |
| **Vrij bericht (in)** | Di01/Di02 | Beide | Specifieke dienst aanroepen |
| **Vrij bericht (uit)** | Du01/Du02 | Beide | Resultaat van dienst retourneren |
| **Foutbericht** | Fo01 | Response | Fout melden (code, plek, omschrijving) |
| **Bevestiging** | Bv01 | Asynchroon (ack) | Ontvangst bevestigen |

> **Kernpunt:** StUF biedt een compleet berichtenrepertoire: kennisgevingen voor het doorgeven van mutaties, vraag/antwoord voor het bevragen van gegevens, vrije berichten voor specifieke diensten, foutberichten voor foutafhandeling, en bevestigingen voor asynchrone acknowledgement. De combinatie van berichttype + entiteit (bijv. `npsLk01`) maakt direct duidelijk waar een bericht over gaat en wat het bedoelt te doen.
