---
title: "6.3 Versies van de StUF-standaard"
date: 2026-08-06
weight: 603
leerlijn: 6
paragraaf: "6.3"
parent: "StUF-standaard"
leerdoel: "Begrip hebben van de twee verschillende versies van de StUF standaard, de belangrijkste verschillen kunnen benoemen en daarmee om kunnen gaan."
---

## 6.3 Versies van de StUF-standaard

Er zijn op dit moment 2 door VNG Realisatie ondersteunde StUF versies, 2.04 en 3.01. Beide versies worden nog gebruikt maar versie 3.01 is de meest gebruikte versie en ook de versie waarvoor de meeste sectormodellen en koppelvlakken zijn ontwikkeld. Eigenlijk is er voor versie 2.04 maar één sectormodel (StUF-BG 2.04) en geen enkel koppelvlak ontwikkelt. In de voorgaande onderdelen 6.1 (Opbouw en werking van StUF) en 6.2 (StUF folder en XML-Schema structuur) hebben we alleen gefocust op versie 3.01. In dit onderdeel gaan we heel kort in versie 2.04 en op de wijze waarop beide versies met elkaar geïntegreerd kunnen worden. 

### Versie 2.04

Versie 2.04 van de StUF standaard is tezamen met het sectormodel StUF-BG 2.04 in 2004 gepubliceerd. Deze versie van de StUF onderlaag en sectormodel wordt ook nu nog ondersteund. T.o.v. StUF-BG 3.10 is het arsenaal aan berichtsoorten van StUF-BG 2.04 veel beperkter. In StUF-BG 2.04 kennen we alleen de vraag- en antwoordberichten (La01, La02, Lv01 en Lv02), kennisgevingberichten (Lk01), een bevestigingsbericht (Bv01) en een foutbericht (Fo01).

Van synchronisatie(verzoek)berichten en vrije berichten is in StUF 2.04 geen sprake.

Waar in StUF 3.01 voor elk berichttype/entiteit combinatie een specifiek bericht is gecreëerd hanteert StUF 2.04 een veel generieker berichtconcept. Zo kent StUF-BG 2.04 slechts één kennisgevingsbericht, één vraagBericht, één synchroonAntwoordBericht en één asynchroonAntwoordBericht. De inhoud van het `<BG:body>` element in deze berichten bestaat uit een keuze van de in XML-Schema gedefinieerde structuren van de diverse in het GFO (de voorloper van het RSGB) onderkende entiteiten. Hieronder een 'kennisgevingsbericht' als voorbeeld:

<img width="400" alt="StUF-BG 2.04 kennisgevingsbericht" src="/Leerlijnen-KCA/images/bg0204-Lk.jpg" />

Zoals je ziet bestaat het `BG:body` element uit een keuze van één van een aantal elementen waarvan de naam overeenkomt met de mnemonic van een entiteit.
Waar nodig is echter wel voorzien in aparte structuren voor kennisgeving-, vraag- en antwoordberichten. Bij het opstellen van een bericht moet dus steeds uit een van deze structuren worden gekozen. Voor een aantal entiteiten zijn deze structuren echter niet anders voor kennisgeving- dan voor vraag- of antwoordbericht.

Ook de structuur voor de `<StUF:stuurgegevens>` is in StUF-BG 2.04 generieker van opzet. 

<img width="300" alt="StUF-BG 2.04 Stuurgegevens" src="/Leerlijnen-KCA/images/bg0204-Stuurgegevens.jpg" />

Voor alle berichten wordt dezelfde structuur gehanteerd. De `<StUF:stuurgegevens>` structuur bevat echter wel specifieke elementen die alleen van toepassing zijn bij specifieke berichttypes. Bij het opstellen van een bericht bepaald de software welke elementen nodig zijn maar kan deze het gerealiseerde bericht slechts beperkt valideren. Wat dat betreft is de 3.10 versie van StUF-BG strakker gedefinieerd al is die definitie wel veel groter en complexer.

Dit alles geeft natuurlijk een veel grotere kans op fouten.

Aangezien deze standaard nog slechts door een klein aantal leveranciers wordt gebruikt en bij een relatief kleine groep gemeenten is geïmplementeerd gaan we er hier niet verder op in. Indien gewenst kun je je nog verder verdiepen in deze versie van de standaard door <a href="https://vng-realisatie.github.io/StUF-BG/documenten/bg0204.pdf" target="_blank">de specificatie</a> daarvan te lezen.

### Integratie versie 2.04 met versie 3.01

Voor zowel versie 2.04 als 3.01 is het sectormodel (zie verderop voor een uitleg) StUF-BG gepubliceerd (2.04 resp. 3.10). Voor de transformatie in de keten van StUF-BG 2.04 naar StUF-BG 3.10 en vice versa zijn mapping tabellen gepubliceerd.
Hieronder zie je bijv. een deel van de mapping tussen de entiteit ADR in StUF-BG 2.04 naar de entiteit AOA in StUF-BG 3.10:

<img width="800" alt="StUF-BG 2.04 Stuurgegevens" src="/Leerlijnen-KCA/images/Mapping-ADR-op-AOA.jpg" />

en vice versa:

<img width="800" alt="StUF-BG 2.04 Stuurgegevens" src="/Leerlijnen-KCA/images/Mapping-AOA-op-ADR.jpg" />

Soms betreft de mapping simpelweg het wijzigen van een elementnaam zoals in de eerste figuur van `woonplaatsnaam` naar `wpl.woonplaatsNaamNen` maar soms ligt dat ook ingewikkelder.
Zoals in de tweede figuur het geval van het element `identificatie` in AOA. Daarvoor is geen tegenhanger aanwezig in StUF-BG 2.04 reden waarom we in dat geval mappen naar het extraElement `identificatieAOA`. Of in het geval van `gor.openbareRuimteNaam`, `gor.straatnaam` en het extraElement `straatnaam` in AOA. Alledrie die elementen moeten gemapt worden op `straatnaam`. Het zal duidelijk zijn dat je niet tegelijkertijd drie elementen daarop kunt mappen. Reden waarom er is beschreven wat de doen als meer dan een van die elementen aanwezig is: 

> Als gor.straatnaam is gevuld, dan wordt deze gemapt, anders gor.openbareRuimteNaam, zonodig wordt de waarde afgekapt op 24 posities.<br/>Binnen de BAG berichtencatalogus is gor.straatnaam in sommige berichten niet beschikbaar. Gebruik dan het extraElement 'straatnaam'.

Zoals je ziet moet er in het geval van `gor.openbareRuimteNaam` ook nog een transformatie uitgevoerd worden aangezien dat element uit 80 posities kan bestaan en `straatnaam` in ADR maar uit 24 posities.

### Resources
- <a href="https://www.gemmaonline.nl/index.php/StUF" target="_blank">StUF Standaard Versiehistorie</a>
- <a href="https://vng-realisatie.github.io/StUF-onderlaag/" target="_blank">VNG Realisatie StUF-documentatie</a>
- <a href="https://github.com/VNG-Realisatie/StUF-Standaarden/issues" target="_blank">Discussies vanaf 1 maart 2020</a>
- <a href="https://vng-realisatie.github.io/StUF-Standaarden/" target="_blank">Discussies van voor 1 maart 2020</a>
