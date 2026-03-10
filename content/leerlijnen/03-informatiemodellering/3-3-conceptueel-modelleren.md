---
title: "3.3 Conceptueel modelleren (MIM niveau 2)"
date: 2026-03-04
weight: 4
leerlijn: 3
paragraaf: "3.3"
leerdoel: "Leerdoel nog toe te voegen"
---

## 3.3 Conceptueel modelleren (MIM niveau 2)

Kan werken op MIM-beschouwingsniveau 2 (conceptueel modelleren).

### Wat is conceptueel modelleren?

Conceptueel modelleren op **MIM niveau 2** richt zich op het vastleggen van de informatiekundige structuur, onafhankelijk van technische implementatie. Het vertaalt businessbehoeften naar een formeel informatiemodel.

#### Doelen van conceptueel modelleren:
- **Communicatie**: Gemeenschappelijke taal tussen business en IT
- **Analyse**: Begrijpen van informatiestromen en -behoeften  
- **Ontwerp**: Basis voor verdere uitwerking naar logische en fysieke modellen
- **Validatie**: Toetsen van volledigheid en consistentie

### MIM niveau 2 kenmerken

#### Focus op betekenis
- **Wat** moet worden geregistreerd (niet hoe)
- **Waarom** is informatie relevant
- **Welke** samenhangen bestaan tussen gegevens

#### Abstractieniveau
- Implementatie-onafhankelijk
- Business-georiënteerd
- Platform-neutraal

### Conceptuele modelleerelementen

#### Objecttypen
Objecttypen representeren verzamelingen van objecten uit de werkelijkheid:

```
Objecttype: Persoon
- Definitie: Een menselijk wezen
- Toelichting: Zowel inwoners als niet-inwoners van Nederland
- Herkomst: Basisregistratie Personen (BRP)
```

#### Attributen
Eigenschappen die objecttypen karakteriseren:

```
Attribuutsoort: Geboortedatum
- Objecttype: Persoon  
- Definitie: De datum waarop een persoon is geboren
- Formaat: Datum (JJJJ-MM-DD)
- Cardinaliteit: [1..1] (verplicht, enkelvoudig)
```

#### Relaties
Verbanden tussen objecttypen:

```
Relatiesoort: Persoon heeft Verblijfplaats
- Bron: Persoon
- Doel: Verblijfsplaats
- Definitie: De plaats waar een persoon zijn/haar hoofdverblijf heeft
- Cardinaliteit: [0..1] (optioneel, enkelvoudig)
```

### Modelleringsproces

#### 1. Domeinanalyse
- **Scope bepalen**: Welk deel van de werkelijkheid modelleren?
- **Stakeholders identificeren**: Wie gebruikt het model?
- **Use cases**: Waarvoor wordt het model gebruikt?

#### 2. Begrippenkader
- **Woordenboek**: Definities van relevante termen
- **Synoniemen**: Verschillende benamingen voor hetzelfde concept
- **Relaties tussen begrippen**: Hiërarchie en samenhang

#### 3. Objectidentificatie
- **Hoofdobjecten**: Centrale entiteiten in het domein
- **Ondersteunende objecten**: Referentiegegevens en classificaties
- **Relatieobjecten**: Objecten die relaties tussen anderen representeren

#### 4. Structuurmodellering
- **Associaties**: Directe verbanden tussen objecttypen
- **Generalisaties**: Is-een relaties en specialisaties
- **Composities**: Deel-geheel relaties

### Kwaliteitseisen

#### Volledigheid
- Alle relevante objecttypen opgenomen
- Attributen compleet gedefinieerd
- Relaties volledig uitgewerkt

#### Consistentie  
- Geen conflicterende definities
- Uniforme naamgeving
- Logische samenhang

#### Verstaanbaarheid
- Duidelijke definities
- Begrijpelijke diagrammen
- Adequate documentatie

### Praktijkvoorbeeld: Burgerzaken

```
Objecttypen:
- Natuurlijk Persoon
- Ingezetene  
- Niet-ingezetene
- Verblijfplaats
- Woonadres

Generalisaties:
Natuurlijk Persoon
├── Ingezetene
└── Niet-ingezetene

Relaties:
- Natuurlijk Persoon heeft Verblijfplaats
- Verblijfplaats heeft Woonadres
- Natuurlijk Persoon heeft Ouders (zelf-referentie)
```

### Tools voor conceptueel modelleren

#### Enterprise Architect
- **MIM-profiel**: Ingebouwde ondersteuning voor MIM
- **UML-diagrammen**: Class diagrams voor objectmodellen
- **RTF-generatie**: Automatische documentatie

#### Archi
- **ArchiMate**: Business en informatielagen
- **Open source**: Gratis beschikbaar
- **Visualisaties**: Verschillende diagramtypes

### Validatietechnieken

#### Walkthrough-sessies
- Doorlopen van use cases met het model
- Validatie met domeinexperts
- Identificatie van ontbrekende elementen

#### Peer review
- Controle door collega-modelleurs
- Toetsing op MIM-conformiteit
- Feedback op modelkwaliteit

#### Prototyping
- Maken van eenvoudige implementatie
- Testen met voorbeelddata
- Validatie van beheerprocessen

### Veelvoorkomende uitdagingen

#### Abstractieniveau
- **Te concreet**: Implementatiedetails sluipen erin
- **Te abstract**: Model wordt onbruikbaar voor implementatie
- **Oplossing**: Focus op informatiebehoeften, niet IT-oplossingen

#### Scope creep
- **Probleem**: Model wordt te breed en complex
- **Oplossing**: Strikte scope en iteratieve aanpak

#### Stakeholder alignment
- **Probleem**: Verschillende interpretaties van begrippen
- **Oplossing**: Explicite definitievalidatie en afstemming

Conceptueel modelleren op MIM niveau 2 vereist een goede balans tussen business-begrip en formele modelleringstechnieken. Het is de cruciale schakel tussen organisatiebehoeften en technische implementatie.