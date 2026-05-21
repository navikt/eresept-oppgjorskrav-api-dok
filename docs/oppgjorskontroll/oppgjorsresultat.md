# Oppgjørsresultat


Endepunkt for å hente oppgjorsresultat for et oppgjørskrav.

| Felt                     | Verdi                          |
|--------------------------|--------------------------------|
| path                     | /oppgjorskrav/oppgjorsresultat |
| query string parameter   | `oppgjorsnr`: et oppgjorsnr    |
| method                   | GET                            |
| content type             | application/json               |

## Response

| Felt               | Type                | Beskrivelse                                                                                |
|--------------------|---------------------|--------------------------------------------------------------------------------------------|
| └─ oppgjorsNr      | String              | Oppgjørsnummer                                                                             |
| └─ behandlingsDato | DateTime            | Dato for behandling av oppgjøret                                                           |                                        | 
| └─ regning         | Collection<Regning> | Liste med regningene i kravet                                                              |
| └─── regningsNr    | String              | Regningsnummer                                                                             |
| └─── krav          | MO                  | Sum krav i NOK                                                                             |
| └─── godkjent      | MO                  | Sum godkjent i NOK                                                                         |
| └─── status        | CS                  | Merknader som er relevante ved avvist eller manuell status, fra kodeverk 7410              |
| └─── merknad       | Collection<CS>      | Merknader som er relevante ved avvist eller manuell status                                 |

| Status | Response                | Forklaring                                                     |
|--------|-------------------------|----------------------------------------------------------------|
| 200    | Oppgjorsresultat (JSON) | Oppgjorsresultat er tilgjengelig og returneres                 |
| 204    |                         | Oppgjorsresultat er ikke produsert og ingen respons returneres |
| 400    |                         | Oppgjøret finnes ikke                                          |
| 451    |                         | Direkteoppgjørsavtale er ikke på plass                         |

Eksempel på response:

```json
{
  "oppgjorsNr": "ee3fea12-7346-46c4-8d4b-8ed343da36a6",
  "behandlingDato": "2026-05-06T16:38:23.457823748+02:00",
  "regning": [
    {
      "regningsNr": "8229d7d0-0123-4e66-809c-2b22db687954",
      "krav": { 
        "v": 70.00, 
        "u": "NOK"
      },
      "godkjent": { 
        "v": 0.00, 
        "u": "NOK"
      },
      "status": {
        "v": "1",
        "dn": "Avvist"
      },
      "merknad": [
        {
          "v": "114",
          "dn": "Resepten er foreldet"
        },
        {
          "v": "15",
          "dn": "ATC-kode må være fylt ut"
        },
        {
          "v": "311",
          "dn": "Utleveringsdatoen er mer enn tre måneder etter reseptens utløpsdato"
        }
      ]
    },
    {
      "regningsNr": "6c5c9f34-6c70-4f46-b06b-9f11916da408",
      "krav": {
        "v": 70.00,
        "u": "NOK"
      },
      "godkjent": {
        "v": 70.00,
        "u": "NOK"
      },
      "status": {
        "v": "2",
        "dn": "Godkjent"
      },
      "merknad": []
    }
  ]
}
```