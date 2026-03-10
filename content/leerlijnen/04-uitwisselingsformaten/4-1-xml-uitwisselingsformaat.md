---
title: "4.1 XML als uitwisselingsformaat"
date: 2026-03-04
weight: 2
leerlijn: 4
paragraaf: "4.1"
leerdoel: "Leerdoel nog toe te voegen"
---

## 4.1 XML als uitwisselingsformaat

Kent XML als uitwisselingsformaat (structuur, syntax, schema's).

### Wat is XML?

**XML** (eXtensible Markup Language) is een markup-taal ontworpen voor het opslaan en transporteren van gestructureerde data. In de overheidscontext is XML de ruggengraat van vele uitwisselingstandaarden, waaronder StUF (Standaard Uitwisseling Formaat).

#### Kernprincipes van XML:
- **Self-describing**: Data en structuur in één formaat
- **Human-readable**: Leesbaar voor mensen én machines  
- **Platform-independent**: Werkt op alle systemen
- **Extensible**: Flexibel uitbreidbaar voor specific behoeften

### XML-structuur en syntax

#### Basis XML-elementen

```xml
<?xml version="1.0" encoding="UTF-8"?>
<BRP:Persoon xmlns:BRP="http://www.stufstandaarden.nl/basisgegevens">
    <BRP:BSN>123456789</BRP:BSN>
    <BRP:Geslachtsnaam>
        <BRP:Voorvoegsel>van der</BRP:Voorvoegsel>
        <BRP:GeslachtsnaamStam>Berg</BRP:GeslachtsnaamStam>
    </BRP:Geslachtsnaam>
    <BRP:Voornamen>Jan Peter</BRP:Voornamen>
    <BRP:Geboortedatum>1985-03-15</BRP:Geboortedatum>
</BRP:Persoon>
```

#### Syntax-regels
- **Case-sensitive**: `<Persoon>` ≠ `<persoon>`
- **Well-formed**: Elke opening-tag heeft matching closing-tag
- **Nested structure**: Elementen kunnen elementen bevatten
- **Attribute syntax**: `<element attribuut="waarde">`

#### Namespaces
```xml
<!-- Namespace declaraties -->
<StUF:Bv03Bericht 
    xmlns:StUF="http://www.stufstandaarden.nl/koppelvlak/stuf"
    xmlns:BG="http://www.stufstandaarden.nl/onderlaag/bg" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    
    <StUF:stuurgegevens>
        <StUF:berichtCode>Lv01</StUF:berichtCode>
        <StUF:zender>
            <StUF:organisatie>0363</StUF:organisatie>
        </StUF:zender>
    </StUF:stuurgegevens>
</StUF:Bv03Bericht>
```

### XML Schema's (XSD)

XML Schema Definition (XSD) definieert de structuur, elementen en datatypes van XML-documenten.

#### Simpel XSD-voorbeeld
```xsd
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.example.com/persoon"
           xmlns:tns="http://www.example.com/persoon">

    <!-- Complex type voor Persoon -->
    <xs:complexType name="PersoonType">
        <xs:sequence>
            <xs:element name="BSN" type="xs:string"/>
            <xs:element name="Voornaam" type="xs:string"/>
            <xs:element name="Achternaam" type="xs:string"/>
            <xs:element name="Geboortedatum" type="xs:date"/>
        </xs:sequence>
    </xs:complexType>

    <!-- Root element -->
    <xs:element name="Persoon" type="tns:PersoonType"/>
</xs:schema>
```

#### XSD-features voor overheidsgebruik

**Datatype-restricties:**
```xsd
<!-- BSN validatie -->
<xs:simpleType name="BSNType">
    <xs:restriction base="xs:string">
        <xs:pattern value="[0-9]{9}"/>
        <xs:length value="9"/>
    </xs:restriction>
</xs:simpleType>

<!-- Postcode validatie -->
<xs:simpleType name="PostcodeType">
    <xs:restriction base="xs:string">
        <xs:pattern value="[1-9][0-9]{3} ?[A-Za-z]{2}"/>
    </xs:restriction>
</xs:simpleType>
```

**Enumeraties voor codelijsten:**
```xsd
<xs:simpleType name="GeslachtType">
    <xs:restriction base="xs:string">
        <xs:enumeration value="M"/>
        <xs:enumeration value="V"/>
        <xs:enumeration value="O"/>
    </xs:restriction>
</xs:simpleType>
```

### XML in StUF-context

#### StUF-berichtstructuur
```xml
<StUF:Bv03Bericht>
    <StUF:stuurgegevens>
        <StUF:berichtCode>Lv01</StUF:berichtCode>
        <StUF:zender>
            <StUF:organisatie>0363</StUF:organisatie>
            <StUF:applicatie>BRP-System</StUF:applicatie>
        </StUF:zender>
        <StUF:ontvanger>    
            <StUF:organisatie>0518</StUF:organisatie>
            <StUF:applicatie>Zaak-System</StUF:applicatie>
        </StUF:ontvanger>
        <StUF:referentieNummer>REF-2024-001</StUF:referentieNummer>
        <StUF:tijdstempel>2024-03-05T14:30:00</StUF:tijdstempel>
    </StUF:stuurgegevens>
    
    <StUF:body>
        <StUF:vraag>
            <StUF:filter>
                <BG:BSN>123456789</BG:BSN>
            </StUF:filter>
        </StUF:vraag>
    </StUF:body>
</StUF:Bv03Bericht>
```

### XML-processing tools

#### Validatie-tools
- **xmllint**: Command-line XML validator (Linux/Mac)
- **XML Spy**: Commercial XML-editor met validatie
- **Oxygen XML Editor**: Professional XML development
- **Online validators**: Voor quick checks

#### Programming libraries

**Java:**
```java
// DOM parsing
DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
Document doc = builder.parse(new File("bericht.xml"));

// XPath queries  
XPath xpath = XPathFactory.newInstance().newXPath();
String bsn = xpath.compile("//BRP:BSN/text()").evaluate(doc);
```

**Python:**
```python
import xml.etree.ElementTree as ET

# Parse XML
tree = ET.parse('bericht.xml')
root = tree.getroot()

# Find elements
bsn = root.find('.//{namespace}BSN').text
```

**C#:**
```csharp
// XDocument (LINQ to XML)
XDocument doc = XDocument.Load("bericht.xml");
var bsn = doc.Descendants("BSN").First().Value;

// XmlDocument (legacy approach)
XmlDocument doc = new XmlDocument();
doc.Load("bericht.xml");
```

### XML Best practices voor overheidstoepassingen

#### Performance-optimalisatie
- **Streaming**: Gebruik SAX/StAX voor grote bestanden
- **Indexing**: XPath-performance met indices verbeteren
- **Compression**: GZIP voor transport van grote XML-berichten

#### Security-overwegingen
```xml
<!-- XXE Prevention: Disable external entities -->
<?xml version="1.0"?>
<!DOCTYPE data [
    <!ENTITY harmless "safe data">
    <!-- Nooit external entities toestaan -->
]>
```

#### Maintainability
- **Consistent naming**: CamelCase vs snake_case conventions
- **Clear namespaces**: Herkenbare URI's gebruiken
- **Documentation**: Annotated XSD's voor business-betekenis

### Veelvoorkomende XML-problemen

#### Character encoding issues
```xml
<!-- Correct encoding-declaratie -->
<?xml version="1.0" encoding="UTF-8"?>

<!-- Common problems -->
- Encoding mismatch tussen declaratie en actual file-encoding
- Special characters niet-properly escaped (&amp; &lt; &gt;)
- BOM (Byte Order Mark) in sommige tools
```

#### Namespace-complicaties
```xml
<!-- Probleem: Default namespace overschrijft child-namespaces -->
<root xmlns="http://default.com">
    <child xmlns="http://other.com">data</child>
</root>

<!-- Oplossing: Expliciete prefixes -->
<def:root xmlns:def="http://default.com" xmlns:other="http://other.com">
    <other:child>data</other:child>
</def:root>
```

### XML vs andere formaten

#### Voordelen van XML:
- **Rich metadata**: Attributes én element-content
- **Schema validation**: Strenge type-checking mogelijk
- **Industry adoption**: Brede tool-support
- **XSLT transformations**: Powerful formatting-capabilities

#### Nadelen van XML:
- **Verbosity**: Veel markup-overhead
- **Parsing-cost**: Meer CPU/memory dan JSON
- **Complexity**: Learning-curve voor developers
- **File sizes**: Groter dan binary-alternatives

### Toekomst van XML in overheidscontext

#### Legacy-ondersteuning
XML blijft relevant voor:
- **Bestaande StUF-implementaties**: Miljoenen berichten per dag
- **Legal-compliance**: Sommige wet- en regelgeving specificeert XML
- **Enterprise-integration**: SOAP-based systemen in large organizations

#### Evolutie naar APIs
- **REST/JSON**: Moderne APIs kiezen vaak voor eenvoudiger formaten  
- **GraphQL**: Query-language voor flexible data-retrieval
- **Protocol Buffers**: High-performance binary alternatives

**Hybride aanpak:**
```
Legacy systems ←→ XML ←→ Translation layer ←→ JSON ←→ Modern APIs
```

Het begrijpen van XML blijft essentieel voor overheidsmedewerkers die werken met gegevensuitwisseling, ook in een tijd waarin nieuwe technologieën dominant worden. XML-expertise faciliteert zowel onderhoud van legacy-systemen als migratie naar modernere architecturen.

**Resources:**
- [W3C XML Specification](https://www.w3.org/XML/)
- [W3C XML Schema](https://www.w3.org/XML/Schema)
- [StUF Standaard Documentatie](https://www.gemmaonline.nl/index.php/StUF)