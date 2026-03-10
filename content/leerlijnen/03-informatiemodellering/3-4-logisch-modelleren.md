---
title: "3.4 Logisch modelleren (MIM niveau 3)"
date: 2026-03-04
weight: 5
leerlijn: 3
paragraaf: "3.4"
leerdoel: "Leerdoel nog toe te voegen"
---

## 3.4 Logisch modelleren (MIM niveau 3)

Kan werken op MIM-beschouwingsniveau 3 (logisch modelleren).

### Wat is logisch modelleren?

Logisch modelleren op **MIM niveau 3** vertaalt het conceptuele model naar een implementatie-onafhankelijke gegevensstructuur. Het voegt technische details toe zonder vast te leggen op een specifiek platform.

#### Verschil met conceptueel niveau:
- **Conceptueel (niveau 2)**: WAT moet worden vastgelegd
- **Logisch (niveau 3)**: HOE kan het worden gestructureerd

### Logische modelkenmerken

#### Uitgebreidere specificatie
- **Datatypes**: Concrete gegevenstypes (tekst, nummer, datum)
- **Lengtebeperkingen**: Maximale aantal karakters of cijfers
- **Formaat-specifaties**: Reguliere expressies, waardenlijsten
- **Constraintregels**: Bedrijfsregels en validatielogica

#### Technische verfijning
- **Kardinaliteiten**: Exacte minimale en maximale aantallen
- **Identificatie**: Primaire sleutels en unieke attributen
- **Referentie-integriteit**: Verwijzingen tussen objecttypen
- **Indexering**: Performance-overwegingen voor toegang

### Logische modelleerelementen

#### Verfijnde attributen
```
Attribuutsoort: BSN (Burgerservicenummer)
- Objecttype: Ingezetene
- Datatype: Numeriek
- Lengte: Exact 9 cijfers
- Patroon: [0-9]{9}
- Constraint: Elf-proef moet kloppen
- Cardinaliteit: [1..1] (verplicht en uniek)
- Authentiek: Ja (bron is BRP)
```

#### Gespecificeerde relaties
```
Relatiesoort: Ingezetene woont op Nummeraanduiding
- Bron: Ingezetene [1..*]
- Doel: Nummeraanduiding [0..1]
- Implementatie: Foreign key relatie
- Constraint: Alleen adressen binnen Nederland
- Geldigheidsperiode: Van-tot datum verplicht
```

#### Gegevensgroepen
```
Gegevensgroep: Contactgegevens
- Compositie van: Persoon
- Bevat attributen:
  - Telefoonnummer: AN20, optioneel
  - E-mailadres: AN100, formaat validatie
  - Website: URL, optioneel
```

### Transformatieregels vanuit conceptueel model

#### Objecttypen → Entiteiten
- Elk objecttype wordt een logische entiteit
- Abstracte objecttypen kunnen worden weggelaten
- Specialisaties kunnen samengevoegd worden

#### Attributen → Velden 
- Conceptuele attributen krijgen concrete datatypes
- Afleidbare attributen kunnen weggelaten worden
- Identificerende attributen worden primaire sleutels

#### Relaties → Koppelingen
- Associaties worden foreign key relaties
- Many-to-many relaties krijgen tussentabellen
- Composities worden geneste structuren

### Logische modelleringspatronen

#### Identificatiepatronen
```
Natuurlijke sleutel:
- BSN (voor personen)
- BAG-ID (voor adressen)
- KvK-nummer (voor bedrijven)

Technische sleutel:
- UUID (universally unique identifier)
- Sequence nummer (oplopende integer)
```

#### Temporele patronen
```
Geldigheidsperiode:
- Begingeldigheid: datum-tijd
- Eindgeldigheid: datum-tijd (optioneel)

State tracking:
- Aangemaakt op: timestamp
- Gewijzigd op: timestamp
- Status: waardenlijst
```

#### Referentiedatapatronen
```
Type catalogus:
- Code: AN10 (primary key)
- Omschrijving: AN100
- Definitie: tekst
- Geldig van: datum
- Geldig tot: datum (optioneel)
```

### Constraintspecificatie

#### Domeinregels
```sql
CHECK (Geboorte_datum <= CURRENT_DATE)
CHECK (Lengte_x > 0 AND Lengte_x < 1000) 
CHECK (Email LIKE '%@%.%')
```

#### Referentiële integriteit
```
FOREIGN KEY (Adres_ID) REFERENCES Nummeraanduiding(ID)
WHERE Status = 'Actief'
```

#### Bedrijfsregels
```
Een persoon kan maximaal op 1 adres ingeschreven staan
Een adres moet bestaan voordat iemand erop ingeschreven kan worden
Wijzigingen in historische records zijn niet toegestaan
```

### Kwaliteitsbewaking

#### Normalisatie
- **1NF**: Geen herhalende groepen
- **2NF**: Geen partiële afhankelijkheid van samengestelde sleutels
- **3NF**: Geen transitieve afhankelijkheden

#### Denormalisatie overwegingen
- **Performance**: Strategische redundantie voor snelheid
- **Usability**: Vereenvoudiging voor eindgebruikers 
- **Integration**: Aansluiting bij bestaande systemen

### Validatietechnieken

#### Automatische controles
- **MIM-conformiteit**: Tooling voor modelvalidatie
- **Consistentie-checks**: Kruisverwijzingen en constraints
- **Volledigheid-analyse**: Coverage van functionele requirements

#### Review-processen
- **Technical review**: Architecten en database-specialisten
- **Domain review**: Functionele experts en data-eigenaren
- **Implementation review**: Ontwikkelaars en system integrators

### Documentatie-eisen

#### Technische specificatie
- **Datatype-definities**: Precieze formaten en beperkingen
- **Constraint-catalogus**: Alle bedrijfs- en technische regels
- **Indexering-strategie**: Performance-optimalisatie

#### Implementatie-guidance
- **Mapping naar platforms**: Hoe het model te implementeren in verschillende databases
- **Migratie-strategie**: Hoe bestaande data over te zetten
- **Test-scenario's**: Hoe het model te valideren

### Tools voor logisch modelleren

#### Database-ontwerp tools
- **ER/Studio**: Enterprise data modellering
- **PowerDesigner**: Comprehensive modellering platform
- **Oracle SQL Developer Data Modeler**: Oracle-specifiek
- **MySQL Workbench**: MySQL-gericht

#### Validatie-tools
- **Schema-validatoren**: Automatische MIM-controle
- **Data-profiling tools**: Analyse van bestaande datasets
- **Constraint-checkers**: Validatie van bedrijfsregels

### Uitdagingen logisch modelleren

#### Platform-onafhankelijkheid behouden
- **Vermijd**: Database-specifieke datatypes
- **Gebruik**: Standaard MIM-datatypes
- **Focus**: Op logische structuur, niet implementatie

#### Performance vs Puurheid
- **Balans**: Tussen gegevenskwaliteit en systeemprestaties
- **Documenteer**: Afwijkingen van standaardpatronen
- **Rechtvaardigen**: Technische beslissingen expliciet

#### Legacy-integratie
- **Analyseer**: Bestaande datastructuren
- **Map**: Oude naar nieuwe modellen
- **Migreer**: Geleidelijk en gecontroleerd

Logisch modelleren vereist zowel conceptueel inzicht als technische kennis. Het is de brug tussen businessbehoeften en technische implementatie, waarbij kwaliteit en implementeerbaarheid in balans moeten blijven.