---
title: "3.9 Adviseren aan MIM-community"
date: 2026-03-04
weight: 10
leerlijn: 3
paragraaf: "3.9"
leerdoel: "Leerdoel nog toe te voegen"
---

## 3.9 Adviseren aan MIM-community

Kan adviseren aan de MIM-community rondom verbeteringen en uitbreidingen van het MIM.

### De MIM-community

Het Metamodel Informatie Modellering (MIM) wordt beheerd door een actieve community van overheidsorganisaties, leveranciers, en kennisorganisaties. Deze community werkt samen aan de doorontwikkeling van De Nederlandse standaard voor informatiemodellering.

#### Community-structuur

**Formele governance:**
- **Geonovum**: Officiële beheerder van de MIM-standaard
- **Technical Committee**: Besluitvorming over standaardwijzigingen
- **Werkgroepen**: Domein-specifieke expertteams
- **Adviesraden**: strategische sturing vanuit verschillende sectoren

**Informele netwerken:**  
- **Gebruikersgroepen**: Praktijkgerichte kennisuitwisseling
- **Expert-netwerken**: Diepgaande technische discussies
- **Community-platforms**: Online forums en discussiegroepen (Pleio, LinkedIn)

#### Participatie-niveaus

**Observer**: Volgt ontwikkelingen, gebruikt MIM passief
**User**: Past MIM toe in eigen projecten, levert feedback
**Contributor**: Draagt actief bij aan discussies en development
**Leader**: Initieert nieuwe ontwikkelingen, coordineert werkgroepen

### Types van community-adviezen

#### 1. Technische verbetervoorstel

**Voorbeeld: Betere ondersteuning voor tijdstempels**
```
Probleemsignaling:
"Huidige MIM-specificatie is onduidelijk over precisie van tijdstempellering:
- Datum-alleen vs datum-tijd
- Tijdzone-afhandeling
- Nauwkeurigheid (seconden vs milliseconden)"

Voorstel:
"Uitbreiden van MIM-datatypen met:
- TemporalDate: Alleen datum (YYYY-MM-DD)
- TemporalDateTime: Datum+tijd zonder timezone 
- TemporalTimestamp: Datum+tijd+timezone (ISO-8601)
- TemporalInterval: Tijdsperiode met begin/eind"

Onderbouwing:
- Analyse van 12 verschillende implementatie-interpretaties
- Vergelijking met internationale standaarden (ISO-8601)
- Impact-assessment op bestaande RSGB-implementaties
```

#### 2. Methodiek-advies

**Voorbeeld: Agile modeling patterns binnen MIM**
```
Observatie:
"Traditionele MIM-aanpak werkt goed voor waterfall-projecten, 
maar is minder geschikt voor agile/scrum omgevingen"

Advies:
"Ontwikkel MIM-annexe voor agile modeling:
- Minimum Viable Model (MVM) concept
- Iterative refinement-patronen
- Living-documentation technieken
- Model-code synchronisatie-strategieën"

Pilotresultaten:  
"3 gemeenten hebben experimenteel gewerkt met lightweight MIM:
- 40% snellere model-tot-implementatie cyclus
- Behoud van semantic consistency
- Verbeterde stakeholder-engagement"
```

#### 3. Tool-ecosysteem advies

**Voorbeeld: Open source tooling-strategie**
```
Analyse:
"Huidige MIM-tooling is voornamelijk commercieel (Enterprise Architect),
wat adoption-drempel verhoogt voor kleinere organisaties"

Recommendatie:
"Ontwikkel open-source tooling-roadmap:
- PlantUML-extensie voor MIM-diagrammen  
- Web-gebaseerde model-editor (JavaScript)
- Command-line validation-tools
- GitHub Actions voor automated checks"

Community-impact:
"Verwachte adoption-stijging van 25%, vooral bij start-ups 
en kleinere overheidsorganisaties"
```

### Proces van advisering

#### 1. Vooronderzoek

**Probleem-identificatie:**
- Verzamel praktijkervaringen uit eigen projecten
- Interview users en implementers
- Analyseer support-vragen en discussies

**Landscape-analyse:**
```
Vergelijk met internationale standaarden:
- UML 2.5 (OMG specificatie)
- ArchiMate 3.1 (The Open Group)
- TOGAF ADM (enterprise architecture)

Bekijk commercial tools:
- Enterprise Architect (Sparx Systems)
- MagicDraw (NoMagic) 
- Lucidchart/Draw.io (voor eenvoudige modellen)

Open source alternatieven:
- PlantUML (text-based modeling)
- Archi (ArchiMate-focused, maar uitbreidbaar)
- Eclipse Modeling Framework
```

#### 2. Uitwerking van voorstel

**RFC-format (Request for Comments):**
```markdown
# MIM-RFC-2024-03: Enhanced Temporal Modeling

## Samenvatting
Voorstel voor uitbreiding van MIM met verbeterde ondersteuning 
voor tijdsgerichte concepten.

## Motivatie
- Huidige ambiguïteit in tijd-semantiek
- Implementatie-inconsistenties tussen tools
- Missing features voor historische data-modeling

## Gedetailleerde specificatie
### Nieuwe data-types
- `TemporalDate`: Precisie-dag, geen timezone
- `TemporalDateTime`: Datum + tijd, geen timezone
- `TemporalTimestamp`: Volledig tijdsstempel met TZ

### UML-profiel uitbreidingen
- Stereotype: <<temporal>>
- Tagged values voor precisie en time-zone policies

### Backward compatibility
- Bestaande `Datum` type blijft supported
- Geleidelijke migratiestrategie

## Impact-analyse
- Breaking changes: Nee
- Tool-wijzigingen vereist: Ja (EA profiel update)
- Training-effect: Minimaal

## Alternatieve overwegingen
- Option A: Geen wijziging (status quo)
- Option B: Alleen metadata-uitbreidingen
- Option C: Deze spec (recommended)

## Implementatie-timeline
- Q1 2024: Community review
- Q2 2024: Prototype in Enterprise Architect
- Q3 2024: Final specification
- Q4 2024: Release in MIM 1.2
```

#### 3. Community consultatie

**Multi-channel engagement:**

**Formeel:**
- RFC-submission via Geonovum proces
- Presentatie tijdens MIM-werkgroepvergaderingen
- Review-ronden met key stakeholders

**Informeel:**
- Blog-posts over overwegingen en achtergrond
- Social media-discussies (LinkedIn expertgroepen)
- Conference-presentations (bijv. GI-symposium)

**Feedback-verwerking:**
```
Feedback classificeren:
├── Technical concerns (implementatie-issues)
├── Semantic concerns (betekenis en definities)
├── Process concerns (governance en timing)
└── Resource concerns (kosten en training)

Response-strategie:
- Acknowledge all feedback publicly
- Address major concerns with specification-updates
- Document decisions and reasoning
- Build consensus through iteration
```

### Effectieve advisering-vaardigheden

#### Technische credibiliteit

**Demonstreer expertise:**
- Verwijs naar eigen succesvolle MIM-implementaties  
- Toon diepgaande kennis van specification-details
- Gebruik concrete voorbeelden uit practice

**Comparative analysis:**
```
"In vergelijking met UML 2.5 associations, 
mist MIM-ondersteuning voor qualified associations:

UML: Person[employer] ←→ [employee] Organization
     ↑ via Employment relationship
     
MIM: Alleen simple associations possible
     → Requires workaround-pattern met intermediate entity"
```

#### Stakeholder-management

**Identificeer decision makers:**
- Formal: Geonovum TC members
- Informal: Respected community voices
- Economic: Major implementers (VNG, municipalities, vendors)

**Build coalition:**
- Zoek early adopters voor pilot-implementations
- Create cross-sector support (gemeenten + provincies + rijk)
- Leverage vendor-interest (sales-opportunities voor tooling)

#### Communication skills

**Write for different audiences:**
```
Technical spec → Implementers and tool-vendors
Executive summary → Management and decision-makers  
Tutorial-style → New adopters and training-materials
FAQ/troubleshooting → Support-scenarios
```

**Speaking opportunities:**
- MIM-gebruikersdag (jaarlijkse community-bijeenkomst)
- GITECOMMUNITY-events
- Gemeente-ICT (vakmagazine en conferenties)
- Academic conferences (ICEGOV, AMCIS)

### Lange-termijn community-betrokkenheit

#### Reputation building

**Consistent contributions:**
- Regular participation in werkgroep-meetings
- Thoughtful responses in community discussions
- Reliable follow-through op commitments

**Thought leadership:**
- Anticipate op trends (API-first, microservices, AI)
- Connect MIM met emerging technologies  
- Advocate for community needs bij formal governance

#### Knowledge sharing

**Mentoring newcomers:**
- Help nieuwe community members bij onboarding
- Explain context and history behind design-decisions
- Share practical implementation-experiences

**Documentation contributions:**
- Improve official MIM-documentation
- Create practical guides and tutorials
- Maintain FAQ and troubleshooting-resources

### Meetbare impact

**Adoption metrics:**
- Number of organizations implementing advised-changes
- Downloads of tools/extensions you contributed to
- Citations of your proposals in other specifications

**Community engagement:**
- Participation in discussions (quality and quantity)
- Speaking invitations en conference-acceptance  
- Collaboration requests from other experts

**Standard evolution:**
- Features/changes that made it into official MIM-versions
- International recognition (referenties vanuit andere landen)
- Academic citations in research-publications

Het adviseren aan de MIM-community vereist een combinatie van technische expertise, strategisch inzicht, en community-engagement skills. Success wordt gemeten aan daadwerkelijke adoptie van voorstellen en long-term verbetering van de MIM-standaard.