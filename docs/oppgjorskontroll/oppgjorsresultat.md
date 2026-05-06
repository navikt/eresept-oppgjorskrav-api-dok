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
| └─ oppgjorsnr      | String              | Oppgjørsnummer                                                                             |
| └─ orgnr           | String              | Organisasjonsnummer som oppgjøret er sendt inn på, som angitt i claim i authrization token |
| └─ behandlingsDato | DateTime            | Dato for behandling av oppgjøret                                                           |                                        | 
| └─ regninger       | Collection<Regning> | Liste med regningene i kravet                                                              |
| └─── regningsnr    | String              | Regningsnummer                                                                             |
| └─── krav          | Number              | Sum krav i NOK                                                                             |
| └─── godkjent      | Number              | Sum godkjent i NOK                                                                         |
| └─── status        | CS                  | Merknader som er relevante ved avvist eller manuell status, fra kodeverk 7410              |
| └─── merknader     | Collection<CS>      | Merknader som er relevante ved avvist eller manuell status                                 |

| Status | Response                | Forklaring                                                     |
|--------|-------------------------|----------------------------------------------------------------|
| 200    | Oppgjorsresultat (JSON) | Oppgjorsresultat er tilgjengelig og returneres                 |
| 204    |                         | Oppgjorsresultat er ikke produsert og ingen respons returneres |
| 400    |                         | Oppgjøret finnes ikke                                          |
| 451    |                         | Direkteoppgjørsavtale er ikke på plass                         |

Eksempel på response:

```json
{
  "oppgjorsnr": "ee3fea12-7346-46c4-8d4b-8ed343da36a6",
  "orgnr": "976924185",
  "behandlingDato": "2026-05-06T16:38:23.457823748+02:00",
  "regninger": [
    {
      "regningsnr": "8229d7d0-0123-4e66-809c-2b22db687954",
      "krav": 70.00,
      "godkjent": 0.00,
      "status": {
        "v": "1",
        "dn": "Avvist"
      },
      "merknader": [
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
      "regningsnr": "6c5c9f34-6c70-4f46-b06b-9f11916da408",
      "krav": 70.00,
      "godkjent": 70.00,
      "status": {
        "v": "2",
        "dn": "Godkjent"
      },
      "merknader": []
    }
  ]
}
```