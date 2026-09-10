---
title: "6.2 StUF folder en XML-Schema structuur"
date: 2026-08-06
weight: 602
leerlijn: 6
paragraaf: "6.2"
parent: "StUF-standaard"
leerdoel: "De folderstructuur van de StUF standaarden kennen en weten hoe de verschillende XML-Schema bestanden met elkaar samenhangen."
---

## 6.2 StUF folder en XML-Schema structuur

In deze paragraaf gaan we in op de folder en XML-Schema structuur van StUf 3.01 en de bijbehorende sectormodellen. Op versie 2.04 van de standaard komen we in een ander onderdeel kort terug.

Voor het kunnen browsen door de StUF folderstructuur en het inhoudelijk kunnen onderzoeken van de XML-Schema's is het van belang om [het zip bestand met de laatste complete patch van StUF 3.01](https://vng-realisatie.github.io/StUF-onderlaag/documenten/20260227_patch34.zip) op te halen. Een andere optie is het plaatsen van de StUFmaster in je eigen folder structuur. De StUFmaster is een beheerinstrument van VNG Realisatie waarmee we versiebeheer toepassen of alle StUF (gerelateerde) documenten en is alleen voor beheerders van StUF en bij VNG Realisatie in beheer zijnde sectormodellen en koppelvlakken beschikbaar. 

> **Let op!** De in dit onderdeel behandelde en getoonde folderstructuren zijn de structuren die als zodanig zijn vastgelegd in de StUFmaster. Bij het publiceren van de zip files voor (patches op) de specifieke StUF standaarden kan het zijn dat niet alle folders terug komen. De zip file voor StUF-BG bevat bijvoorbeeld niet de folders voor StUF-ZKN (zkn0310) en StUF-ZTC (ztc0310). Als de folders echter wel zijn opgenomen dan zijn ze opgenomen in de structuur die in de StUFmaster is vastgelegd.

Neem voor het plaatsen van de StUFmaster contact op met de beheerder van de StUF standaard.
Daarnaast moet je ook kunnen beschikken over een XML-Schema viewer/editor.

### XML-Schema folderstructuur

De StUF standaard en de bijbehorende sectormodellen bestaat naast de documentatie ook uit een set van XML-Schemabestanden die een sterke samenhang hebben met elkaar. De folderstructuur waarin die XML-Schema bestanden zijn opgeslagen is een vast gegeven en de folders, en daarmee ook de XML-Schema bestanden, bevinden zich altijd op dezelfde relatieve locatie van elkaar. We leggen deze structuur hier uit.

**Basisfolderstructuur (0301)**

<img width="150" alt="basisfolder structuur" src="/Leerlijnen-KCA/images/basisfolderstructuur.jpg" />

De XML- en WSDL-schema's behorende bij de onderlaag van de StUF 3.01 standaard bevinden zich in de folder 301. Het gaat daarbij om de volgende bestanden:
* **stuf0301.xsd**<br/>
* **stuf0301mtom.xsd**<br/>Bevat componenten voor het kunnen opnemen van binaire inhoud in de StUF-berichten van de sectormodellen en koppelvlakken.
* **stuf0301_services.wsdl**<br/>Definieert portType en Binding componenten voor het triggerbericht.
* **stuf0301_types.wsdl**<br/>Definieert in WSDL termen de mesages voor de bevestigings-, fout- en triggerberichten.

Deze folder is essentieel voor alle StUF 3.01 XML-Schema's en moet dus ook altijd aanwezig zijn.

**Ondersteunende standaarden**

In de set van bij StUF benodigde folders komen ook wat folders voor die noodzakelijk zijn omdat de XML-Schema's daarin direct of indirect worden geïmporteerd in de StUF XML-Schema's. Het gaat om die roze gearceerde folders in de onderstaande image:

<img width="150" alt="Ondersteunende standaarden" src="/Leerlijnen-KCA/images/ondersteunende-standaarden.jpg" />

Een van die standaarden is 'GML' (Geography Markup Language). Dit is een open, internationaal erkend bestandsformaat (gebaseerd op XML), dat wordt gebruikt om geografische informatie en digitale kaarten op te slaan en uit te wisselen. Deze standaard wordt bijv, gebruikt in de entiteit 'Wegdeel' (StUF-BG 3.10) om de geometrische informatie van zo'n wegdeel mee te kunnen verzenden.

**Sectormodellen en hun berichtencatalogi**

<img width="150" alt="Sectormodellen" src="/Leerlijnen-KCA/images/sectormodellen.jpg" />

De hier blauw gearceerde folders bevatten de implementaties van de horizontale sectormodellen. Dat is naast documentatie ook XML-Schema en voorbeeld WSDL-Schema componenten waarmee de entiteittypes, attribuuttypes, relatietypes en berichten die gerelateerd zijn met een informatiemodel.

De folder bg0310 betreffen de XML-Schema's en voorbeeld WSDL-Schema's voor het op het informatiemodel RSGB gebaseerde sectormodel StUF-BG 3.10. De folder zkn0310 betreffen de XML-Schema's en voorbeeld WSDL-Schema's voor het op het informatiemodel RGBZ gebaseerde sectormodel StUF-ZKN 3.10. En de folder bg0310 betreffen tenslotte de XML-Schema's en voorbeeld WSDL-Schema's voor het op het informatiemodel ImZTC gebaseerde sectormodel StUF-ZTC 3.10. 

Naast de folder 'entiteiten', waarin de generieke XML-Schema definities per entiteit (zie de informatiemodellen) te vinden zijn, bevatten deze sectormodellen ook nog standaard een tweetal folders die we berichtencatalogi noemen:

* mutatie
* vraagAntwoord 

<img width="210" alt="Berichtencatalogi" src="/Leerlijnen-KCA/images/berichtencatalogi.jpg" />

Deze folders bevatten op de functie van de berichtencatalogus toegespitste aanscherpingen van de in te folder 'entiteiten' gedefinieerde XML-Schema  definities. En daarnaast ook op de functie van de berichtencatalogus betrekking hebbende voorbeeld WSDL-Schema's.

Een berichtcatalogus specificeert samen met zijn voorbeeld-wsdl's een verzameling services waarvan een systeem moet aangeven in hoeverre het deze implementeert in voor dat systeem specifieke wsdl's.

**Koppelvlak berichtcatalogi**

Naast de standaard berichtcatalogi kunnen er in een sectormodel ook berichtencatalogi zijn opgenomen voor specifieke op een sectormodel gebaseerde koppelvlakken. Hieronder zie je voor zowel het sectormodel StUF-BG als StUF-ZKN een voorbeeld:

<img width="200" alt="Koppelvlakmappen berichtencatalogi" src="/Leerlijnen-KCA/images/koppelvlakmappen-berichtencatalogi.jpg" />

In de berichtcatalogus 'bg0310/bag' vind je de definitie van samengestelde berichten, gebaseerd op de werkprocessen BAG, die gebruikt kunnen worden voor het melden van BAG mutaties aan alle andere gemeentelijke applicaties.

In de berichtcatalogus 'zkn0310/zs-dms' vind je de definitie van de berichten waarmee aan zaaksysteem en een document management systeem gevuld en bevraagd kan worden.

### Samenhang XML-Schema's

Laten we eens naar de XML-Schema's van StUF-BG 3.10 (folder 'bg0310') kijken zonder daarbij al te veel te focussen op de inhoudelijke aspecten van de in deze XML-Schema's opgenomen entiteiten maar vooral op de generiek toegepaste principes. We nemen daarvoor als voorbeeld het redelijk eenvoudige entiteittype 'Huishouden' (ook wel bekend onder de mnemonic 'HHD' dat in het RSGB aan gerelateerde objecttype is toegekend) en starten met de basale complexTypes. Daarna kijken we hoe de complexTypes voor de mutatie-, synchronisatie- en vraagAntwoordberichten zich hiertoe verhouden. Daar waar nodig zullen we daar andere entiteiten bij betrekken. Voor andere sectormodellen zoals StUF-ZKN 3.10 gelden dezelfde principes.

Op vrije berichten gaan we in dit onderdeel niet in. Vrije berichten vinden hun toepassing nagenoeg alleen binnen de StUF koppelvlakken, reden waarom we daarop in het onderdeel dat daarover gaat op terugkomen.

### _XML-Schema's in 'entiteiten'_

Deze folder bevat de volgende schema's:
* bg0310_ent_basis.xsd
* bg0310_simpleTypes.xsd
* bg0310_stuf_simpleTypes.xsd

***bg0310_ent_basis.xsd***

*Complextypes voor basis entiteittypes*

In 'bg0310_ent_basis.xsd' zijn alle complexTypes die een rol spelen binnen StUF-BG 3.10 op een generieke wijze gemodelleerd opgenomen. Dus zodanig dat ze als basis kunnen dienen voor alle type berichten. In principe is voor elk entiteittype in het RSGB (zie daarvoor de module over het RSGB informatiemodel) een basis complexType gedefinieerd (`XXX-basis`). 'In principe' omdat we bij het verStUFfen van het RSGB technische keuzes maken. Op het verStUFfen zullen we in een ander onderdeel van deze module in gaan. Voor 'HHD' is in ieder geval de complexType `HHD-basis` opgenomen die er als volgt uitziet:

<img width="400" alt="HHD-basis complexType" src="/Leerlijnen-KCA/images/HHD-basis.jpg" />

Dit is tevens de maximale omvang van een antwoordbericht.

Zoals je ziet zijn alle elementen hierin optioneel. Daardoor kunnen we in restrictions op dit complexType die we voor de specifieke berichten maken elk element dat we willen weglaten. Binnen een basis complexType zijn de complexTypes voor de historie-elementen overigens altijd basis complexTypes.

*Relaties*

Basis entiteiten kunnen relaties bevatten. Zo bevat `HHD-basis` de relaties `BG:isGehuisvestIn` en `BG:heeftAlsLeden`. De wijze waarop relaties worden opgenomen in basis entiteiten is nagenoeg altijd hetzelfde. In de entiteit die de relatie bevat wordt deze relatie als een element met een onderscheidende naam (bijv. `heeftAlsOuders`) opgenomen en altijd na de `StUF:tijdvakGeldigheid`, `StUF:tijdstipRegistratie`, `StUF:extraElementen`, `historieMaterieel` en/of `historieFormeel` elementen geplaatst. 

Een relatie wordt altijd gemodelleerd in een eigen complexType waarvan de naamgeving (in de basis complexType) aan de volgende syntax voldoet:

`[Mnemonic van het entiteittype dat de relatie bevat][Mnemonic van het entiteittype waar de relatie naartoe loopt of een specialisatie daarvan]-basis`

Voor de relatie `BG:heeftAlsLeden` heet de complexType dus `HHDNPS-basis` die er als volgt uitziet:

<img width="400" alt="HHDNPS-basis complexType" src="/Leerlijnen-KCA/images/HHDNPS-basis.jpg" />

De mnemonic 'NPS' betekent overigens 'Natuurlijk Persoon'.

Een relatie element bevat altijd minimaal het `gerelateerde` element (waarover zo meer) maar mag ook andere elementen bevatten die dan iets zeggen over de relatie (maar niets over de `gerelateerde` entiteit). Zo kan de relatie `BG:heeftAlsEchtgenootPartner` die 2 'NPS' entiteiten met elkaar verbindt ook de elementen `BG:soortVerbintenis` en `BG:datumSluiting` bevatten. 

> **Let op!** In het RSGB zijn relaties met eigen attributen over het algemeen gemodelleerd als relatieklassen (zie leerlijn 7). In een StUF sectormodel wordt die constructie vaak vertaalt naar een relatie element met naast het element `gerelateerde` o.a. ook de attribuutsoorten zoals gedefinieerd in de relatieklasse. In het onderdeel waarin we het verStUFfingsdocument bespreken komen we daar op terug.

Daarnaast kan een relatie ook nog elementen als `StUF:tijdvakRelatie`, `StUF:tijdvakGeldigheid`, `StUF:tijdstipRegistratie`, `StUF:extraElementen` en `historieMaterieel` bevatten en zelfs eigen relaties.

Zoals gezegd bevatten relaties ook een `gerelateerde` element die binnen een basis complexType zelf ook altijd een basis complexType is. Normaliter krijgen de `BG:gerelateerde` elementen dus een complexType toegekend met de naam `xxx-basis`. 

> **Let op!** In het geval van `HHDNPS-basis` hebben de complexTypes voor de gerelateerde echter de wat uitgebreidere namen `TGOAOT-basis` en `NPSNINING-basis`. In het onderdeel waarin we het verStUFfingsdocument bespreken leggen we uit waarom hier in sommige situaties voor wordt gekozen.

*Andere gebruikelijke elementen*

Naast relaties kunnen basis entiteiten ook nog de volgende elementen bevatten:
* `StUF:tijdvakGeldigheid`
* `StUF:tijdvakRelatie`
* `StUF:tijdstipRegistratie`
* `StUF:extraElementen`
* `historieMaterieel`
* `historieFormeel`

De functie van de eerste drie en de laatste twee heeft te maken met temporele aspecten van het vastleggen van gegevens en relaties. Voor de meeste attributen en een aantal relaties is het van belang vast te leggen op welk tijdstip deze welke waarde had, een aanvang hebben genomen of juist zijn beëindigd. Niet alleen m.b.t. de waarde in de werkelijkheid (materiële historie) maar ook m.b.t. de waarde in de registratie (formele historie). Dit is bijv. van belang wanneer in juridische procedures moet worden aangetoond dat op een bepaald tijdstip bekend was dat iets een bepaalde waarde had. Het element `StUF:tijdvakGeldigheid` heeft daarbij betrekking op tijdstippen in de werkelijkheid van gegevens, `StUF:tijdvakRelatie` op tijdstippen in de werkelijkheid van relaties en `StUF:tijdstipRegistratie` op tijdstippen in de registratie.

Het element `StUF:extraElementen` is tenslotte bedoelt om te voorzien in enige flexibiliteit in de XML-Schema's zodat leveranciers t.b.v. eigen behoeftes extra informatie met een bericht mee kunnen sturen. Tevens wordt dit op het moment veelvuldig gebruikt om XML-Schema's wel aan te kunnen passen aan nieuwe wetgeving zonder de backwardse compatibiliteit van de XML-Schema's aan te tasten. Omdat deze extraElementen relatief vrije elementen zijn (er wordt met een spreadsheet gestandaardiseerd) kan hier geen acurate schema-validatie op plaatsvinden. Dat gaat ten kosten van de standaard-operabiliteit.

Binnen StUF 3.01 is het ook mogelijk om het met `StUF:extraElementen` vergelijkbare maar krachtiger constructie `StUF:aanvullendeElementen` te gebruiken. Daarmee kan wel schema-validatie worden toegepast. Tot noch toe is dat echter nog nergens binnen een StUF 3.01 sectormodel of koppelvlak geïmplementeerd.

*Complextypes voor kerngegevens*

Naast de complexType 'HHD-basis' is ook het complexType `HHD-kerngegevens` aanwezig:  

<img width="400" alt="HHD-kerngegevens complexType" src="/Leerlijnen-KCA/images/HHD-kerngegevens.jpg" />

Kerngegevens zijn een deelverzameling van de attributen en relaties van een entiteittype aan de hand waarvan een object kan worden geïdentificeerd. `HHD-kerngegevens` definieert dus de kerngegevens van het 'HHD' entiteittype en wordt gedefinieerd als een restriction op het “HHD-basis” complexType met als elementen diens kerngegevens en de kernrelaties. Ook hierin zijn alle elementen optioneel omdat niet altijd alle kerngegevens beschikbaar hoeven te zijn. Binnen een kerngegevens complexType zijn de complexTypes voor de gerelateerden van relaties en voor de historie-elementen altijd kerngegevens complexTypes.

*Elementgroepen en tabel-entiteiten*

Tenslotte bevat het XML-Schema 'bg0310_ent_basis.xsd' ook nog complexTypes voor elementgroepen en tabel-entiteiten. Elementgroepen zijn herbruikbare gegevensgroepen en tabel-entiteiten zijn (dynamische) enumeraties.
Een goed voorbeeld van een elementgroep is `verblijfBuitenlandGrp`:

<img width="400" alt="Group verblijfBuitenlandGrp" src="/Leerlijnen-KCA/images/verblijfBuitenlandGrp.jpg" />

Een goed voorbeeld van een tabel-entiteit is `LND-tabel`:

<img width="400" alt="LND (Landen) tabel" src="/Leerlijnen-KCA/images/LND-tabel.jpg" />

*Relaties tussen complexTypes*

Hieronder hebben we nog even voor het entiteittype HHD de relaties op hoofdlijnen tussen de diverse complexTypes binnen de XML-Schema's in de 'entiteiten' folder gevisualiseerd.

<img width="300" alt="Structuur complexTypes" src="/Leerlijnen-KCA/images/Structuur-entiteiten.jpg" />

In het geval van 'Huishouden' (HHD) ziet dat er dan als volgt uit:

<img width="300" alt="Voorbeeldstructuur HHD complexTypes" src="/Leerlijnen-KCA/images/Voorbeeldstructuur-entiteiten.jpg" />

> **Let op!** Zoals al eerder gesteld hebben de complexTypes voor de gerelateerde soms wat uitgebreidere namen. In de bovenstaande illustratie hebben we voor de eenvoud de naam `NPS-basis` gebruikt. In werkelijkheid heeft deze echter de naam `NPSNINING-basis`. We verwijzen weer naar het onderdeel waarin we het verStUFfingsdocument bespreken voor meer uitleg daarover.

***bg0310_stuf_simpleTypes.xsd***

Dit schema bevat een aantal datatypes en extensions daarop in de StUF namespace maar specifiek bedoelt voor toepassing in StUF-BG 3.10.
Denk aan datatypes voor de elementen `BG:ingangsdatumObject` en `BG:einddatumObject` binnen het entiteittype 'HHD'. Dit soort datatypes komen in meerdere sectormodellen terug en om die reden zijn ze in de StUF namespace ondergebracht.

Daarnaast importeert het t.b.v. toepassing in StUF-BG 3.10 bijv. ook een aantal constructs uit de StUF namespace die worden gebruikt om een aantal standaard StUF attributen te definiëren op elke StUF-BG 3.10 basis complexType. Hieronder een voorbeeld m.b.t. het 'HHD' entiteittype:

<img width="300" alt="HHD StUF attributes" src="/Leerlijnen-KCA/images/HHD-StUF-attributes.jpg" />

Het attribuut `StUF:entiteittype` krijgt altijd een fixed waarde mee die gelijk is aan de mnemonic die voorkomt in de naam van het complexType van het objecttype of relatietype.

***bg0310_simpleTypes.xsd***

Als je kijkt naar het complexType `HHD-basis` dan zie je dat daarin enkele elementen worden gedefinieerd voor het entiteittype HHD maar die geen relaties vertegenwoordigen. Elementen die binnen de StUF-BG namespace vallen zoals `nummer`, `soort` en `grootte`. Sommige daarvan kunnen ook in andere StUF-BG entiteittypes voorkomen. De datatypes van deze elementen worden in dit XML-Schema en dus ook in de StUF-BG namespace gedefinieerd.

Die definitie gebeurd in 2 stappen. 
* Eerst wordt het simpleType voor het basis datatype gedefinieerd waarbij wordt vastgelegd of het datatype gebaseerd is op een string, integer, boolean of een nog ander basisttype. Daarnaast wordt daarin facetten als lengte, minimale waarde, regular expression, enumeratie waardes, etc... vastgelegd. Naamgeving van deze datatypes zijn over het algemeen gebaseerd op de naam van het element dat ze gebruikt;
* Dan wordt een complexType gedefinieerd dat het simpleType uitbreidt met een aantal standaard XML attributes. De naam van deze datatypes bestaat uit de naam van het basis datatype aangevuld met de extensie `-e`.

Hieronder zie je het datatype `HuishoudenSoort-e` dat wordt gebruikt in het element `soort` binnen het 'HHD' entiteittype.

<img width="600" alt="HHD e-type" src="/Leerlijnen-KCA/images/HHD-e-type.jpg" />

Zoals je rechts ziet is het gebaseerd op het basis datatype `BG:HuishoudenSoort`, betreft het een enumeration en beschikt het over een tweetal StUF attributes.

**Opdracht**

Bestudeer de XML-Schema's in de folder 'bg0310/entiteiten' en probeer de in deze paragraaf beschreven principes terug te vinden in andere entiteiten dan 'HHD'.

### _XML-Schema's in 'mutaties'_

In de bestudering van de StUF standaard heb je al gezien dat er verschillende types kennisgeving- en synchronisatieberichtsoorten bestaan en wat de onderdelen daarvan zijn. Hier proberen we je inzicht te geven in de principes die ten grondslag liggen aan de vorm van de complexTypes en elementen die we gebruiken om die kennisgevings- en synchronisatieberichten vorm te geven. We behandelen daarbij eerst de complexTypes en elementen van de kennisgevingsberichten en komen in de laatste subparagraaf van deze paragraaf terug op die van de synchronisatieberichten.

De folder 'mutaties' bevat de volgende schema's:
* bg0310_msg_mutatie.xsd
* bg0310_ent_mutatie.xsd
* bg0310_msg_stuf_mutatie.xsd

**Kennisgevingsberichten**

***bg0310_msg_mutatie.xsd***

Dit XML-Schema bevat de elementen die de StUF-BG 3.10 kennisgevingberichten vertegenwoordigen en de complexTypes met de elementen die het eerste niveau van die berichten vormen. 
Hieronder het 'hhdLk01' bericht als voorbeeld:

<img width="600" alt="hhdLk01 bericht" src="/Leerlijnen-KCA/images/hhdLk01.jpg" />

Dit betreft een asynchroon kennisgevingsbericht (soms ook mutatieberichten genoemd) voor het entiteittype HHD waarbij de body van dit bericht in een apart complexType is gedefinieerd. 

De basisstructuur van het kennisgevingsbericht, dat deels ook wordt toegepast op een groot deel van de andere berichttypen, bevat altijd een `stuurgegevens` element met een op de entiteit-bericht combinatie aangepaste complexType. Dat complexType is in de basis een restriction van het in de StUF 3.01 namespace aanwezige `Stuurgegevens` complexType. Een kennisgevingsbericht bevat ook een `parameters` element dat een specifiek op het type bericht toegesneden complexType kent dat eveneens in de basis een restriction is van een in de StUF 3.01 namespace aanwezig complexType. Tenslotte bevat een kennisgevingsbericht een `object` element dat 1 of 2 keer voor mag komen. Voor de reden daarvoor verwijs ik je naar de StUF standaard. In de volgende paragraaf gaan we dieper in op hoe de restriction tot stand komt.

***bg0310_ent_mutatie.xsd***

Dit schema bevat alle complexTypes met de specifieke kennisgevingstructuren voor de diverse entiteittypen. In deze paragraaf proberen je inzicht te geven in de principes die ten grondslag liggen aan de vorm van de complexTypes die de payload, dus de inhoud van het `object` element, van het bericht vormen.

De inhoud van het `object` (of eigenlijk specifiek het`BG:object`) element in dat bericht is gedefinieerd in het complexType `HHD-kennisgeving` op basis van het `HHD-basis` complexType. Deze kent t.o.v. dat '-basis' type een aantal verschillen. Een aantal daarvan hebben te maken met een generiek toegepast principe om het geschikt te maken voor een kennisgevingbericht:

1. Het element `BG:historieMaterieel`, dat wel in `HHD-basis` aanwezig was, komt daar niet voor. Voor een evt. in het '-basis' type aanwezige `BG:historieFormeel` element (niet aanwezig in `HHD-basis`) geldt hetzelfde. Historische waardes van gegevens worden niet met deze elementen doorgegeven maar ontstaan in de tijd. Mocht het toch nodig zijn om die historische tijdlijn te herstellen dan hebben we daar synchronisatieberichten voor. In kennisgevingberichten komen deze elementen dus niet voor;
2. De attributen `StUF:noValue` en `StUF:scope` zijn verwijderd in `HHD-kennisgeving`. Deze hebben alleen een functie in vraagAntwoord berichten;
3. De attributen `StUF:entiteittype` en `StUF:verwerkingssoort` op het `BG:object` element zijn verplicht gemaakt. Als we een kennisgevingbericht sturen moeten we de ontvangende applicatie immers wel vertellen over welk entiteitype het gaat en hoe de verstrekte gegevens moeten worden verwerkt (toevoegen, wijzigen, verwijderen, etc...);
4. In de complexTypes van de relaties is het element `BG:gerelateerde` verplicht gemaakt. Een relatie zonder een gerelateerde is immers geen relatie;
5. De attributen `StUF:entiteittype` en `StUF:verwerkingssoort` zijn op het relatie element verplicht gemaakt. Ook hier moet immers duidelijk zijn om welk entiteittype het gaat en hoe dat verwerkt moet worden;
6. De complexTypes voor de 'gerelateerde' elementen zijn gebaseerd op de '-kerngegevens' complexTypes van de bijbehorende entiteittypes. De achtergrond voor de keuze voor kerngegevens in een gerelateerde in een mutatie is dat het voor een mutatie voldoende is om de gerelateerde te kunnen identificeren c.q. te kunnen vastleggen met gegevens die de gerelateerde uniek identificeren. In een latere mutatie kunnen de gegevens van een gerelateerde altijd nog aangevuld worden;
7. De attributen `StUF:noValue` en `StUF:scope` zijn op de `gerelateerde` elementen verwijderd om dezelfde reden als genoemd bij 2. Op de `BG:gerelateerde` binnen `BG:heeftAlsLeden` is dat echter, waarschijnlijk abusievelijk, achterwege gebleven;
8. De attributen `StUF:entiteittype` en `StUF:verwerkingssoort` zijn op de `gerelateerde` elementen verplicht gemaakt om dezelfde reden als genoemd bij 3 en 5.

*Relaties tussen complexTypes*

Hieronder hebben we nog even de relaties op hoofdlijnen en in generieke zin tussen de diverse complexTypes binnen de XML-Schema's in de 'mutatie' folder gevisualiseerd. Daarin zijn ook de afleidingen van de basis entiteiten meegenomen.

<img width="600" alt="Structuur mutaties complexTypes" src="/Leerlijnen-KCA/images/Structuur-mutaties.jpg" />

*In enkele gevallen kan de naamgeving afwijken.

In het geval van 'Huishouden' (HHD) ziet dat er dan als volgt uit:

<img width="750" alt="Voorbeeldstructuur HHD mutaties complexTypes" src="/Leerlijnen-KCA/images/Voorbeeldstructuur-mutaties.jpg" />

***bg0310_msg_stuf_mutatie.xsd***

In dit schema worden specifieke per berichttype gedefinieerde complexTypes uit het 'stuf0301.xsd' XML-Schema verder aangescherpt voor gebruik in de kennisgevingberichten. Dat aanscherpen betreft eigenlijk niet meer dan het beperken van de waarde van het XML attribute `StUF:entiteittype` binnen de `Stuurgegevens` complexTypes zodat die niet conflicteert met het entiteittype waar het bericht betrekking op heeft.

**Samengestelde kennisgevingberichten**

We kennen een tweetal verschillende samengestelde kennisgevingen, Lk03 en Lk04 berichten. Hier als voorbeeld het 'bagIN_Lk03' bericht:

<img width="750" alt="Voorbeeld: bagIN_Lk03 bericht" src="/Leerlijnen-KCA/images/bagIN_Lk03-Samengestelde-kennisgeving.jpg" />

Samengestelde kennisgevingen bevatten:
* Een op het betreffende Lk03 of Lk04 bericht toegesneden specifieke stuurgegevens element. In het voorbeeld het 'stuurgegevens' element direct in 'bagIN_Lk03'. Daarin krijgt het element `functie` een specifieke op het bericht van toepassing zijnde waarde zoals in het voorbeeld de waarde 'bagIN'.
* Gevolgd door twee of meer op het betreffende Lk03 of Lk04 bericht toegesneden specifieke enkelvoudige kennisgevingen (Lk03 berichten bevatten Lk01 berichten en Lk04 berichten bevatten Lk02 berichten). In het voorbeeld vertegenwoordigd door de elementen 'aoaLk01', 'ligStaLk01' en 'vboLk01'. Dit zijn complete enkelvoudige kennisgevingberichten dus zoals je in het voorbeeld ziet incl. stuurgegevens en parameters. De complexTypes die in samengestelde berichten gebruikt worden voor de specifieke enkelvoudige berichten zijn gebaseerd op complexTypes die gebruikt worden in de generieke enkelvoudige kennisgevingen.

<img width="730" alt="Structuur samengestelde mutaties complexTypes" src="/Leerlijnen-KCA/images/Structuur-samengestelde-mutaties.jpg" />

*In enkele gevallen kan de naamgeving afwijken.

Hieronder een voorbeeld uit het 'ligStaLk01' bericht binnen het samengestelde kennisgevingbericht 'bagIN_Lk03':

<img width="750" alt="Voorbeeldstructuur samengestelde mutaties complexTypes" src="/Leerlijnen-KCA/images/Voorbeeldstructuur-samengestelde-mutaties.jpg" />

**Synchronisatieberichten**

Synchronisatieberichten maken deels gebruik van de complexTypes voor kennisgevingsberichten. We verkennen in de onderstaande paragrafen de schemastructuren voor dat soort berichten waarbij we eerst kijken naar de berichtstructuur van de verschillende synchronisatieberichten en daarna zullen inzoomen op de payload van die berichten.

***Berichtstructuur***

We beperken ons in deze paragraaf tot het schetsen van de top-level structuur van de syndchronisatieberichten. 

We onderkennen een achttal verschillende soorten synchronisatie berichten:
* Sa01 en Sa02;
* Sa03 en Sa04;
* Sh01 en Sh02;
* Sh03 en Sh04.

De eerste twee zijn synchronisatieberichten voor actuele gegevens. Een verzoek voor het verzenden daarvan kan verstuurd worden met een Sa03 of een Sa04.
Het Sh01 en Sh02 bericht zijn synchronisatieberichten voor actuele en historische gegevens. Met een Sh03 en een Sh04 kan een verzoek voor het verzenden daarvan worden gedaan.

Hieronder zie van alle paren één variant m.b.t. het HHD objecttype en daarbij in het kort een omschrijving van de opbouw:

<img width="550" alt="Voorbeeld: bagIN_Lk03 bericht" src="/Leerlijnen-KCA/images/hhdSa01.jpg" />

Een Sa01/Sa02 bericht bevat een toevoegkennisgeving en het complexType van het 'object' element is over het algemeen een 'XXX-kennisgeving' maar kan ook een een restriction daarop zijn.

<img width="550" alt="Voorbeeld: bagIN_Lk03 bericht" src="/Leerlijnen-KCA/images/hhdSa03.jpg" />

Het 'object' element in een Sa03/Sa04 bericht heeft een 'XXX-kerngegevens' complexType tenzij er geen kerngegevens zijn. In dat geval is het een 'XXX-kennisgeving' complexType.

<img width="550" alt="Voorbeeld: bagIN_Lk03 bericht" src="/Leerlijnen-KCA/images/hhdSh01.jpg" />

Het Sh01/Sh02 bericht is wat complexer. 
* Deze bevat direct na het stuurgegevens element eerst een 'actueel' element met daarin een toevoegkennisgevingbericht voor het synchroniseren van actuele gegevens. Dit 'actueel' element is gelijk aan de inhoud van het gerelateerde Sa01 of Sa02 bericht;
* Daarna een 'historie' element met daarin een toevoegkennisgevingbericht voor het synchroniseren van de oudste historische bekende gegevens gevolgd door een wijzigkennisgevingbericht voor het synchroniseren van de wijzigingen daarop. Het element 'historie' is daarin optioneel omdat niet alle objecten al historie opgebouwd hoeven te hebben;
* De 'object' elementen in dit bericht gebruiken allemaal dezelfde complexType;
* Het 'actueel' en 'ouste' element in dit bericht gebruiken eveneens beiden dezelfde complexType.

<img width="550" alt="Voorbeeld: bagIN_Lk03 bericht" src="/Leerlijnen-KCA/images/hhdSh03.jpg" />

Ook in een Sa03/Sa04 bericht heeft het 'object' element een 'XXX-kerngegevens' complexType tenzij er geen kerngegevens zijn. In dat geval is het een 'XXX-kennisgeving' complexType.

**Opdracht**

Bestudeer de XML-Schema's in de folder 'bg0310/mutaties' en probeer de in deze paragraaf beschreven principes terug te vinden in andere entiteiten dan 'HHD'.

### _XML-Schema's in 'vraagAntwoord'_

Tenslotte gaan we nog in de 'vraagAntwoord' berichten. Ook hier liggen een aantal principes ten grondslag aan de vorm van de complexTypes en elementen die we gebruiken om die vraagAntwoordberichten vorm te geven.

Deze folder bevat de volgende schema's:
* bg0310_msg_vraagAntwoord.xsd
* bg0310_ent_vraagAntwoord.xsd
* bg0310_msg_stuf_vraagAntwoord.xsd

**VraagAntwoordberichten**

***bg0310_msg_vraagAntwoord.xsd***

Dit XML-Schema bevat de elementen die de StUF-BG 3.10 vraag- en antwoordberichten vertegenwoordigen en de group definities met de vraagbodies.

Hieronder het 'hhdLv01' bericht:

<img width="600" alt="hhdLv01 bericht" src="/Leerlijnen-KCA/images/hhdLv01.jpg" />

en het 'hhdLa01' bericht als voorbeeld:

<img width="600" alt="hhdLa01 bericht" src="/Leerlijnen-KCA/images/hhdLa01.jpg" />

Het betreft bij elkaar horende synchrone berichten voor wederom het entiteittype HHD.

Ook hier zie je `stuurgegevens` element terugkomen met een op de entiteit-bericht combinatie aangepaste complexType dat ook hier in de basis een restriction van het in de StUF 3.01 namespace aanwezige `Stuurgegevens` complexType is. Ook het `parameters` element heeft een specifiek op het type bericht toegesneden complexType dat eveneens in de basis een restriction is van een in de StUF 3.01 namespace aanwezig complexType.
Een antwoordbericht kent daarnaast nog een optioneel `melding` element waarmee de supplier meldingen m.b.t. de verwerking van de vraag kan versturen naar de consumer.

Voor de payload van het bericht wordt hier gebruik gemaakt van een elders in dit schema gedefinieerde group. Elk group component kent dezelfde vorm die je in de illustratie van het 'hhdLv01' bericht terugziet (`gelijk`, `vanaf`, `totEnMet`, `scope` en `start`).

***bg0310_ent_vraagAntwoord.xsd***

Dit XML-Schema kan de volgende complexTypes per entiteittype bevatten:

1. de body voor het antwoordbericht (`XXX-antwoord`);
2. de body voor de selectie EN de scope elementen van het vraagbericht (`XXX-vraag`);
6. de body voor alleen de selectie elementen van het vraagbericht (`XXX-vraagSelectie`);
7. de body voor alleen het scope element van het vraagbericht (`XXX-vraagScope`).
3. de body van `gerelateerde` elementen in vraag- en antwoordberichten (`XXX-gerelateerdeVraag`, `XXX-gerelateerdeVraagSelectie`, `XXX-gerelateerdeVraagScope` en `XXX-gerelateerdeAntwoord`);
4. de body van een evt. `historieMaterieel` element (`XXX-historieMaterieel`);
5. de body van een evt. `historieFormeel` en `historieFormeelRelatie` element (resp. `XXX-historieFormeel`, `XXX-historieFormeelRelatie`);

Het aantal verschillende complexTypes geeft al aan dat de structuur van de vraag- en antwoordberichten wat uitgebreider zijn, m.n. de vraagberichten.

*vraagberichten*

Zoals we al eerder aangaven bestaat de payload van een vraagbericht uit een vijftal elementen:

<img width="300" alt="HHD-VraagBody complexType" src="/Leerlijnen-KCA/images/HHD-VraagBody.jpg" />

* Met de `BG:gelijk`, `BG:vanaf` of `BG:totEnMet` kunnen de selectiecriteria in een vraagbericht worden gedefinieerd. Deze elementen hebben altijd een `XXX-vraag` of `XXX-vraagSelectie` complexType als body. Een `XXX:vraagSelectie` complexType is alleen van toepassing als de inhoud van het `BG:scope` element afwijkt van de inhoud van de selectie elementen.
* Met het `G:scope` element kunnen de terug te retourneren elementen worden gedefinieerd. Dit element heeft altijd een `XXX-vraag` of `XXX:vraagScope` complexType als body waarbij de laatste natuurlijk alleen van toepassing is op `BG:scope` elementen die een andere inhoud hebben dan de selectie elementen.<br/><br/>De belangrijkste regels voor de vraag complexTypes zijn:
  - ze bevatten geen van allen historie-elementen (`historieMaterieel` en`historieFormeel`);
  - het attribute `StUF:verwerkingssoort` mag er niet in voorkomen;
  - de elementen voor attributen en relaties mogen maximaal één keer voorkomen. 
  - alleen in de topfundamenteel van een vraag complexType mag het attribute `StUF:scope` voorkomen. Dit is een alternatief voor het gebruik van het `BG:scope` element.

  Welke gerelateerde complexTypes gebruikt moeten worden (`XXX-gerelateerdeVraag`, `XXX-gerelateerdeVraagSelectie` of `XXX-gerelateerdeVraagScope`) is afhankelijk van hetzelfde criterium als hierboven bij de `XXX-vraag`, `XXX-vraagSelectie` of `XXX-vraagScope` is beschreven. Soms kan in deze situaties echter een `XXX-gerelateerde` worden gebruikt.
* Met het `BG:start` element kan het object waarmee moet worden gestart worden gedefinieerd. Dit element heeft altijd een `XXX-antwoord` complexType als body.

*antwoordberichten*

In tegenstelling tot vraagberichten kunnen in antwoordberichten wel historie-elementen voorkomen. We willen met de vraagberichten Lv07 t/m Lv14 immers juist de materiële danwel formele historie opvragen. Over het algemeen hebben 'gerelateerde' elementen in antwoordberichten  het complexType `XXX-gerelateerdeAntwoord` maar ook hier kan soms een `XXX-gerelateerde` worden gebruikt.

In de topfundamenteel, een relatie element of een `gerelateerde` element binnen een antwoordbericht mogen de attributes `StUF:verwerkingssoort` en `StUF:scope` niet voorkomen. De gegevens in een antwoordbericht hoeven immers niet verwerkt te worden en dienen ook niet voor het bepalen van een scope. Ook het attribute `StUF:noValue` mag  niet voorkomen in het element voor de topfundamenteel en het `gerelateerde` element.

*Relaties tussen complexTypes*

Hieronder hebben we nog even de relaties op hoofdlijnen en in generieke zin tussen de diverse complexTypes binnen de XML-Schema's in de 'vraagAntwoord' folder gevisualiseerd. Daarin zijn ook de afleidingen van de basis entiteiten meegenomen.

<img width="750" alt="Structuur vraagAntwoord complexTypes" src="/Leerlijnen-KCA/images/Structuur-vraagAntwoord.jpg" />

*In enkele gevallen kan de naamgeving afwijken.

In het geval van 'Huishouden' (HHD) ziet dat er dan als volgt uit:

<img width="850" alt="Voorbeeldstructuur HHD vraagAntwoord complexTypes" src="/Leerlijnen-KCA/images/Voorbeeldstructuur-vraagAntwoord.jpg" />

***bg0310_msg_stuf_vraagAntwoord.xsd***

In dit schema worden specifieke per berichttype gedefinieerde complexTypes uit het 'stuf0301.xsd' XML-Schema verder aangescherpt voor gebruik in de vraag- en antwoordberichten. Dat aanscherpen betreft:
* de parameters voor synchrone danwel asynchrone berichttypes (met of zonder voorzieningen voor formele- en materiële historie). 
* de stuurgegevens per berichttype/entiteittype combinatie waarin o.a. weer de waarde van het XML attribute `StUF:entiteittype` wordt beperkt zodat die niet conflicteert met het entiteittype waar het bericht betrekking op heeft.

Daarnaast voorziet dit XML-Schema nog in simpleTypes t.b.v. sortering.

**Opdracht**

Bestudeer de XML-Schema's in de folder 'bg0310/vraagAntwoord' en probeer de in deze paragraaf beschreven principes terug te vinden in andere entiteiten dan 'HHD'.

### Verdere verdieping

Lees voor een verdere verdieping in de folderstructuren, XML-Schema structuren en naamgevingsconventies de <a href="https://vng-realisatie.github.io/StUF-onderlaag/documenten/Diversen/Stuf_best_practices.pdf" target="_blank">Ontwerpregels en best practices voor op StUF 3.01 gebaseerde berichten</a>.

