---
title: "1.1 Overzicht van gemeentelijke applicatielandschappen"
date: 2026-03-04
weight: 1
leerlijn: 1
paragraaf: "1.1"
---

<div class="back-link-wrapper">
  <a href="{{< relref "../01-gemeentelijk-ict-landschap" >}}" class="back-link">Terug naar leerlijn 1</a>
</div>

## 1.1 Overzicht van gemeentelijke applicatielandschappen

Begrijpt de rol van StUF in het gemeentelijke applicatielandschap.

Een gemiddelde gemeente gebruikt tientallen tot meer dan honderd applicaties die samen het **applicatielandschap** vormen. Deze applicaties ondersteunen alle gemeentelijke taken — van burgerzaken tot belastingheffing, van vergunningverlening tot het sociaal domein. Hieronder een overzicht van de belangrijkste categorieën en systemen.

### Kernsystemen en basisregistraties

| Systeem / Registratie | Afkorting | Functie |
|---|---|---|
| **Basisregistratie Personen** | BRP | Persoonsgegevens van alle inwoners (voorheen GBA) |
| **Basisregistratie Adressen en Gebouwen** | BAG | Alle adressen en gebouwen in Nederland |
| **Basisregistratie Grootschalige Topografie** | BGT | Gedetailleerde kaart van alle objecten in de openbare ruimte |
| **Basisregistratie Kadaster** | BRK | Eigendomsgegevens van percelen en appartementsrechten |
| **Basisregistratie Waarde Onroerende Zaken** | WOZ | WOZ-waarden van onroerend goed |
| **Handelsregister** | HR (NHR) | Gegevens van alle ondernemingen en rechtspersonen |

### Zaakgericht werken

| Systeem | Functie |
|---|---|
| **Zaaksysteem** (bijv. Rx.Mission, Decos JOIN) | Registratie en afhandeling van alle "zaken" (aanvragen, meldingen, vergunningen) |
| **Documentmanagementsysteem** (DMS) | Opslag en beheer van documenten die bij zaken horen |
| **Archiefbeheer** | Duurzame bewaring en vernietiging volgens de Archiefwet |

### Domeinspecifieke applicaties

| Domein | Typische applicaties | Voorbeelden |
|---|---|---|
| **Burgerzaken** | Burgerzakenmodule | Centric/PinkRoccade burgerzaken |
| **Belastingen** | Belastingsysteem | Centric Belastingen, Hefpunt |
| **Sociaal domein** | Regiesysteem, Wmo/Jeugd | GWS4all, Suite voor Sociaal Domein |
| **Vergunningen (Omgevingswet)** | VTH-systeem (Vergunning, Toezicht, Handhaving) | PowerBrowser, Squit XO, Rx.Mission |
| **Openbare ruimte** | Beheersysteem openbare ruimte | Obsurv, GBI |
| **Financiën** | Financieel systeem | Key2Financiën, Unit4 |

### Integratie- en servicelagen

Omdat al deze systemen met elkaar moeten communiceren, is er een **integratielaag** nodig. Hier speelt StUF een centrale rol:

```
┌──────────────────────────────────────────────────────────┐
│                    Inwoners / Bedrijven                   │
│              (website, portaal, e-formulier)              │
└──────────────┬───────────────────────────┬───────────────┘
               │                           │
               ▼                           ▼
┌──────────────────────┐   ┌──────────────────────────────┐
│   Zaaksysteem        │   │   Vergunningensysteem        │
│                      │   │   (VTH-applicatie)           │
└──────────┬───────────┘   └──────────────┬───────────────┘
           │         StUF-berichten        │
           ▼                               ▼
┌──────────────────────────────────────────────────────────┐
│              MSB / ESB / Integratieplatform              │
│        (MSB = MSb, bijv. MSB Gemeente, MuleSoft)        │
│        Routeert berichten tussen systemen                │
└──┬──────────┬──────────┬──────────┬──────────┬──────────┘
   │          │          │          │          │
   ▼          ▼          ▼          ▼          ▼
┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────────────┐
│ BRP  │ │ BAG  │ │ WOZ  │ │ DMS  │ │ Belastingen  │
└──────┘ └──────┘ └──────┘ └──────┘ └──────────────┘
```

| Component | Rol | Relatie met StUF |
|---|---|---|
| **MSB** (MSb/MSB) | MSb (MSB = MSb) routeert StUF-berichten tussen applicaties | Centrale hub voor berichtenverkeer |
| **ESB** (Enterprise Service Bus) | Breder integratieplatform (MSB is gemeentelijke variant) | Kan StUF-berichten transformeren en routeren |
| **Gegevensmakelaar** | Ontsluit basisregistraties voor afnemers | Vertaalt bevragingen naar StUF-vraag/antwoord-berichten |

### Typisch berichtenverkeer

Een concreet voorbeeld van hoe StUF door het applicatielandschap stroomt:

1. **Inwoner verhuist** en meldt dit bij de balie of via een e-formulier
2. De medewerker verwerkt de verhuizing in het **BRP-systeem**
3. Het BRP-systeem stuurt een **StUF-BG kennisgevingsbericht** (`Lk01`) met het nieuwe adres
4. Het **MSB** ontvangt het bericht en routeert het naar alle geabonneerde systemen:
   - Het **belastingsysteem** past de aanslag aan
   - Het **zaaksysteem** koppelt lopende zaken aan het nieuwe adres
   - Het **sociaal domein-systeem** werkt de cliëntgegevens bij
5. Elk ontvangend systeem stuurt een **bevestiging** (`Bv03`) terug

Dit "publish-subscribe"-patroon — waarbij één mutatie automatisch naar alle belanghebbende systemen wordt doorgestuurd — is de kern van StUF-gebaseerde integratie.

### Landschap in beweging: van StUF naar API's

Het gemeentelijke applicatielandschap is in transitie:

| Aspect | Traditioneel (StUF) | Modern (API-strategie) |
|---|---|---|
| **Protocol** | SOAP/XML | REST/JSON |
| **Standaard** | StUF-BG, StUF-ZKN | Haal Centraal, ZGW API's |
| **Berichtenverkeer** | MSB/ESB | API Gateway |
| **Koppeling** | Punt-naar-punt via MSB | API-first, losse koppeling |

> **Belangrijk:** StUF is nog jarenlang in gebruik. Veel gemeenten draaien een **hybride landschap** waarin zowel StUF-koppelingen als nieuwe API's naast elkaar bestaan. Kennis van StUF blijft daarom essentieel — ook voor het begrijpen en migreren van bestaande koppelingen.
