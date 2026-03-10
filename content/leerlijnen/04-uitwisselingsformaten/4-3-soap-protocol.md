---
title: "4.3 SOAP-protocol"
date: 2026-03-04
weight: 4
leerlijn: 4
paragraaf: "4.3"
leerdoel: "Leerdoel nog toe te voegen"
---

## 4.3 SOAP-protocol

Kent het SOAP-protocol voor webservices (structuur, WSDL, security, foutafhandeling).

### Wat is SOAP?

**SOAP** (Simple Object Access Protocol, later hernoemd naar Service Oriented Architecture Protocol) is een messaging-protocol voor het uitwisselen van gestructureerde informatie in web services. SOAP is een W3C-standaard die zwaar wordt gebruikt in enterprise- en overheidsomgevingen voor betrouwbare, veilige communicatie tussen systemen.

#### Kerneigenschappen:
- **Protocol-agnostic**: Werkt over HTTP, HTTPS, SMTP, TCP
- **Platform-independent**: Kan tussen verschillende systemen communiceren
- **Extensible**: Uitbreidbaar met headers en security-features
- **Standardized**: Strikte W3C-specificatie met tooling-support

### SOAP-berichtstructuur

#### Basis SOAP-envelope

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope 
    xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
    xmlns:tns="http://example.org/persoon-service">
    
    <!-- Optionele headers -->
    <soap:Header>
        <tns:Authentication>
            <tns:Username>gemeente_amsterdam</tns:Username>
            <tns:Token>abc123-xyz789</tns:Token>
        </tns:Authentication>
    </soap:Header>
    
    <!-- Verplichte body -->
    <soap:Body>
        <tns:GetPersoonRequest>
            <tns:BSN>123456789</tns:BSN>
        </tns:GetPersoonRequest>
    </soap:Body>
</soap:Envelope>
```

#### SOAP-response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope 
    xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
    xmlns:tns="http://example.org/persoon-service">
    
    <soap:Body>
        <tns:GetPersoonResponse>
            <tns:Persoon>
                <tns:BSN>123456789</tns:BSN>
                <tns:Voornaam>Jan</tns:Voornaam>
                <tns:Achternaam>Berg</tns:Achternaam>
                <tns:Geboortedatum>1985-03-15</tns:Geboortedatum>
                <tns:Adres>
                    <tns:Straat>Hoofdstraat 42</tns:Straat>
                    <tns:Postcode>1234AB</tns:Postcode>
                    <tns:Plaats>Amsterdam</tns:Plaats>
                </tns:Adres>
            </tns:Persoon>
        </tns:GetPersoonResponse>
    </soap:Body>
</soap:Envelope>
```

#### SOAP Fault (foutafhandeling)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <soap:Fault>
            <soap:Code>
                <soap:Value>soap:Client</soap:Value>
                <soap:Subcode>
                    <soap:Value>tns:InvalidBSN</soap:Value>
                </soap:Subcode>
            </soap:Code>
            <soap:Reason>
                <soap:Text xml:lang="nl">
                    BSN '12345678X' is geen geldig Burgerservicenummer
                </soap:Text>
            </soap:Reason>
            <soap:Detail>
                <tns:ValidationError>
                    <tns:Field>BSN</tns:Field>
                    <tns:Message>BSN moet uit precies 9 cijfers bestaan</tns:Message>
                    <tns:Code>BSN_INVALID_FORMAT</tns:Code>
                </tns:ValidationError>
            </soap:Detail>
        </soap:Fault>
    </soap:Body>
</soap:Envelope>
```

### WSDL (Web Service Description Language)

WSDL beschrijft de interface van een SOAP web service - welke operaties beschikbaar zijn, hoe berichten gestructureerd zijn, en hoe de service te bereiken is.

#### WSDL-structuur

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions 
    xmlns="http://schemas.xmlsoap.org/wsdl/"
    xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
    xmlns:tns="http://example.org/persoon-service"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    targetNamespace="http://example.org/persoon-service">

    <!-- Data types (XSD schema) -->
    <types>
        <xsd:schema targetNamespace="http://example.org/persoon-service">
            <xsd:complexType name="Persoon">
                <xsd:sequence>
                    <xsd:element name="BSN" type="xsd:string"/>
                    <xsd:element name="Voornaam" type="xsd:string"/>
                    <xsd:element name="Achternaam" type="xsd:string"/>
                    <xsd:element name="Geboortedatum" type="xsd:date"/>
                </xsd:sequence>
            </xsd:complexType>
            
            <xsd:element name="GetPersoonRequest">
                <xsd:complexType>
                    <xsd:sequence>
                        <xsd:element name="BSN" type="xsd:string"/>
                    </xsd:sequence>
                </xsd:complexType>
            </xsd:element>
            
            <xsd:element name="GetPersoonResponse">
                <xsd:complexType>
                    <xsd:sequence>
                        <xsd:element name="Persoon" type="tns:Persoon"/>
                    </xsd:sequence>
                </xsd:complexType>
            </xsd:element>
        </xsd:schema>
    </types>

    <!-- Abstract message definitions -->
    <message name="GetPersoonRequestMessage">
        <part name="parameters" element="tns:GetPersoonRequest"/>
    </message>
    <message name="GetPersoonResponseMessage">
        <part name="parameters" element="tns:GetPersoonResponse"/>
    </message>

    <!-- Abstract interface (operations) -->
    <portType name="PersoonServiceInterface">
        <operation name="GetPersoon">
            <input message="tns:GetPersoonRequestMessage"/>
            <output message="tns:GetPersoonResponseMessage"/>
        </operation>
    </portType>

    <!-- Concrete protocol binding -->
    <binding name="PersoonServiceBinding" type="tns:PersoonServiceInterface">
        <soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
        <operation name="GetPersoon">
            <soap:operation soapAction="http://example.org/persoon-service/GetPersoon"/>
            <input>
                <soap:body use="literal"/>
            </input>
            <output>
                <soap:body use="literal"/>
            </output>
        </operation>
    </binding>

    <!-- Concrete endpoint -->
    <service name="PersoonService">
        <port name="PersoonServicePort" binding="tns:PersoonServiceBinding">
            <soap:address location="https://api.gemeente.nl/soap/persoon-service"/>
        </port>
    </service>
</definitions>
```

#### WSDL-gebruik voor code-generation

**Java (met wsimport):**
```bash
# Generate Java classes van WSDL
wsimport -keep -s src/main/java https://api.gemeente.nl/soap/persoon-service?wsdl

# Usage in code
PersoonService service = new PersoonService();
PersoonServiceInterface port = service.getPersoonServicePort();
GetPersoonResponse response = port.getPersoon(request);
```

**C# (met svcutil of Visual Studio):**
```bash
# Generate C# proxy classes
svcutil https://api.gemeente.nl/soap/persoon-service?wsdl /out:PersoonServiceProxy.cs

# Usage in code
var client = new PersoonServiceClient();
var response = await client.GetPersoonAsync(new GetPersoonRequest 
{ 
    BSN = "123456789" 
});
```

### SOAP Security (WS-Security)

#### Username Token Profile

```xml
<soap:Header>
    <wsse:Security 
        xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd"
        xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">
        
        <wsse:UsernameToken wsu:Id="UsernameToken-1">
            <wsse:Username>gemeente_amsterdam</wsse:Username>
            <wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordDigest">
                R1VrV0lPMCsvOUI3Ylc1M2V1VE9vUT09
            </wsse:Password>
            <wsse:Nonce EncodingType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-soap-message-security-1.0#Base64Binary">
                MTIzNDU2Nzg5MA==
            </wsse:Nonce>
            <wsu:Created>2024-03-05T14:30:00Z</wsu:Created>
        </wsse:UsernameToken>
    </wsse:Security>
</soap:Header>
```

#### X.509 Certificate Security

```xml
<soap:Header>
    <wsse:Security>
        <wsse:BinarySecurityToken
            ValueType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-x509-token-profile-1.0#X509v3"
            EncodingType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-soap-message-security-1.0#Base64Binary"
            wsu:Id="X509Token">
            MIICdTCCAd4CAQAwDQ...
        </wsse:BinarySecurityToken>
        
        <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <!-- Digital signature components -->
        </ds:Signature>
    </wsse:Security>
</soap:Header>
```

#### SAML Token Profile (overheidscontext)

```xml
<soap:Header>
    <wsse:Security>
        <saml:Assertion 
            xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
            ID="assertion-123"
            Version="2.0"
            IssueInstant="2024-03-05T14:30:00Z">
            
            <saml:Issuer>https://identity.overheid.nl</saml:Issuer>
            
            <saml:Subject>
                <saml:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">
                    gemeente_amsterdam_user_001
                </saml:NameID>
            </saml:Subject>
            
            <saml:AttributeStatement>
                <saml:Attribute Name="gemeente">
                    <saml:AttributeValue>Amsterdam</saml:AttributeValue>
                </saml:Attribute>
                <saml:Attribute Name="rol">
                    <saml:AttributeValue>BAG_Beheerder</saml:AttributeValue>
                </saml:Attribute>
            </saml:AttributeStatement>
        </saml:Assertion>
    </wsse:Security>
</soap:Header>
```

### SOAP in overheidscontext

#### StUF over SOAP

StUF (Standaard Uitwisseling Formaat) wordt vaak over SOAP getransporteerd:

```xml
<!-- StUF Lv01 bericht over SOAP -->
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Header>
        <wsse:Security>
            <!-- Authentication headers -->
        </wsse:Security>
    </soap:Header>
    
    <soap:Body>
        <StUF:Lv01Bericht 
            xmlns:StUF="http://www.stufstandaarden.nl/koppelvlak/stuf"
            xmlns:BG="http://www.stufstandaarden.nl/onderlaag/bg">
            
            <StUF:stuurgegevens>
                <StUF:berichtCode>Lv01</StUF:berichtCode>
                <StUF:zender>
                    <StUF:organisatie>0363</StUF:organisatie>
                    <StUF:applicatie>BRP-Service</StUF:applicatie>
                </StUF:zender>
                <StUF:ontvanger>
                    <StUF:organisatie>0518</StUF:organisatie>
                    <StUF:applicatie>Zaak-Service</StUF:applicatie>
                </StUF:ontvanger>
            </StUF:stuurgegevens>
            
            <StUF:vraag>
                <StUF:filter>
                    <BG:BSN>123456789</BG:BSN>
                </StUF:filter>
            </StUF:vraag>
        </StUF:Lv01Bericht>
    </soap:Body>
</soap:Envelope>
```

#### Digipoort/Diginetwerk

Voor communicatie met centrale overheidsvoorzieningen:

```xml
<soap:Envelope 
    xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
    xmlns:dp="http://www.logius.nl/digipoort">
    
    <soap:Header>
        <!-- PKIoverheid certificaat -->
        <wsse:Security>
            <wsse:BinarySecurityToken>
                <!-- PKI certificaat data -->
            </wsse:BinarySecurityToken>
        </wsse:Security>
    </soap:Header>
    
    <soap:Body>
        <dp:RoutingInformation>
            <dp:BerichtSoort>BelastingAangifte</dp:BerichtSoort>
            <dp:Organisatie>12345678</dp:Organisatie>
        </dp:RoutingInformation>
        
        <dp:BerichtData>
            <!-- Actual business data -->
        </dp:BerichtData>
    </soap:Body>
</soap:Envelope>
```

### SOAP-implementatie en tools

#### Server-side implementaties

**Java (JAX-WS):**
```java
@WebService
public class PersoonService {
    
    @WebMethod
    public GetPersoonResponse getPersoon(GetPersoonRequest request) {
        // Validate BSN
        if (!isValidBSN(request.getBSN())) {
            SOAPException fault = new SOAPException("Ongeldig BSN");
            throw new SOAPFaultException(createFault("Client", "InvalidBSN", 
                "Het opgegeven BSN is niet geldig"));
        }
        
        // Business logic
        Persoon persoon = persoonRepository.findByBSN(request.getBSN());
        
        GetPersoonResponse response = new GetPersoonResponse();
        response.setPersoon(persoon);
        return response;
    }
    
    private boolean isValidBSN(String bsn) {
        return bsn != null && bsn.matches("[0-9]{9}");
    }
}
```

**C# (WCF):**
```csharp
[ServiceContract(Namespace = "http://example.org/persoon-service")]
public interface IPersoonService
{
    [OperationContract]
    [FaultContract(typeof(ValidationFault))]
    GetPersoonResponse GetPersoon(GetPersoonRequest request);
}

public class PersoonService : IPersoonService
{
    public GetPersoonResponse GetPersoon(GetPersoonRequest request)
    {
        // Validation
        if (!IsValidBSN(request.BSN))
        {
            var fault = new ValidationFault 
            { 
                Message = "Ongeldig BSN format",
                Field = "BSN" 
            };
            throw new FaultException<ValidationFault>(fault);
        }
        
        // Business logic
        var persoon = _persoonRepository.GetByBSN(request.BSN);
        return new GetPersoonResponse { Persoon = persoon };
    }
}
```

#### Client-side consumption

**Python (zeep library):**
```python
from zeep import Client
from zeep.wsse.username import UsernameToken

# Create client with authentication
wsdl_url = 'https://api.gemeente.nl/soap/persoon-service?wsdl'
client = Client(wsdl_url, wsse=UsernameToken('username', 'password'))

# Call service
try:
    response = client.service.GetPersoon(BSN='123456789')
    print(f"Gevonden: {response.Persoon.Voornaam} {response.Persoon.Achternaam}")
    
except Exception as e:
    print(f"SOAP Fault: {e}")
```

**PHP (SoapClient):**
```php
<?php
$wsdl = 'https://api.gemeente.nl/soap/persoon-service?wsdl';
$options = [
    'login' => 'username',
    'password' => 'password',
    'soap_version' => SOAP_1_2,
    'trace' => true,
    'exceptions' => true
];

try {
    $client = new SoapClient($wsdl, $options);
    
    $request = ['BSN' => '123456789'];
    $response = $client->GetPersoon($request);
    
    echo "Gevonden: " . $response->Persoon->Voornaam;
    
} catch (SoapFault $fault) {
    echo "SOAP Fault: " . $fault->getMessage();
    echo "Request: " . $client->__getLastRequest();
}
?>
```

### SOAP vs REST vergelijking

#### Wanneer SOAP kiezen:

**✅ SOAP-voordelen:**
- **Enterprise security**: Uitgebreide WS-Security standaarden
- **ACID transactions**: Database-like transaction support
- **Formal contracts**: WSDL biedt strikte interface-definitie
- **Built-in error handling**: Gestandaardiseerde fault-structuur
- **Legacy integration**: Veel enterprise-systemen zijn SOAP-based

**✅ SOAP use cases:**
- Financial transactions (banktransacties)
- Government-to-government communication
- Mission-critical systems waar ACID-properties crucial zijn
- Integration met .NET/Java enterprise-stacks
- Compliance-vereisten die WS-Security mandateren

#### Wanneer REST preferen:

**⚠️ REST-voordelen:**
- **Simplicity**: Makkelijker te implementeren en debuggen  
- **Performance**: Minder overhead, snellere processing
- **Web-friendly**: Native HTTP-verbs, cacheable
- **Mobile-friendly**: Lightweight, JSON-based
- **Developer experience**: Intuitive API design

**⚠️ REST use cases:**
- Public APIs voor third-party developers
- Web/mobile applications
- Microservices-architectuur
- Real-time/streaming data  
- Rapid prototyping

### SOAP Best practices voor overheid

#### Error handling

```xml
<!-- Structured error response -->
<soap:Fault>
    <soap:Code>
        <soap:Value>soap:Client</soap:Value>
        <soap:Subcode>
            <soap:Value>gov:ValidationError</soap:Value>
        </soap:Subcode>
    </soap:Code>
    <soap:Reason>
        <soap:Text xml:lang="nl">Gegevens validatie gefaald</soap:Text>
    </soap:Reason>
    <soap:Detail>
        <gov:ErrorDetails>
            <gov:ErrorCode>BSN_INVALID</gov:ErrorCode>
            <gov:Field>BSN</gov:Field>
            <gov:Description>BSN moet 9 cijfers bevatten</gov:Description>
            <gov:HelpUrl>https://docs.gemeente.nl/errors/BSN_INVALID</gov:HelpUrl>
        </gov:ErrorDetails>
    </soap:Detail>
</soap:Fault>
```

#### Logging en monitoring

```xml 
<!-- Custom headers voor tracing -->
<soap:Header>
    <gov:RequestMetadata>
        <gov:RequestId>req_abc123</gov:RequestId>
        <gov:Timestamp>2024-03-05T14:30:00Z</gov:Timestamp>
        <gov:OrganisationCode>0363</gov:OrganisationCode>
        <gov:ApplicationName>BRP-Console</gov:ApplicationName>
        <gov:UserContext>
            <gov:UserId>user.gemeente@amsterdam.nl</gov:UserId>
            <gov:Role>BAG_Reader</gov:Role>
        </gov:UserContext>
    </gov:RequestMetadata>
</soap:Header>
```

#### Performance optimization

```java
// Connection pooling
@Bean
public HttpComponentsClientHttpRequestFactory httpRequestFactory() {
    HttpComponentsClientHttpRequestFactory factory = 
        new HttpComponentsClientHttpRequestFactory();
    factory.setConnectionTimeout(5000);
    factory.setReadTimeout(30000);
    
    // Connection pooling
    PoolingHttpClientConnectionManager connManager = 
        new PoolingHttpClientConnectionManager();
    connManager.setMaxTotal(100);
    connManager.setDefaultMaxPerRoute(20);
    
    HttpClient httpClient = HttpClientBuilder.create()
        .setConnectionManager(connManager)
        .build();
    factory.setHttpClient(httpClient);
    
    return factory;
}
```

### SOAP-migratie naar REST

Voor organisaties die van SOAP naar REST willen migreren:

#### Gefaseerde migration

**Fase 1: Wrapper-layer**
```java
@RestController
public class PersoonRestController {
    
    @Autowired
    private PersoonSoapClient soapClient;
    
    @GetMapping("/api/personen/{bsn}")
    public ResponseEntity<PersoonDTO> getPersoon(@PathVariable String bsn) {
        try {
            // Call existing SOAP service
            GetPersoonResponse soapResponse = soapClient.getPersoon(bsn);
            
            // Transform to REST DTO
            PersoonDTO restDTO = PersoonMapper.toDTO(soapResponse.getPersoon());
            
            return ResponseEntity.ok(restDTO);
            
        } catch (SOAPFaultException e) {
            return ResponseEntity.badRequest()
                .body(ErrorMapper.toRestError(e));
        }
    }
}
```

**Fase 2: Parallel implementations**
- SOAP en REST endpoints parallel beschikbaar
- Gradual migration van clients
- A/B testing voor performance/reliability

**Fase 3: SOAP deprecation** 
- Clear communication naar consumers
- Sunset headers in SOAP responses
- Monitoring van SOAP usage

SOAP blijft een belangrijk protocol in overheidscontext, vooral voor mission-critical, secure communicatie tussen systemen. Hoewel REST dominanter wordt voor nieuwe development, is SOAP-kennis essentieel voor het onderhouden en migreren van bestaande enterprise-systemen.

**Resources:**
- [W3C SOAP Specification](https://www.w3.org/TR/soap12/)
- [WS-Security Profile](https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-soap-message-security-1.0.pdf)
- [StUF Standaard](http://www.stufstandaarden.nl/)
- [Digikoppeling Koppelvlakspecificatie](https://www.logius.nl/diensten/digikoppeling)