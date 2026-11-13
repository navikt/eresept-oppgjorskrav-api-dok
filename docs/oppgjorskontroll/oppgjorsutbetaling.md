# Oppgjørsutbetaling


Endepunkt for å hente oppgjorsutbetaling for et oppgjørskrav.

Flere oppgjør kan i teorien (muligens i en  nær fremtid) batches i en utbetaling. 
En oppgjørsutbetaling kan derfor inneholde referanser til flere oppgjørskrav.
Pt. er det kun ett oppgjørskrav i en oppgjørsutbetaling, og en endring av dette vil varsles.

Dersom behandling av oppgjøret resulterer i et null-oppgjør (kr 0 utbetalt) og oppgjøret ikke er avvist
vil oppgjørets status vises som Utbetalt når det er ferdig behandlet. Fordi det er et null-oppgjør vil det aldri
foretas en utbetaling, og et forsøk på å hente en OppgjorsUtbetaling vil resultere i http status 204.



| Felt                     | Verdi                            |
|--------------------------|----------------------------------|
| path                     | /oppgjorskrav/oppgjorsutbetaling |
| query string parameter   | `oppgjorsnr`: et oppgjorsnr      |
| method                   | GET                              |
| content type             | application/json                 |

## Response

| Felt             | Type    | Beskrivelse                                   |
|------------------|---------|-----------------------------------------------|
| └─ oppgjor       | Oppgjor | Oppgjørsnummer                                |
| └─── oppgjorsNr  | String  | Oppgjørsnummer                                |
| └─── godkjent    | MO      | Godkjent beløp                                |
| └─ utbetalingsnr | String  | Unik identifikasjon av den enkelte utbetaling |                                        | 
| └─ utbetalt      | MO      | Sum utbetalt                                  |
| └─ utbetaltDato  | Date    | Dato for utbetaling av oppgjøret              |

| Status | Response                  | Forklaring                                                       |
|--------|---------------------------|------------------------------------------------------------------|
| 200    | Oppgjorsutbetaling (JSON) | Oppgjorsutbetaling er tilgjengelig og returneres                 |
| 204    |                           | Oppgjorsutbetaling er ikke produsert og ingen respons returneres |
| 400    |                           | Oppgjøret finnes ikke                                            |
| 451    |                           | Direkteoppgjørsavtale er ikke på plass                           |

Eksempel på response:

```json
{
  "oppgjor": [
    {
      "oppgjorsNr": "xbb7babfb-c7f0-41d6-9848-a7ed02284a41",
      "godkjent": {
        "v": 127.20,
        "u": "NOK"
      }
    ]
  },
  "utbetalingsnr": "510000000020998",
  "utbetalt": {
    "v": 127.20,
    "u": "NOK"
  },
  "utbetaltDato": "2026-05-07"
}
```