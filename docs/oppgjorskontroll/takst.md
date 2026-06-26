# Takst

| Felt                    | Verdi                                                  |
|-------------------------|--------------------------------------------------------|
| path                    | takst/takster                                          |
| query string parameter | `includeInactive`: inkluder utgåtte takster i responsen |
| method                  | GET                                                    |
| content type            | application/json                                       |

| Status | Response | Forklaring                                                             |
|--------|----------|------------------------------------------------------------------------|
| 200    | Takster  | Takster                                                                |
| 451    |          | Direkteoppgjørsavtale er ikke på plass, eller utleverer er ikke apotek |
