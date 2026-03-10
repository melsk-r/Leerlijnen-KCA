---
title: "5.4 XML-schemavalidatie"
date: 2026-03-04
weight: 5
leerlijn: 5
paragraaf: "5.4"
leerdoel: "Leerdoel nog toe te voegen"
---

<div class="back-link-wrapper">
  <a href="{{< relref "../05-xml-technologieen" >}}" class="back-link">Terug naar leerlijn 5</a>
</div>

## 5.4 XML-schemavalidatie

Begrijpt XML-schemavalidatie en kan documenten valideren tegen schema's.

### Welgevormdheid: de basisregels

Een XML-document dat aan alle syntaxregels voldoet, heet **welgevormd** (Engels: *well-formed*). Een document dat niet welgevormd is, wordt door elke XML-verwerker geweigerd — er is geen "beetje goed".

**Regel 1: Precies één root-element**

```xml
<!-- Fout: -->
<voornaam>Jan</voornaam>
<achternaam>de Vries</achternaam>

<!-- Goed: -->
<persoon>
  <voornaam>Jan</voornaam>
  <achternaam>de Vries</achternaam>
</persoon>
```

**Regel 2: Elke openingstag heeft een sluitingstag**

```xml
<!-- Fout: -->
<voornaam>Jan

<!-- Goed: -->
<voornaam>Jan</voornaam>
```

**Regel 3: Elementen moeten correct genest zijn**

```xml
<!-- Fout (kruisend): -->
<naam><voornaam>Jan</naam></voornaam>

<!-- Goed: -->
<naam><voornaam>Jan</voornaam></naam>
```

**Regel 4: Attribuutwaarden staan tussen aanhalingstekens**

```xml
<!-- Fout: -->
<inwoner bsn=123456789>

<!-- Goed: -->
<inwoner bsn="123456789">
```

**Regel 5: XML is hoofdlettergevoelig**

`<Naam>`, `<naam>` en `<NAAM>` zijn drie *verschillende* elementen. De sluitingstag moet exact overeenkomen met de openingstag.

### Naamgevingsregels

| Geldig | Ongeldig | Reden |
|---|---|---|
| `<voornaam>` | `<1e-naam>` | Begint met een cijfer |
| `<achter_naam>` | `<achter naam>` | Bevat een spatie |
| `<geboorte-datum>` | `<xml-datum>` | Begint met "xml" (gereserveerd) |
| `<_tijdelijk>` | `<-test>` | Begint met een koppelteken |

In StUF worden vaak **camelCase** (bijv. `geboortedatum`) of **koppeltekens** (bijv. `geboorte-datum`) gebruikt.

### Speciale tekens en entity references

Sommige tekens hebben een speciale betekenis in XML en moeten vervangen worden:

| Teken | Probleem | Oplossing |
|---|---|---|
| `<` | Begin van een tag | `&lt;` |
| `>` | Einde van een tag | `&gt;` |
| `&` | Begin van een entity | `&amp;` |
| `"` | Conflicteert met attribuutwaarden | `&quot;` |
| `'` | Conflicteert met attribuutwaarden | `&apos;` |

Voorbeeld:

```xml
<opmerking>Temperatuur is &lt; 0 graden</opmerking>
```

### Compleet voorbeeld

```xml
<kennisgeving>
  <stuurgegevens>
    <zender>BRP-systeem</zender>
    <ontvanger>Zaaksysteem</ontvanger>
    <tijdstip>2026-03-03T10:30:00</tijdstip>
  </stuurgegevens>
  <inhoud>
    <inwoner burgerservicenummer="123456789">
      <naam>
        <voornaam>Jan</voornaam>
        <voorvoegsel>de</voorvoegsel>
        <achternaam>Vries</achternaam>
      </naam>
      <adres type="woonadres">
        <straat>Kerkstraat</straat>
        <huisnummer>12</huisnummer>
        <postcode>3511AB</postcode>
        <woonplaats>Utrecht</woonplaats>
      </adres>
      <overlijdensdatum/>
    </inwoner>
  </inhoud>
</kennisgeving>
```

Dit voorbeeld bevat alle behandelde concepten: één root-element, nesting, tekst-elementen, attributen, een leeg element en correcte welgevormdheid. De structuur met stuurgegevens en inhoud lijkt al op een StUF-bericht (zie leerlijn 6).
