# JWE-kryptering av POST-requests – `POST/JWE`

API-et bruker **JWE (JSON Web Encryption)** for å kryptere enkelte POST-forespørsler.
Det betyr at klienten krypterer forespørselen med eresept sin offentlige nøkkel JWK (Json Web Key).

**Merk:** JWE-signering benyttes **ikke**. Kun kryptering er i bruk. Det betyr at klienten ikke trenger å ha et eget sertifikat eller JWK for signering.

---

## Hva er JWE?

**JSON Web Encryption (JWE)** er et URL-sikkert og kompakt format for kryptering av strukturert data ved hjelp av JSON-baserte mekanismer.
Formatet brukes når man trenger å sende sensitive data kryptert, for eksempel i en POST-request til et API.

Et JWE består av fem deler, separert med punktum:

```
<protected_header>.<encrypted_key>.<initialization_vector>.<ciphertext>.<authentication_tag>
```

---

## Komponenter i en JWE

### 1. Protected Header

Inneholder metadata som beskriver hvordan JWE er kryptert, og hvilken type innhold det bærer.

Eksempel:

```json
{
  "alg": "RSA-OAEP-256",
  "enc": "A256GCM",
  "kid": "12345",
  "typ": "JWT",
  "cty": "JWT",
  "zip": "DEF"
}
```

Forklaring på feltene:

* `alg` – Asymmetrisk algoritme brukt for å kryptere nøkkelen (f.eks. `RSA-OAEP-256`)
* `enc` – Symmetrisk algoritme brukt på innholdet (f.eks. `A256GCM`)
* `kid` – ID til nøkkelen som brukes (matcher en JWK fra KUHR sitt nøkkelsett)
* `typ` – Typen token, ofte `JWT`
* `cty` – Content type (innholdstype), ofte også `JWT`
* `zip` – Valgfri kompresjonsalgoritme brukt før kryptering (f.eks. `DEF`)

---

### 2. Encrypted Key (CEK)

En tilfeldig generert symmetrisk nøkkel (Content Encryption Key) brukes til å kryptere selve innholdet.
Denne nøkkelen krypteres med mottakerens offentlige nøkkel (asymmetrisk).

---

### 3. Initialization Vector (IV)

En tilfeldig verdi som brukes av den symmetriske algoritmen for å sikre unik kryptering for hver melding.
Må være forskjellig for hver forespørsel.

---

### 4. Ciphertext (Payload)

I vårt tilfelle er dette en M18 som XML.

Payload signeres **ikke** av klienten i dette oppsettet. Kun kryptering brukes.

---

### 5. Authentication Tag

Brukes ved autentisert kryptering (f.eks. `AES-GCM`) for å sikre at innholdet ikke er endret under transport.
Dette er en integritetsbeskyttelse, ikke en signatur.

---

### Kotlin eksempel på kryptering av en POST-request

#### Payload
Payload vil normalt være en M18 (XML), men her er det bare dummy-XML for illustrasjon:

```xml
<foo>bar</foo>
```

#### Public JWK

```json
{
    "kty": "RSA",
    "nbf": 1769502362,
    "e": "AQAB",
    "use": "enc",
    "kid": "oldest",
    "exp": 1767256003,
    "alg": "RSA-OAEP-256",
    "n": "zLluViVBf7ZkALlU2bp22_zuCN1K7qqSUxW2vi2bcz01h9mJ2MsN6CWC0hfD0n--obOO7v3bybAS-dv0E2xyDv7KzIlXdsxGpYaycki8gyG00iWyie4A056Jhehx09qxpKTUvoCYY02JNHrzg5shvhPvJ75tJXuqtJvXhWo8MBX1p9fxdb_yWuxRn-Hb14lv1Cfgq5F-0PHbA9gNlrB_EszdSKesL8Zs_yNV3VlLHG1FyYmrYHjVNv39QTMmreZDATiu24t5NfMjwtT_8sJQI7VVmlER-llRS7pmeeZFNyDMWv6wHgnH8fU7ecSxw6spN_mBvKHDboZoYURMYNxj5w"
}
```

#### Kotlin kode

```kotlin
val payload = Payload("<foo>bar</foo>")
val publicKey = jwkKey.toPublicJWK() as RSAKey
val header = JWEHeader.Builder(JWEAlgorithm.RSA_OAEP_256, EncryptionMethod.A256GCM)
   .keyID(publicKey.keyID)
   .type(JOSEObjectType(MediaType.APPLICATION_XML_VALUE))
   .build()
val jweObject = JWEObject(header, payload)
val encrypter = RSAEncrypter(publicKey)
jweObject.encrypt(encrypter)
val jweString = jweObject.serialize()
```

#### Resultat JWE String

```
eyJraWQiOiJvbGRlc3QiLCJ0eXAiOiJhcHBsaWNhdGlvbi94bWwiLCJlbmMiOiJBMjU2R0NNIiwiYWxnIjoiUlNBLU9BRVAtMjU2In0.GJHGJ90MRz6XnGCcC2E0CBc-PJnOjwZFDEfJ2SsEuw808IHXtGbNd6VIfhfCf1Smo7p8x2WFw9FXFp1ArRpwRJi6nAQa29m92xgO7aMkv5GkTOpJbSKAj6zzWYNE1N8KTriuZAPX45BX6XhBWylUSHDO1jj0GY9oJWNybtOEii_NVYUR7wIsMLzEWG0bW2sEpx_R5x6_V_PAA4htkue6DpiXMvyuvUUlA4LjEHKWa90uguF1PLYDDb0afjIQVjhln19BIdnt3xNF9c_gRSoqxU8u_FQrKyPHzEwLVDKNE2IzQCw_Nt1xwXcGWs8X4pzzkhgQos4-DKQ4jQe9BQNi1w.4uG5O6c4hS0Ebs3k.C2vvbqevtbj98SimjvA.8ZBaEynLAjSBohGH3Moddw
```

### Kotlin eksempel på dekryptering av en kryptert body

#### Private JWK
```json
{
    "alg": "RSA-OAEP-256",
    "d": "AQg1nXdznXgKpK_YuH5GiwLQXCq_NQPPrV2pYT0Pgp23GftHfeavtm2dGA3v1p7-k8DjtBP0_d0i38xeA5O4b7QjdbOC4fq2p8uGbnDzUeU_4g8bXs_chG17yePdJ3v7s9XW5ICFkl6WMm6WsOiLzUd0HUBYV71PXNUsEl1HIS1dS00KfFU2Bb98X6eynvbNe5HOLFdd6BKwW0uLHVjv_d1jrdzmXRQa6wCbLw0aXTbLub15_oHsM7Fy-mCccW-z6pI2KJTvpYE-LbNnZim22Ms1tZIw6Pml-joxvs1Oxc5WA9aFMJPsFlJCwWIRZJ7bNUTXy9SBEqtPxERKUEo3XQ",
    "dp": "xVR_6cBqvqbz0dHZ77MzPKYMkdcPwyKcFY9XrY995xWPIyTH5n6EHLKiyj5ACEqUr8ikmJbPqCSml2vz3_DBY_91eo3aSa58rd8_C24GnUNbRCFiNbvCvxQ_8cd23l7nEaECp5FQzdf6yr2Xl7vjn8guOSb61Li7rfL3ucRxFls",
    "dq": "XNaqD_GdyXbj6YyaCuLIWyg2OQCGjKP3YlmRnjGC11Y20OxViGJ6bTlr81HgFBYCyaX4mARVy8vRQNeuOyE9L3zm5nsqatZ5Kmjkmgfdy2rIZ6KVcH6p8jE8cnm6G_BK7DJ_tStIB2Kp74C6L0xFI3Th-kNtrEhif3_mlsv-V5E",
    "e": "AQAB",
    "exp": 1767256003,
    "kid": "oldest",
    "kty": "RSA",
    "n": "zLluViVBf7ZkALlU2bp22_zuCN1K7qqSUxW2vi2bcz01h9mJ2MsN6CWC0hfD0n--obOO7v3bybAS-dv0E2xyDv7KzIlXdsxGpYaycki8gyG00iWyie4A056Jhehx09qxpKTUvoCYY02JNHrzg5shvhPvJ75tJXuqtJvXhWo8MBX1p9fxdb_yWuxRn-Hb14lv1Cfgq5F-0PHbA9gNlrB_EszdSKesL8Zs_yNV3VlLHG1FyYmrYHjVNv39QTMmreZDATiu24t5NfMjwtT_8sJQI7VVmlER-llRS7pmeeZFNyDMWv6wHgnH8fU7ecSxw6spN_mBvKHDboZoYURMYNxj5w",
    "nbf": 1769502362,
    "p": "1agX6fmMTkArjIc0Jor7N0d4DOgQc_8ZnjkK6VB4WFueIpq7YjzN2VRpd6y1MoFueiQ_U9P2-oHg-2er-GPI_Mj4J5E19e3MXF6J9xSeT6Fu8EplbNnzSBXjGuI3GYGkB7tMl51Wscft-jlrYHc3CRaOJ69jU18zZOCr746QMPs",
    "q": "9UwoMigFkFWthRADi4wb4vExgvrq3_LUrRIqG1yxR-LAicE7nvvG3D8tFuPWnNuzNXhdcgj6Liv6WdfHt9SnyMi0XKlv-2KrAYx6ZRvIN-dvM608wpIrW0-fRPdWIom15qAOR91K9RZuP-9BmZm-r7lg4kpPWB7eGtvwi-VNHQU",
    "qi": "a9Ib_pFFklUN0W5Mrjbu6szb4QJrWYewEzqRWArm9dDXaBblf80_Ht1Rc8WmBb5cP1qHNAkOzwIcCDPaT7YDBx_3MhOn4AGhiduaj0C0-PVZMQxz9oLiAtDXNevAOPjdUuCWRWFvkesox1Q6LMsQuoByiwfsbwVXfssK2ZFazEA",
    "use": "enc"
}
```

#### Kotlin kode for å dekryptere en JWE string

```kotlin
    val jweObject = JWEObject.parse(jweString)
    val jwk = getMatchingKey(jweObject.header.keyID)
    val jwk = oppgjorskravKeyService.getMatchingKey(jweObject.header.keyID)
        val decrypter = when (jwk.keyType) {
        KeyType.RSA -> RSADecrypter(jwk as RSAKey)
        KeyType.EC -> ECDHDecrypter(jwk as ECKey)
        else -> throw IllegalArgumentException("unsupported key type: ${jwk.keyType}")
    }
    jweObject.decrypt(decrypter)
    val payload: Payload = jweObject.payload
    val plainString = payload.toString()
```

#### Pseudo C# kode
Kode generert av AI og over jodet ikke testet eller verifisert
```c#
// 1. Setup the Payload and Keys
var payloadString = "<foo>bar</foo>";
var rsaParams = jwks.Keys.First().ToRSAParameters(); // Logic to extract RSA params from your JWK
var rsaKey = new RsaSecurityKey(rsaParams) { KeyId = "your-key-id" };
 
// 2. Encrypting the JWE
var encryptingCredentials = new EncryptingCredentials(
    rsaKey, 
    SecurityAlgorithms.RsaOaep256, 
    SecurityAlgorithms.Aes256Gcm
);
 
var handler = new System.IdentityModel.Tokens.Jwt.JwtSecurityTokenHandler();
// Note: In .NET, we often use a JwtProxy or manual JWE construction for raw strings
var jweString = handler.CreateEncodedJwt(new SecurityTokenDescriptor
{
    EncryptingCredentials = encryptingCredentials,
    Claims = new Dictionary<string, object> { { "data", payloadString } },
    Type = "application/xml"
});

 
// 3. Decrypt to Verify
var result = handler.DecryptJwt(jweString, new TokenValidationParameters
{
    TokenDecryptionKey = rsaKey,
    ValidateAudience = false,
    ValidateIssuer = false
});
 
var decryptedPayload = result.Claims.First(c => c.Type == "data").Value;
Assert.Equal(payloadString, decryptedPayload);
 
```


## 🔗 Nyttige lenker

* [RFC 7516 – JWE-spesifikasjon](https://datatracker.ietf.org/doc/html/rfc7516)
* [Scott Brady – JWE forklaring](https://www.scottbrady.io/jose/json-web-encryption)
* [Nimbus JOSE + JWT eksempler](https://connect2id.com/products/nimbus-jose-jwt/examples/signed-and-encrypted-jwt)


