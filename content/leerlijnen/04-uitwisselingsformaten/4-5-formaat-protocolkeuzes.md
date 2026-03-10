---
title: "4.5 Formaat- en protocolkeuzes"
date: 2026-03-04
weight: 6
leerlijn: 4
paragraaf: "4.5"
leerdoel: "Leerdoel nog toe te voegen"
---

## 4.5 Formaat- en protocolkeuzes

Kan afgewogen keuzes maken tussen verschillende uitwisselingsformaten en protocollen op basis van context en requirements.

### Decision Framework voor Format- en Protocolkeuze

Het kiezen van het juiste uitwisselingsformaat en protocol is een strategische beslissing die lange-termijn impact heeft op maintainability, performance en interoperabiliteit. Voor overheidsorganisaties zijn er specifieke overwegingen die deze keuze beïnvloeden.

#### Evaluatie-criteria

**Technical Criteria:**
- **Performance**: Message-size, parsing-speed, bandwidth-usage
- **Complexity**: Development-effort, debugging-ease, tooling
- **Interoperability**: Cross-platform support, legacy-compatibility  
- **Security**: Built-in security features, vulnerability-resistance
- **Scalability**: High-volume handling, caching-support

**Business Criteria:**
- **Standards compliance**: Wet- en regelgeving-vereisten
- **Legacy integration**: Bestaande systemen en interfaces
- **Future-proofing**: Modernization-roadmap compatibility  
- **Cost**: Development, maintenance en operational costs
- **Risk**: Vendor lock-in, obsolescence-risk

**Organizational Criteria:**
- **Team expertise**: Available skills and knowledge
- **Tooling availability**: Development and monitoring tools
- **Support ecosystem**: Community, documentation, training
- **Governance**: Architectural guidelines and policies

### Format-vergelijking Matrix

#### XML vs JSON vs Binary

| Aspect | XML | JSON | Binary (Protocol Buffers) |
|--------|-----|------|----------------------------|
| **Human Readable** | ✅ Excellent | ✅ Excellent | ❌ None |
| **Message Size** | ❌ Large (100%) | ✅ Medium (60-70%) | ✅ Small (20-30%) |
| **Parsing Speed** | ❌ Slow | ✅ Fast | ✅ Very Fast |
| **Schema Validation** | ✅ XSD | ✅ JSON Schema | ✅ Proto definitions |
| **Metadata Support** | ✅ Rich attributes | ⚠️ Limited | ✅ Rich typing |
| **Legacy Support** | ✅ Extensive | ⚠️ Modern systems | ❌ Limited |
| **Browser Support** | ✅ Native | ✅ Native | ❌ Requires libraries |
| **Debugging** | ✅ Easy | ✅ Easy | ❌ Difficult |

#### Protocol-vergelijking Matrix

| Aspect | SOAP/HTTP | REST/HTTP | GraphQL/HTTP | gRPC/HTTP2 |
|--------|-----------|-----------|--------------|------------|
| **Complexity** | ❌ High | ✅ Low | ⚠️ Medium | ⚠️ Medium |
| **Performance** | ❌ Slow | ✅ Good | ✅ Good | ✅ Excellent |
| **Caching** | ❌ Limited | ✅ Excellent | ⚠️ Complex | ❌ Limited |
| **Security** | ✅ WS-Security | ⚠️ Custom | ⚠️ Custom | ✅ Built-in TLS |
| **Tooling** | ✅ Mature | ✅ Excellent | ✅ Growing | ⚠️ Limited |
| **Real-time** | ❌ No | ❌ No | ✅ Subscriptions | ✅ Streaming |
| **Standards** | ✅ W3C | ⚠️ Best practices | ⚠️ Facebook | ✅ Google/CNCF |

### Use Case-based keuzerichtlijnen

#### Overheid-to-Overheid (G2G) Communicatie

**Scenario**: Gemeente Amsterdam moet persoonsgegevens uitwisselen met Belastingdienst

**Recommended**: SOAP + XML
```xml
<!-- Reden: Compliance, security, legacy-compatibility -->
<soap:Envelope>
    <soap:Header>
        <wsse:Security>
            <wsse:UsernameToken>
                <wsse:Username>gemeente_amsterdam</wsse:Username>
                <!-- PKIoverheid certificaat -->
            </wsse:UsernameToken>
        </wsse:Security>
    </soap:Header>
    <soap:Body>
        <stuf:Lv01Bericht>
            <!-- StUF-compliant persoon-data -->
        </stuf:Lv01Bericht>
    </soap:Body>
</soap:Envelope>
```

**Overwegingen:**
- ✅ Voldoet aan StUF-standards
- ✅ WS-Security voor PKIoverheid-compliance
- ✅ WSDL-gebaseerde service-contracts
- ❌ Performance-overhead acceptabel voor batch-processing

#### Public API voor Developers (G2B)

**Scenario**: Gemeente wil open data beschikbaar maken voor app-developers

**Recommended**: REST + JSON
```http
GET /api/v1/evenementen?datum_van=2024-03-01&categorie=cultuur HTTP/1.1
Accept: application/json
Accept-Language: nl-NL

HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: public, max-age=3600

{
  "evenementen": [
    {
      "id": "evt_123",
      "titel": "Concertgebouw Concert", 
      "datum": "2024-03-15T20:00:00+01:00",
      "locatie": {
        "naam": "Concertgebouw",
        "adres": "Concertgebouwplein 10, Amsterdam"
      },
      "_links": {
        "self": "/api/v1/evenementen/evt_123",
        "tickets": "https://tickets.concertgebouw.nl/evt_123"
      }
    }
  ],
  "pagination": {
    "total": 156,
    "page": 1,
    "per_page": 25,
    "links": {
      "next": "/api/v1/evenementen?page=2"
    }
  }
}
```

**Overwegingen:**
- ✅ Developer-friendly (JSON, HTTP-verbs)
- ✅ Excellent caching voor performance
- ✅ Wide tooling support (curl, Postman, etc.)
- ✅ Self-describing met HATEOAS-links

#### Real-time Citizen Services (G2C)

**Scenario**: Burger-app die real-time updates wil over aanvraag-status

**Option 1**: Server-Sent Events + JSON
```javascript
// Client-side
const eventSource = new EventSource('/api/aanvragen/aan_123/status-stream');

eventSource.onmessage = (event) => {
  const statusUpdate = JSON.parse(event.data);
  console.log('Status update:', statusUpdate);
};

// Server-side (Node.js)
app.get('/api/aanvragen/:id/status-stream', (req, res) => {
  res.writeHead(200, {
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive'
  });
  
  const sendUpdate = (status) => {
    res.write(`data: ${JSON.stringify({
      aanvraag_id: req.params.id,
      status: status,
      timestamp: new Date().toISOString()
    })}\n\n`);
  };
  
  // Subscribe to status-changes
  statusService.subscribe(req.params.id, sendUpdate);
});
```

**Option 2**: GraphQL Subscriptions
```graphql
# Subscription
subscription AanvraagStatusUpdates($aanvraagId: ID!) {
  aanvraagStatusUpdate(aanvraagId: $aanvraagId) {
    id
    status
    beschrijving
    verwachte_afhandeling
    behandelaar {
      naam
      afdeling
    }
  }
}

# Client response
{
  "data": {
    "aanvraagStatusUpdate": {
      "id": "aan_123",
      "status": "IN_BEHANDELING",
      "beschrijving": "Uw aanvraag wordt beoordeeld door de vakspecialist",
      "verwachte_afhandeling": "2024-03-20T15:00:00Z"
    }
  }
}
```

#### High-Performance Batch Processing

**Scenario**: Nightly sync van 100k+ records tussen systemen

**Recommended**: gRPC + Protocol Buffers
```protobuf
// persoon.proto
syntax = "proto3";
package gemeente.sync;

message Persoon {
  string bsn = 1;
  string voornaam = 2;
  string achternaam = 3;
  int64 geboortedatum_unix = 4; // Unix timestamp for efficiency
  Adres adres = 5;
}

message Adres {
  string straat = 1;
  int32 huisnummer = 2;
  string postcode = 3;
  string woonplaats = 4;
}

message SyncPersonenRequest {
  repeated Persoon personen = 1;
  int64 batch_id = 2;
}

message SyncPersonenResponse {
  int32 processed_count = 1;
  repeated string failed_bsns = 2;
  string status = 3;
}

service PersonenSyncService {
  rpc SyncPersonen(SyncPersonenRequest) returns (SyncPersonenResponse);
  rpc StreamPersonen(SyncPersonenRequest) returns (stream SyncPersonenResponse);
}
```

**Implementation (Go):**
```go
type server struct {
    pb.UnimplementedPersonenSyncServiceServer
}

func (s *server) SyncPersonen(ctx context.Context, req *pb.SyncPersonenRequest) (*pb.SyncPersonenResponse, error) {
    log.Printf("Processing batch %d with %d personen", req.BatchId, len(req.Personen))
    
    processedCount := 0
    var failedBSNs []string
    
    for _, persoon := range req.Personen {
        if err := s.processPersoon(persoon); err != nil {
            failedBSNs = append(failedBSNs, persoon.Bsn)
            log.Printf("Failed to process BSN %s: %v", persoon.Bsn, err)
        } else {
            processedCount++
        }
    }
    
    return &pb.SyncPersonenResponse{
        ProcessedCount: int32(processedCount),
        FailedBsns:     failedBSNs,
        Status:         "COMPLETED",
    }, nil
}
```

### Migration Strategies

#### Legacy SOAP naar Modern REST

**Phase 1: Façade Pattern**
```java
@RestController
public class ModernPersonenController {
    
    @Autowired
    private LegacyPersoonSoapClient legacySoapClient;
    
    @GetMapping("/api/v1/personen/{bsn}")
    public ResponseEntity<PersonenDTO> getPersoon(@PathVariable String bsn) {
        try {
            // Call legacy SOAP service
            GetPersonResponse soapResponse = legacySoapClient.getPersoon(bsn);
            
            // Transform SOAP response to REST DTO
            PersonenDTO restResponse = PersonenMapper.fromSoap(soapResponse);
            
            return ResponseEntity.ok()
                .cacheControl(CacheControl.maxAge(Duration.ofMinutes(15)))
                .eTag(restResponse.getVersion())
                .body(restResponse);
                
        } catch (SOAPFaultException e) {
            // Transform SOAP fault to REST error
            return ResponseEntity.badRequest()
                .body(ErrorMapper.fromSoapFault(e));
        }
    }
}
```

**Phase 2: Parallel Implementation**
```java
@Configuration
public class PersonenServiceConfiguration {
    
    @Bean
    @ConditionalOnProperty(name = "personen.service.type", havingValue = "soap")
    public PersonenService soapPersonenService() {
        return new SoapPersonenServiceImpl();
    }
    
    @Bean
    @ConditionalOnProperty(name = "personen.service.type", havingValue = "rest")
    public PersonenService restPersonenService() {
        return new RestPersonenServiceImpl();
    }
}
```

**Phase 3: Feature Toggle Migration**
```yaml
# application.yml
features:
  new-personen-api:
    enabled: true
    rollout-percentage: 25  # Gradual rollout
    
personen:
  service:
    type: ${PERSONEN_SERVICE_TYPE:rest}  # Environment-based toggle
```

#### XML to JSON Content Negotiation

```java
@RestController
public class FlexiblePersonenController {
    
    @GetMapping(value = "/api/personen/{bsn}", 
               produces = {MediaType.APPLICATION_JSON_VALUE, 
                          MediaType.APPLICATION_XML_VALUE})
    public ResponseEntity<PersonenData> getPersoon(
            @PathVariable String bsn,
            @RequestHeader(name = "Accept", defaultValue = "application/json") String acceptHeader) {
        
        PersonenData data = personenService.getPersoon(bsn);
        
        // Set appropriate content-type based on request
        MediaType contentType = acceptHeader.contains("xml") 
            ? MediaType.APPLICATION_XML 
            : MediaType.APPLICATION_JSON;
            
        return ResponseEntity.ok()
            .contentType(contentType)
            .body(data);
    }
}
```

### Performance Benchmarking

Voor data-driven keuzes zijn performance-metingen essentieel:

#### Message Size Comparison

```javascript
// Test data: 1000 persoon-records
const testData = generatePersonenData(1000);

// XML (StUF-style)
const xmlSize = createXMLRepresentation(testData).length;
console.log(`XML size: ${xmlSize} bytes`);  // ~2.5MB

// JSON 
const jsonSize = JSON.stringify(testData).length;
console.log(`JSON size: ${jsonSize} bytes`); // ~1.8MB (-28%)

// Protocol Buffers
const protobufSize = serializeToProtobuf(testData).length;
console.log(`Protobuf size: ${protobufSize} bytes`); // ~0.6MB (-76%)

// Compressed JSON (gzip)
const compressedSize = gzip(JSON.stringify(testData)).length;
console.log(`Compressed JSON: ${compressedSize} bytes`); // ~0.4MB (-84%)
```

#### Parsing Performance

```javascript
const Benchmark = require('benchmark');
const suite = new Benchmark.Suite();

// Test parsing performance
suite
  .add('XML parsing (DOMParser)', function() {
    const parser = new DOMParser();
    const doc = parser.parseFromString(xmlData, 'application/xml');
    extractPersonenFromXML(doc);
  })
  .add('JSON parsing (native)', function() {
    const data = JSON.parse(jsonData);
    extractPersonenFromJSON(data);
  })
  .add('Protocol Buffers parsing', function() {
    const data = protobuf.PersonenList.decode(protobufData);
    extractPersonenFromProtobuf(data);
  })
  .on('cycle', function(event) {
    console.log(String(event.target));
  })
  .on('complete', function() {
    console.log('Fastest is ' + this.filter('fastest').map('name'));
  })
  .run();

// Results:
// JSON parsing (native) x 10,234 ops/sec ±1.2%
// Protocol Buffers parsing x 8,456 ops/sec ±2.1% 
// XML parsing (DOMParser) x 2,123 ops/sec ±3.4%
```

### Security Considerations per Protocol

#### SOAP Security Features

```xml
<!-- 1. WS-Security Username Token -->
<wsse:Security>
    <wsse:UsernameToken>
        <wsse:Username>gemeente_user</wsse:Username>
        <wsse:Password Type="...#PasswordDigest">...</wsse:Password>
        <wsse:Nonce>MTIzNDU2Nzg5MA==</wsse:Nonce>
        <wsu:Created>2024-03-05T14:30:00Z</wsu:Created>
    </wsse:UsernameToken>
</wsse:Security>

<!-- 2. XML Signature for message integrity -->
<ds:Signature>
    <ds:SignedInfo>
        <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256"/>
    </ds:SignedInfo>
    <!-- Signature details -->
</ds:Signature>

<!-- 3. XML Encryption for sensitive data -->
<xenc:EncryptedData Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes256-cbc"/>
    <xenc:CipherData>
        <xenc:CipherValue>encrypted_sensitive_data_here</xenc:CipherValue>
    </xenc:CipherData>
</xenc:EncryptedData>
```

#### REST Security Best Practices

```http
# 1. OAuth 2.0 + JWT Bearer tokens
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...

# 2. HTTPS with strong TLS
HTTP/2 200
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

# 3. Input validation & sanitization
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff

# 4. Rate limiting
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 856
```

#### gRPC Security

```go
// Server-side TLS configuration
creds, err := credentials.NewServerTLSFromFile("server.crt", "server.key")
if err != nil {
    log.Fatalf("Failed to load TLS credentials: %v", err)
}

server := grpc.NewServer(grpc.Creds(creds))

// Client-side with mutual TLS authentication
cert, err := tls.LoadX509KeyPair("client.crt", "client.key")
creds := credentials.NewTLS(&tls.Config{
    Certificates: []tls.Certificate{cert},
})

conn, err := grpc.Dial(address, grpc.WithTransportCredentials(creds))
```

### Cost Analysis Framework

#### Total Cost of Ownership (TCO) Model

```javascript
// Cost calculation model
function calculateTCO(protocol, format, projectDuration) {
    const costs = {
        development: {
            soap_xml: { initial: 40, ongoing: 15 },      // Higher complexity
            rest_json: { initial: 20, ongoing: 8 },       // Developer-friendly
            grpc_protobuf: { initial: 30, ongoing: 10 }   // Learning curve
        },
        infrastructure: {
            soap_xml: { monthly: 200 },      // Higher CPU/memory usage
            rest_json: { monthly: 120 },     // Good caching, lower usage
            grpc_protobuf: { monthly: 100 }  // Most efficient
        },
        maintenance: {
            soap_xml: { yearly: 25 },        // Complex debugging
            rest_json: { yearly: 12 },       // Simple debugging
            grpc_protobuf: { yearly: 18 }    // Tool limitations
        }
    };
    
    const combo = `${protocol}_${format}`;
    const devCosts = costs.development[combo];
    const infraCosts = costs.infrastructure[combo];
    const maintCosts = costs.maintenance[combo];
    
    const totalCost = 
        devCosts.initial + 
        (devCosts.ongoing * projectDuration) +
        (infraCosts.monthly * 12 * projectDuration) +
        (maintCosts.yearly * projectDuration);
        
    return {
        development: devCosts.initial + (devCosts.ongoing * projectDuration),
        infrastructure: infraCosts.monthly * 12 * projectDuration,
        maintenance: maintCosts.yearly * projectDuration,
        total: totalCost
    };
}

// Example comparison
const scenarios = [
    { protocol: 'soap', format: 'xml' },
    { protocol: 'rest', format: 'json' },
    { protocol: 'grpc', format: 'protobuf' }
];

scenarios.forEach(scenario => {
    const costs = calculateTCO(scenario.protocol, scenario.format, 3); // 3-year project
    console.log(`${scenario.protocol}/${scenario.format}:`, costs);
});
```

### Decision Matrix Tool

Voor structured decision-making kunnen organisaties gebruikmaken van weighted scoring:

```javascript
// Decision matrix voor protocol/format keuze
class ProtocolDecisionMatrix {
    constructor(weights = {}) {
        this.weights = {
            performance: 0.2,
            security: 0.25,
            maintainability: 0.15,
            legacy_compatibility: 0.15,
            developer_experience: 0.1,
            cost: 0.15,
            ...weights
        };
    }
    
    evaluate(alternatives) {
        const scored = alternatives.map(alt => {
            const score = Object.keys(this.weights).reduce((total, criterion) => {
                return total + (alt.scores[criterion] * this.weights[criterion]);
            }, 0);
            
            return { ...alt, totalScore: score };
        });
        
        return scored.sort((a, b) => b.totalScore - a.totalScore);
    }
}

// Usage example
const matrix = new ProtocolDecisionMatrix({
    security: 0.3,          // High weight for government context
    legacy_compatibility: 0.25,
    performance: 0.2,
    cost: 0.15,
    maintainability: 0.1
});

const alternatives = [
    {
        name: 'SOAP + XML',
        scores: {
            performance: 6,
            security: 9,
            maintainability: 5,
            legacy_compatibility: 10,
            developer_experience: 4,
            cost: 5
        }
    },
    {
        name: 'REST + JSON',
        scores: {
            performance: 8,
            security: 7,
            maintainability: 9,
            legacy_compatibility: 6,
            developer_experience: 9,
            cost: 8
        }
    }
];

const results = matrix.evaluate(alternatives);
console.log('Recommended solution:', results[0].name);
```

### Future-Proofing Strategies

#### Technology Roadmap Alignment

```mermaid
gantt
    title Protocol Evolution Timeline
    dateFormat  YYYY-MM-DD
    section Legacy
    SOAP/XML Maintenance    :done, legacy1, 2024-01-01, 2026-12-31
    section Current
    REST/JSON APIs         :active, current1, 2024-01-01, 2028-12-31
    GraphQL Implementation :graphql1, 2025-01-01, 2027-12-31
    section Future
    gRPC Migration         :grpc1, 2026-01-01, 2029-12-31
    Event Streaming        :stream1, 2027-01-01, 2030-12-31
```

#### API Versioning Strategy

```http
# URL-based versioning (voor major changes)
GET /api/v1/personen/123456789
GET /api/v2/personen/123456789

# Header-based versioning (voor minor changes)  
GET /api/personen/123456789
API-Version: 2024-03-01

# Content-negotiation versioning
GET /api/personen/123456789
Accept: application/vnd.gemeente.persoon.v2+json
```

Het kiezen van formaten en protocollen vereist een holistische benadering waarbij technische, business en organisatorische factoren afgewogen worden. Voor overheidsorganisaties is het belangrijk om keuzes te maken die zowel huidige behoeften dekken als toekomstige flexibiliteit behouden, met bijzondere aandacht voor security, compliance en legacy-compatibility.

**Resources:**
- [API Design Guidelines Nederland](https://docs.geostandaarden.nl/api/API-Strategie/)
- [NORA Architectuurprincipes](https://www.noraonline.nl/)
- [Digikoppeling Architectuur](https://www.logius.nl/diensten/digikoppeling/documentatie)
- [Technology Radar for Government](https://github.com/ThoughtWorks/build-your-own-radar)