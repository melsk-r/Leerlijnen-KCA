---
title: "1.4 Speciale XML-constructies"
weight: 4
---

# 1.4 Speciale XML-constructies

In de vorige secties heb je geleerd hoe je met elementen, attributen en namespaces gegevens structureert in XML. Maar een XML-document kan meer bevatten dan alleen gegevens. Er zijn een aantal **speciale constructies** die je regelmatig tegenkomt – sommige technisch noodzakelijk, andere handig in de praktijk. In deze sectie behandelen we ze stuk voor stuk.

## De XML-declaratie

### Wat is het?

De **XML-declaratie** is de allereerste regel van een XML-document. Je hebt hem misschien al eens gezien:

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

Het is een instructie aan de XML-verwerker die zegt: *"Dit is een XML-document, en hier zijn de technische details die je nodig hebt om het correct te lezen."*

### Herkenning

De XML-declaratie herken je aan:
- Het begint met `<?xml` (let op: **kleine letters**)
- Het eindigt met `?>`
- Het staat altijd op de **allereerste regel**, vóór al het andere – er mag zelfs geen lege regel of witruimte boven staan

### De onderdelen

De XML-declaratie kan tot drie onderdelen bevatten:

| Onderdeel | Verplicht? | Betekenis | Voorbeeld |
|---|---|---|---|
| `version` | Ja (als je een declaratie opneemt) | Welke versie van de XML-standaard wordt gebruikt | `version="1.0"` |
| `encoding` | Nee, maar sterk aanbevolen | Welke tekencodering het bestand gebruikt | `encoding="UTF-8"` |
| `standalone` | Nee | Of het document afhankelijk is van externe definities | `standalone="yes"` |

**`version`** – In de praktijk is dit vrijwel altijd `"1.0"`. Er bestaat ook versie 1.1, maar die wordt zelden gebruikt. Alle StUF-berichten gebruiken versie 1.0.

**`encoding`** – Dit vertelt de computer hoe de bytes in het bestand vertaald moeten worden naar tekens. De meest gebruikte codering is **UTF-8**, die alle tekens uit vrijwel alle talen kan weergeven. Als je de encoding weglaat, gaat een verwerker uit van UTF-8 of UTF-16.

> **Waarom is encoding belangrijk?** Stel je voor: een systeem stuurt een XML-bericht met Nederlandse tekens als "ë" of "ü". Als het ontvangende systeem niet weet welke encoding is gebruikt, kan het die tekens verkeerd interpreteren. Door `encoding="UTF-8"` op te nemen voorkom je dit probleem.

**`standalone`** – Dit geeft aan of het document afhankelijk is van externe DTD's (Document Type Definitions). In de praktijk van StUF wordt dit attribuut zelden gebruikt, omdat StUF werkt met XSD's in plaats van DTD's.

### Voorbeelden

De meest voorkomende vorm:
```xml
<?xml version="1.0" encoding="UTF-8"?>
```

Volledig met alle onderdelen:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
```

Minimaal (alleen version):
```xml
<?xml version="1.0"?>
```

### Is de XML-declaratie verplicht?

Technisch gezien: **nee**. Een XML-document zonder declaratie is nog steeds geldig. Maar het is een sterke aanbeveling om de declaratie altijd op te nemen, vooral met de encoding. In de praktijk van gegevensuitwisseling – en zeker bij StUF – is de XML-declaratie standaard aanwezig.

### Een compleet voorbeeld

Zo ziet een volledig XML-document er inclusief declaratie uit:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kennisgeving>
  <stuurgegevens>
    <zender>BRP-systeem</zender>
    <ontvanger>Zaaksysteem</ontvanger>
  </stuurgegevens>
</kennisgeving>
```

De declaratie staat op regel 1, het root-element begint op regel 2. Er mag **niets** boven de declaratie staan – geen spatie, geen lege regel, geen commentaar.

## Commentaar

### Wat is het?

Net als in programmeertalen kun je in XML **commentaar** toevoegen: tekst die bedoeld is voor menselijke lezers en die door de computer wordt genegeerd. Commentaar wordt niet verwerkt en heeft geen effect op de gegevens.

### Syntax

Commentaar begint met `<!--` en eindigt met `-->`:

```xml
<!-- Dit is commentaar -->
```

Commentaar mag ook meerdere regels beslaan:

```xml
<!--
  Dit is een langer stuk commentaar
  dat over meerdere regels loopt.
-->
```

### Waar mag commentaar staan?

Commentaar mag vrijwel overal in het document staan, behalve:
- **Vóór** de XML-declaratie (als die er is)
- **Binnen** een tag (dus niet midden in `<element ... >`)

Geldige plekken:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Commentaar na de declaratie, vóór het root-element -->
<registratie>
  <!-- Commentaar tussen elementen -->
  <naam>Jan de Vries</naam>
  <!-- Nog een opmerking -->
</registratie>
<!-- Commentaar na het root-element (geldig maar ongebruikelijk) -->
```

### Regels voor commentaar

Er zijn twee belangrijke regels:

1. **Commentaar mag geen dubbel streepje (`--`) bevatten** behalve als onderdeel van de sluitsequentie `-->`.

   Fout:
   ```xml
   <!-- Dit is een -- onjuist commentaar -->
   ```

   Goed:
   ```xml
   <!-- Dit is een correct commentaar -->
   ```

2. **Commentaar mag niet eindigen op een streepje vóór `>`**:

   Fout:
   ```xml
   <!-- Eindigt op drie streepjes --->
   ```

### Praktisch gebruik

In de context van gegevensuitwisseling wordt commentaar spaarzaam gebruikt. XML-berichten die tussen systemen worden verstuurd bevatten zelden commentaar – het is immers extra data die verstuurd maar niet verwerkt wordt. Commentaar is wél handig in:

- **Documentatie en voorbeeldbestanden** – om uit te leggen wat een bepaald element betekent
- **Configuratiebestanden** – om opties toe te lichten
- **Tijdelijk uitschakelen** van XML-fragmenten tijdens het testen

> **Let op:** Hoewel je met commentaar een stuk XML kunt "uitschakelen", werkt dit niet als het uitgeschakelde fragment zelf al commentaar bevat – je kunt commentaar niet nesten.

## CDATA-secties

### Het probleem

In sectie 1.2 heb je geleerd dat bepaalde tekens in XML een speciale betekenis hebben en daarom vervangen moeten worden door entity references. Zo moet `<` worden geschreven als `&lt;` en `&` als `&amp;`.

Maar wat als je een groot stuk tekst hebt dat vol staat met zulke tekens? Denk bijvoorbeeld aan een stukje programmacode of een rekenformule:

```
if (temperatuur < 0 && windsnelheid > 50) { alarm = true; }
```

Als je dit in een normaal XML-element wilt opnemen, moet je alle speciale tekens vervangen:

```xml
<conditie>if (temperatuur &lt; 0 &amp;&amp; windsnelheid &gt; 50) { alarm = true; }</conditie>
```

Dat is lastig leesbaar en foutgevoelig. Hier biedt een **CDATA-sectie** uitkomst.

### Wat is CDATA?

CDATA staat voor **Character Data**. Een CDATA-sectie is een blok tekst waarin je alle tekens letterlijk kunt gebruiken, zonder dat de XML-verwerker ze als markup interpreteert. De verwerker leest de inhoud als "gewone tekst" en doet er verder niets mee.

### Syntax

Een CDATA-sectie begint met `<![CDATA[` en eindigt met `]]>`:

```xml
<conditie><![CDATA[if (temperatuur < 0 && windsnelheid > 50) { alarm = true; }]]></conditie>
```

Nu kun je `<`, `>`, `&` en alle andere tekens gewoon gebruiken zonder entity references.

### CDATA over meerdere regels

CDATA-secties mogen meerdere regels beslaan:

```xml
<script><![CDATA[
function controleer(waarde) {
    if (waarde < 0) {
        return "negatief";
    } else if (waarde > 100) {
        return "te hoog";
    }
    return "ok";
}
]]></script>
```

### Regels voor CDATA

| Regel | Toelichting |
|---|---|
| De tekst `]]>` mag niet voorkomen in de CDATA-inhoud | Dit is de sluitsequentie, dus het zou het blok voortijdig beëindigen |
| CDATA-secties kunnen niet genest worden | Je kunt geen `<![CDATA[ ... <![CDATA[ ... ]]> ... ]]>` schrijven |
| CDATA mag alleen in element-inhoud staan | Niet in attribuutwaarden of in een tag zelf |

### CDATA vs. entity references

Wanneer gebruik je wat?

| Situatie | Aanbevolen aanpak |
|---|---|
| Een enkel speciaal teken in gewone tekst | Entity reference (`&lt;`, `&amp;`) |
| Veel speciale tekens, bijv. ingesloten code | CDATA-sectie |
| Attribuutwaarden met speciale tekens | Entity references (CDATA kan hier niet) |

### CDATA in de praktijk bij StUF

In StUF-berichten komen CDATA-secties niet vaak voor, maar ze worden soms gebruikt wanneer een element vrije tekst of een ingebed document bevat – bijvoorbeeld wanneer een XML-fragment als tekst wordt meegegeven binnen een groter bericht. Het is goed om te weten dat ze bestaan, ook al zul je ze niet dagelijks tegenkomen.

## Processing instructions

### Wat is het?

Een **processing instruction** (verwerkingsinstructie, afgekort: PI) is een speciale aanwijzing in het XML-document die bedoeld is voor een specifieke toepassing. Het is een manier om extra informatie mee te geven aan software die het document verwerkt, zonder dat die informatie onderdeel is van de XML-gegevens zelf.

### Syntax

Een processing instruction begint met `<?`, gevolgd door een **doelnaam** (target) en optioneel aanvullende tekst, en eindigt met `?>`:

```xml
<?doelnaam instructietekst?>
```

### Het meest voorkomende voorbeeld: stylesheets

De bekendste processing instruction is de verwijzing naar een XSLT-stylesheet:

```xml
<?xml-stylesheet type="text/xsl" href="weergave.xsl"?>
```

Dit vertelt een toepassing (bijv. een browser): *"Als je dit document wilt weergeven, gebruik dan het stylesheet `weergave.xsl` om de XML om te zetten naar een leesbaar formaat."*

### Ander voorbeeld

```xml
<?mijn-applicatie verwerk-als="kennisgeving" prioriteit="hoog"?>
```

Hier is `mijn-applicatie` het doel en is de rest instructietekst die de betreffende applicatie kan interpreteren. Andere applicaties negeren deze PI – ze is specifiek voor `mijn-applicatie`.

### Regels voor processing instructions

- De doelnaam mag **niet** `xml` zijn (in welke hoofdlettercombinatie ook) – die is gereserveerd voor de XML-declaratie
- Een PI mag overal in het document staan, behalve binnen een tag
- De inhoud van een PI is vrije tekst – er is geen vaste syntax voor het deel na de doelnaam

### Waar in het document?

Processing instructions staan meestal aan het begin van het document, direct na de XML-declaratie:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="stuf-weergave.xsl"?>
<kennisgeving xmlns="http://www.egem.nl/StUF/StUF0301">
  <!-- ... inhoud ... -->
</kennisgeving>
```

### Processing instructions bij StUF

In StUF-berichten worden processing instructions zelden gebruikt. SOAP-webservices verwerken berichten programmatisch, niet via stylesheets. Maar in test- en documentatiescenario's kun je een `xml-stylesheet` PI tegenkomen om een StUF-bericht leesbaar weer te geven in een browser.

## Alles samen: een compleet XML-document

Laten we alle speciale constructies combineren in één voorbeeld:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="weergave.xsl"?>
<!-- Voorbeeld-kennisgeving voor de StUF-cursus -->
<kennisgeving xmlns="http://www.egem.nl/StUF/StUF0301"
              xmlns:bg="http://www.egem.nl/StUF/sector/bg/0310">
  <stuurgegevens>
    <!-- Gegevens over zender en ontvanger -->
    <zender>BRP-systeem</zender>
    <ontvanger>Zaaksysteem</ontvanger>
    <tijdstip>2026-03-04T09:15:00</tijdstip>
  </stuurgegevens>
  <inhoud>
    <bg:natuurlijkPersoon>
      <bg:voornamen>Jan</bg:voornamen>
      <bg:geslachtsnaam>Vries</bg:geslachtsnaam>
      <bg:opmerking><![CDATA[Naam bevat voorvoegsel "de" – let op: de < en > tekens zijn hier gewoon tekst]]></bg:opmerking>
    </bg:natuurlijkPersoon>
  </inhoud>
</kennisgeving>
```

In dit document zie je:
1. **XML-declaratie** (regel 1) – versie en encoding
2. **Processing instruction** (regel 2) – verwijzing naar een stylesheet
3. **Commentaar** (regel 3 en 8) – toelichting voor de menselijke lezer
4. **Namespaces** (regel 4-5) – default namespace en prefix `bg`
5. **CDATA-sectie** (regel 16) – vrije tekst met speciale tekens
6. **Elementen en attributen** – de reguliere XML-structuur

> **Tip:** Dit is in grote lijnen de opbouw van een echt StUF-bericht. In Deel 3 ga je dergelijke berichten in detail ontleden.

## Minder voorkomende constructies

Volledigheidshalve nog twee constructies die je zelden tegenkomt, maar die je moet kunnen herkennen:

### DTD (Document Type Definition)

Vóórdat XSD bestond, werd de structuur van XML-documenten beschreven met een **DTD**. Een DTD kan intern in het document staan of extern worden verwezen:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE kennisgeving SYSTEM "kennisgeving.dtd">
<kennisgeving>
  <!-- ... -->
</kennisgeving>
```

De `<!DOCTYPE ...>` declaratie staat na de XML-declaratie maar vóór het root-element. In de wereld van StUF worden geen DTD's gebruikt – alles wordt gedefinieerd met XSD (wat je leert in Deel 2). Je hoeft DTD's niet te kunnen schrijven, maar het is goed om de syntax te herkennen zodat je niet schrikt als je het tegenkomt in oudere documenten.

### Entiteit-declaraties

Binnen een DTD kun je eigen **entiteiten** definiëren – herbruikbare stukjes tekst:

```xml
<!DOCTYPE bericht [
  <!ENTITY organisatie "VNG Realisatie">
]>
<bericht>
  <afzender>&organisatie;</afzender>
</bericht>
```

Hier wordt `&organisatie;` vervangen door "VNG Realisatie". Dit is vergelijkbaar met de ingebouwde entity references (`&lt;`, `&amp;`) maar dan zelfgedefinieerd. In de praktijk van StUF kom je dit niet tegen.

## Samenvatting

| Constructie | Syntax | Doel |
|---|---|---|
| **XML-declaratie** | `<?xml version="1.0" encoding="UTF-8"?>` | Versie en tekencodering aangeven |
| **Commentaar** | `<!-- tekst -->` | Toelichting voor menselijke lezers |
| **CDATA-sectie** | `<![CDATA[ tekst ]]>` | Tekst met speciale tekens letterlijk opnemen |
| **Processing instruction** | `<?doel instructie?>` | Aanwijzing voor een specifieke toepassing |
| **DOCTYPE** | `<!DOCTYPE root SYSTEM "...">` | Verwijzing naar een DTD (verouderd in StUF-context) |

> **Kernpunt:** Naast elementen en attributen kent XML een aantal speciale constructies. De belangrijkste is de XML-declaratie, die versie en encoding aangeeft. Commentaar maakt documenten leesbaar voor mensen. CDATA-secties staan toe om tekst met speciale tekens letterlijk op te nemen. Processing instructions geven aanwijzingen aan specifieke toepassingen. In de dagelijkse praktijk van StUF kom je vooral de XML-declaratie en incidenteel commentaar tegen.
