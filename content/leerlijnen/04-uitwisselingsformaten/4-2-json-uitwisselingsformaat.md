---
title: "4.2 JSON als uitwisselingsformaat"
date: 2026-03-04
weight: 3
leerlijn: 4
paragraaf: "4.2"
leerdoel: "Leerdoel nog toe te voegen"
---

## 4.2 JSON als uitwisselingsformaat

Kent JSON als modern uitwisselingsformaat (syntax, schema's, voordelen ten opzichte van XML).

### Wat is JSON?

**JSON** (JavaScript Object Notation) is een lichtgewicht, text-based data-uitwisselingsformaat. Hoewel oorspronkelijk ontworpen voor JavaScript, is JSON nu de de-facto standaard geworden voor moderne web-APIs en wordt steeds vaker ingezet in overheidscontext als alternatief voor XML.

#### Kerneigenschappen:
- **Compact**: Minder overhead dan XML
- **Human-readable**: Eenvoudig te lezen en schrijven
- **Native JavaScript**: Perfect voor web-applicaties  
- **Language-agnostic**: Support in alle moderne programmeertalen

### JSON-syntax en structuur

#### Basis JSON-datatypes

```json
{
  "string_waarde": "tekst",
  "number_waarde": 42,
  "decimal_waarde": 3.14159,
  "boolean_waarde": true,
  "null_waarde": null,
  "array_waarde": [1, 2, 3, "vier"],
  "object_waarde": {
    "nested_key": "nested_value"
  }
}
```

#### Persoon-voorbeeld (vergelijkbaar met XML)

```json
{
  "persoon": {
    "bsn": "123456789",
    "geslachtsnaam": {
      "voorvoegsel": "van der",
      "geslachtsnaamStam": "Berg"
    },
    "voornamen": "Jan Peter",
    "geboortedatum": "1985-03-15",
    "adres": {
      "straatnaam": "Hoofdstraat",
      "huisnummer": 42,
      "huisnummertoevoeging": "A",
      "postcode": "1234AB",
      "woonplaatsnaam": "Amsterdam"
    },
    "contactgegevens": [
      {
        "soort": "email",
        "waarde": "jan.vandenberg@example.com"
      },
      {
        "soort": "telefoon",
        "waarde": "06-12345678"
      }
    ]
  }
}
```

#### JSON vs XML-vergelijking

**XML-versie:**
```xml
<persoon>
    <bsn>123456789</bsn>
    <voornaam>Jan</voornaam>
    <achternaam>Berg</achternaam>
</persoon>
```

**JSON-versie:**
```json
{
  "persoon": {
    "bsn": "123456789",
    "voornaam": "Jan", 
    "achternaam": "Berg"
  }
}
```

**Difference**: 47% minder characters in JSON!

### JSON Schema's

JSON Schema definieert de structuur en validatie-regels voor JSON-documenten, vergelijkbaar met XSD voor XML.

#### Basis JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://overheid.nl/schemas/persoon.json",
  "title": "Persoon",
  "description": "Schema voor persoonsgegevens in overheidscontext",
  "type": "object",
  "required": ["bsn", "voornaam", "achternaam"],
  
  "properties": {
    "bsn": {
      "type": "string",
      "pattern": "^[0-9]{9}$",
      "description": "Burgerservicenummer (9 cijfers)"
    },
    "voornaam": {
      "type": "string",
      "minLength": 1,
      "maxLength": 100
    },
    "achternaam": {
      "type": "string", 
      "minLength": 1,
      "maxLength": 200
    },
    "geboortedatum": {
      "type": "string",
      "format": "date",
      "description": "Geboortedatum in YYYY-MM-DD formaat"
    },
    "geslacht": {
      "type": "string",
      "enum": ["M", "V", "O"],
      "description": "Geslacht: M=Man, V=Vrouw, O=Onbekend"
    }
  },
  
  "additionalProperties": false
}
```

#### Geavanceerde Schema-features

**Conditionale validatie:**
```json
{
  "type": "object",
  "properties": {
    "leeftijd": {"type": "integer"},
    "toestemming_nodig": {"type": "boolean"}
  },
  "if": {
    "properties": {"leeftijd": {"minimum": 18}}
  },
  "then": {
    "properties": {"toestemming_nodig": {"const": false}}
  },
  "else": {
    "properties": {"toestemming_nodig": {"const": true}}
  }
}
```

**Array-validatie:**
```json
{
  "type": "object",
  "properties": {
    "contactgegevens": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["soort", "waarde"],
        "properties": {
          "soort": {
            "enum": ["email", "telefoon", "mobiel"]
          },
          "waarde": {
            "type": "string",
            "minLength": 1
          }
        }
      },
      "minItems": 1,
      "maxItems": 10
    }
  }
}
```

### JSON in API-context

#### RESTful API-voorbeeld

**Request (POST /api/personen):**
```json
{
  "persoon": {
    "bsn": "987654321",
    "voornaam": "Maria", 
    "achternaam": "Jansen",
    "geboortedatum": "1990-12-25",
    "adres": {
      "straat": "Kerkstraat 15",
      "postcode": "5678CD",
      "plaats": "Utrecht"
    }
  }
}
```

**Response (Success - HTTP 201):**
```json
{
  "status": "success",
  "data": {
    "id": "pers_abc123",
    "persoon": {
      "bsn": "987654321",
      "voornaam": "Maria",
      "achternaam": "Jansen", 
      "geboortedatum": "1990-12-25",
      "created_at": "2024-03-05T14:30:00Z",
      "updated_at": "2024-03-05T14:30:00Z"
    }
  },
  "links": {
    "self": "/api/personen/pers_abc123",
    "adres": "/api/personen/pers_abc123/adres"
  }
}
```

**Response (Error - HTTP 400):**
```json
{
  "status": "error",
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "De verstrekte gegevens zijn niet geldig",
    "details": [
      {
        "field": "bsn",
        "error": "BSN moet uit exacte 9 cijfers bestaan"
      },
      {
        "field": "geboortedatum", 
        "error": "Ongeldige datumnotatie. Gebruik YYYY-MM-DD"
      }
    ]
  },
  "timestamp": "2024-03-05T14:30:00Z",
  "request_id": "req_xyz789"
}
```

### JSON-processing tools en libraries

#### Command-line tools
```bash
# jq - JSON query en transformation tool
cat persoon.json | jq '.persoon.voornaam'
# Output: "Jan"

# Pretty-print JSON
cat compact.json | jq '.'

# Filter arrays
cat personen.json | jq '.personen[] | select(.leeftijd > 18)'

# Transform data
cat input.json | jq '{naam: .voornaam + " " + .achternaam, bsn}'
```

#### Programming libraries

**JavaScript/Node.js:**
```javascript
// Native parsing
const data = JSON.parse(jsonString);
const jsonString = JSON.stringify(object, null, 2);

// Validation met ajv
const Ajv = require('ajv');
const ajv = new Ajv();
const validate = ajv.compile(schema);
const valid = validate(data);
if (!valid) console.log(validate.errors);
```

**Python:**
```python
import json
import jsonschema

# Parse/stringify
data = json.loads(json_string)
json_string = json.dumps(data, indent=2, ensure_ascii=False)

# Validation
schema = {...}  # JSON Schema object
jsonschema.validate(instance=data, schema=schema)
```

**Java:**
```java
// Jackson library
ObjectMapper mapper = new ObjectMapper();
Persoon persoon = mapper.readValue(json, Persoon.class);
String json = mapper.writeValueAsString(persoon);

// JSON Schema validation
JsonSchema schema = JsonSchemaFactory.getInstance().getSchema(schemaNode);
Set<ValidationMessage> errors = schema.validate(jsonNode);
```

**C#:**
```csharp
// Newtonsoft.Json
var persoon = JsonConvert.DeserializeObject<Persoon>(json);
var json = JsonConvert.SerializeObject(persoon, Formatting.Indented);

// JSON.NET Schema validation
JSchema schema = JSchema.Parse(schemaJson);
bool valid = data.IsValid(schema, out IList<string> errors);
```

### JSON Best practices voor overheid

#### API-design patterns

**HAL (Hypertext Application Language):**
```json
{
  "_links": {
    "self": {"href": "/api/personen/123"},
    "adres": {"href": "/api/personen/123/adres"},
    "documenten": {
      "href": "/api/personen/123/documenten",
      "templated": true
    }
  },
  "bsn": "123456789",
  "voornaam": "Jan",
  "achternaam": "Berg",
  "_embedded": {
    "adres": {
      "_links": {"self": {"href": "/api/adressen/456"}},
      "straat": "Hoofdstraat 1",
      "postcode": "1234AB"
    }
  }
}
```

**JSON:API specification:**
```json
{
  "data": {
    "type": "personen",
    "id": "123", 
    "attributes": {
      "voornaam": "Jan",
      "achternaam": "Berg"
    },
    "relationships": {
      "adres": {
        "links": {
          "related": "/api/personen/123/adres"
        },
        "data": {"type": "adressen", "id": "456"}
      }
    }
  },
  "included": [
    {
      "type": "adressen",
      "id": "456",
      "attributes": {
        "straat": "Hoofdstraat 1",
        "postcode": "1234AB"
      }
    }
  ]
}
```

#### Security-overwegingen

**Input sanitization:**
```json
// NOOIT direct user-input in JSON opnemen zonder validation
{
  "user_input": "<script>alert('xss')</script>",  // DANGER!
  "safe_input": "&lt;script&gt;alert('xss')&lt;/script&gt;"  // Escaped
}
```

**JSON injection prevention:**
```javascript
// FOUT: String concatenation
const unsafeJson = '{"name": "' + userInput + '"}';

// CORRECT: Object construction + JSON.stringify
const safeObject = {name: userInput};
const safeJson = JSON.stringify(safeObject);
```

#### Performance-optimalisatie

**Streaming voor grote datasets:**
```javascript
// JSONStream voor Node.js
const JSONStream = require('JSONStream');
fs.createReadStream('large-dataset.json')
  .pipe(JSONStream.parse('data.*'))
  .on('data', (record) => {
    // Process individual records
  });
```

**Paginering patterns:**
```json
{
  "data": [...],
  "pagination": {
    "current_page": 1,
    "total_pages": 50,
    "total_items": 1250,
    "items_per_page": 25,
    "links": {
      "first": "/api/personen?page=1",
      "prev": null,
      "next": "/api/personen?page=2", 
      "last": "/api/personen?page=50"
    }
  }
}
```

### JSON vs XML in overheidscontext

#### Wanneer JSON kiezen:

**✅ Voordelen:**
- **Web-APIs**: Native support in browsers/JavaScript
- **Mobile apps**: Minder data-usage, sneller parsing
- **Microservices**: Lightweight communication tussen services
- **Documentation**: Eenvoudiger te begrijpen voor developers
- **Performance**: 15-30% kleiner, 2-3x sneller parsing

**✅ Use cases:**
- Nieuwe API's en webservices
- Real-time data-uitwisseling
- Integration met moderne front-end frameworks
- IoT en mobile-first applicaties

#### Wanneer XML behouden:

**⚠️ XML blijft relevant voor:**
- **Legacy-systems**: Bestaande StUF-implementaties
- **Compliance**: Wettelijke eisen die XML specificeren
- **Complex metadata**: Rich attribute-modellen nodig
- **XSLT transformations**: Bestaande processing pipelines
- **Industry standards**: Sectoren met XML-vereisten

#### Hybride-scenario's

**XML-to-JSON transformation:**
```javascript
// Express middleware voor backwards compatibility
app.use('/api/legacy', (req, res, next) => {
  if (req.headers['content-type'] === 'application/xml') {
    // Convert incoming XML to JSON
    const jsonData = xml2json(req.body);
    req.body = jsonData;
  }
  next();
});
```

**Content-negotiation:**
```bash
# Client vraagt JSON
curl -H "Accept: application/json" /api/personen/123

# Client vraagt XML  
curl -H "Accept: application/xml" /api/personen/123
```

### Toekomst van JSON in overheid

#### Emerging patterns

**GraphQL:**
```json
// Query
{
  persoon(bsn: "123456789") {
    voornaam
    achternaam  
    adres {
      postcode
      plaats
    }
  }
}

// Response 
{
  "data": {
    "persoon": {
      "voornaam": "Jan",
      "achternaam": "Berg",
      "adres": {
        "postcode": "1234AB", 
        "plaats": "Amsterdam"
      }
    }
  }
}
```

**JSON-LD (Linked Data):**
```json
{
  "@context": "https://overheid.nl/context/persoon",
  "@type": "Persoon",
  "bsn": "123456789",
  "naam": "Jan Berg",
  "woontIn": {
    "@type": "Adres",
    "postcode": "1234AB"
  }
}
```

### Migratie van XML naar JSON

#### Geleidelijke overgang
1. **Dual support**: API's die zowel XML als JSON accepteren
2. **Schema mapping**: XSD naar JSON Schema conversie
3. **Testing**: Parallel data-validation tijdens transitie
4. **Documentation**: Clear migration-guides voor consumers
5. **Deprecation**: Planned obsolescence van XML-endpoints

Het begrijpen van JSON is essentieel geworden voor moderne overheidsapplicaties. Terwijl XML zijn plaats behoudt in legacy-systemen, biedt JSON significante voordelen voor nieuwe ontwikkeling, vooral in web-context en API-ecosystemen.

**Resources:**
- [JSON Specification (RFC 7159)](https://tools.ietf.org/html/rfc7159)
- [JSON Schema](https://json-schema.org/)
- [Government APIs Best Practices](https://www.api.gov.nl/)
- [API Design Guide Nederland](https://docs.geostandaarden.nl/api/API-Strategie/)