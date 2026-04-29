# Eresept oppgjørskontroll API

**(Dette er dokumentasjon for et API som skal overta for dagens ebXML-baserte løsning – det er ikke et ferdig produkt for allmenn bruk.)**

API-et brukes av apotek og bandasjister for innsending av oppgjørskrav og rapportering av egenandeler til Helfo.
Innsending skjer gjennom en oppgjørskravsmelding (M18) via en HTTP POST-request. API-et gir også mulighet til å hente status, hente M22, M23 og vedtaksbrev, og kontrollere om utleverer har avtale om direkteoppgjølr med Helfo.

## Forutsetninger

For å kunne bruke API-et må helseaktøren være registrert hos Helfo med gyldig direkteoppgjørsavtale for innsending av oppgjørskrav.

Se [direkteoppgjørsavtale for apotek](https://www.helfo.no/Apotek/slik-inngar-eller-endrer-apotek-avtale-om-direkteoppgjor) og
[direkteoppgjøsravtalle for bandasjist](https://www.helfo.no/Bandasjist/slik-inngar-eller-endrer-bandasjist-avtale-om-direkte-oppgjor)
for mer informasjon om inngåelse av avtale.

### Apotek og bandasjister autentiseres via HelseID.
Det forventes at vi finner utleverers organisasjonsnummer som et claim i HelseID-tokenet (`helseid://claims/client/claims/orgnr_child`, eller 
`helseid://claims/client/claims/orgnr_parent` dersom `helseid://claims/client/claims/orgnr_child` ikke er satt), 
og at dette samsvarer med organisasjonsnummeret som er oppgitt i
`/mh:MsgHead/mh:MsgInfo/mh:Sender/mh:Organisation/mh:Ident[mh:TypeId/@V='LOK']/mh:Id/text()`, eller 
`/mh:MsgHead/mh:MsgInfo/mh:Sender/mh:Organisation/mh:Ident[mh:TypeId/@V='ENH']/mh:Id/text()` dersom 
`/mh:MsgHead/mh:MsgInfo/mh:Sender/mh:Organisation/mh:Ident[mh:TypeId/@V='LOK']/mh:Id/text()` ikke er angitt.

Selve innsendingen skjer ved å sende en M18 kryptert med Eresepts offentlige nøkkel.
Etter innsending kan krav følges opp ved å hente status og kontrollresultater.

## API-endepunkter

| Navn                                                           | Path                      | Scope                        | Audience          | Type     | Beskrivelse                                                              |
|----------------------------------------------------------------|---------------------------|------------------------------|-------------------|----------|--------------------------------------------------------------------------|
| [Test av utleverer](ping.md)                                   | /oppgjorskrav/ping        | hdir:eresept-ok/oppgjorskrav | hdir:eresept-ok   | GET/TEXT | Svarer med pong om utleverer er autorisert for å sende inn oppgjørskrav. |
| [Send inn oppgjørskrav (M18)](m18.md)                          | /oppgjorskrav/m18         | hdir:eresept-ok/oppgjorskrav | hdir:eresept-ok   | POST/JWE | Innsending av M18.                                                       |
| [Hent status for innsendt oppgjørskrav](status.md)             | /oppgjorskrav/status      | hdir:eresept-ok/oppgjorskrav | hdir:eresept-ok   | GET/JSON | Returnerer status for et eller flere krav.                               |
| [Hent M22 for innsendt oppgjørskrav](m22.md)                   | /oppgjorskrav/M22         | hdir:eresept-ok/oppgjorskrav | hdir:eresept-ok   | GET/XML  | Returnerer M22 for et enkelt krav.                                       |
| [Hent M23 for innsendt oppgjørskrav](m23.md)                   | /oppgjorskrav/M23         | hdir:eresept-ok/oppgjorskrav | hdir:eresept-ok   | GET/XML  | Returnerer M23 for et enkelt krav.                                       |
| [Hent vedtaksbrev for innsendt oppgjørskrav](vedtaksbrev.md)   | /oppgjorskrav/vedtaksbrev | hdir:eresept-ok/oppgjorskrav | hdir:eresept-ok   | GET/PDF  | Returnerer vedtaksbrev for et enkelt krav.                               |
| [Hent Eresept Public Json Web Key - JWK](jwk.md)               | /eresept/oppgjorskrav/jwk | hdir:eresept-ok/oppgjorskrav | hdir:eresept-ok   | GET/JSON | Returnerer eresepts offentlige krypteringsnøkkel (JWK).                  |


### Typer endepunkt

| Type         | Beskrivelse                                                                                                                                                                                                 |
|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **POST/JWE** | HTTP POST der request-body er kryptert som JWE. Responsen returneres som ukryptert JSON. Der payload ikke er av type JSON vil innholdet base64 enkodes før kryptering og base64 dekodes etter dekryptering. |
| **GET/JSON** | HTTP GET som returnerer ukryptert JSON.                                                                                                                                                                     |
| **GET/XML**  | HTTP GET som returnerer ukryptert XML.                                                                                                                                                                      |
| **GET/PDF**  | HTTP GET som returnerer ukryptert PDF.                                                                                                                                                                      |
| **GET/TEXT** | HTTP GET som returnerer ukryptert tekst                                                                                                                                                                     |

*(Ukryptert betyr at dataene kun er sikret med HTTPS, ikke innholdskryptert.)*

## Autentisering og autorisasjon

### HelseID

Foretrukket autentiseringsmekansime.

Mer informasjon:
[HelseID – NHN utviklerportal](https://utviklerportal.nhn.no/informasjonstjenester/helseid/)

For APIet brukes HelseID scope **hdir:eresept:oppgjorskontroll**

#### Organisasjonsnumre
APIet krever organisasjonsnummer, hovedenhet oog eventuell underenhet. 
Se [organisasjonsnumre](https://utviklerportal.nhn.no/informasjonstjenester/helseid/bruksmoenstre-og-eksempelkode/bruk-av-helseid/docs/tekniske-mekanismer/organisasjonsnumre_no_nbmd) 
for hvordan man kan få dette inkludert i tokenet.


## Kryptering av helseopplysninger

Endepunkter av typen **POST/JWE** krever ende-til-ende-kryptering med JSON Web Encryption (JWE).
Dataene må krypteres med eresepts offentlige nøkkel før innsending.

Dette er et ekstra sikkerhetstiltak på grunn av mengden sensitive helseopplysninger som behandles.

Mer informasjon:
[JWE-kryptering av POST-requests (POST/JWE)](jwe_ende_til_ende_kryptering.md)

## Miljøer

| Miljø | KUHR Krav API URL                                                                      | Maskinporten                                                                                                                               | HelseID                                                                                                                              |
| ----- |----------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| DEV   | [https://teameresept-gw.ekstern.dev.nav.no](https://teameresept-gw.ekstern.dev.nav.no) | [https://test.maskinporten.no/.well-known/oauth-authorization-server](https://test.maskinporten.no/.well-known/oauth-authorization-server) | [https://helseid-sts.test.nhn.no/.well-known/openid-configuration](https://helseid-sts.test.nhn.no/.well-known/openid-configuration) |
| PROD  | [https://teameresept-gw.nav.no](https://teameresept-gw.nav.no)                         | [https://maskinporten.no/.well-known/oauth-authorization-server](https://maskinporten.no/.well-known/oauth-authorization-server)           | [https://helseid-sts.nhn.no/.well-known/openid-configuration](https://helseid-sts.nhn.no/.well-known/openid-configuration)           |


## Versjonering
Header `x-api-version` brukes for å angi versjon dersom endringer gjør at det blir nødvendig å skille på versjoner.
Pt. har den ingen effekt, vi er på versjon 1.

## OpenAPI-spesifikasjon

*(lenke kommer kanskje)*


## Endringslogg

Det føres en endringslogg for dokumentasjonen til API-et for å kunne følge oppdateringer:
[Endringslogg](endringslogg.md)

