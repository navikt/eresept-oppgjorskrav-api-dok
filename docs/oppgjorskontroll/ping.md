# Ping

Endepunkt som kan brukes til å kontrollere om oppsettet er riktig og at utleverer er 
korrekt registrert med direkteoppgjørsavtale.



| Felt                    | Verdi              |
|-------------------------|--------------------|
| path                    | /oppgjorskrav/ping |
| method                  | GET                |
| content type            | text/plain         |


| Status | Response | Forklaring                             |
|--------|----------|----------------------------------------|
| 200    | pong     | Avtale ok                              |
| 451    |          | Direkteoppgjørsavtale er ikke på plass |
