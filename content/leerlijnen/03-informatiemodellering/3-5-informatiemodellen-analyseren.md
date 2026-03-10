---
title: "3.5 Informatiemodellen analyseren en wijzigen"
date: 2026-03-04
weight: 6
leerlijn: 3
paragraaf: "3.5"
leerdoel: "Leerdoel nog toe te voegen"
---

## 3.5 Informatiemodellen analyseren en wijzigen

Kan bestaande informatiemodellen analyseren en wijzigingsvoorstellen doen.

### Analyse van bestaande modellen

De analyse van informatiemodellen vereist een systematische aanpak om kwaliteit, actualiteit en geschiktheid voor het doel te beoordelen.

#### Analysedimensies

**1. Structurele kwaliteit**
- **Volledigheid**: Zijn alle benodigde objecttypen, attributen en relaties aanwezig?
- **Consistentie**: Zijn er conflicterende definities of dubbelingen?  
- **Coherentie**: Hangen de modelelementen logisch samen?

**2. Semantische kwaliteit**
- **Begrippenduidelijkheid**: Zijn definities eenduidig en begrijpelijk?
- **Domeindepping**: Is de werkelijkheidsrepresentatie accuraat?
- **Terminologie**: Is naamgeving consistent en herkenbaar?

**3. Technische kwaliteit** 
- **MIM-conformiteit**: Volgt het model de MIM-standaard?
- **Normalisatie**: Is datastructuur geoptimaliseerd?
- **Implementeerbaarheid**: Is het model praktisch realiseerbaar?

### Analysetechnieken

#### Model review checklist

**Structureel niveau:**
```
□ Alle hoofdobjecttypen geïdentificeerd
□ Primaire identificatie voor elk objecttype
□ Relaties volledig gespecificeerd (cardinaliteit)
□ Geen weesattributen of -objecttypen
□ Generalisatie/specialisatie-hiërarchie logisch
```

**Semantisch niveau:**
```
□ Alle objecttypen hebben adequate definitie
□ Attributen hebben herkomst-specificatie
□ Waardenlijsten compleet en actueel
□ Synoniemen en homonomen geïdentificeerd  
□ Bedrijfsregels expliciet vastgelegd
```

**Technisch niveau:**
```
□ Datatypes correct gespecificeerd
□ Lengtebeperkingen realistisch
□ Formaat-constraints gedefinieerd
□ Performance-impact geanalyseerd
□ Integratie-mogelijkheden onderzocht
```

#### Gap-analyse

**Vergelijken met requirementen:**
1. **Functionele dekking**: Ondersteunt het model alle use cases?
2. **Non-functionele eisen**: Performance, schaalbaarheid, security
3. **Integratievereisten**: Aansluiting op andere systemen
4. **Compliance**: Wet- en regelgeving, privacy, auditeerbaarheid

**Vergelijken met standaarden:**
- **RSGB**: Voor basisregistratie-objecttypen
- **Sectormodellen**: Domeinspecifieke referentiemodellen
- **Internationale standaarden**: Voor interoperabiliteit

### Wijzigingstypen

#### Correctieve wijzigingen
- **Herstel van fouten**: Onjuiste definities, datatypes, cardinaliteiten
- **Consistency-fixes**: Oplossen van modelconflicten
- **Compliance-updates**: Afstemming op nieuwe wet- en regelgeving

#### Adaptieve wijzigingen  
- **Scope-uitbreiding**: Toevoegen van nieuwe objecttypen of attributen
- **Detailniveau-aanpassing**: Verfijning of vereenvoudiging van het model
- **Standaard-alignment**: Afstemming op nieuwe versies van MIM of RSGB

#### Perfectieve wijzigingen
- **Optimalisatie**: Verbetering van performance-eigenschappen
- **Usability-enhancement**: Gebruiksvriendelijker maken
- **Documentatie-verbetering**: Duidelijker definities en voorbeelden

### Wijzigingsproces

#### 1. Impact-analyse
```
Wijzigingsvoorstel: Toevoegen attribuut 'E-mailadres' aan Persoon

Impact-analyse:
- Bestaande implementaties: 3 systemen moeten aangepast
- Datamigratie: Nieuwe velden vullen of leeg laten
- Interfaces: StUF-berichten uitbreiden
- Tijdsinvestering: 40 uur development + testing

Risico's:
- Breaking change voor bestaande interfaces
- Data-inconsistentie tijdens transitie
- Extra validatie-complexiteit
```

#### 2. Stakeholder-consultatie
- **Functioneel beheer**: Acceptatie van wijzigingsvoorstel
- **Technisch beheer**: Haalbaarheid en implementatie-strategie  
- **Gebruikers**: Impact op werkprocessen
- **Leveranciers**: Aanpassingen in applicaties

#### 3. Versiebeheer
**Semantic versioning:**
- **Major (X.0.0)**: Breaking changes, incompatibel
- **Minor (x.Y.0)**: Backward compatible extensies  
- **Patch (x.y.Z)**: Bug fixes, geen functionaliteitswijziging

**Transitiestrategie:**
- **Parallelle versies**: Oude en nieuwe model tijdelijk naast elkaar
- **Geleidelijke migratie**: Stapsgewijs overgaan naar nieuwe versie
- **Deprecated-markering**: Oude elementen markeren als vervallen

### Praktijkvoorbeeld: RSGB-analyse

**Aanleiding:** Nieuwe AVG-vereisten voor privacymarkering

**Analyse:**
```
Huidige situatie (RSGB 3.0):
- Geen privacy-classificatie attributen
- Geen consent-tracking objecttypen
- Retention-regels niet gemodelleerd

Gewenste situatie:
- Privacy-impact per attribuutsoort
- Consent-objecttype voor toestemmingen  
- Retention-policy gekoppeld aan objecttypen
```

**Wijzigingsvoorstel:**
1. **Uitbreiden attribuutsoort** met privacy-classificatie
2. **Nieuw objecttype** 'Toestemming' toevoegen
3. **Relatie** tussen Persoon en Toestemming  
4. **Enumeratie** voor privacy-categorieën

### Tools voor model-analyse

#### Analyse-tools
- **Model-validators**: Automatische MIM-conformiteitscontrole
- **Dependency-analyzers**: Inzicht in model-samenhang
- **Diff-tools**: Vergelijking tussen modelversies
- **Metrics-calculators**: Complexity en quality metrics

#### Visualisatie-tools
- **Model-browsers**: Navigeerbare weergave van grote modellen
- **Impact-diagrams**: Visualisatie van wijzigingseffecten
- **Coverage-maps**: Dekking van requirementen door model

### Uitdagingen bij model-analyse

#### Complexiteit beheersen
- **Modularisatie**: Opdelen in beheersbare delen
- **Abstractieniveaus**: Verschillende detailniveaus hanteren
- **Focus**: Prioriteren van belangrijkste aspecten

#### Evolutie vs Stabiliteit
- **Balans**: Tussen doorontwikkeling en backward compatibility
- **Communicatie**: Duidelijke roadmaps en deprecation-schedules
- **Ondersteuning**: Migratie-tools en -documentatie

#### Stakeholder-management
- **Verwachtingen**: Managen van verschillende belangen
- **Timing**: Coördineren van wijzigingsmomenten
- **Consensus**: Bereiken van akkoord over wijzigingen

### Best practices

**Structureel:**
- Gebruik geautomatiseerde validatie waar mogelijk
- Documenteer alle wijzigingsbeslissingen
- Hanteer consistente review-procedures

**Procesmatig:**
- Betrekt alle relevante stakeholders tijdig
- Maak impact-evaluaties expliciet en transparant  
- Plan voldoende tijd voor testing en validatie

**Communicatief:**
- Gebruik heldere wijzigingslogboeken
- Creëer begrijpelijke migratieguides
- Organiseer kennisoverdracht-sessies

Het analyseren en wijzigen van informatiemodellen vereist zowel technische als communicatieve vaardigheden. Het is een continue proces van balanceren tussen stabiliteit en vernieuwing, waarbij kwaliteit en bruikbaarheid centraal staan.