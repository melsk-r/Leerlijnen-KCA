---
title: "2.2 Relatie StUF, RSGB en GEMMA-standaarden"
date: 2026-03-04
weight: 3
leerlijn: 2
paragraaf: "2.2"
leerdoel: "Leerdoel nog toe te voegen"
---

## 2.2 Relatie StUF, RSGB en GEMMA-standaarden

Kent de relatie tussen StUF, RSGB en andere GEMMA-standaarden.

### De GEMMA-standaardenfamilie

Binnen GEMMA vormen verschillende standaarden een samenhangend ecosysteem:

#### StUF (Standaard Uitwisseling Formaat)
- **Functie**: Uitwisselingsstandaard voor berichten tussen systemen
- **Technologie**: XML-gebaseerd berichtenprotocol
- **Gebruik**: Zorgt voor gestandaardiseerde communicatie tussen applicaties

#### RSGB (Referentie Semantisch Gegevensmodel Basisregistraties)
- **Functie**: Semantisch model voor basisregistratiegegevens
- **Scope**: Definieert objecttypen, attributen en relaties
- **Gebruik**: Basis voor informatiemodellen in gemeentelijke systemen

### Onderlinge relaties

```
RSGB (Semantiek) ←→ StUF (Uitwisseling) ←→ Applicaties
       ↓                    ↓                  ↓
   Datamodel         Berichtenmodel      Implementatie
```

#### Hoe ze samenwerken:
1. **RSGB** definieert *wat* de gegevens betekenen
2. **StUF** definieert *hoe* gegevens uitgewisseld worden
3. **Applicaties** implementeren beide standaarden

### Andere GEMMA-standaarden

#### RGBZ (Referentie Gegevensmodel Burgerzaken)
- Specifiek voor zaakgericht werken
- Gebruikt StUF voor uitwisseling
- Bouwt voort op RSGB-principes

#### iWlz (informatiemodel Wet langdurige zorg)
- Domeinspecifiek voor WMO/Wlz
- Volgt GEMMA-architectuurprincipes
- Gebruikt StUF-patronen voor berichten

### Evolutie naar API's

De GEMMA-standaarden evolueren:
- **Traditioneel**: StUF-berichten via SOAP
- **Modern**: RESTful API's met JSON
- **Toekomst**: Event-driven architecturen

**Continuïteit**: Semantiek uit RSGB blijft relevant, ongeacht de technische implementatie.

### Praktische toepassing

Voor een informatiemodelleur betekent dit:
- Gebruik RSGB-gegevensmodel als basis
- Implementeer StUF voor bestaande koppelingen  
- Bereid voor op API-transitie
- Behoud semantische consistentie

**Resources:**
- [RSGB Modellen](https://www.gemmaonline.nl/index.php/RSGB)
- [StUF Standaard](https://www.gemmaonline.nl/index.php/StUF)
- [GEMMA Architectuur](https://www.gemmaonline.nl/index.php/GEMMA)