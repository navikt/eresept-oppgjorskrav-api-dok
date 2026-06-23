# Oppgjørskrav

Endepunkt for innsending av ett oppgjørskrav på JSON format.

| Felt                    | Verdi                      |
|-------------------------|----------------------------|
| path                    | /oppgjorskrav/oppgjorskrav |
| method                  | POST                       |
| content type            | text/plain                 |


| Status | Response | Forklaring                                                                                          |
|--------|----------|-----------------------------------------------------------------------------------------------------|
| 204    |          | Oppgjørskrav er validert ok og sendes til videre behandling                                         |
| 400    |          | Oppgjørskravet er ikke gyldig, f.eks. ikke-validerende XML, ugyldig XAdES signatur på en regning mm |
| 451    |          | Direkteoppgjørsavtale er ikke på plass                                                              |

## Valideringer

Følgende valideringer gjøres før oppgjøret lagres og sendes til behandling:

- validering av input iht JSON schema
- XML-validering av M1
- Validering av XAdES signaturer på alle M1
- Alle regler som kan føre til avvisning av innsendingen (ikke enkeltregninhger)

I test-miljøet kontrolleres det i tillegg at kun syntetiske fødselsnummer benyttes.

Dersom valideringene feiler er ikke oppgjøret gyldig og vil bli avvist (400 Bad Request).





