---
title: "Oefening 1"
date: 2026-03-13
weight: 3
leerlijn: 5
paragraaf: "5.1.1"
---

## Oefening 1: Visitekaartje

Teneinde de input voor visitekaartjes automatisch te kunnen verwerken, heeft de verantwoordelijke
afdeling van een groot IT-bedrijf een web-pagina gebouwd. Daarin worden de gegevens ingebracht die op een visitekaartje
geplaatst kunnen worden. Deze gegevens worden in XML-formaat naar het bedrijf
gestuurd, dat uiteindelijk de visitekaartjes produceert. Het XML-schema waarin dit XML-formaat is vastgelegd gaat u nu
gebruiken om een XML-bestand te creëren.

**Benodigde files**
* Download [Visitekaartje.zip](/Leerlijnen-KCA/bestanden/Visitekaartje.zip) en plaats de inhoud (Visitekaartje.xsd) op een locatie waar jij er makkelijk bij kunt.

**Opdracht**
* Open 'Altova XMLSpy 2024 Enterprise XML Editor'.;
* Creëer een nieuw XML-document via “File – New” en kies de optie “xml    Extensible Markup language 1.0”;
* Als je nu op OK klikt krijgt je het “Choose Schema or DTD” menu te zien. Selecteer de optie “Assign Schema/DTD file”;
* Klik vervolgens achter het invoerveld op het map icoontje voor “Browse file” en selecteer het bestand “Visitekaartje.xsd” waar je dat eerder hebt opgeslagen;
* Klik op “Open” en vervolgens op “OK”;
* XMLSPY heeft nu een XML-bestand voor je aangemaakt met de minimaal benodigde set elementen en deze geopend in “Text view”. 
Alle verplichte elementen zijn aanwezig. Zij bevatten alleen nog geen inhoud.<br>Voorzie de elementen van inhoud en kijk of 
je ook nog enkele niet verplichte elementen aan het bestand kunt toevoegen.<br>Valideer daarvoor regelmatig het bestand door te klikken op het groene <img width="22" style="display: block; margin-top: 0; margin-bottom: 0;" alt="Validate" src="/Leerlijnen-KCA/images/validate.jpg" /> 
icoontje en corrigeer indien nodig de fouten. Je kunt ook valideren via het menu “XML – Validate XML” of m.b.v. “F8”.<br>Eventueel kan je via “DTD\Schema – Go to 
Schema” de mogelijke structuur bekijken;
* Bewaar het bestand ergens als “Visitekaartje.xml”;
* Voeg aan het bestand nog ergens de elementen “Unit” en “Pager” toe en wel zodanig dat het bestand well-formed
blijft. Je kunt dit checken door te klikken op het gele <img width="22" style="display: block; margin-top: 0; margin-bottom: 0;" alt="Check wellformedness" src="/Leerlijnen-KCA/images/check-wellformed.jpg" /> 
icoontje, via het menu “XML – Check Well-Formedness” of m.b.v. “F7”;
* Bewaar het bestand als “Visitekaartje-wf.xml”.
