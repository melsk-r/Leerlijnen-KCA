---
title: "5.2 XML-namespaces"
date: 2026-03-12
weight: 3
leerlijn: 5
paragraaf: "5.2"
leerdoel: "Leerdoel nog toe te voegen"
---

<!--div class="back-link-wrapper">
  <a href="{{< relref "../05-xml-technologieen" >}}" class="back-link">Terug naar leerlijn 5</a>
</div-->

## 5.2 XML-namespaces

Begrijpt het doel van XML-namespaces en kan de techniek daarachter toepassen in de context van meerdere standaarden.

### Probleem

In de voorgaande paragraaf gaven we al aan dat XML voorziet in een techniek waarmee computers, maar ook mensen, ondubbelzinnig kunnen begrijpen wat elk gegeven betekent.
Maar wat als je nu gegevens wil vastleggen of uitwisselen over verschillende entiteiten die weliswaar een andere betekenis hebben maar wel dezelfde naam gebruiken. Denk bijvoorbeeld aan een 'Bank', dit kan een meubel zijn maar ook een financiële instelling. In de onderstaande tabel geven we nog een aantal voorbeelden:

| Begrip | Betekenis 1 | Betekenis 2 |
| --- | --- | --- |
| Gerecht | Een bereide maaltijd. | Een rechtbank of juridische instantie. |
| Stam | De hoofdsteel van een boom. | De basisvorm van een werkwoord. |
| Bal | Een bolvormig voorwerp. | Een formele dansparty (het bal). |
| Schimmel | Een schimmelcultuur (op voedsel of huid). | Een wit paard. |
| Vorst | Temperatuurrange waarbij water bevriest (vrieskou). | Een staatshoofd (koning/keizer). |

Stel nu dat we in 1 XML bestand zowel een 'vorst' element in de zin van een temperatuurrange als een 'vorst' element in de zin van een staatshoofd willen opnemen. Het volgende XML fragment geeft daar een voorbeeld van.

```xml
<gebeurtenis>
  <titel>Vorst aan de grond.</titel>
  <beschrijving>
    Bij een temperatuur van
    <vorst>-10 graden</vorst>
    viel
    <vorst>Koning Willem Alexander<vorst>
    op de grond.
  </beschrijving>
</gebeurtenis>
```
Als mens kunnen we dit op de e.o.a. wijze wel duiden maar een computer kan dat niet. 

## Oplossing

Hier komen XML namespaces om de hoek kijken. Een namespace is een collectie van namen, oftewel het vocabulaire van een bepaald informatiedomein waarbinnen termen uniek zijn. Nu is het weliswaar handig om te weten dat de naam 'vorst' in de 'weerkunde' namespace iets anders betekent dan in de 'staatsrechtelijk' namespace maar daar heb je op zich nog niet zo veel aan in een XML bestand. Daarmee kan een computer immers nog niet bepalen wanneer het element 'vorst' nu als iets weerskundig of als iets staatsrechtelijk' moet worden geïnterpreteerd. XML voorziet echter in een techniek om deze termen uniek te maken. Dat uniek maken gebeurd m.b.v. een URI en wel door die te declareren in het XML bestand. De URI is dus de identifier van de namespace. Het declareren kan in principe op elke gewenst niveau in een XML bestand zolang het maar niet gebeurd op een lager niveau dan waar voor het eerst een element in die namespace wordt gebruikt. Over het algemeen gebeurd de declaratie echter op het root element. De declaratie vindt plaats m.b.v. het attribute 'xmlns' dat als waarde de URI heeft. Hieronder het voorgaande voorbeeld uitgebreid met een namespace declaratie.

```xml
<gebeurtenis xmlns="http://www.gebeurtenissen.nl">
  <titel>Vorst aan de grond.</titel>
  <beschrijving>
    Bij een temperatuur van
    <vorst>-10 graden</vorst>
    viel
    <vorst>Koning Willem Alexander<vorst>
    op de grond.
  </beschrijving>
</gebeurtenis>
```

Hiermee worden alle elementen toegekend aan de namespace met de identifier "http://www.gebeurtenissen.nl". Die namespace bevat dus de begrippen:
* gebeurtenis
* titel
* beschrijving
* vorst

Dit lost het probleem dus nog niet op want in feite is er in dit fragment sprake van 3 verschillende namespaces, nl.:
* gebeurtenissen
* weerkunde
* staatsrechtelijk

We moeten dus nog enkele namespaces declareren. Laten we het voorbeeld uitbreiden:

```xml
<gebeurtenis xmlns="http://www.gebeurtenissen.nl"
             xmlns="http://www.weerkunde.nl"
             xmlns="http://www.staatsrecht.nl">
  <titel>Vorst aan de grond.</titel>
  <beschrijving>
    Bij een temperatuur van
    <vorst>-10 graden</vorst>
    viel
    <vorst>Koning Willem Alexander<vorst>
    op de grond.
  </beschrijving>
</gebeurtenis>
```

Helaas biedt ook dit ons nog steeds niet de mogelijkheid de elementen aan een van de namespaces toe te kennen. Daarom kunnen we elke namespace declaratie voorzien van een alias. De syntax daarvoor is `xmlns:[alias]`.
De namespace declaraties op het root element kunnen we daarmee als volgt aanpassen:

```xml
xmlns:gbrt="http://www.gebeurtenissen.nl"
xmlns:weer="http://www.weerkunde.nl"
xmlns:stsr="http://www.staatsrecht.nl"
```
in het fragment zelf kunnen we vervolgens de elementen aan de namespaces koppelen met de volgende syntax `[alias]:[elementnaam]` dat moet zowel op de begintags als op de eindtags. Het complete XML fragment komt er daarmee als volgt uit te zien:

```xml
<gbrt:gebeurtenis xmlns:gbrt="http://www.gebeurtenissen.nl"
             xmlns:weer="http://www.weerkunde.nl"
             xmlns:stsr="http://www.staatsrecht.nl">
  <gbrt:titel>Vorst aan de grond.</gbrt:titel>
  <gbrt:beschrijving>
    Bij een temperatuur van
    <weer:vorst>-10 graden</weer:vorst>
    viel
    <stsr:vorst>Koning Willem Alexander<stsr:vorst>
    op de grond.
  </gbrt:beschrijving>
</gbrt:gebeurtenis>
```

Het is zelfs mogelijk om een van die namespaces als default namespace aan te wijzen. Dat gebeurd door voor die namespace geen alias te definiëren en de elementen in die namespace dan ook geen prefix te geven. Het fragment ziet er dan als volgt uit:

```xml
<gebeurtenis xmlns="http://www.gebeurtenissen.nl"
             xmlns:weer="http://www.weerkunde.nl"
             xmlns:stsr="http://www.staatsrecht.nl">
  <titel>Vorst aan de grond.</titel>
  <beschrijving>
    Bij een temperatuur van
    <weer:vorst>-10 graden</weer:vorst>
    viel
    <stsr:vorst>Koning Willem Alexander<stsr:vorst>
    op de grond.
  </beschrijving>
</gebeurtenis>
```
