# JWK

Endepunkt for å hente nyeste eresept public JWK som skal brukes for å kryptere oppgjørskrav som skal sendes inn.


| Felt                    | Verdi            |
|-------------------------|------------------|
| path                    | oppgjorskrav/jwk |
| method                  | GET              |
| content type            | application/json |


| Status | Response   | Forklaring                                         |
|--------|------------|----------------------------------------------------|
| 200    | JWK (JSON) | _nyeste_ jwk returneres. Nøkkel roteres periodisk. |
| 451    |            | Direkteoppgjørsavtale er ikke på plass             |
