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

| Felt               | Type           | Beskrivelse                                                                                |
|--------------------|----------------|--------------------------------------------------------------------------------------------|
|                    | Array          | Liste over oppgjørskrav                                                                    |
| └─ oppgjorsNr      | String         | Oppgjørsnummer                                                                             |
| └─ status          | CS             | Status på oppgjørskravet. Verdier fra kodeverk TBD                                         |                                        | 
| └─ mottatt         | DateTime       | Tidspunkt for når oppgjørskravet ble mottatt                                               |
| └─ merknad         | Collection<CS> | Merknader som er relevante ved avvist eller manuell status                                 |
| └─ antallRegninger | Number         | Antall regninger i kravet                                                                  |
| └─ sumKrav         | MO             | Sum krav i NOK                                                                             |
| └─ sumGodkjent     | MO             | Sum godkjent i NOK                                                                         |
| └─ utbetalingsnr   | String         | Unik identifikasjon av den enkelte utbetaling, er utfylt om utbetaling er foretatt         |


| Status | Response | Forklaring                             |
|--------|----------|----------------------------------------|
| 200    | JSON     |                                        |
| 451    |          | Direkteoppgjørsavtale er ikke på plass |

Eksempel på request-url:

```
/eresept/oppgjorskrav/status?oppgjorsnr=b244ff4f-b701-4d16-81d8-53a052daff6b,c7887780-0410-40ec-b221-bcdc99cf27ca,xyz
```
Eksempel på response:

```json
[
  {
    "oppgjorsNr": "b244ff4f-b701-4d16-81d8-53a052daff6b",
    "status": {
      "v": "2",
      "dn": "Godkjent"
    },
    "mottatt": "2026-02-02T10:09:57.260+01:00",
    "antallRegninger": 2,
    "krav": {
      "v": 759.10,
      "u": "NOK"
    },
    "godkjent": {
      "v": 759.10,
      "u": "NOK"
    }
  },
  {
    "oppgjorsnr": "c7887780-0410-40ec-b221-bcdc99cf27ca",
    "orgnr": "998858968",
    "status": {
      "v": "5",
      "dn": "Avvist"
    },
    "mottatt": "2026-03-02T11:12:45.257+01:00",
    "merknad": [
      { 
        "v":  "100", 
        "dn": "bare et eksempel på en merknad" }
    ],
    "antallRegninger": 163,
    "krav": {
      "v": 78901.32,
      "u": "NOK"
    },
    "godkjent": {
      "v": 0.00,
      "u": "NOK"
    }
  },
  {
    "oppgjorsnr": "xyz",
    "status": {
      "v": "6",
      "dn": "Ukjent"
    },
    "merknad": [],
    "antallRegninger": 0,
    "krav": {
      "v": 0.00,
      "u": "NOK"
    },
    "godkjent": {
      "v": 0.00,
      "u": "NOK"
    }
  }
]
```
