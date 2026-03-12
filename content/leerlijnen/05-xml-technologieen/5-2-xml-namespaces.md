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

### Doel

In de voorgaande paragraaf gaven we al aan dat XML voorziet in een techniek waarmee computers, maar ook mensen, ondubbelzinnig kunnen begrijpen wat elk gegeven betekent.
Maar wat als je nu gegevens wil vastleggen of uitwisselen over entiteiten die weliswaar een verschillende betekenis hebben maar wel dezelfde naam gebruiken. Denk bijvoorbeeld aan een 'Bank', dit kan een meubel zijn maar ook een financiële instelling. In de onderstaande tabel geven we nog een aantal voorbeelden:

| Begrip | Betekenis 1 | Betekenis 2 |
| --- | --- | --- |
| Gerecht | Een bereide maaltijd. | Een rechtbank of juridische instantie. |
| Stam | De hoofdsteel van een boom. | De basisvorm van een werkwoord. |
| Bal | Een bolvormig voorwerp. | Een formele dansparty (het bal). |
| Schimmel | Een schimmelcultuur (op voedsel of huid). | Een wit paard. |
| Vorst | Bevroren water (vrieskou). | Een staatshoofd (koning/keizer). |

In dat geval komen XML namespaces om de hoek kijken. Een namespace is een vocabulaire van een bepaald informatiedomein waarbinnen termen uniek zijn. Het is weliswaar handig om te weten dat de naam 'vorst' in de 'Weerkunde' namespace iets anders betekent dan in de 'Staatsrechtelijke' namespace maar daar heb je op zich nog niet zo veel aan in een XML bestand want daarmee kan een computer ze nog bepalen wanneer het element 'vorst' nu als iets weerskundig of als iets staatsrechtelijk' moet worden geïnterpreteerd. Het volgende XML fragment geeft daar een voorbeeld van.

```xml
<gebeurtenis>
  <titel>Vorst aan de grond.</titel>
  Bij een temperatuur van <vorst>-10 graden</vorst> viel <vorst>Koning Willem Alexander<vorst> op de grond.
</gebeurtenis>
```

XML voorziet in een techniek om deze termen uniek te maken. 
