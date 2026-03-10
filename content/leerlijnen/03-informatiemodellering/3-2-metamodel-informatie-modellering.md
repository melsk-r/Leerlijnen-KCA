---
title: "3.2 Metamodel Informatie Modellering (MIM)"
date: 2026-03-04
weight: 3
leerlijn: 3
paragraaf: "3.2"
leerdoel: "Leerdoel nog toe te voegen"
---

## 3.2 Metamodel Informatie Modellering (MIM)

Kent en begrijpt het Metamodel Informatie Modellering (MIM).

### Wat is MIM?

Het **Metamodel Informatie Modellering (MIM)** is de Nederlandse standaard voor het maken van informatiemodellen. Het biedt een gemeenschappelijke taal en methodologie voor:

- **Semantische interoperabiliteit**: Eenduidige begrippenkaders
- **Modelconsistentie**: Uniforme modelleringsregels
- **Uitwisselbaarheid**: Models die verschillende tools kunnen interpreteren

### MIM-architectuur

#### Beschouwingsniveaus
MIM kent vier beschouwingsniveaus:

| Niveau | Naam | Focus | Doelgroep |
|--------|------|-------|-----------|
| **1** | Model van begrippen | Betekenis en definities | Business, beleidsmakers |
| **2** | Conceptueel model | Informatiekundige structuur | Informatieanalist |
| **3** | Logisch model | Implementatie-onafhankelijke gegevensstructuur | Gegevensarchitect |
| **4** | Fysiek model | Technische implementatie | Database-ontwerper |

#### Kernconcepten

**Basisobjecten:**
- **Objecttype**: Verzameling van objecten met gemeenschappelijke eigenschappen
- **Attribuutsoort**: Eigenschap van een objecttype
- **Relatiesoort**: Verband tussen objecttypen
- **Gegevensgroepen**: Logische groepering van attributen

**Specialisaties:**
- **Generalisatie**: Is-een relatie tussen objecttypen
- **Specialisatie**: Specifiekere variant van een algemeen objecttype
- **Compositie**: Deel-geheel relaties

### MIM in de praktijk

#### Nederlandse context
MIM wordt gebruikt in:
- **RSGB**: Referentie Semantisch Gegevensmodel Basisregistraties
- **RGBZ**: Referentie Gegevensmodel Burgerzaken
- **iWlz**: Informatiemodel Wet langdurige zorg
- **IMRO**: Informatiemodel Ruimtelijke Ordening

#### Modelleerregels
- **Naamgeving**: Nederlandse conventies voor objecten en attributen
- **Cardinaliteit**: Minimale en maximale aantallen in relaties
- **Multipliciteit**: 0..1, 1..*, etc.
- **Waardebereik**: Toegestane waarden voor attributen

### MIM-specificatie

#### Huidige versie
- **MIM versie**: 1.1.1 (vastgesteld 2022)
- **Beheerder**: Geonovum
- **Status**: Nederlandse standaard

#### Kernprincipes
1. **Platformonafhankelijk**: Niet gebonden aan specifieke technologie
2. **UML-gebaseerd**: Gebruikt UML als basisnotatie
3. **Uitbreidbaar**: Profielen voor domeinspecifieke behoeften
4. **Gestandaardiseerd**: Vaste regels en conventies

### Toolondersteuning

#### Modelleringstools
- **Enterprise Architect**: MIM-profiel beschikbaar
- **Archi**: ArchiMate-gebaseerde modellering
- **GenDoc**: Automatische documentgeneratie
- **ShacMIM**: SHACL-validatie van MIM-modellen

#### Validatie
- **Automatische controles**: Syntactische en semantische validatio
- **Kwaliteitsregels**: Consistentie en volledigheidscontroles
- **Conformiteitstoets**: Naleving van MIM-specificatie

### Leertraject MIM

**Basisniveau:**
- MIM-principes begrijpen
- Beschouwingsniveaus kennen
- Eenvoudige modellen lezen

**Gevorderd:**
- Zelf MIM-conforme modellen maken
- Validatieregels toepassen
- Tooling effectief gebruiken

**Expert:**
- Complexe domeinmodellen ontwikkelen
- MIM-uitbreidingen en profielen maken
- Bijdragen aan MIM-ontwikkeling

### Resources

**Officiële documentatie:**
- [MIM-specificatie Geonovum](https://docs.geostandaarden.nl/mim/mim/)
- [MIM Github Repository](https://github.com/Geonovum/MIM-Werkomgeving)

**Praktische handleidingen:**
- MIM in de praktijk (VNG Realisatie)
- Modelleringshandleidingen per domein
- Tooling-specifieke handleidingen

**Community:**
- MIM-gebruikersgroep
- Expertnetwerk informatiemodellering
- Geonovum kennisplatform

Het beheersen van MIM is essentieel voor het maken van kwalitatief hoogwaardige, interoperabele informatiemodellen binnen de Nederlandse overheid.