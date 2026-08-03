# Epimethean Challenge

A play-by-mail-style 4X space game of interstellar exploration, colonisation, and conquest, inspired
by **Empyrean Challenge**.

## Rights and permission

**Empyrean Challenge is owned by James Colombo.** In **February 2023** he gave permission to use the
manual and to create this game engine. Epimethean Challenge exists under that permission.

That permission is not a transfer of ownership. Mr Colombo retains the rights to Empyrean Challenge.

The **player handbook**, circulated as the *Empyrean Cluster Wars Manual*, is an extract of the
original Empyrean Challenge website. It is **not redistributed here**: it was never committed to this
repository, and it has been removed from the working tree now that extraction is complete. It was
read as evidence and every rule restated in our own words rather than quoted.

Two **sidecars** were distributed alongside it — a spreadsheet of unit statistics and a Word document
giving the turn sequence in detail — and are used under the same permission. `docs/units.md` is the
spreadsheet, transcribed; the turn sequence document has been absorbed into the rules manual and
removed as redundant.

Our own work — the rules manual, the supporting documents, and the engine once it exists — is
MIT licensed. See [LICENSE](./LICENSE).

## Status

Specification only. There is a Go module (`github.com/mdhender/ecw1`, Go 1.26.4) and no source files
yet. The current work is the normative specification the engine and database model will be generated
from.

| Document | What it is |
|----------|------------|
| [`docs/rules-manual.md`](./docs/rules-manual.md) | **Normative.** The rules, and only the rules. |
| [`docs/home-world-setup.md`](./docs/home-world-setup.md) | The official starting position and the arithmetic that closes it. |
| [`docs/conflicts-and-gaps.md`](./docs/conflicts-and-gaps.md) | Decisions taken, source conflicts, and rules no source supplies. |
| [`docs/units.md`](./docs/units.md) | Per-unit statistics. Informative; the manual governs. |
| [`docs/entity-model.md`](./docs/entity-model.md) | How an implementation may represent ownership. Not normative. |

## Documentation

Documentation lives in [`docs/`](./docs), organised on [Diátaxis](https://diataxis.fr/). See
[`docs/README.md`](./docs/README.md) for what belongs in each folder.
