# Vedtaksbrev

Endepunkt for å hente vedtaksbrev for et oppgjørskrav.

| Felt                    | Verdi                       |
|-------------------------|-----------------------------|
| path                    | /oppgjorskrav/vedtaksbrev   |
| query string parameter  | `oppgjorsnr`: et oppgjorsnr |
| method                  | GET                         |
| content type            | application/pdf             |


| Status | Response          | Forklaring                                                |
|--------|-------------------|-----------------------------------------------------------|
| 200    | Vedtaksbrev (PDF) | Vedtaksbrev er tilgjengelig og returneres                 |
| 204    |                   | Vedtaksbrev er ikke produsert og ingen respons returneres |
| 400    |                   | Oppgjøret finnes ikke                                     |
| 451    |                   | Direkteoppgjørsavtale er ikke på plass                    |
