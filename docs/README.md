# Documentation

Documentation for **Epimethean Challenge**, a play-by-mail-style 4X space game.

This folder is organised on [Diátaxis](https://diataxis.fr/), which holds that there are four
distinct kinds of documentation, each answering a different need, and that mixing them in one
document serves none of them well. The four are separated here into four folders.

## The four kinds

Two questions place any page. Does it serve **action** — doing something — or **cognition** —
knowing something? And does it serve **study**, where the reader is acquiring skill, or **work**,
where the reader is applying it?

|                       | Action — *doing*          | Cognition — *knowing*           |
|-----------------------|---------------------------|---------------------------------|
| **Study** — acquiring | [`tutorial/`](./tutorial) | [`explanation/`](./explanation) |
| **Work** — applying   | [`how-to/`](./how-to)     | [`reference/`](./reference)     |

### [`tutorial/`](./tutorial) — lessons

A tutorial is a lesson that carries a newcomer through a first experience of the game. Its purpose
is confidence, not completion: a reader finishes a tutorial knowing they can play, having played.
It takes responsibility for the reader's success, so it must work exactly as written, every time.

Expect pages that say *we will*. A first turn, walked through end to end.

### [`how-to/`](./how-to) — recipes

A how-to guide addresses a goal a competent player or implementer already has, and shows the way to
it. It assumes you know what you are doing and want to get something done. Unlike a tutorial it may
skip, branch, and assume.

Expect pages titled with the goal itself — *how to found a colony in a new system*, *how to run a
turn* — that answer *if you want this, do that*.

### [`reference/`](./reference) — the machinery

Reference describes the game and only describes it: the entities, the formulas, the item statistics,
the order set, the turn sequence. It is neutral about what you are trying to do with it, complete,
and free of interpretation. Its structure mirrors the structure of the game itself, the way a chart
mirrors a coastline.

Expect statements of fact — *a mine removes `25 × TL` ore per turn* — with no strategy, no tutorial,
and no argument. Worked arithmetic appears only where a formula would otherwise be ambiguous.

This is the bulk of what exists today, because the reference material is what an engine and a
database model are generated from.

### [`explanation/`](./explanation) — the reasoning

Explanation provides context and joins things together. It answers *why*: why a rule takes the shape
it does, what was decided and against what alternatives, what a design costs and what it buys. It is
allowed opinion and perspective, which reference is not.

Expect discussion — the reasoning behind a mechanic, the trade-offs in a model, the history of a
decision — read at leisure rather than at the table.

## Why the separation matters here

The rules are normative: an engine and a database model are generated from them. A rule softened by
a tutorial's hand-holding, or hedged by an explanation's reasoning, is a rule an implementer can
read two ways. Keeping the four apart is what lets the reference material be read as specification,
and lets the reasoning behind it be recorded in full without diluting it.

## Status

The documents are being moved into these folders; most still sit at the root of `docs/` and are
listed in the manual's own Sources section. A table of contents will be added once the moves are
done.

## Sources and provenance

Epimethean Challenge is inspired by Vern Holford's **Empyrean Challenge**, a play-by-mail game. Three
documents were the evidence these rules were extracted from, and they do not all stand alike.

**The player handbook**, circulated as the *Empyrean Cluster Wars Manual*, is an extract of the
original Empyrean Challenge website, which was the game's source of truth. It is third party and its
author has **not** permitted direct use of its contents. It was kept out of this repository by
`.gitignore`, was never committed, and has been removed. Nothing here is copied from it: it was read
as evidence and every rule restated in our own words.

Its source, recorded for provenance and no longer reachable:

<http://empyreanchallenge.com/ECWxManual/>

**Two sidecars** travelled with it, distributed separately rather than folded into the handbook or
the website: a spreadsheet of unit statistics and a Word document giving the turn sequence in more
detail than the handbook's own chapter. **James gave permission to use these as game documentation.**
`docs/units.md` is the spreadsheet, transcribed. The turn sequence document has been absorbed into
the manual's §3 and removed — as redundant, not for any question of rights.

Everything else in these folders is our own work, under the repository's [MIT licence](../LICENSE).
