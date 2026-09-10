---
title: "6.4 Koppelvlakspecificaties"
date: 2026-08-06
weight: 604
leerlijn: 6
paragraaf: "6.4"
parent: "StUF-standaard"
leerdoel: "De onderdelen van StUF koppelvlak standaarden kunnen lezen en beheren met alle documenten die daarbij hoort."
---

## 6.4 Koppelvlakspecificaties

### Wat zijn StUF koppelvlakspecificaties?

Een **StUF koppelvlakspecificatie** is een set van documenten die zo precies als mogelijk beschrijft hoe twee systemen met elkaar kunnen communiceren via StUF-berichten. Het definieert de berichten, scenario's en aanvullenderegels die nodig zijn voor succesvolle gegevensuitwisseling. Een StUF koppelvlak specificeert hoe de gegevensuitwisseling tussen applicaties en voorzieningen eruit moet zien. Het legt concrete afspraken vast waarmee specifieke gegevens kunnen worden gecommuniceerd. Afspraken over specifieke berichten die tussen bepaalde referentiecomponenten uitgewisseld moeten en/of kunnen worden, afspraken over aanscherping van de berichten uit een StUF sectormodel en afspraken over de wijze waarop die berichten verstuurd moeten worden. In een StUF koppelvlakspecificatie is ook altijd aangegeven op welke referentiecomponenten de specificatie van toepassing is. Een StUF sectormodel is wat dat betreft minder restrictief en laat meer ruimte open voor eigen interpretatie.

Een aantal bekende StUF koppelvlakken zijn:
* <a href="https://vng-realisatie.github.io/Zaak-en-Documentservices/" target="_blank">Zaak- en Documentservices</a>
* <a href="https://vng-realisatie.github.io/Documentcreatieservices/Documentatie" target="_blank">Documentcreatieservices</a>
* en <a href="https://vng-realisatie.github.io/StUF-Jeugdzorg/" target="_blank">StUF Jeugdzorg</a>

#### Componenten van een StUF koppelvlakspecificatie

De set van documenten van een StUF koppelvlakspecificatie bestaat minimaal uit een **functionele en technische specificatie** en een **berichtcatalogus**. In de eerste staat beschreven wat, waarom en hoe precies wordt uitgewisseld. Denk daarbij ook aan beveiliging en autorisatie. Over het algemeen is dit beschreven in een Word bestand dat wordt gepubliceerd m.b.v. een pdf. Het tweede bestaat uit een set van XML-Schema bestanden die soms gebaseerd zijn op een sectormodel en in dat geval een aanscherping op de berichten in het StUF sectormodel bevatten. Vaak ziet men dat in een koppelvlak ook vrije berichten worden gedefinieerd ten einde tegemoet te kunnen komen aan specifieke wensen m.b.t. de uit te wisselen gegevens.

Optioneel kan een StUF koppelvlakspecificatie ook nog **scenario beschrijvingen** bevatten. Workflows (zowel de happy als de unhappy) en use cases welke bijv. beschreven zijn m.b.v. een Excel spreadsheet. Tenslotte kunnen ook nog **implementatie-voorbeelden** van concrete berichten zijn toegevoegd.

Het opstellen van een koppelvlakspecificatie start altijd vanuit een specifieke behoefte van de business en vindt zijn basis in een informatiemodel, bijv. het RSGB, RGBZ, een ander, niet door VNG Realisatie beheerd, model of een specifiek voor het koppelvlak opgesteld model.

### Functionele en technische specificatie

In een functionele en technische specificatie kan je o.a. de volgende onderdelen tegenkomen:
* Functionaliteit op hoofdlijnen
* Architectuur
* Specificatie per bericht/service
* Aanvullende regels
* Foutafhandeling
* informatiemodel
* VerStUFfing
* Beveiliging en autorisatie
* Protocolbinding
* Foutafhandeling

Bij het opstellen van de StUF koppelvlakken zijn hierin eigen keuzes gemaakt.

Het beheren van StUF-koppelvlakspecificaties vereist grondige kennis van zowel de technische als functionele aspecten van gegevensuitwisseling. Een goede specificatie is de basis voor succesvolle systeemintegratie en interoperabiliteit binnen de overheid.

### Vrij berichttype

Een StUF koppelvlakspecicatie is DE plaats waar je een StUF vrij bericht tegen kunt komen. Met een vrij bericht kun je nl. relatief eenvoudig aan de specifieke requirements van een koppelvlak tegemoet komen.
Vrije berichten kunnen gebaseerd zijn op berichten in de sectormodellen en dus op een entiteittype uit het RSGB, RGBZ of ImZTC maar kunnen ook een geheel ander informatiemodel als basis hebben.
Vrije berichten die gebaseerd zijn op het RSGB, RGBZ of ImZTC kunnen zo nodig uitgebreid zijn met elementen die niet in de informatiemodellen voorkomen maar uit informatiemodellen die daar een extensie op zijn.
Een goed voorbeeld daarvan is het bericht 'uitkomstMeldingGeneriekDi01' uit StUF-Jeugdzorg:

<img width="400" alt="Di01-bericht-op-basis-van ZAK" src="/Leerlijnen-KCA/images/Di01-bericht-op-basis-van-ZAK.jpg" />

**Resources:**
- [StUF Sectormodellen en koppelvlakken](https://standaarden.vng.nl/StUF-koppelvlakken-en-sectormodellen)
- [StUF Testplatform](https://www.stuftest.nl/)
