---
title: "1.1 Wat is XML?"
weight: 1
---

# 1.1 Wat is XML?

## Het probleem: hoe wissel je gegevens uit?

Stel je voor: twee gemeentelijke systemen moeten gegevens over een inwoner uitwisselen. Het ene systeem slaat gegevens op in een database, het andere in een heel ander formaat. Hoe zorg je ervoor dat beide systemen elkaars gegevens begrijpen?

Je zou de gegevens in platte tekst kunnen sturen, bijvoorbeeld:

```
Jan de Vries, Kerkstraat 12, 3511AB, Utrecht
```

Maar dit levert direct problemen op:
- Wat als iemand twee achternamen heeft? Waar begint het adres dan?
- Hoe weet het ontvangende systeem welk veld wat is?
- Wat als er een komma in een adres staat?

Er is dus een manier nodig om gegevens **gestructureerd** vast te leggen, zodat zowel mensen als computers ondubbelzinnig kunnen begrijpen wat elk gegeven betekent.

## Markup: tekst met betekenis

De oplossing heet **markup** (letterlijk: "opmaak" of "annotatie"). Het idee is simpel: je plaatst **labels** (ook wel "tags" genoemd) om stukjes tekst heen, zodat duidelijk is wat elk stukje betekent.

Een **tag** herken je aan de punthaken `<` en `>`. Een tag heeft altijd een naam die beschrijft wat de inhoud voorstelt. Tags komen in paren: een **openingstag** en een **sluitingstag**. De sluitingstag begint met `</`.

Voorbeeld:

```xml
<voornaam>Jan</voornaam>
```

Hier is:
- `<voornaam>` de **openingstag** – deze markeert het begin van het gegeven
- `Jan` de **inhoud** (of: tekstinhoud) – het feitelijke gegeven
- `</voornaam>` de **sluitingstag** – deze markeert het einde van het gegeven

Het geheel – openingstag + inhoud + sluitingstag – noemen we een **element**. Het element hierboven heet "voornaam" en bevat de waarde "Jan".

## Wat is XML precies?

**XML** staat voor **eXtensible Markup Language** – vrij vertaald: een *uitbreidbare opmaaktaal*. Laten we die naam ontleden:

| Begrip | Betekenis |
|---|---|
| **eXtensible** (uitbreidbaar) | Je mag zelf tagnamen bedenken. Er is geen vaste lijst met toegestane tags – je kiest namen die passen bij jouw gegevens. |
| **Markup** (opmaak/annotatie) | Je voegt labels (tags) toe aan tekst om er structuur en betekenis aan te geven. |
| **Language** (taal) | Er zijn vaste regels waaraan je je moet houden, zodat computers de tekst betrouwbaar kunnen verwerken. |

XML is dus een **set afspraken** over hoe je gegevens in tekstformaat vastlegt, zodat:
1. Een mens kan lezen en begrijpen wat er staat
2. Een computer de gegevens automatisch kan verwerken
3. Verschillende systemen (ongeacht programmeertaal of platform) de gegevens kunnen uitwisselen

## Een eerste volledig voorbeeld

Laten we het inwoner-voorbeeld van hierboven nu in XML vastleggen:

```xml
<inwoner>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
  <adres>
    <straat>Kerkstraat</straat>
    <huisnummer>12</huisnummer>
    <postcode>3511AB</postcode>
    <woonplaats>Utrecht</woonplaats>
  </adres>
</inwoner>
```

Merk op:
- Elk gegeven zit in een eigen element met een beschrijvende naam
- Elementen kunnen **genest** worden: het element `<adres>` bevat zelf weer elementen (`<straat>`, `<huisnummer>`, etc.)
- Door de nesting ontstaat een **boomstructuur** (hiërarchie): `inwoner` is de "stam", met `voornaam`, `achternaam` en `adres` als "takken"
- Het buitenste element (`<inwoner>`) heet het **root-element** (wortel-element) – elk XML-document heeft er precies één
- De **inspringen** (witruimte) zijn niet verplicht, maar maken het voor mensen veel leesbaarder

## Vergelijking met HTML

Misschien heb je weleens gehoord van **HTML** – de taal waarmee webpagina's worden gemaakt. HTML gebruikt ook tags, maar er is een belangrijk verschil:

| | HTML | XML |
|---|---|---|
| **Doel** | Beschrijven hoe tekst er *uitziet* (presentatie) | Beschrijven wat gegevens *betekenen* (data) |
| **Tagnamen** | Vast: `<h1>`, `<p>`, `<div>`, etc. | Vrij te kiezen: `<inwoner>`, `<postcode>`, etc. |
| **Strengheid** | Vergevingsgezind: een browser probeert fouten te repareren | Streng: bij een fout stopt de verwerking |

HTML zegt: *"Dit is een koptekst, dit is een alinea."*
XML zegt: *"Dit is een voornaam, dit is een postcode."*

Voor gegevensuitwisseling tussen systemen is XML daarom veel geschikter dan HTML. Het gaat immers niet om hoe iets eruitziet, maar om wat de gegevens **betekenen**.

## Waarom XML? De voordelen

1. **Leesbaar voor mens én machine**
   XML is gewone tekst. Je kunt het openen in Kladblok/Notepad en direct zien wat erin staat. Tegelijkertijd kunnen programma's het betrouwbaar verwerken.

2. **Platform- en taalonafhankelijk**
   Of een systeem nu in Java, C#, Python of iets anders is geschreven – XML kan overal gelezen en geschreven worden. Het is gewoon tekst.

3. **Zelfbeschrijvend**
   Doordat de tagnamen beschrijven wat de inhoud is, heb je geen apart document nodig om te begrijpen wat `<postcode>3511AB</postcode>` betekent.

4. **Uitbreidbaar**
   Je kunt altijd nieuwe elementen toevoegen zonder bestaande structuren te breken. Dit maakt XML geschikt voor standaarden die in de loop der tijd groeien.

5. **Valideerbaar**
   Met schema's (zoals XSD, dat later in deze cursus aan bod komt) kun je formeel vastleggen welke elementen en waarden zijn toegestaan, en automatisch controleren of een document aan de regels voldoet.

## Waar wordt XML in de praktijk gebruikt?

XML wordt breed ingezet, onder meer voor:

- **Gegevensuitwisseling tussen organisaties** – Denk aan gemeenten die inwonersgegevens uitwisselen (precies waar StUF voor is ontworpen)
- **Configuratiebestanden** – Veel softwaresystemen gebruiken XML om instellingen vast te leggen
- **Documentformaten** – Microsoft Office-bestanden (.docx, .xlsx) zijn intern ZIP-bestanden gevuld met XML
- **Webservices (SOAP)** – Een veelgebruikt protocol voor communicatie tussen systemen, gebaseerd op XML-berichten
- **RSS-feeds** – Nieuwsfeeds op websites worden in XML uitgedrukt

In de context van deze cursus is het eerste punt het meest relevant: XML als drager voor gestructureerde gegevensuitwisseling tussen gemeentelijke systemen.

## Samenvatting

| Concept | Uitleg |
|---|---|
| **Tag** | Een label tussen punthaken, bijv. `<voornaam>` |
| **Element** | Een openingstag + inhoud + sluitingstag, bijv. `<voornaam>Jan</voornaam>` |
| **Root-element** | Het buitenste element dat alle andere elementen omvat |
| **Nesting** | Elementen binnen elementen, waardoor een boomstructuur ontstaat |
| **XML** | Een uitbreidbare opmaaktaal voor het gestructureerd vastleggen van gegevens in tekstvorm |

> **Kernpunt:** XML is een manier om gegevens zo op te schrijven dat zowel mensen als computers ze ondubbelzinnig kunnen lezen. Je gebruikt zelfgekozen tags om betekenis te geven aan stukjes tekst, en je houdt je aan vaste regels zodat alles machinaal verwerkbaar blijft.
