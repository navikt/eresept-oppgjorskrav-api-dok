# Oppgjørsutbetaling


Endepunkt for å hente oppgjorsutbetaling for et oppgjørskrav.

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
  "oppgjor": {
    "oppgjorsNr": "xbb7babfb-c7f0-41d6-9848-a7ed02284a41",
    "godkjent": {
      "v": 127.20,
      "u": "NOK"
    }
  },
  "utbetalingsnr": "510000000020998",
  "utbetalt": {
    "v": 127.20,
    "u": "NOK"
  },
  "utbetaltDato": "2026-05-07"
}
```