---
title: "6.1 Opbouw en werking van StUF"
date: 2026-08-06
weight: 601
leerlijn: 6
paragraaf: "6.1"
parent: "StUF-standaard"
leerdoel: "Begrip opbouwen van de generieke onderlaag (StUF 03.01), horizontale sectormodellen (bv. StUF-BG), verticale sectormodellen (bv. StUF-WOZ) en specifieke koppelvlakken."
---

## 6.1 Opbouw en werking van StUF

### Wat is StUF?

**StUF** (Standaard Uitwisseling Formaat) is een Nederlandse standaard voor berichtgebaseerde gegevensuitwisseling tussen informatiesystemen in de overheid. StUF biedt een gestandaardiseerde manier voor systemen om gestructureerd informatie uit te wisselen, onafhankelijk van de onderliggende technologie.

**Kernprincipes van StUF:**

***1. Standaardisatie***
* **Gestandaardiseerde berichten**: Voorgedefinieerde bericht-types met generieke functionaliteit;
* **Uniforme structuur**: Consistente opbouw over sectoren;
* **Gedeelde vocabulaire**: Eenduidige semantiek.

***2. Ontkoppeling***
* **Technisch**: Verschillende systemen/technologieën kunnen StUF-berichten uitwisselen;
* **Semantisch**: Business-logica gescheiden van transport;
* **Temporeel**: Zowel synchroon als asynchroon berichtverkeer mogelijk.

***3. Herbruikbaarheid***
* **Onderlaag**: Herbruikbare generieke basis-componenten;
* **Sectormodellen**: Herbruikbare domein-specifieke uitbreidingen;
* **Koppelvlakken**: Concrete implementatie-specificaties.

### Historie

De eerste versie van StUF (1.05) is in 1998 gepubliceerd. Inmiddels worden alleen de versies 2.04 en 3.01 ondersteund door VNG. In deze module focussen we echter op versie 3.01 aangezien dat de meest gebruikte versie is. In een volgende onderdeel van deze module ([6.3 Versies van de StUF-standaard](../6.3-versies-van-de-stuf-standaard)) zullen we iets dieper ingaan op beide versies en de verschillen daartussen.

In 2024 is overigens een traject gestart voor de transitie van StUF naar REST-JSON API's. De verwachting is dat de transitie een doorlooptijd van 10 jaar zal beslaan maar ook dat de StUF standaard daardoor op termijn zal komen te vervallen. Tijdens die transitie moet StUF als werkbare standaard beheerd en onderhouden worden.

### StUF-architectuur

StUF kent een gelaagde opbouw waarbij de lagen steeds specifieker worden in hun toepassing. Iedere laag bouwt voort op wat de onderliggende laag biedt. In de onderstaande diagram hebben we dit gevisualiseerd.

<img width="400" alt="StUF lagenmodel" src="/Leerlijnen-KCA/images/StUF-lagenmodel.png" />

<!-- Hieronder de mermaid code waarmee bovenstaande image is gegenereerd.
     Vervang '&hyphen;' hierin voor '-'.
	 
flowchart BT
    A["Protocolbindingen"] &hyphen;-> B["StUF onderlaag"]
    B &hyphen;-> C["StUF horizontale sectormodellen"]
    C &hyphen;-> D["StUF verticale sectormodellen"]
    B &hyphen;-> E["StUF-koppelvlakken"]
    C &hyphen;-> E
    D &hyphen;-> E
-->
**Protocolbindingen**

De ‘StUF protocolbindingen’ beschrijven de afspraken voor het gebruik van StUF in combinatie met drie manieren van transport:

* via bestandsoverdracht;
* via transport op basis van WSDL 1.1 met SOAP 1.1 en http(s) als onderliggend transportmechanisme;
* of via transport op basis van Digikoppeling.

**De StUF onderlaag**

De StUF onderlaag is een XML Schema-gebaseerd framework met:

* basisstructuren voor diverse berichttypen;
* gemeenschappelijke datatypen;
* voorzieningen voor identificatie en tijdsstempeling.

aangevuld met documentatie over generieke proceslogica. De StUF onderlaag definieert dus geen domeininhoud.

**StUF horizontale sectormodellen**

StUF sectormodellen StUF-BG, StUF-ZKN. Basis- en zaakgegevens spelen in vrijwel alle domeinen een rol. Deze:

* zijn gebaseerd op domein-specifieke informatiemodellen (RSGB, RGBZ);
* bevatten in XML Schema uitgedrukte business-objecten, relaties en generieke maar op een domein van toepassing zijnde berichten;
* worden aangevuld met sectorspecifieke regels/documentatie.

**StUF verticale sectormodellen**

Maken waar mogelijk gebruik maken van de definities in de horizontale sectormodellen StUF-BG, StUF-ZKN.  Een voorbeeld is StUF-WOZ (gebaseerd op het IMWOZ) dat overigens niet in beheer is bij VNG Realisatie.

**StUF Koppelvlakken**

Deze kunnen gebaseerd zijn op StUF sectormodellen of op de voor de toepassing specifiek opgestelde informatiemodellen.
Deze bevatten:

* specifieke voor de toepassing in XML Schema uitgedrukte berichten, business-objecten en datatypes;
* een strakke beschrijving van het gewenste gedrag zo mogelijk als een verdere aanscherping van de sectorspecifieke regels/documentatie.

<br/>In de volgende paragraaf gaan we iets dieper in op deze lagen.

### StUF onderlaag

In deze en volgende paragrafen benoemen we een aantal belangrijke aspecten van StUF. We proberen hier geenszins volledig te zijn. De getoonde berichten of fragmenten daarvan zijn slechts ter illustratie en mogelijk ook niet volledig of zelfs valide.

**Basisstructuren**

Een StUF bericht kent grofweg de volgende opbouw:

```XML
<berichtnaam>
	<stuurgegevens>
		...
	</stuurgegevens>
	<parameters>
		...
	</parameters>
	berichttype specifieke structuren t.b.v. berichtinhoud.
</berichtnaam>

```

De `parameters` zijn optioneel en komen dus niet in alle berichten voor.

Afhankelijk van het type bericht worden de berichttype specifieke structuren ingericht. Een kennisgevingsbericht voorziet bijv. in voorzieningen waarmee de situatie van voor de wijziging en de situatie van na de wijziging kan worden gecommuniceerd. Een vraagbericht bevat echter weer structuren waarmee o.a. selecties kunnen worden gespecificeerd.

***Stuurgegevens-componenten***

T.b.v. het element `stuurgegevens` is in de StUF namespace is het complexType `Stuurgegevens` gedefinieerd. Om te kunnen voorzien in specifieke behoeftes per type bericht is dat aangescherpt naar bijv. de volgende complextypes:

* `stuurgegevensLa01`
* `stuurgegevensLv07`
* `stuurgegevensLk03`
* `stuurgegevensDi01`
* `stuurgegevensDu02`
* etc...

Waar ik het hier over het element `stuurgegevens` heb mag je ook deze specifieke versies daarvan lezen. De `stuurgegevens`bevatten voorzieningen t.b.v.:

* berichttype identificatie;
* bericht routering;
* bericht tracking;
* bericht correlatie.

**Berichttype identificatie**

Voor de verwerking van een bericht is het van belang dat de ontvanger kan bepalen wat voor type bericht het betreft en op welk entiteittype het betrekking heeft.

```xml
<XXX:stuurgegevens>
    <!-- Berichttype identificatie -->
    <StUF:berichtcode>Lv01</StUF:berichtcode>
	
	...

    <!-- Entiteit waar het bericht betrekking op heeft -->
    <StUF:entiteittype>NPS</StUF:entiteittype>    
</XXX:stuurgegevens>
```

**Bericht routering**

De StUF onderlaag biedt mechanismen voor het kunnen routeren van berichten. Binnnen het element 'StUF:stuurgegevens' kun je daarvoor de elementen 'StUF:zender' en 'StUF:ontvanger' gebruiken.

```xml
<XXX:stuurgegevens>
    ...
    
    <!-- Afzender identificatie -->
    <StUF:zender>
        <StUF:organisatie>0363</StUF:organisatie>      <!-- CBS-code gemeente -->
        <StUF:applicatie>BRP-Service</StUF:applicatie>  <!-- Systeem naam -->
        <StUF:administratie>01</StUF:administratie>      <!-- Optional: administratie -->
        <StUF:gebruiker>api_user_001</StUF:gebruiker>   <!-- Technical user -->
    </StUF:zender>
    
    <!-- Ontvanger identificatie -->
    <StUF:ontvanger>
        <StUF:organisatie>0518</StUF:organisatie>
        <StUF:applicatie>Zaak-DMS-v3</StUF:applicatie>
    </StUF:ontvanger>
    
    ...
</XXX:stuurgegevens>
```

**Bericht tracking**

StUF voorziet in voorzieningen om een bericht uniek te kunnen identificeren. Berichten die onafhankelijk van elkaar zijn aangemaakt door verschillende systemen kunnen toevallig hetzelfde referentienummer hebben, omdat StUF geen voorschriften geeft voor de opbouw van het referentienummer. StUF eist wel dat de combinatie van referentienummer en zender (verzendende organisatie, applicatie en administratie) uniek is. De door een verzendend systeem toegekende referentienummers moeten dus allemaal verschillend zijn.

```xml
<XXX:stuurgegevens>
    ...
    
    <!-- Bericht tracking -->
    <StUF:referentienummer>REF-ZAAK-20240305-045</StUF:referentienummer>
    <StUF:tijdstipbericht>20240305143015</StUF:tijdstipbericht>
	
	...
</XXX:stuurgegevens>
```

**Bericht correlatie**

Voor berichten die een reactie zijn op een ander bericht, is het wenselijk te weten op welk bericht wordt gereageerd. Hiervoor kan in deze berichten het stuurgegeven `<crossRefnummer>` worden opgenomen.

```xml
<XXX:stuurgegevens>
    ...
	
    <!-- Cross-reference naar eerder bericht -->
    <StUF:crossRefnummer>REF-ZAAK-20240305-045</StUF:crossRefnummer>    
    
	...
</XXX:stuurgegevens>
```

### StUF-berichttypen

**Vraag-/Antwoord-berichten (Request/Response)**

Met een vraagbericht worden gegevens opgevraagd die vervolgens met een antwoordbericht worden verstrekt. Van beide bestaan diverse varianten (Lv01 t/m Lv14 en La01 t/m La14) waar overigens niet elk sectormodel/koppelvlak altijd allemaal in voorziet. De variaties hebben betrekking op synchroon/asynchroon, met of zonder materiële/formele historie en het peiltijdstip waarvoor de dan geldende gegevens moeten worden teruggegeven.

Hieronder van een voorbeeld van een setje bij elkaar horende vraag- en antwoordberichten uit de StUF-BG 3.10 standaard.
Zoals je ziet heeft het onderstaande bericht de naam `npsLv01'. Lv01 heeft aan dat het om een vraagbericht gaat, `nps` betekent dat het bericht betrekking heeft op het entiteittype met de mnemonic 'NPS' wat staat voor Natuurlijk Persoon. Zo heeft elke entiteittype dat geïmplementeerd wordt in een StUF sectormodel zijn eigen mnemonic. 

***Vraagbericht***

```xml
<BG:npsLv01>
    <BG:stuurgegevens>
        <StUF:berichtcode>Lv01</StUF:berichtcode>
		...
        <StUF:referentienummer>BRP-20260523-109</StUF:referentienummer>
		...
		<StUF:entiteittype>NPS</StUF:entiteittype>
    </BG:stuurgegevens>
	...
    <BG:gelijk>
        <BG:inp.bsn>123456789</BG:inp.bsn>
    </BG:gelijk>
	<BG:scope>
        <BG:object StUF:entiteittype="NPS">
            <BG:burgerservicenummer xsi:nil=”true”/>
            <BG:geslachtsnaam>
                <BG:voorvoegselGeslachtsnaam xsi:nil=”true”/>
                <BG:geslachtsnaam xsi:nil=”true”/>
            </BG:geslachtsnaam>
            <BG:voornamen xsi:nil=”true”/>
            <BG:geboortedatum xsi:nil=”true”/>
			...
        </BG:object>
	</BG:scope>
</BG:npsLv01>
```
M.b.v. het `<gelijk>` element wordt aangegeven aan welk selectie criterium de terug te geven gegevens moeten voldoen. In dit geval moet het om de gegevens gaan van de persoon met het burgerservicenummer '123456789'. Naast `<gelijk>` zijn er nog de selectie mogelijkheden `<vanaf>` en `<totEnMet>`.
Met het `<scope>` element wordt aangegeven welke gegevens gewenst zijn. Naast dit element zijn er nog andere mogelijkheden om de scope te definiëren. In feite betreft het hier de bevragingsparameters.

***Antwoordbericht***

Hieronder het bij het hierboven geschetse voorbeeld vraagbericht horende antwoordbericht.

```xml
<BG:npsLa01>
    <BG:stuurgegevens>
        <StUF:berichtcode>La01</StUF:berichtcode>
        ...
        <StUF:crossRefnummer>BRP-20260523-109</StUF:crossRefnummer>
		...
		<StUF:entiteittype>NPS</StUF:entiteittype>
    </BG:stuurgegevens>
    <BG:antwoord>
        <BG:object StUF:entiteittype="NPS" StUF:verwerkingssoort="I">
            <BG:burgerservicenummer>123456789</BG:burgerservicenummer>
            <BG:geslachtsnaam>
                <BG:voorvoegselGeslachtsnaam>van der</BG:voorvoegselGeslachtsnaam>
                <BG:geslachtsnaam>Berg</BG:geslachtsnaam>
            </BG:geslachtsnaam>
            <BG:voornamen>Jan Peter</BG:voornamen>
            <BG:geboortedatum>19850315</BG:geboortedatum>
			...
        </BG:object>
    </BG:antwoord>
</BG:npsLa01>
```

Vraag- en antwoorberichten vormen vaste sets. Een Lv01 wordt altijd beantwoord met een La01, een Lv02 altijd met een La02, etc...

**Kennisgeving- en synchronisatieberichten**

Met kennisgevingsberichten, ook wel mutatieberichten genoemd, kunnen mutaties op een registratie worden doorgegeven. Net als bij vraag- en antwoordberichten bestaan er hiervan meerdere varianten (Lk01 t/m Lk06). De variaties hebben betrekking op enkelvoudig/samengesteld, synchroon/asynchroon, met of zonder toekomstmutaties.

StUF synchronisatie(verzoek)berichten worden gebruikt om de gegevens tussen verschillende applicaties eenduidig en consistent te houden.

De belangrijkste redenen om een StUF-synchronisatie(verzoek)bericht in te zetten zijn:

* **Historie corrigeren en opbouwen:**<br/>Het verwerken van correcties in historische gegevens is complex. Het synchronisatiebericht wordt gebruikt om een object, inclusief de volledige historie, in één keer opnieuw op te bouwen en zo fouten te herstellen;
* **Realisatie van integratie en consistentie:**<br/>Het synchroon houden van persoons- of zaakgegevens in de afnemende applicaties, zodat alle systemen (bijvoorbeeld van een gemeente) exact dezelfde actuele gegevens tonen;
* **Initiële vulling van systemen:**<br/>Bij de ingebruikname van een nieuwe applicatie of na een systeemmigratie kunnen alle actuele basisgegevens in één keer overgedragen en geladen worden;
* **Foutafhandeling en synchronisatieverzoeken:**<br/>Wanneer een applicatie data mist of uit de pas loopt, kan deze met een specifiek synchronisatieverzoekbericht (bijv. een Sa03 of Sa04) een actueel synchronisatiebericht opvragen bij de bron.

Ook hier kennen we weer diverse varianten synchronisatieberichten (Sa01, Sa02, Sh01, Sh02)en synchronisatieverzoekberichten (Sa03, Sa04, Sh03, Sh04). De variaties hebben hier betrekking op synchroon/asynchroon en alleen actueel of ook historische en toekomstige mutaties.

Hieronder een voorbeeld van een kennisgevingbericht uit de StUF-BG 3.10 standaard.

***Kennisgevingbericht**

```xml
<BG:npsLk01>
	<BG:stuurgegevens>
		<StUF:berichtcode>Lk01</StUF:berichtcode>
		...
		<StUF:entiteittype>NPS</StUF:entiteittype>
	</BG:stuurgegevens>
	<BG:parameters>
		<StUF:mutatiesoort>W</StUF:mutatiesoort>
		<StUF:indicatorOvername>V</StUF:indicatorOvername>
	</BG:parameters>
	<BG:object StUF:entiteittype="NPS" StUF:verwerkingssoort="W">
		<BG:inp.bsn StUF:exact="true">123456789</BG:inp.bsn>
		<BG:sub.telefoonnummer StUF:exact="true">+31612345678</BG:sub.telefoonnummer>
		<BG:sub.emailadres StUF:exact="true">ppeters14@hotmail.com</BG:sub.emailadres>
		...
	</BG:object>
	<BG:object StUF:entiteittype="NPS" StUF:verwerkingssoort="W">
		<BG:inp.bsn StUF:exact="true">123456789</BG:inp.bsn>
		<BG:sub.telefoonnummer StUF:exact="true">+31623456789</BG:sub.telefoonnummer>
		<BG:sub.emailadres StUF:exact="true">peter.peters@gmail.com</BG:sub.emailadres>
		...
	</BG:object>
</BG:npsLk01>
```
Met het `<BG:parameter>` element `<StUF:mutatiesoort>` wordt aangegeven dat het om een wijziging gaat.

StUF onderkent o.a. de volgende verwerkingssoorten:

| Code | Betekenis | Gebruik |
|------|-----------|---------|
| **T** | Toevoeging | Nieuw object wordt aangemaakt |
| **W** | Wijziging | Bestaand object wordt gewijzigd |
| **V** | Verwijdering | Object wordt verwijderd |

Het element `<StUF:indicatorOvername>` geeft aan dat de gegevens verplicht moeten worden overgenomen. 

Omdat het om een wijziging gaat moeten er twee `<BG:object>` elementen aanwezig zijn. Één om de huidige situatie weer te geven en één om de nieuwe situatie weer te geven. Beide `<BG:object>` elementen bevatten alleen de verplichte kerngegevens (voor de entiteit 'NPS' alleen het element `<BG:inp.bsn>` en de gegevens die wijzigen. Indien het element `<StUF:mutatiesoort>` de waarde 'T' had gehad dan had één `<BG:object>` element volstaan. 

**Vrije berichten**

In een service georiënteerde architectuur is soms meer of andere functionaliteit nodig. Bij het doorgeven van een gebeurtenis als een verhuizing is de beperking tot één type object bijvoorbeeld onwenselijk. In één bericht wil je de verhuizing van een heel gezin kunnen doorgeven. Hierbij wil je ook de mogelijkheid hebben om de aangever en de verhuizende personen en het oude en nieuwe adres als afzonderlijke objecten in het bericht op te nemen. Een kennisgeving of een samengestelde kennisgeving voldoet in deze situatie niet. StUF biedt in dan met vrije berichten een oplossing.

Ook van vrije berichten bestaan meerdere varianten (Di01, Du01, Di02 en Du02). De variaties hebben in dit geval betrekking op synchroon/asynchroon en verzoek/response.

Hieronder een voorbeeld vrij bericht uit het StUF-Jeugdzorg koppelvlak.

***Notificatiebericht***

```xml
<JZ:notificatiejrDi01 >
  <JZ:stuurgegevens>
    <StUF:berichtcode>Di01</StUF:berichtcode>
    ...
    <StUF:referentienummer>1-13579246801@rvdk.minvenj.nl</StUF:referentienummer>
    ...
    <StUF:functie>notificatiejrDi01</StUF:functie>
  </JZ:stuurgegevens>
  <JZ:object StUF:entiteittype="ZAK">
    <StUF:extraElementen>
      <StUF:extraElement naam="signaaltype">Notificatie vrijwillige
        jeugdreclassering</StUF:extraElement>
    </StUF:extraElementen>
    <ZKN:isVan StUF:entiteittype="ZAKZKT">
      <ZKN:gerelateerde StUF:entiteittype="ZKT">
        <ZKN:omschrijvingGeneriek>Signaal behandelen</ZKN:omschrijvingGeneriek>
      </ZKN:gerelateerde>
    </ZKN:isVan>
    <ZKN:heeftBetrekkingOp StUF:entiteittype="ZAKOBJ">
      <ZKN:gerelateerde StUF:entiteittype="OBJ">
        <ZKN:natuurlijkPersoon StUF:entiteittype="NPS">
          <StUF:extraElementen>
            <StUF:extraElement naam="vreemdelingennummer">1122334455</StUF:extraElement>
          </StUF:extraElementen>
        </ZKN:natuurlijkPersoon>
      </ZKN:gerelateerde>
      <ZKN:omschrijving>jeugdige</ZKN:omschrijving>
      <StUF:extraElementen>
        <StUF:extraElement naam="toezichtSoort">Tijdens of na kortdurende detentie</StUF:extraElement>
        <StUF:extraElement naam="toezichtBegindatum">20250602</StUF:extraElement>
      </StUF:extraElementen>
    </ZKN:heeftBetrekkingOp>
  </JZ:object>
</JZ:notificatiejrDi01>
```
Je ziet hier meteen een voorbeeld van een bericht uit een koppelvlak dat gebaseerd is op StUF-ZKN 3.10.

### StUF-tijdlijnen en geldigheid

De eigenschappen van een object kunnen in de tijd wijzigen, bijvoorbeeld doordat een persoon een aantal keren ver­huist. Een object kan ook niet langer bestaan, bijvoorbeeld een overleden persoon. In het eerste geval spreken we over een historisch gegeven en in het tweede geval over een historisch object. 

* Een historisch object is een object dat in de werkelijkheid niet meer bestaat maar nog wel van belang is. Een historisch object heeft als actuele gegevens de laatste (actuele) waarden, voordat het object ophield te bestaan. Deze waarden zijn nog steeds geldig.
* Onder een historisch gegeven wordt verstaan een gegeven met een waarde die vroeger geldig was, dat wil zeggen met een eind geldigheid in het verleden.
* Onder een actueel gegeven wordt verstaan een gegeven dat nu geldig is, dat wil zeggen met een begin geldigheid in het verleden of heden en een eind geldigheid die geen waarde heeft of in de toekomst ligt.
* Onder een toekomstig gegeven wordt een gegeven verstaan met een begin geldigheid in de toekomst. In de praktijk komt dit niet voor en naar de huidige inzichten wordt het ook niet toegepast.

Gegevens kunnen om twee redenen wijzigen:
1. In de werkelijkheid verandert de waarde van het gegeven. Ten gevolge daarvan wordt de waarde en het moment waarop de wijziging in de werkelijkheid ingaat in de registratie vastgelegd. Dit wordt in het stelsel van basisregistraties gedefinieerd als materiële historie.
2. In de werkelijkheid is er niets veranderd, maar de waarde van het gegeven wordt veranderd om een administratieve fout te corrigeren. Tevens wordt daarbij het moment waarop die correctie plaatsvindt in de regisratie vastgelegd. Dit wordt in het stelsel van basisregistraties gedefinieerd als formele historie.

In het stelsel van basisregistratie dienen beide historische tijdlijnen te worden ondersteund. En in bovenstaande voorbeeld berichten (behalve in die van StUF Jeugdzorg) moet daar dan ook nog worden voorzien in de daarvoor noodzakelijke elementen. Hieronder een korte uitleg daarvan.

**Materiële historie**

Materiële historie wordt geregistreerd door middel van het metagegeven `<StUF:tijdvakGeldigheid>`, bestaande uit `<StUF:beginGeldigheid>` en `<StUF:eindGeldigheid>`, dat de periode aangeeft gedurende welke een gegeven of groep van gegevens in de werkelijkheid een bepaalde waarde heeft (gehad). 

```xml
<!-- Wanneer was iets geldig in de werkelijkheid -->
<BG:object StUF:entiteittype="NPS">
    ...
    <BG:tijdvakGeldigheid>
        <StUF:beginGeldigheid>20240101</StUF:beginGeldigheid>
        <StUF:eindGeldigheid StUF:noValue="nietGeautoriseerd" />
    </BG:tijdvakGeldigheid>
</BG:object>
```

De gegevens in het bericht waar dit fragment is opgenomen waren vanaf 1 januari 2024 geldig in de werkelijkheid en zijn dat op het tijdstip van verzendig van het bericht nog steeds.

**Formele historie**

Formele historie wordt geregistreerd door middel van het metagegeven `<StUF:tijdstipRegistratie>`, dat aangeeft op welk tijdstip de waarde(n) van een gegeven of een groep gegevens in de registratie is gewijzigd c.q. zijn opgenomen.

```xml
<!-- Wanneer werd iets geregistreerd in het systeem -->
<BG:object StUF:entiteittype="NPS">
    <BG:tijdstipRegistratie>20240105143000</BG:tijdstipRegistratie>
</BG:object>
```

De gegevens in het bericht waar dit fragment is opgenomen zijn op 5 januari 2024 om 14:30 opgenomen in de registratie. StUF kent geen `tijdstipEindeRegistratie`. Dat betekent dat de formele eindgeldigheid van een waarde alleen kan worden afgeleid uit een nieuwe `tijdstipRegistratie`. Daarnaast kan, als de registratie van een gegeven wordt beëindigt, dat in StUF alleen worden gecommuniceerd door een leeg voorkomen van die gegevens te versturen met een nieuwe `tijdstipRegistratie`. Tegenwoordig zien we dit als een omissie van StUF.

### StUF-foutafhandeling

Bij synchroon berichtenverkeer wordt de respons over dezelfde verbinding wordt teruggegeven als waarover het verzoek is gedaan. Het voordeel van synchroon verkeer is dat de verzoekende partij kan wachten op de respons op de verbinding waarover het verzoek gedaan is. Als de respons er binnen een zekere time-out tijd is, dan is het verzoek geslaagd en anders faalt het verzoek. Synchroon berichtenverkeer stelt hoge eisen aan de aanbieder van een service.

Asynchroon wil zeggen dat de respons over een, andere meestal nieuw opgezette, verbinding wordt gegeven. Service-aanbieders waarbij de belasting van de service sterk kan variëren, geven vaak de voorkeur aan asynchroon berichtenverkeer, want dan kunnen zij conform de eigen capaciteit de binnenkomende berichten verwerken. Asynchroon berichtenverkeer is dus robuuster, maar heeft als nadeel dat de serviceverzoeker herhaaldelijk zal moeten checken of er inmiddels een antwoord is ontvangen (pollen). De service-aanbieder verschuift dus een deel van zijn probleem naar de serviceverzoeker. Ook bij asynchroon berichtenverkeer kan er veel mis gaan.
Gebruik van intermediaire nodes maakt het berichtenverkeer nog gevoeliger voor fouten.

StUF kent daarom een eigen vorm van foutafhandeling van zowel synchroon als asynchroon berichtenverkeer. We onderkennen daarbij bevestigingsberichten (Bv01 t/m Bv04) en foutberichten (Fo01 t/m Fo03) in zowel synchrone als asynchrone varianten.

Hieronder een voorbeeld van een Fo01 foutbericht als functionele asynchrone respons.

**Foutbericht**

```xml
<StUF:Fo01Bericht>
    <StUF:stuurgegevens>
        <StUF:berichtcode>Fo01</StUF:berichtcode>
		...
        <StUF:crossRefnummer>REF-BRP-20240305-001</StUF:crossRefnummer>
    </StUF:stuurgegevens>
    
    <StUF:body>
        <StUF:code>StUF057</StUF:code>
        <StUF:plek>http://www.gemeente.nl/BRP-GWS</StUF:plek>
        <StUF:omschrijving>BSN niet gevonden in gegevensregistratie</StUF:omschrijving>
        <StUF:details>
            Er is geen persoon gevonden met BSN 123456789 in de BRP-registratie
        </StUF:details>
    </StUF:body>
</StUF:Fo01Bericht>
```

### Verdere verdieping

Je hebt nu een globale kennis van wat StUF is en hoe het er uitziet. Lees nu de <a href="https://vng-realisatie.github.io/StUF-onderlaag/documenten/Stuf0301.pdf" target="_blank">de specificatie van StUF Standaard</a> om je het onderwerp verder eigen te maken.

### Meer resources

- <a href="https://vng-realisatie.github.io/StUF-onderlaag/documenten/Stuf.bindingen.030204.pdf" target="_blank">Protocolbindingen voor StUF (versie 3.2.4)</a>
- <a href="https://vng-realisatie.github.io/StUF-onderlaag/" target="_blank">Website StUF onderlaag</a>
- <a href="https://vng-realisatie.github.io/StUF-BG/" target="_blank">Website StUF-BG</a>
- <a href="https://github.com/VNG-Realisatie/StUF-Standaarden/issues" target="_blank">Discussies vanaf 1 maart 2020</a>
- <a href="https://vng-realisatie.github.io/StUF-Standaarden/" target="_blank">Discussies van voor 1 maart 2020</a>
