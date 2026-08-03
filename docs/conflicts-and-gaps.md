# Conflicts and Gaps

Open questions against [`rules-manual.md`](./rules-manual.md): places where the sources contradict each other, and
rules no source supplies. This is a tracking document, not a rulebook. The manual states rules; this states what is
still unsettled about them.

> **Section numbers match the manual's.** The manual keeps §20 and §21 as headings that point here, and this document
> keeps their numbering, so a reference to `§20.3` or `§21.4` means the same thing in both files.

Three vocabularies run through the manual and are resolved here:

| Tag           | Meaning                                                                                                      | Recorded in |
|---------------|---------------------------------------------------------------------------------------------------------------|-------------|
| `CONFLICT-nn` | The sources contradict each other. Both readings are recorded; neither is authoritative until a decision closes it. | [§20](#20-source-conflicts) |
| `GAP-nn`      | No source supplies the rule. Implementations must not fill a gap by invention; they must escalate it.        | [§21](#21-gaps) |
| `D-nn`        | A design-owner decision closing a conflict or a gap. Append-only; entries are never rewritten.               | [§0.4](./rules-manual.md#04-decisions-on-record) of the manual |

Withdrawn identifiers are not reused. A gap closed by decision is removed from [§21](#21-gaps) and the decision that
closed it says so.

---

## 20. Source conflicts

Open conflicts must be resolved by decision, not by implementation choice.

| ID            | Subject                          | Reading A (handbook)                                                                           | Reading B (`units.md` or elsewhere)                                                                | Resolution                                                                                             |
|---------------|----------------------------------|------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| `CONFLICT-01` | Structure capacity               | "Structure required to enclose one volume unit = `TL^2 / ratio`"                               | "One structure unit encloses `TL^2 / ratio` volume"                                                | Closed by `D-05`: reading B. Reading A is the same quotient stated backwards.                          |
| `CONFLICT-02` | Ration bounds                    | Minimum 25%, maximum 100%                                                                      | May exceed 100%; capped by a 5-turn stockpile and 0.25 food per unit per turn                      | **Open**                                                                                               |
| `CONFLICT-03` | Factory output                   | `5 × TL` `MU` per turn (`20 × TL` per year)                                                    | `20 × TL` `MU` per turn                                                                            | Closed by `D-03`: reading A                                                                            |
| `CONFLICT-04` | Tech level range                 | 1 to 200                                                                                       | 0 to 10                                                                                            | Closed by `D-02`: reading A                                                                            |
| `CONFLICT-05` | Transfer range                   | Same tactical coordinates required                                                             | Tactical distance 1 or less                                                                        | **Open**                                                                                               |
| `CONFLICT-06` | Pick Up across players           | Always fails                                                                                   | Permitted with mutual `Ally`                                                                       | **Open**                                                                                               |
| `CONFLICT-07` | Hyper engine range               | `sqrt(TL) × 3` `LY`                                                                            | `sqrt(TL) + 4` `LY`                                                                                | Closed by `D-03`: reading A                                                                            |
| `CONFLICT-08` | Orbit decay in empty orbits      | Empty orbits do not cause decay                                                                | Decay applies in any orbit                                                                         | **Open**                                                                                               |
| `CONFLICT-09` | Target category effect           | +50% chance to hit items in the category                                                       | Total damage × 0.8, named category absorbs 4× proportionally                                       | **Open** (both readings are the handbook's)                                                            |
| `CONFLICT-11` | Transport fuel                   | Chart text truncated: "`× TL^2` fuel"                                                          | `0.1 × TL^2` fuel                                                                                  | **Open**: the handbook figure is unrecoverable from the source                                         |
| `CONFLICT-12` | Structure scale and naming       | `Structure` mass `0.1 × TL`; `Light Structure` mass `0.01 × TL`                                | `STRC` mass `1 × TL`; `STRL` mass `0.1 × TL`. `units.md` `STRL` equals the handbook's `Structure`. | Closed by `D-07`: there is no rename, so `D-03` applies and the handbook statistics stand. Both `units.md` rows are in error                            |
| `CONFLICT-13` | Missile launcher build cost      | Assembly chart `15 × TL` metals; summary chart `15 + TL` metals                                | `15 × TL`                                                                                          | Closed by `D-03`: `15 × TL` (both sources' assembly chart)                                             |
| `CONFLICT-14` | Non-assembly stowed volume       | No unassembled volume at all (`N/A` in the chart); crating is a cargo hold rule only            | Every non-assembly item has a stowed volume; half for `ACFT`, `AMSL`, `AWPN`, `MRBT`, equal to full volume for the other eleven | **Deferred** pending research into the handbook's design history                                       |
| `CONFLICT-15` | Military robot volume            | `TL + 10`                                                                                      | `2 × (TL + 10)` assembled, `TL + 10` stowed, despite not being an assembly item                    | **Deferred**, follows `CONFLICT-14`                                                                    |
| `CONFLICT-16` | Pre-maneuver missile fire timing | Order text says "after all ship maneuvers", contradicting its own name and the combat sequence | Combat sequence places it before maneuvers                                                         | **Open**, though the order text is plainly a copy error                                                |
| `CONFLICT-17` | Game name                        | "Empyrean Cluster Wars"                                                                        | "Epimethean Challenge" / "Empyrean Challenge"                                                      | Closed by `D-01`: **Epimethean Challenge**                                                             |

`CONFLICT-14` and `CONFLICT-15` are deferred by the design owner, not merely unexamined. Known intent: cargo is a
bookkeeping compromise whose purpose is to make the ships that haul goods between systems cheaper to build. The
mechanism is acknowledged to be fragile, and the decision waits on research into how the handbook's design arrived at
it. Nothing should be built on either reading until then.

Two findings for that research. First, the handbook is not silent on non-assembly stowed volume: its Non-Assembly
Items and Population Units charts both print `N/A` in the Volume Unassembled column for every row, so the single-volume
reading is stated, not merely inferred from the absence of a figure. Second, `units.md`'s stowed volumes do not follow
one rule. `ACFT`, `AMSL`, `AWPN`, and `MRBT` are half their full volume; the other eleven non-assembly items repeat the
full volume. `AMSL` and `MSSL` are identical in every other statistic yet differ here, which suggests data entry rather
than design.

### 20.1 Units adopted without rules

`D-04` keeps these in the unit vocabulary. No source supplies rules for them.

| Code   | Name      | What is missing                                                                                                                                                                  |
|--------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `BMR`  | Beamer    | Stated to beam `5000 × TL^2` `MU`. `D-08` fixes when the `Beam` order resolves; what beaming does, what it targets, and its range remain undesigned. (`GAP-30`)                  |
| `SPY`  | Spy       | The handbook describes a "superspy" concept explicitly as not implemented. No espionage mechanic exists. (`GAP-29`)                                                              |
| `PRTO` | Prototype | The handbook treats prototypes as ordinary higher-`TL` items, not a distinct unit with its own mass and volume. Whether both models coexist is undecided. (`GAP-31`)             |

`WRKR` and `RBL` were adopted by `D-04` without rules and are given them by `D-12`: both are cadre, that is,
assignments of population units. `units.md`'s reclassification of `POL`, `CNST`, `TRNE`, and `SPAG` from population
types to `Cadre` is adopted by the same decision. See [§5.2](./rules-manual.md#52-living-types-and-cadre-assignments).

### 20.2 Naming divergences

| Handbook                    | `units.md`                                  |
|-----------------------------|---------------------------------------------|
| Hyper Engines               | Hyper Drive (`HDRV`)                        |
| Military Supplies           | Combat Supplies (`CSUP`)                    |
| Robot Probe Vehicle         | Probe (`PROB`)                              |
| Structure / Light Structure | `STRC` / `STRL` (codes only, no rename; `D-07`) |
| Metals / Non-Metals         | Metallics (`METL`) / Non-Metallics (`NMTL`) |

`Military Supplies` and `Combat Supplies` are one item, not two. The rename is deliberate: rebels are not military, and
a future revision may let them draw on the same supplies. Use `CSUP` and "combat supplies" throughout.

### 20.3 Corrections to `units.md`

| Item                         | Correction                                                                                                                                                                                                                          | Status  |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| Tech Level range             | Stated 0–10. `D-02` sets 1–200.                                                                                                                                                                                                    | Applied |
| `STRC` and `STRL` Output     | Read "divided by structural requirement (based on Entity type)". The divisor is the **Structure Ratio**; the Structural Requirement is a different quantity ([§4.2](./rules-manual.md#42-volume-space-and-structure)). The `1 × TL^2` figure itself is adopted by `D-05`. | Applied |
| `STRC` and `STRL` statistics | Every statistic on both rows was shifted one item: `STRL` carried `Structure`'s figures and `STRC` a tenfold rescale of them. Corrected values per `D-07` are in the table below.                                                   | Applied |
| `HDRV` jump range            | Output read `sqrt(TL) + 4`. `CONFLICT-07`, closed by `D-03`, gives `sqrt(TL) × 3`.                                                                                                                                                  | Applied |
| `FACT` output                | Output read `20 × TL` mass per turn. `CONFLICT-03`, closed by `D-03`, gives `5 × TL` per turn, which is `20 × TL` per **year**.                                                                                                     | Applied |
| `CSUP` volume                | Volume read `0.04`, equal to its mass. The handbook's Non-Assembly chart gives volume `0.02`. Stowed volume was moved with it to keep the two equal, pending `CONFLICT-14`.                                                         | Applied |
| `RBL` output                 | Read "Tally of population willing to rebel; does not allocate the underlying units", which contradicts `D-12`: rebels *are* assigned Living units.                                                                                  | Applied |
| `BMR` statistics             | Mass `40 × (TL + 115)`, metals `10 × (TL + 210)`, non-metals `30 × TL + 2500`, fuel `20 × TL^2`, 1 professional per 25. **No source.** The Beamer appears nowhere in the handbook, in any chart or text. Provenance unknown (`GAP-30`). | **Open** |
| `PRTO` statistics            | Mass `TL` but volume `3 × TL`, and build costs `TBD`. No source; the handbook has no Prototype item (`GAP-31`).                                                                                                                     | **Open** |
| `TRNS` fuel                  | `0.1 × TL^2`. The handbook figure is truncated to "`× TL^2` fuel" and is unrecoverable (`CONFLICT-11`).                                                                                                                             | **Open** |
| `LSU` and `FACT` requirements | Both rows read "fuel or power" and omit the solar substitution of [§7.8](./rules-manual.md#78-solar-power), under which life supports, factories, farms, and laboratories in an orbiting colony in orbits 1–5 consume solar power instead. The `LAB` row omits it too. This is load-bearing rather than cosmetic: it is what makes the starting `OBC` of `D-21` viable at Tech Level 1, and a row read literally would charge it 1,200 fuel per turn it does not owe. | **Open** |
| `POWR` requirements          | The Operational Requirements column reads "Open Surface Colonies ONLY", which is a placement restriction, not an operating requirement. Power plants require no labor, no fuel, and no power ([§19.4](./rules-manual.md#194-operating-requirements)), and their functioning quantity per planet is capped at `Habitability Factor × 100,000` ([§7.7](./rules-manual.md#77-power-plants)); the row records none of this. | **Open** |
| `MRBT` volume                | `2 × (TL + 10)`, twice the handbook's `TL + 10`, as though it were an assembly item (`CONFLICT-15`).                                                                                                                                | Deferred |
| `Volume Stowed` column       | Present for every non-assembly item; the handbook gives those items no unassembled volume at all (`CONFLICT-14`).                                                                                                                   | Deferred |

Structure statistics (`D-03`, `D-07`):

| Column             | `STRC` (Structure)                 | `STRL` (Light Structure)           |
|--------------------|------------------------------------|------------------------------------|
| Mass               | `0.1 × TL`                         | `0.01 × TL`                        |
| Volume, assembled  | Negative, magnitude `TL^2 / StructureRatio` ([§4.2](./rules-manual.md#42-volume-space-and-structure)) | Negative, magnitude `TL^2 / StructureRatio` |
| Volume, stowed     | `0.05 × TL`                        | `0.005 × TL`                       |
| Metals to build    | `0.07 × TL`                        | `0.005 × TL`                       |
| Non-metals to build | `0.03 × TL`                       | `0.005 × TL`                       |

`units.md` records the assembled volume of both items as negative and states the enclosure in its Output column, since
the magnitude depends on the containing entity's Structure Ratio and so cannot be a per-item constant.

**Verification status.** Every row of `units.md` has been compared field by field against the handbook's Assembly
Items, Non-Assembly Items, and Population Units charts.

- All 15 **assembly items** agree exactly on mass, assembled volume, stowed volume, and both build costs. `units.md`
  states assembled volume where the handbook states unassembled, and the two are consistent under the doubling rule in
  [§19.1](./rules-manual.md#191-assembly-and-volume-model).
- All **population and cadre** rows agree: mass 1, volume 1, no build cost.
- All **non-assembly items** agree on mass, volume, and both build costs except `CSUP` and `MRBT`, both listed above.
- The remaining divergences are the `Volume Stowed` column and the unsourced `BMR` and `PRTO` rows.

---

## 21. Gaps

A gap is a rule the sources do not supply. Implementations must not fill a gap by invention.

### 21.1 World generation and setup

| ID       | Gap                                                                                                                                                                                                                                                                                                                     |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-01` | **Cluster generation.** How the gamemaster generates a cluster: cube size, number of star systems, their placement, star counts per system, planet placement and type distribution, habitability factor distribution, deposit counts, deposit types, yields, and ore quantities. The handbook assumes a cluster exists. Home systems are generated here too. `D-21` fixes the home planet's habitability, its orbit, and the three unlimited deposits the starting mine groups occupy ([§1.7](./rules-manual.md#17-starting-position)); how many other planets the home system holds, of what types, in which orbits, and with what deposits is the residue of the withdrawn `GAP-02` and is settled here. |
| `GAP-03` | **Player setup.** Number of players per game, and how home systems are separated. The starting Tech Level is no longer part of this: `D-24` fixes it at 1 in every item type in every game, which is what lets `D-21` and `D-23` state the starting position as a single set of numbers rather than a family of them. |
| `GAP-04` | **Turn administration.** Turn cadence, order submission, deadlines, order validation, failure reporting, and what a player report contains.                                                                                                                                                                             |

### 21.2 Engine determinism

| ID       | Gap                                                                                                                                                                                                                     |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-05` | **Produce Output and Send Output stages.** Named in the turn sequence with no content, by both the handbook and `docs/turn-sequence.md`.                                                                                |
| `GAP-06` | **Entity iteration order.** The order in which colonies and ships are processed inside the Production stage, and inside every other stage that is not explicitly ordered.                                               |
| `GAP-14` | **Labor allocation.** How scarce professionals, unskilled, and automation are distributed across farms, mines, laboratories, and factories; whether allocation is per-group or per-item; rounding of partial operation. |
| `GAP-15` | **Fuel and power allocation.** Priority when fuel and power are insufficient for all assembled items, across production, life support, weapons, and propulsion. Whether power is consumed before fuel. `D-08` fixes only the timing of life support consumption, at the population-change step of the Production stage. |
| `GAP-17` | **Rounding.** A general rounding rule. Only ship speed (floor) and defensive weapon counts (ceiling) are specified. The starting position does not depend on one: `D-21` chooses structure counts so that summed enclosure capacity is an integer in both colonies ([§4.2](./rules-manual.md#42-volume-space-and-structure)), and `D-23` preserves that. The gap bites from the first turn a player assembles structure of its own, because per-unit enclosure is `TL^2 / StructureRatio` and is fractional in every entity but an `OPC`. |
| `GAP-18` | **Randomness.** Which events are random, their distributions, and seeding. The tactical distance metric. The distribution of arrival positions within a Close/Medium/Long band.                                         |
| `GAP-19` | **Damage allocation algorithm.** Per-category weights, the random distribution, the four-pass logic, and how items within a category are selected for destruction.                                                      |

### 21.3 Population and economy

| ID       | Gap                                                                                                                                                                                                                                                                                                                       |
|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-07` | **Birth rate and unskilled promotion.** The birth rate function between its 0.25% and 10% bounds; the automatic unemployable-to-unskilled rate.                                                                                                                                                                           |
| `GAP-08` | **Death rate.** The death rate formula; its relationship to life support capacity and habitability; the magnitude of the increase when over capacity or under-fuelled; the magnitude of the reduction when within capacity.                                                                                               |
| `GAP-09` | **Discontent model.** Malcontent increase and decrease formulas; the malcontent threshold that creates rebels; rebel recruitment rate; rebel food and consumer goods theft quantities; police arrest and kill rates; police injury rate; special agent effectiveness; strike and riot selection, magnitude, and duration. |
| `GAP-43` | **Back pay and composite pay.** Whether back pay decays or is ever cleared; whether constructor and special agent pay rates track changes to the rates they are composed of.                                                                                                                                              |
| `GAP-57` | **Upkeep of independent entities.** An independent entity issues no orders and can neither send nor receive goods ([§4.8](./rules-manual.md#48-ownership-and-independence)), so nothing in the manual supplies its food, consumer goods, pay, or life support fuel, and no player can supply them from outside. Whether it continues to farm, mine, and manufacture on its existing assignments, whether its population starves and dies on the ordinary rates ([§5.5](./rules-manual.md#55-death-rate), [§6.1](./rules-manual.md#61-food)), and whether an independent ship's unpaid crew accrues back pay ([§6.3](./rules-manual.md#63-back-pay)) are all unstated. The blockade on `Transfer` and `Give` is deliberate — it is what stops an independent colony from being resupplied into someone's hands — but it also denies a player any way to feed one it intends to capture. `D-21` sharpens this: the starting `OBC` can grow no food at all and is fed by transfer from the `OPC` every turn, so an orbiting colony that goes independent starves by construction rather than by neglect, and every orbiting colony founded before `Farms-2` is reachable inherits the same dependency. |
| `GAP-58` | **Pay rates for the remaining cadre.** Pay follows the assignment (`D-12`), and [§6.2](./rules-manual.md#62-consumer-goods-and-pay) supplies rates for `TRNE`, `CNST`, and `SPAG` only. `WRKR`, `POL`, `RBL`, and `SPY` have none. `WRKR` is the pressing case, because every staffed farm, mine, and factory has one and the assignment is not optional; the reading that fits `D-12` is that a `WRKR` is paid its Living type's rate, since `WRKR` records a labor requirement being met and adds nothing of its own, but that is not stated. `POL` needs a rate of its own, being drafted rather than merely recorded. Whether rebels are paid at all, and what a spy costs, follow the designs those units still lack (`GAP-29`, `GAP-09`). `D-21` avoids the question at setup by assigning none of the four. |

### 21.4 Entity model

| ID       | Gap                                                                                                                                                                                                      |
|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-12` | **Space accounting, work in process.** Whether a batch under manufacture consumes enclosed volume, and when its resources leave Storage Inventory. `D-05` settles the Space Available formula and the semantics of negative assembled volume; `D-22` settles stockpiles, which are enclosed, and records that cargo holds were never open, [§4.4](./rules-manual.md#44-cargo-holds) having bounded them by Space Available all along ([§4.2](./rules-manual.md#42-volume-space-and-structure)). A four-turn batch that occupies volume from the turn it starts and one that occupies none until it finishes differ by a full year of a colony's factory output, so the two readings are not close. |
| `GAP-13` | **Mass accounting.** What is included in an entity's total mass for speed, lift, probe reporting, and control-planet ties: stockpiles, cargo hold contents, WIP, docked ships.                           |
| `GAP-16` | **Solar power scope.** Whether solar power is available to surface colonies or ships in orbits 1–5, or only to orbiting colonies. Whether solar power is unlimited.                                      |
| `GAP-23` | **Transport capacity pool.** Whether transfer capacity is one per-turn pool shared by transfers, pick ups, loads, unloads, surveys, set ups, add-ons, junking, and combat conveyance, or separate pools. |
| `GAP-25` | **Junk and Give details.** Constructor cost of junking. What accompanies a given entity: population, cargo hold, docked ships, home port reassignment, mine group and factory group state.               |

### 21.5 Combat

| ID       | Gap                                                                                                                                                                                                                               |
|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-22` | **Ground combat details.** How "largest invading force" is measured; the wounded-versus-dead split of casualties; how multi-turn battle state is stored; how combat factors map back to specific unit `TL`s when applying losses. Whether **militia** take casualties at all: they contribute combat factors but are absent from the loss-distribution rule ([§14.15](./rules-manual.md#1415-casualties)). If they do take casualties, which Living types and cadre are eligible for the militia draft, and how losses distribute across them, must also be settled. `D-20` settles that an independent colony drafts militia like any other ([§14.13](./rules-manual.md#1413-militia)), so the eligibility and casualty questions above apply to it unchanged. |
| `GAP-40` | **Unimplemented combat features.** Excluding friends and allies from close proximity targeting. Rebel activities described as intended rather than implemented.                                                                   |
| `GAP-41` | **Orbit decay.** The docking-protection rule for ships without space drives is recorded as not implemented.                                                                                                                       |
| `GAP-53` | **Military supplies status.** The handbook's item chart annotates `CSUP` "to be deleted", meaning its assembly and operational status should be re-examined, not that the item is removed (`D-11`). Whether `CSUP` should be an assembly item, what its operating requirements are, and whether rebels consume it as well as soldiers, is unreviewed. Not a blocker and not on the roadmap; the item stands as written until then. |

### 21.6 Systems named but not designed

| ID       | Gap                                                                                                                                                                                                                                                                                                                                                                                            |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-56` | **Rebels and the race holding test.** `D-19` settles the player side: rebels are excluded from it, so a colony whose only soldiers and professionals are rebels goes independent and is held by no player ([§18.2](./rules-manual.md#182-holding)). The race side is untouched. Rebels are population units of their race, and `D-18` counts every type, so as written a race holds a colony through people who have stopped working and are stealing its food — and an independent colony can be held outright by the race whose rebels emptied it. Whether `D-18`'s "counting every type" is deliberate here or merely unexamined must be decided; the type-blindness of the race test is on record as intended, which argues for admitting rebels, while nothing on record shows the rebel case was in view when it was written. Bears on `GAP-09`, which leaves rebel numbers unspecified, and on `D-16`, since captured population arrives with its malcontents. |
| `GAP-27` | **Victory evaluation.** Whether victory is checked every turn; how the game ends; what happens when two conditions are satisfied on the same turn, which the handbook addresses only in passing, in the Diplomacy chapter, with "there can only be one Winner". `H`, the number of habitable planets in the cluster, follows from `GAP-01`, as does whether players are told what `H` is. |
| `GAP-28` | **Markets.** The orders grammar contains `SELL` and `BUY`. No market, price, or trading mechanic exists in any source.                                                                                                                                                                                                                                                                         |
| `GAP-29` | **Espionage.** `SPY` is adopted by `D-04` with no rules. The handbook describes a superspy concept explicitly as not implemented. An espionage mechanic must be designed: what a spy does, where it operates, how it is detected, and how it interacts with police, special agents, and rebels.                                                                                                |
| `GAP-30` | **Beamer.** `BMR` is adopted by `D-04` with a mass, a cost, an operating requirement, and an output of `5000 × TL^2` `MU` beamed. `D-08` places the `Beam` order in the Transfer stage, between `Transfer` and `Pick Up`, which suggests beaming conveys mass rather than damaging a target. What beaming does, what it targets, its range, and whether it draws on transport capacity must still be designed. Two questions are now load-bearing rather than incidental: whether `Beam` conveys **population**, and whether it may target an entity with **no owner**. If both were yes, a ship could beam a single professional into an independent entity and take it without a battle, bypassing the order blockade of [§4.8](./rules-manual.md#48-ownership-and-independence) and the militia defence of `D-20`, and making independence trivially reversible at range by whoever arrives first — the outcome `D-19` exists to prevent. |
| `GAP-31` | **Prototype as a unit.** `PRTO` is adopted by `D-04`. Whether it is a distinct manufacturable unit, or a marker for any item held above the player's `TL` as in [§8.4](./rules-manual.md#84-prototypes), must be decided; the two models are not compatible as written.                                                                                                                                         |
| `GAP-32` | **Cadre model, residual cases.** `D-12` settles the model itself ([§5.2](./rules-manual.md#52-living-types-and-cadre-assignments)). `D-13` settles how a cadre is released and `D-14` separates migration from assignment. Two cases remain. (a) `units.md` scopes `WRKR` to `FACT`, `FARM`, and `MINE`, while [§19.4](./rules-manual.md#194-operating-requirements) also gives laboratories a labor requirement; whether laboratory staff are `WRKR` is undecided. (b) `SPY` has no rules at all (`GAP-29`). |
| `GAP-33` | **Race in order syntax.** `D-15` defines a race and `D-16` settles its statistics and how one is acquired. What remains is the order syntax, which the handbook states three incompatible ways: `Pick Up`'s format carries a `Race ID#` bound to the population unit; `Draft` and `Disband` say a `Race ID#` is mandatory while their formats have no field for it; `Pay` says race IDs are not used at all. Which orders take a race, and where in the syntax, must be settled with the order set (`GAP-51`). |
| `GAP-34` | **Ship designs.** `Set Up` accepts a design ID in place of an item list, and supports a multiplier on a design. The design system is undefined.                                                                                                                                                                                                                                                |
| `GAP-36` | **Glossary.** The handbook references a glossary of terms throughout and contains only the term list, not the definitions.                                                                                                                                                                                                                                                                     |
| `GAP-38` | **`Accept` order.** Proposed but not on the roadmap for implementation (`D-09`). It has no rules and no place in the turn sequence. Recorded so the name is not reused for something else.                                                                                                                                                                                                     |
| `GAP-39` | **`Supply` order.** No longer functional. Fuel resupply to forces in a multi-turn battle therefore has no mechanism.                                                                                                                                                                                                                                                                           |
| `GAP-51` | **Order set.** The order set for this game is not decided. `docs/orders-grammar.txt` describes a different order vocabulary (`BOMBARD`, `RAID`, `SPY`, `SELL`, `BUY`, `COLONIZE`, `PERMIT`, `NEWS`, `MININGCHANGE`) than the handbook's. Until the order set is decided, the grammar file is unusable and [§22](./rules-manual.md#22-order-catalogue) is provisional.                                           |
