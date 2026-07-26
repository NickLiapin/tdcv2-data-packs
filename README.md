# TDC v2 — data packs

Downloadable data for **TDC v2**, a deterministic test-data generator. The packs
live here rather than inside the library so that updating the engine never wipes
the data you chose to download, and so a gigabyte of names is not shipped to
someone who only wanted a UUID.

## How it works

1. Fetch [`index.json`](./index.json) — the catalogue.
2. Pick the bundles you want.
3. Download each bundle's `file`, check it against its `sha256`, unzip it into
   your data directory.

`tdcv2 pack` does all of that for you:

```bash
tdcv2 init            # once per project: decide where packs live
tdcv2 pack list       # see the catalogue
tdcv2 pack add common en usa
```

The same three commands exist in all three implementations — Node, Python and
Java — and write the same project file, so a pack downloaded by one is found by
the others. The format is deliberately plain: a JSON index and zip archives,
readable by anything.

## One bundle per axis

A bundle carries **a language, or a country, or neither** — never a mash. `en` is
the English language with no country in it, `usa` is the country with no language
in it, `common` is neither. They compose:

```
US English      =  common + en + usa
Canadian French =  common + fr + canada
English, no country  =  common + en
```

A single `en-us` bundle would make the second and third impossible.

**108 bundles: `common`, 10 languages, 97 countries.** See
[`index.json`](./index.json) for the current list. It is generated from the packs
themselves — never hand-edited — and every `bytes` and `sha256` in it is computed
from the zip beside it.

A language or country that is not listed is not finished. An entry here is a
promise that every address under it resolves, so a folder holding a single file
does not get one.

## What is inside a bundle

Plain UTF-8 text: one value per line, one file per list, addressed by its path.
`en/person/lastName.txt` is the address `en.person.lastName`. Some files carry a
small `---` header — a weighted list pairs each value with a count, so a run of
30,000 rows contains as many Jameses as the census says, not approximately.

No code, ever. That is what keeps the output identical across the three
implementations.

## Provenance and licence

The English and US material is built from public-domain government sources — US
Census surname frequencies, Social Security given-name frequencies, USPS street
and place data. The other languages and countries are hand-authored or generated
for this project, with formats (national ID numbers, IBANs, postcodes) written to
their published specifications and verified against real check-digit algorithms.

None of it is personal data. These are frequency lists and format rules; no file
here describes a real person.
