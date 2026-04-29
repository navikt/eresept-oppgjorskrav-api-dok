# Status på oppgjørskrav

Endepunkt på status på et eller flere oppgjørskrav.
Svaret viser status på alle oppgjørskrav med angitt oppgjørsnummer.
Normalt vil det bare være ett oppgjørskrav pr oppgjørsnummer, men dersom noen oppgjørskrav er avvist 
vil også disse være inkludert i responsen.

| Felt                   | Verdi                                                   |
|------------------------|---------------------------------------------------------|
| path                   | /oppgjorskrav/status                                    |
| query string parameter | `oppgjorsnr`: et eller flere oppgjorsnr, komma-separert |
| method                 | GET                                                     |
| content type           | application/json                                        |


## Response

| Felt                | Type     | Beskrivelse                                                                                |
|---------------------|----------|--------------------------------------------------------------------------------------------|
|                     | Array    | Liste over oppgjørskrav                                                                    |
| └─ oppgjorsnr       | String   | Oppgjørsnummer                                                                             |
| └─ orgnr            | String   | Organisasjonsnummer som oppgjøret er sendt inn på, som angitt i claim i authrization token |
| └─ status           | CS       | Status på oppgjørskravet. Verider fra kodeverk TBD                                         |                                        | 
| └─ mottatt          | DateTime | Tidspunkt for når oppgjørskravet ble mottatt                                               |
| └─ antall regninger | Number   | Antall regninger i kravet                                                                  |
| └─ sumKrav          | Number   | Sum krav i NOK                                                                             |
| └─ sumGodkjent      | Number   | Sum godkjent i NOK                                                                         |


| Status | Response | Forklaring                             |
|--------|----------|----------------------------------------|
| 200    | JSON     |                                        |
| 451    |          | Direkteoppgjørsavtale er ikke på plass |

Eksempel på request-url:

```
/eresept/oppgjorskrav/status?oppgjorsnr=b244ff4f-b701-4d16-81d8-53a052daff6b,c7887780-0410-40ec-b221-bcdc99cf27ca
```
Eksempel på response:

```json
[
  {
    "oppgjorsnr": "b244ff4f-b701-4d16-81d8-53a052daff6b",
    "orgnr": "974589109",
    "status": {
      "v": "2",
      "dt": "Godkjent"
    },
    "mottatt": "2026-02-02T10:09:57.260+01:00",
    "antallRegninger": 2,
    "sumKrav": 759.10,
    "sumGodkjent": 759.10
  },
  {
    "oppgjorsnr": "c7887780-0410-40ec-b221-bcdc99cf27ca",
    "orgnr": "998858968",
    "status": {
      "v": "3",
      "dt": "Utbetalt"
    },
    "mottatt": "2026-03-02T11:12:45.257+01:00",
    "antallRegninger": 163,
    "sumKrav": 78901.32,
    "sumGodkjent": 67802.45
  }
]
```
