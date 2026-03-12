---
title: "5.1 XML syntax en structuur"
date: 2026-03-12
weight: 2
leerlijn: 5
paragraaf: "5.1"
leerdoel: "Leerdoel nog toe te voegen"
---


## 5.1 XML syntax en structuur

Begrijpt de syntax en structuur van XML-documenten.

### Het probleem: hoe wissel je gegevens uit?

Stel je voor: twee gemeentelijke systemen moeten gegevens over een inwoner uitwisselen. Het ene systeem (A) slaat gegevens op in een database, het andere (B) in een heel ander formaat. Hoe zorg je ervoor dat beide systemen elkaars gegevens begrijpen?

Je zou de gegevens in platte tekst kunnen sturen, bijvoorbeeld:

```
Jan de Vries, Kerkstraat 12, 3511AB, Utrecht
```

Maar dit levert direct problemen op:
- Wat als iemand twee achternamen heeft? Waar begint het adres dan?
- Hoe weet het ontvangende systeem welk veld wat is?
- Wat als er een komma in een adres staat?

In het verleden werd dit probleem vaak opgelost door specifiek voor deze situatie software te schrijven. Als systeem A vervolgens dezelfde gegevens ook moest uitwisselen met nog een ander systeem (C) werd weer nieuwe code geschreven. Je kunt je voorstellen dat er na verloop van tijd een kluwen van verbindingen was gelegd om gegevens uit te wisselen. 

<img width="220" alt="image" src="https://github.com/user-attachments/assets/67913f5a-3db3-4df5-a1c6-8bcdecc37ea5" />

In de praktijk wordt dit vaak het **"spaghetti-probleem"** genoemd: wanneer *N* systemen met *M* andere systemen gegevens moeten uitwisselen, zijn er tot N×M koppelvlakken nodig — elk met een eigen formaat. Dit leidt tot onbeheersbare "copy-paste"-integraties. De oplossing: één gemeenschappelijke taal voor gegevensuitwisseling.

<img width="220" alt="image" src="https://github.com/user-attachments/assets/4972f388-e351-4339-af6d-d169d2731a70" />


Er is dus een manier nodig om gegevens **gestructureerd** vast te leggen, zodat computers, maar ook mensen, ondubbelzinnig kunnen begrijpen wat elk gegeven betekent.

### Markup: tekst met betekenis

De oplossing heet **markup** (letterlijk: "opmaak" of "annotatie"). Je plaatst **labels** (ook wel "tags" genoemd) om stukjes tekst heen, zodat duidelijk is wat elk stukje betekent.

Een **tag** herken je aan de punthaken `<` en `>`. Tags komen in paren: een **openingstag** en een **sluitingstag** (begint met `</`).

```xml
<voornaam>Jan</voornaam>
```

- `<voornaam>` — de **openingstag**
- `Jan` — de **inhoud** (het feitelijke gegeven)
- `</voornaam>` — de **sluitingstag**

Het geheel noemen we een **element**.

### Wat is XML precies?

**XML** staat voor **eXtensible Markup Language** – een *uitbreidbare opmaaktaal*:

| Begrip | Betekenis |
|---|---|
| **eXtensible** | Je mag zelf tagnamen bedenken — er is geen vaste lijst. |
| **Markup** | Je voegt labels (tags) toe aan tekst om er structuur en betekenis aan te geven. |
| **Language** | Er zijn vaste regels waaraan je je moet houden, zodat computers de tekst betrouwbaar kunnen verwerken. |

XML is dus een **set afspraken** over hoe je gegevens in tekstformaat vastlegt, zodat:
1. Een mens kan lezen en begrijpen wat er staat
2. Een computer de gegevens automatisch kan verwerken
3. Verschillende systemen de gegevens kunnen uitwisselen

XML is een universeel geaccepteerde standaard voor informatie-uitwisseling (referentie: [W3C XML-specificatie](http://www.w3.org/XML/)).

> **Belangrijk:** XML is **geen** programmeertaal en **geen** systeem. XML is puur een formaat — een manier om gegevens op te schrijven. Je hebt altijd software (een applicatie, een koppelvlak) nodig die XML leest of schrijft.

### De oorsprong van XML

XML is niet uit het niets ontstaan, maar is de jongste telg in een familie van markup-talen:

| Jaar | Taal | Toelichting |
|---|---|---|
| **1969** | **GML** | Generalized Markup Language — ontwikkeld bij IBM door Charles Goldfarb |
| **1978** | **SGML** | Standard Generalized Markup Language — internationale standaard (ISO 8879) |
| **1992** | **HTML** | HyperText Markup Language — vereenvoudiging van SGML voor het World Wide Web |
| **1997** | **XML** | eXtensible Markup Language — vereenvoudiging van SGML, specifiek gericht op *gegevensuitwisseling* |

```
GML (1969) ──→ SGML (1978) ──→ HTML (1992) ──→ WWW
                            └──→ XML  (1997) ──→ Gegevensuitwisseling
```

HTML en XML zijn dus beide afstammelingen van SGML, maar met een heel ander doel: HTML beschrijft hoe informatie er *uitziet*, XML beschrijft wat informatie *betekent*.
XHTML is dan weer de HTML vorm die voldoet aan de XML syntax.

### De XML-declaratie

Elk XML-document begint (optioneel, maar sterk aanbevolen) met een **XML-declaratie**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

| Onderdeel | Betekenis |
|---|---|
| `<?xml ... ?>` | Herkenningsteken: dit is een XML-document (een zgn. *processing instruction*) |
| `version="1.0"` | De gebruikte XML-versie (vrijwel altijd 1.0) |
| `encoding="UTF-8"` | De tekencodering — bepaalt welke letters/tekens het document kan bevatten |

De declaratie staat altijd op de **allereerste regel** en vóór het root-element.

### Elementen: de bouwstenen van XML

**Elementen met tekst als inhoud** — de eenvoudigste vorm:

```xml
<voornaam>Jan</voornaam>
<huisnummer>12</huisnummer>
<geboortedatum>1985-03-15</geboortedatum>
```

> Voor XML is alles tekst. Het getal `12` en de datum `1985-03-15` zijn gewoon tekenreeksen. Pas met XSD (zie 5.3) kun je afdwingen dat een waarde een getal of datum moet zijn.

**Elementen met kind-elementen** — nesting:

```xml
<adres>
  <straat>Kerkstraat</straat>
  <huisnummer>12</huisnummer>
  <postcode>3511AB</postcode>
  <woonplaats>Utrecht</woonplaats>
</adres>
```

De elementen `<straat>`, `<huisnummer>` etc. zijn **kind-elementen** (child elements) van `<adres>`. Het element `<adres>` is het **ouder-element** (parent element). Zo ontstaat een **boomstructuur**.

**Lege elementen** — een element zonder inhoud:

```xml
<overlijdensdatum></overlijdensdatum>
<!-- of verkort: -->
<overlijdensdatum/>
```

**Root-element** — elk XML-document heeft precies één buitenste element dat alle andere elementen omvat:

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

### Attributen: extra informatie bij een element

Een **attribuut** staat in de openingstag en heeft de vorm `naam="waarde"`:

```xml
<inwoner burgerservicenummer="123456789">
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
</inwoner>
```

**Regels voor attributen:**
- De waarde staat altijd tussen aanhalingstekens (enkele of dubbele);
- Een element mag meerdere attributen hebben, gescheiden door spaties;
- Elk attribuut mag maar één keer voorkomen op hetzelfde element;
- Attributen hebben geen vaste volgorde.

**Wanneer een attribuut, wanneer een kind-element?**

| Gebruik een **attribuut** voor... | Gebruik een **kind-element** voor... |
|---|---|
| Metagegevens (informatie *over* het gegeven) | De feitelijke gegevens zelf |
| Korte, enkelvoudige waarden | Waarden die zelf weer structuur kunnen bevatten |
| Identificatoren, typen, datumstempels | Inhoudelijke data |

> **Let op:** Of een gegeven als een element of juist als een attribuut moet worden gedefinieerd is sterk afhankelijk van het informatiedomein. In een domein dat gaat over temperaturen wordt de waarde 'Celsius' waarschijnlijk in een element opgeslagen, in een domein dat gaat over het weer is het waarschijnlijk een attribuut.

> **Let op:** In StUF-berichten worden attributen veelvuldig gebruikt, onder meer voor het aangeven van het mutatiesoort en de aanduiding van sleutelwaarden.

### Voorbeeld uit de praktijk: een CV in XML

Dit voorbeeld laat zien hoe een eenvoudig document in XML wordt vastgelegd:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CV salarisnummer="12345" auteur="J. de Vries" documentdatum="2022-07-15">
  <persoonlijke_gegevens>
    <naam>Jan de Vries</naam>
    <adres>
      <straat>Kerkstraat</straat>
      <huisnummer>12</huisnummer>
      <postcode>3511AB</postcode>
      <woonplaats>Utrecht</woonplaats>
    </adres>
  </persoonlijke_gegevens>
</CV>
```

Herken je de onderdelen?
1. **XML-declaratie** — `<?xml version="1.0" encoding="UTF-8"?>`
2. **Elementen** — `<CV>`, `<persoonlijke_gegevens>`, `<naam>`, `<adres>`, etc. — elk met begin- en eindtag
3. **Attributen** — `salarisnummer`, `auteur`, `documentdatum` — metagegevens over het CV zelf

### Voorbeeld uit de praktijk: een weerrapport in XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Weerrapport datum="2023-03-11">
  <Station naam="Ameland">
    <Weersgegevens weertype="zwaar-bewolkt">
      <Temperatuur eenheid="Celsius">18,1</Temperatuur>
      <Wind richting="Z.O.">
        <Snelheid eenheid="m/sec">4</Snelheid>
      </Wind>
      <Zicht eenheid="m">900</Zicht>
      <Druk eenheid="hPa">1167</Druk>
    </Weersgegevens>
  </Station>
</Weerrapport>
```

Merk op hoe de attributen hier meerdere rollen vervullen:
- **Identificatie**: `naam="Ameland"` vertelt welk station het betreft
- **Classificatie**: `weertype="zwaar-bewolkt"` geeft het type weer aan
- **Eenheden**: `eenheid="Celsius"`, `eenheid="m/sec"` — metagegevens over hoe de waarde gelezen moet worden

### Vergelijking met HTML

| | HTML | XML |
|---|---|---|
| **Doel** | Beschrijven hoe tekst *gepresenteerd* moet worden (presentatie). | Beschrijven wat gegevens *betekenen* (data). |
| **Tagnamen** | Vast: `<h1>`, `<p>`, `<div>`, etc. | Vrij te kiezen: `<inwoner>`, `<postcode>`, etc. |
| **Strengheid** | Vergevingsgezind: een browser probeert fouten te repareren. | Streng: bij een fout stopt de verwerking. |
| **Oorsprong** | SGML-vereenvoudiging voor het web (1992). | SGML-vereenvoudiging voor gegevensuitwisseling (1997). |

### Waarom XML?

XML is de universeel geaccepteerde standaard voor informatie-uitwisseling. De belangrijkste redenen:

1. **Leesbaar voor mens én machine** — gewone tekst, overal te openen;
2. **Platform-, leverancier- en taalonafhankelijk** — werkt met Java, C#, Python, en elk besturingssysteem;
3. **Zelfbeschrijvend** — tagnamen vertellen wat de inhoud is;
4. **Uitbreidbaar** — nieuwe elementen toevoegen zonder bestaande structuren te breken;
5. **Valideerbaar** — met schema's (XSD) formeel controleerbaar;
6. **Niet meer weg te denken** — het zit inmiddels in vrijwel elke softwarelaag (configuratie, berichten, documenten).

### De XML-familie: terminologie

XML staat niet op zichzelf, maar maakt deel uit van een familie van technologieën:

| Technologie | Rol |
|---|---|
| **XML** | Eenvoudige syntax om informatie herkenbaar te maken voor applicaties en machines. |
| **XML Schema (XSD)** | Regels over de structuur van informatie — welke elementen/attributen zijn toegestaan? (zie 5.3). |
| **Namespace** | Vocabulaire van een bepaald informatiedomein waarbinnen termen uniek zijn (zie 5.2). |
| **XSLT** | Stylesheet-taal waarmee XML omgezet wordt naar een andere structuur, vormgeving of toepassing. |

### XML-tools

Om met XML te werken gebruik je doorgaans drie soorten tools:

| Tool | Functie |
|---|---|
| **XML-parser** | Leest een XML-document en interpreteert de structuur (controleert o.a. welgevormdheid en validiteit). |
| **XSLT-processor** | Combineert een XML-document met een XSLT-stylesheet en produceert output (HTML, PDF, ander XML). |
| **XML-editor** | Tool om XML-documenten (en schema's en stylesheets) te creëren en te bewerken. Heeft vaak een parser en XSLT-processor ingebouwd. Voorbeeld: Altova Authentic, Oxygen XML Editor. |
| **XML-Schema editor** | Tool om XML-documenten (en schema's en stylesheets) te creëren en te bewerken. Heeft vaak een parser en XSLT-processor ingebouwd. Voorbeeld: Altova XMLSpy, Oxygen XML Developer. |

> In de gemeente komt je XML tegen in koppelvlakbeschrijvingen (StUF/WSDL), in configuratiebestanden, en in berichtuitwisseling tussen systemen.
