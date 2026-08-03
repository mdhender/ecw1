# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

**Epimethean Challenge** — a play-by-mail-style 4X space game inspired by Vern Holford's
Empyrean Challenge. The repository is currently **specification-only**: there is a Go module
(`github.com/mdhender/ecw1`, Go 1.26.4) with no source files yet. All current work is producing
`docs/rules-manual.md`, the normative spec from which the game engine and database model will be
generated.

Go commands work as usual once code exists (`go build ./...`, `go test ./...`,
`go test -run TestName ./pkg/...`); there is nothing to build or test today.

## Document authority

The manual's own §0.2 is the authority; the ranking is:

| File | Status |
|------|--------|
| `docs/rules-manual.md` | **Normative.** Our own work, licensed to distribute. If it disagrees with an implementation, amend the manual first. |
| Player handbook | **Removed.** An extract of the original website. Used under James Colombo's permission but never redistributed: read it for evidence, never quote it. Never committed. Anything it alone could answer — `CONFLICT-14`, `CONFLICT-15` — needs a fresh copy from `../ecw2`. |
| `docs/units.md` | Informative, work in progress. Transcribed from the unit spreadsheet, one of the handbook's two sidecars. Source of canonical item codes only (`D-03`); several statistics are known errors. |

## Working on the rules manual

The manual is Diataxis **reference** material. Describe the machinery; do not explain, instruct,
editorialize, or add tutorials, strategy, or narrative examples. Illustrative arithmetic appears
only where a formula is otherwise ambiguous.

Three tracking vocabularies run through the document and must be kept consistent:

- `GAP-nn` (§21) — a rule no source supplies. **Never invent a rule to fill a gap**; state the gap
  and escalate to the design owner. New gaps exposed by a decision get added.
- `CONFLICT-nn` (§20) — sources contradict each other. Both readings are recorded; neither is
  authoritative until a decision closes it. Closing one means filling in its Resolution column.
- `D-nn` (§0.4) — a design-owner decision. **Entries are never rewritten**, only appended. A
  decision names the conflict or question it closes and its consequences.

All three now live in `docs/conflicts-and-gaps.md`, not in the manual. The manual keeps §0.4, §20,
and §21 as headings that point there, and that document keeps the manual's numbering.

When a decision lands, the follow-through is: append `D-nn` in §0.4 of
`docs/conflicts-and-gaps.md` → rewrite the affected body section of the manual to state the rule as
fact with no hedging (remove the candidate-readings passage) → update the Resolution column in §20
→ update §21 for gaps closed or newly exposed → note any correction `docs/units.md` now needs.

Ask the design owner (the repo owner) rather than guessing when evidence is genuinely ambiguous —
wrong foundational formulas are expensive to unwind once a data model depends on them.

## Manual conventions

- Section cross-references use markdown anchors (`[§13.4](#134-system-probe)`); keep them working
  when renumbering.
- Every unit statistic is a function of that unit instance's own `TL` (1–200, per `D-02`); a
  `Mines-3` and a `Mines-7` are distinct items that coexist.
- Notation is defined in §0.5 (`MU`, `VU`, `TDU`, `S/C` = Entity, `x^2` = squared). One population
  unit = 100 individuals.
- Item codes come from `units.md` even where the mechanic comes from the handbook (`D-03`);
  §20.2 tracks the naming divergences.

## Repository conventions

- `*-prompt.md` at the repo root are working prompts from the owner to the agent. They are
  gitignored and are task input, not project documentation.
- `*.pdf` is gitignored, which is what kept the unlicensed handbook out of the repository. It has
  since been removed altogether. Do not commit a copy or quote it verbatim.
- **Empyrean Challenge is owned by James Colombo**, who gave permission to use the manual and create
  this engine. The root `README.md` carries the notice; keep it there. The
  permission covers use, not redistribution: restate, never quote, and never commit the handbook.
- The project is MIT licensed, which covers **our own work** only — not Empyrean Challenge itself.
