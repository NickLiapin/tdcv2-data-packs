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

The same three commands exist in all five implementations — Node, Python, Java,
Rust and C# — and write the same project file, so a pack downloaded by one is
found by the others. The format is deliberately plain: a JSON index and zip
archives, readable by anything.

To pick up a newer copy of a bundle you already have, add it again:
`tdcv2 pack add usa` replaces the installed one, and a downloaded bundle always
takes precedence over the starter packs built into the library.

## Which versions read them

Measured on 17 October 2026, for every bundle in this catalogue: each generator
in it, drawn twice, in a project holding only `common`, `en` and that bundle,
under releases 0.1.0, 0.2.0, 0.2.1, 0.3.0 and 0.3.5 and the current code.

- **0.3.0 and newer** run every generator in every bundle.
- **0.2.1** runs every bundle except the countries added after it was released;
  it does not know those country names yet.
- **0.2.0 and older** download and unpack every bundle, but cannot run the
  generators that compute a check digit — they predate the `<use>` tag those
  are written with. Plain lists still work. Upgrade to use the rest.

That publish made no older release worse: every bundle one of them could run
before it was replaced still runs after.

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

**294 bundles: `common`, 95 languages, 198 countries.** See
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

No code, ever. That is what keeps the output identical across the five
implementations.

## Provenance and licence

The English and US material is built from public-domain government sources — US
Census surname frequencies, Social Security given-name frequencies, USPS street
and place data. The other languages and countries are hand-authored or generated
for this project, with formats (national ID numbers, IBANs, postcodes) written to
their published specifications and verified against real check-digit algorithms —
accepting numbers the issuing authority itself publishes, and rejecting the same
numbers with one digit changed.

Where a country publishes no algorithm, the pack computes none and says so in the
file's own description rather than inventing one that would look verified and
disagree with every real number. The same applies to a country with no IBAN: the
absence is checked against the ISO 13616 registry before the file is left out.

None of it is personal data. These are frequency lists and format rules; no file
here describes a real person.
