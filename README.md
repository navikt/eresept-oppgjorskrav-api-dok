# Helserefusjons-APIer

API-dokumentasjon for Eresept OppgjørsKontroll.

📖 **Les dokumentasjonen:** [https://studious-giggle-7emp4vm.pages.github.io/](https://studious-giggle-7emp4vm.pages.github.io/)

> Når repoet gjøres public vil URL-en bli: https://navikt.github.io/eresept-oppgjorskrav-api-dok/

Dokumentasjonen er skrevet i Markdown og rendres til HTML med [Zensical](https://zensical.org/).

## Forutsetninger

- Python ≥ 3.10

## Installer avhengigheter

Kjøres én gang, eller når `requirements.txt` endres:

```shell
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
```

## Lokal utvikling

Start en lokal utviklingsserver med live reload:

```shell
.venv/bin/zensical serve
```

Åpne [http://127.0.0.1:8000](http://127.0.0.1:8000) i nettleseren. Endringer i Markdown-filene under `docs/` 
oppdateres automatisk.

## Bygge HTML

For å generere statisk HTML til `site/`-mappen:

```shell
.venv/bin/zensical build
```

## Prosjektstruktur

| Mappe/fil          | Beskrivelse                                        |
|--------------------|----------------------------------------------------|
| `docs/`            | Markdown-kildefiler for dokumentasjonen             |
| `zensical.toml`    | Zensical-konfigurasjon (navigasjon, tema, features) |
| `site/`            | Generert HTML (ikke rediger manuelt)               |
| `requirements.txt` | Python-avhengigheter                                |

## Navigasjon

Navigasjonsstrukturen i dokumentasjonen styres manuelt via `nav`-seksjonen i `zensical.toml`. Den må oppdateres
når du:

- **Legger til en ny side** — Legg til en linje under riktig seksjon med tittel og filsti.
- **Fjerner en side** — Fjern den tilhørende linjen fra `nav`.
- **Endrer tittel eller rekkefølge** — Oppdater tittelen eller flytt linjen til ønsket posisjon.

Nye Markdown-filer som ikke er lagt til i `nav` vil ikke vises i navigasjonen.


