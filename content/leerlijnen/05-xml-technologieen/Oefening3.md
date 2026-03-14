---
title: "Oefening 3"
date: 2026-03-14
weight: 3.2
leerlijn: 5
paragraaf: "5.2.1"
oefendoel: "Oefen met het ordenen van een XML bestand in namespaces."
---

## Oefening 3: StUF Zaken

Je krijgt nu een sneak preview in een StUF bericht. Het is een goed voorbeeld van het toepassen van namespaces en we kijken dus nog niet naar de semantiek achter het bericht. 

**Benodigde files**
* Download [ZakLk01.zip](/Leerlijnen-KCA/bestanden/ZakLk01.zip) en plaats de inhoud (ZakLk01.xml) op een locatie waar jij er makkelijk bij kunt.
* Download [Zkn0310-schema.zip](/Leerlijnen-KCA/bestanden/Zkn0310-schema.zip) en plaats de inhoud (een aantal XML-Schema bestanden) op een locatie waar jij er makkelijk bij kunt.

**Opdracht**
* Open het bestand 'ZakLk01.xml' met 'Altova XMLSpy 2024 Enterprise XML Editor';
* Definieer op het root element de volgende namespaces met een door jezelf gekozen prefix:
  1. 'http://www.egem.nl/StUF/StUF0301'
  2. 'http://www.w3.org/2001/XMLSchema-instance'
  3. 'http://www.egem.nl/StUF/sector/zkn/0310' 
* Ken alle elementen binnen het element 'stuurgegevens' toe aan de eerste namespace;
* Doe hetzelfde met alle elementen binnen het element 'parameters' en met de elementen 'tijdvakGeldigheid', 'tijdvakRelatie' en alle elementen daarin;
* Ook het element 'tijdstipRegistratie' behoort tot die namespace;
* Ken de eerste namespace ook toe aan de volgende attributen in het xml bestand:
  - 'entiteittype'
  - 'sleutelVerzendend'
  - 'verwerkingssoort'
  - 'noValue'
* Ken de tweede namespace ook toe aan het attribuut 'nil' in het xml bestand;
* Ken de derde namespace tenslotte toe aan alle resterende elementen in het xml bestand en dus ook aan de elementen:
  - 'stuurgegevens'
  - 'parameters'
* Check de Wellformedness van het bestand en corrigeer daar waar nodig;
* Assign het XML-Schema bestand 'zkn0310_msg_totaal_resolved2025.xsd' (eerder verkregen door 'Zkn0310-schema.zip' uit te pakken);
* XMLSPY geeft nu aan dat het bestand valide is of geeft een of meer foutmeldingen. Corrigeer indien nodig de foutmeldingen;
* Bewaar het bestand.
