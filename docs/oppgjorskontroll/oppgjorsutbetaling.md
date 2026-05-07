# Oppgjørsutbetaling


Endepunkt for å hente oppgjorsutbetaling for et oppgjørskrav.

| Felt                     | Verdi                            |
|--------------------------|----------------------------------|
| path                     | /oppgjorskrav/oppgjorsutbetaling |
| query string parameter   | `oppgjorsnr`: et oppgjorsnr      |
| method                   | GET                              |
| content type             | application/json                 |

## Response

| Felt             | Type           | Beskrivelse                                                                                |
|------------------|----------------|--------------------------------------------------------------------------------------------|
| └─ oppgjorsnr    | String         | Oppgjørsnummer                                                                             |
| └─ orgnr         | String         | Organisasjonsnummer som oppgjøret er sendt inn på, som angitt i claim i authrization token |
| └─ utbetalingsnr | String         | Unik identifikasjon av den enkelte utbetaling                                              |                                        | 
| └─ utbetalt      | Number         | Sum utbetalt                                                                               |
| └─ utbetaltDato  | Date           | Dato for utbetaling av oppgjøret                                                           |

| Status | Response                  | Forklaring                                                       |
|--------|---------------------------|------------------------------------------------------------------|
| 200    | Oppgjorsutbetaling (JSON) | Oppgjorsutbetaling er tilgjengelig og returneres                 |
| 204    |                           | Oppgjorsutbetaling er ikke produsert og ingen respons returneres |
| 400    |                           | Oppgjøret finnes ikke                                            |
| 451    |                           | Direkteoppgjørsavtale er ikke på plass                           |

Eksempel på response:

```json
{
  "oppgjorsnr": "xbb7babfb-c7f0-41d6-9848-a7ed02284a41",
  "orgnr": "888888888",
  "utbetalingsnr": "510000000020998",
  "utbetalt": 127.20,
  "utbetaltDato": "2026-05-07"
}
```