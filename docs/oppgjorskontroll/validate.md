# Validate

Endepunkt for validering av JSON oppgjørskrav.

| Felt                    | Verdi                  |
|-------------------------|------------------------|
| path                    | /oppgjorskrav/validate |
| method                  | POST                   |
| content type            | text/plain             |


| Status | Response | Forklaring                             |
|--------|----------|----------------------------------------|
| 204    |          | Oppgjørskrav er validert ok iht skjema |
| 400    |          | Oppgjørskravet validerer ikke          |
| 451    |          | Direkteoppgjørsavtale er ikke på plass |

## Versjonering

Se [Versjonering](eresept_ok_oppgjorskrav.md#Versjonering).