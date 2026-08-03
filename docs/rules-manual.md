# Epimethean Challenge — Rules Manual

Normative reference for the game engine and database model.

This document is reference material: it describes the machinery and only describes it. It contains no tutorials, no
strategy, and no worked narrative examples. Illustrative calculations appear only where a formula is otherwise
ambiguous.

---

## 0. About this document

### 0.1 Status

This manual is **normative**. Where an implementation and this manual disagree, this manual is wrong or the
implementation is wrong; resolve the disagreement by amending this manual first.

### 0.2 Sources

| Source                                       | Status                        | Notes                                                                                                                                                                                                  |
|----------------------------------------------|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Player handbook (`docs/player-handbook.pdf`) | Informative                   | Third-party document. Not redistributable. Rules extracted here; the PDF is to be deleted once extraction is accepted.                                                                                 |
| Unit table (`docs/units.md`)                 | Informative, work in progress | Source of the canonical item codes (`D-03`). Its statistics diverge from the handbook in places and are not normative; several are known errors requiring correction. See [§20](#20-source-conflicts). |
| Turn sequence (`docs/turn-sequence.md`)      | **Normative input**           | The design owner's own specification of the turn sequence. Where it and the handbook differ, it governs (`D-08`). Its content is absorbed into [§3](#3-turn-processing-sequence).                       |
| Orders grammar (`docs/orders-grammar.txt`)   | **Not a source**              | Incomplete and describes an order set that does not match the handbook. Excluded until the order set is decided. See [GAP-51](#21-gaps).                                                               |
| Entity model (`docs/entity-model.md`)        | **Not a source**              | Downstream of this manual, not input to it. Records how an implementation may represent ownership and the direction of orders (`D-19`). Not normative; where it and this manual disagree, this manual governs. |
| Home world setup (`docs/home-world-setup.md`) | **Normative**, for the starting state only | Holds the contents of the two starting colonies in seed form, and the arithmetic that closes them (`D-21`). [§1.7](#17-starting-position) fixes the shape of the starting position and governs where the two overlap; the rest of this manual governs unconditionally. That document states a starting state, never a rule. |

No text in this manual is copied from the handbook. All rules are restated.

### 0.3 How to read this manual

- **Facts** are stated as facts: "A mine removes `25 × TL` ore per turn."
- **Gaps** — rules the sources do not supply — are tagged `GAP-nn` and are collected in [§21](#21-gaps). A gap is a
  decision the design owner must make. Implementers must not invent a rule for a gap; they must escalate it.
- **Conflicts** — places where the sources contradict each other — are tagged
  `CONFLICT-nn` and are collected in [§20](#20-source-conflicts). Each conflict names the candidate readings; none is
  authoritative until chosen.

### 0.4 Decisions on record

Decisions taken by the design owner. Each closes a source conflict or an open question. Later decisions append to this
list; entries are never rewritten.

| #      | Decision                                                                                           | Consequence                                                                                                                                                                                                                           |
|--------|----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `D-01` | The game is named **Epimethean Challenge**.                                                        | Closes `CONFLICT-17`. The handbook's "Empyrean Cluster Wars" and the prompt's "Empyrean Challenge" are historical names only.                                                                                                         |
| `D-02` | Tech Levels run **1 through 200**, as in the handbook.                                             | Closes `CONFLICT-04`. `docs/units.md` states 0–10 and is in error; it must be corrected.                                                                                                                                              |
| `D-03` | **Item codes** come from `docs/units.md`; **mechanics and statistics** come from the handbook.     | Closes `CONFLICT-03`, `CONFLICT-07`, and `CONFLICT-13`, all of which are `units.md` statistics that contradict handbook mechanics. Where `units.md` and the handbook agree on a mechanic, the item code is still the `units.md` code. |
| `D-04` | The units `BMR`, `SPY`, `WRKR`, `RBL`, and `PRTO` are **adopted into the game** but have no rules. | They remain in the unit vocabulary. Rules for each must be designed before an engine can implement them. See `GAP-29` through `GAP-32`.                                                                                               |
| `D-05` | One assembled structure unit **encloses** `TL^2 / StructureRatio` volume units, where `TL` is the structure unit's own and `StructureRatio` is the containing entity's. | Closes `CONFLICT-01` in favour of reading B. The handbook's Structure Ratio sentence states the relation backwards; its Structural Requirement paragraph is the same rule stated on the demand side. Settles the Space Available formula and the meaning of negative assembled volume, narrowing `GAP-12`. Enclosure does not depend on structure mass, so `CONFLICT-12` stays open without blocking the entity model. Raises `GAP-52`. |
| `D-06` | **Both structure items are retained.** `STRC` exists because `STRL` may be manufactured only in an orbiting colony. | Closes the question raised as `GAP-52`, which is withdrawn; its number is not reused. The restriction on manufacturing `STRL` ([§7.4](#74-factories-and-manufacturing)) is a load-bearing invariant, not an incidental rule: a player expanding into a new system may found an open surface colony, and that colony can only become self-sufficient by manufacturing `STRC` locally. `STRL` being lighter and cheaper for the same enclosure is intended. |
| `D-07` | `STRC` is the item code for **Structure** and `STRL` for **Light Structure**. `units.md` renames neither item. | Closes `CONFLICT-12`. The rename hypothesis is withdrawn, so `D-03` governs unimpeded and the handbook statistics stand for both items. `units.md` carries `Structure`'s statistics on its `STRL` row and a tenfold rescale of them on its `STRC` row; both rows are in error. See [§20.3](#203-corrections-to-unitsmd). |
| `D-08` | `docs/turn-sequence.md` is **normative design input**. Where it and the handbook differ on the turn sequence, it governs. | Absorbed into [§3](#3-turn-processing-sequence). Adds `Beam` to the Transfer stage, narrowing `GAP-30`; splits the production-stage population changes so that graduations and retirements precede production and births and deaths follow rebel actions; and fixes life support fuel and power consumption at the population-change step, narrowing `GAP-15`. It leaves Produce Output and Send Output empty, confirming `GAP-05`. |
| `D-09` | The `Accept` order is **not in the game**. | Overrides `D-08` on this one point: `turn-sequence.md` listed `Accept` in the Prefire segment and that line is removed. The order is proposed but is not on the roadmap for implementation. `GAP-38` is restated to say so, and the name is reserved rather than reused. |
| `D-10` | A **ship's Structure Ratio is 8**, the same as an orbiting colony's. | Closes `GAP-10`, which is withdrawn from [§21](#21-gaps); its number is not reused. No source supplies a ship ratio, so this is a design decision rather than an extracted rule. Every entity type now has a ratio, which is what `D-05` requires before anything can be sized. A ship and an orbiting colony of the same structure `TL` enclose the same volume per structure unit. |
| `D-11` | **Military supplies (`CSUP`) are retained.** The handbook's "to be deleted" annotation on the item chart is a note to re-examine the item's assembly and operational status, not to remove the item from the game. | `CONFLICT-10` rested on the opposite reading and is deleted from [§20](#20-source-conflicts); it was never a source conflict, and its number is not reused. The follow-through is recorded as `GAP-53`. Soldiers continue to consume 1 per combat round ([§14.11](#1411-ground-combat-units)). |
| `D-12` | **The Cadre model is adopted.** A cadre is an *assignment* of tangible population units, recorded so the engine can report where they are. It is not a kind of being. | Closes the main question of `GAP-32`, which is narrowed to the residual cases. Population units are the only tangible population and are counted once: mass, volume, food, and life support belong to the unit and never to the assignment. `TRNE`, `CNST`, `POL`, `SPAG`, `WRKR`, `RBL`, and `SPY` are cadre; `UEM`, `USK`, `PRO`, and `SOL` are Living types. Pay follows the assignment ([§6.2](#62-consumer-goods-and-pay)). `WRKR` records the direct labor requirements of [§19.4](#194-operating-requirements) being met, and is never an additional requirement. Supplies the rules `D-04` left open for `WRKR` and `RBL`. |
| `D-13` | **Disbanding a cadre returns its population units to the Living type they were assigned from**, available for reassignment. | Closes residual case (b) of `GAP-32`. Reverses the handbook, which returns special agents to Unskilled although they are drafted from Professionals — a permanent downgrade that penalised the player for having used them. Under `D-12` a cadre is only an assignment, so releasing one cannot destroy skill. Special agents now return to Professionals ([§5.2](#52-living-types-and-cadre-assignments), [§17.2](#172-disband)). Arrest is unaffected: rebels arrested by police still become Unemployables. |
| `D-14` | **Category migration and cadre assignment are separate mechanisms.** Migration moves a unit between Living types and changes the Living counts; assignment records what a unit is doing and changes no count. | Closes residual case (a) of `GAP-32`: soldiers are a Living type because drafting one *migrates* it out of Unskilled, while drafting police, constructors, or trainees only *assigns* an unskilled unit that is still counted as unskilled. Migration is automatic except for the draft and disband of soldiers. Restructures [§5.2](#52-living-types-and-cadre-assignments) into a Living table and a cadre table, and sharpens [§17.1](#171-draft) and [§17.2](#172-disband), which described both mechanisms as conversion. |
| `D-15` | **A race is the entire population of one home world at the start of a new game.** Each player begins with exactly one race. The engine assigns each race a sequential `Race ID#`. | Closes most of `GAP-33`, which the handbook left as an undefined glossary term. Matches the backstory, in which each cluster colony was settled by one surviving rebel race. Population units are therefore tracked per race, which is what the `Pick Up` syntax requires. Sequential numbering discloses nothing, because every player already knows how many players and races a game has. `GAP-33` is narrowed to whether races differ in any statistic and how a player comes to hold another race's population. |
| `D-16` | **Races are identical in every statistic**; they differ only in identity. A player comes to hold population of another race **only by capturing another player's ship or colony**. | Closes `GAP-33` except for the order syntax. Capture is the sole route, so a player's holdings are single-race until it first takes an enemy entity, and the race discriminator does no work before then. Captured population transfers with the entity and keeps its race ([§14.16](#1416-invasion-resolution)); race is fixed for the life of the unit and survives any change of owner. A race-based victory condition is planned (`GAP-54`), which is what makes that permanent provenance load-bearing rather than cosmetic. |
| `D-17` | **Victory is decided by holding habitable planets.** A race holds a colony through its resident `SOL` or `PRO` units and a planet through a majority of its colonies; a player holds a planet by holding every colony on it. Solo and Race victory each require holding `floor(H × 0.5) + 1` habitable planets while no rival holds more than `ceil(H × 0.1) + 1`. | Replaces the handbook's Domination victory. **Victory points no longer exist**: the per-100,000 rate, the Habitability Factor cap, and the oldest-colony contention rule are all dropped, and `GAP-27` and `GAP-42` are restated without them. Adapted from an earlier version of the game that used "species" for race and interposed a Faction layer between player and entity; Faction is deferred and reads as Player throughout ([§18.2](#182-holding)). Closes `GAP-54`, which is withdrawn. **Correction on record:** the source wrote both thresholds with `ceil`, which is not a majority — `ceil(C × 0.5) + 1` demands 2 colonies on a 1-colony planet, making single-colony planets unholdable and contradicting the every-colony rule for players, and `ceil(H × 0.5) + 1` makes a one-planet cluster unwinnable. `floor` is used instead and yields a true majority at every size. |
| `D-18` | **A race holds a colony by holding a majority of its population**, `floor(P × 0.5) + 1` of the colony's `P` population units, counting every type. | Replaces the "at least one `SOL` or `PRO` of that race" test taken from the earlier version. A majority is unique, so at most one race holds a colony and no colony is counted toward two races at once ([§18.2](#182-holding)). A colony where no race reaches a majority is held by no race. Race holding is now demographic and type-blind, while player holding still turns on ownership plus a soldier or professional; the asymmetry is intended. |
| `D-19` | **The Faction layer is not adopted into the rules.** A player owns entities directly. Every entity is owned by exactly one player or is **independent**, owned by none. An entity holding no Soldier and no Professional outside the `RBL` cadre is independent: it accepts no orders, keeps everything it holds, and returns to play only when a player places a soldier or professional in it. Victory is counted by **position** — a player's seat in the game, whether or not an account still plays it. `Master / Client` is not in the game. | Closes `GAP-55`, `GAP-49`, and `GAP-42`; all three are withdrawn and their numbers are not reused. Supersedes `D-17`'s reading of Faction as Player by removing the term: the indirection an implementation may want between the agency directing orders and the entity receiving them is an implementation concern, recorded in `docs/entity-model.md`, which is not normative. [§18.2](#182-holding) needs no third holding subject — an independent entity has no owner, so no player holds it, and the planet test already requires **every** colony on the planet, so one independent colony there denies the planet to every player while its population goes on holding for its race under `D-18`. Elimination stops being a trigger needing its own definition and becomes a readable state: a player owning no entity is eliminated, which covers zero population and also a player left holding only unemployables and unskilled. Narrows `GAP-56` to the race test alone. Raises `GAP-57`, the upkeep of independent entities, and extends `GAP-22` with whether an independent entity drafts militia. `Master / Client` follows `D-09`'s pattern: the name is reserved and its gap withdrawn rather than left open, its ground covered by the `Give` order and by out-of-game delegation, which confers no ownership, no diplomatic status, and no holding. |
| `D-20` | **Combat targeting is unrestricted by ownership and by diplomatic status, and an independent colony drafts militia in its own defence.** Any entity may be named as the target of a bombardment or an invasion. No status protects an entity from attack and none is required to attack one, so an ally may be fired on without warning. Independence confers no protection either, and an independent colony resists invasion with militia exactly as an owned one does. | Settles the militia question `D-19` added to `GAP-22`; that gap is restated without it and the rest of it stands. Stated as fact rather than left to silence: no source restricts targeting, and [§14.11](#1411-ground-combat-units) makes soldiers, militia, military robots, and assault craft usable against "any entity", the sole exception being assault weapons, restricted to surface colonies on physical grounds. Recording it matters because [§4.8](#48-ownership-and-independence) closes `Transfer`, `Pick Up`, `Give`, `Load Cargo`, and `Unload` against independent entities, leaving invasion the only route back into play; that route now rests on a stated rule rather than on an absence, and a later decision restricting targeting would make independence permanent. Taking an independent colony is not free: militia are drafted up to half its population ([§14.13](#1413-militia)), so a large one resists in proportion to its size, while an independent ship raises none and falls to any force that reaches it. Professionals are not ground combat units, so invasion delivers soldiers; a professional enters an entity only once a player owns it again. |
| `D-21` | **The contents of the two starting home colonies are fixed.** The home `OPC` opens with 50,000 population units, 350,000 assembled `STRC-1`, 600 `Farms-1`, 600 `Mines-1` in three groups, 1,500 `Factories-1` in two groups, 100 `Laboratories-1`, 1,500 `Power Plants-1`, 10 `Sensors-1`, 200 `Transports-1`, and food and consumer goods stockpiles at the four-turn target. The home `OBC` opens with 1,000 population units, 200,000 assembled `STRC-1`, 1,200 `Life Supports-1`, 150 `Factories-1` building `STRL-1`, and 50 `Transports-1`. The home planet carries three unlimited deposits, one per resource type, at 80% yield. Full tables are in [`home-world-setup.md`](./home-world-setup.md); [§1.7](#17-starting-position) carries the shape of the starting position only. | Closes `GAP-02`, which is withdrawn; its number is not reused. No source supplies any of it: the handbook fixes the shape of the starting position and nothing inside it, and its quick-start page is an unwritten placeholder, so this is a design decision rather than an extracted rule. Every quantity is chosen to make a stated rule close. Both colonies satisfy `D-19` several times over. The `OBC` is viable at Tech Level 1 only because it is in orbit 3: [§7.8](#78-solar-power) gives its life supports and factories solar power, so it burns no fuel, and it needs one `LSU-1` per population unit because `TL^2` is 1. It cannot feed itself at all — `Farms-1` are `OPC`-only and `Farms-2` … `Farms-5` need a Tech Level the player lacks — so it is supplied by transfer every turn by construction, which extends `GAP-57`: were it to go independent it would starve, and no player could feed it. Both colonies are built of `STRC` because `STRL` may be manufactured only in an orbiting colony (`D-06`): seeding the `OBC` with light structure would give it the one item whose existence depends on the entity it is holding up, and the seed has to be reachable from itself. Nothing is lost by it — enclosure is `TL^2 / StructureRatio` for both items on the containing entity's ratio ([§4.2](#42-volume-space-and-structure)) — and the `OBC`'s factory group manufactures the `STRL` it converts to. The same route refounds an orbiting colony later: `STRC` from the `OPC` sets one up, and it makes its own light structure thereafter. `Power Plants-1` make the `OPC` fuel-independent for production, so its mined fuel accumulates against later ship travel. Setup deliberately avoids three open questions rather than resolving them by assumption: no factory group holds work in process, which is the residue of `GAP-12`; no `POL`, `SPAG`, `SPY`, or `RBL` is assigned, so no unstated pay rate is needed (`GAP-58`); and every structure count is chosen so that summed enclosure capacity is an integer, so `GAP-17` does not bite at setup. Two setup facts are stated by exception: the `OPC` controls the home planet from turn 1 although [§16.1](#161-control-planet) otherwise requires control to be ordered, and both colonies are **home colonies**, a term [§15.3](#153-give) used without defining. The starting `Ration` of 100% is within bounds under both readings of `CONFLICT-02`, which is untouched. The residue of `GAP-02` — the home system's planet count, layout, and remaining deposits — is world generation and moves to `GAP-01`. |
| `D-22` | **Stockpiles and cargo hold contents count toward the volume an entity must enclose.** Food and consumer goods held in the population's stockpile occupy enclosed volume exactly as the same goods in Storage Inventory do. The two exemptions in [§4.2](#42-volume-space-and-structure) are the only ones: `Metals` and `Non-Metals` in an `OPC` or `ESC`. | Closes `GAP-12` except for work in process, to which it is narrowed. [§4.2](#42-volume-space-and-structure) already requires structure to enclose *all* of an entity's items, resources, and population and names one exemption; a stockpile is goods the entity holds, held on a separate ledger ([§6.4](#64-stockpiles)) rather than in a separate place. Two rules presuppose it: [§7.2](#72-farms) discards food produced with no Space Available to hold it, and [§7.3](#73-mines) limits fuel extraction to what fits, both of which are only meaningful if commodities consume enclosed volume. Cargo holds were never open: [§4.4](#44-cargo-holds) bounds a hold by the ship's Space Available and charges its contents 50% of normal volume, so `GAP-12`'s listing of them was stale. Work in process is the true residue — no source says when a batch's resources leave Storage Inventory or what volume a partly built batch occupies. The consequence is large and load-bearing for `D-21`: `FOOD` is 3 `VU` per unit and a four-turn stockpile is one food unit per population unit, so the home `OPC`'s food stockpile is 150,000 of its 305,350 `VU` of enclosed contents — three times the volume of the people eating it and half the colony's structure. |
| `D-23` | **The home planet's unlimited deposits yield 80% for `Fuel`, 40% for `Metals`, and 40% for `Non-Metals`.** The starting `OPC` is re-sized to match: mine groups of 150, 450, and 500 `Mines-1`; Factory Group 1 raised to 960 `Factories-1`; 1,750 `Power Plants-1`; and a population of 4,000 `PRO` and 42,500 `USK` within the same 50,000 units. Full tables remain in [`home-world-setup.md`](./home-world-setup.md). | Supersedes `D-21` on the yields and on every quantity above; `D-21` stands as written for everything else and its 80%-across-the-board figure is historical. That figure was chosen because `25 × 0.8` is a whole number, which is convenience rather than evidence. No source gives home-planet yields, but the handbook's one sample survey report shows them varying by resource type, with non-metals lowest in both of its readings, and the starting position consumes more non-metals than metals because `CSGD` costs `0.4 NMTL` against `0.2 METL`. Uniform yields therefore made the scarcer resource the cheaper to win. The consequence is deliberate and visible: halving the yield on the two resources the colony actually consumes takes its mines from 600 to 1,100, of which 950 work metals and non-metals, and their labor is what forces professionals from 3,500 to 4,000. Extraction is unaffected by the change — a mine removes `25 × TL` ore whatever the deposit yields ([§7.3](#73-mines)) — so what moves is the refining loss, and mine count is the only lever a player has against it until Tech Level rises. Every balance still closes with a margin, and the `OBC` is untouched, having no mines and taking its resources by transfer. Space Available in the `OPC` falls from 44,650 to 34,660 `VU` as the extra mines and power plants are enclosed. |

### 0.5 Notation and conventions

| Symbol | Meaning                                                  |
|--------|----------------------------------------------------------|
| `TL`   | Tech Level of the specific unit instance being described |
| `MU`   | Mass Unit                                                |
| `VU`   | Volume Unit (also called Space)                          |
| `TDU`  | Tactical Distance Unit = 10,000 miles                    |
| `LY`   | Light Year                                               |
| `RP`   | Research Point                                           |
| `S/C`  | Ship or Colony; the generic term is **Entity**           |
| `x^2`  | x squared                                                |

**Own, hold, control.** Three distinct relations that the sources all render with the English word "control". They are
never interchangeable in this manual:

| Term        | Relation                                                                | Defined in                            |
|-------------|-------------------------------------------------------------------------|---------------------------------------|
| **Owns**    | A player to an entity. An entity has one owner or none (`D-19`).        | [§4.8](#48-ownership-and-independence) |
| **Holds**   | A player or a race to a colony or a planet. Computed, never ordered.    | [§18.2](#182-holding)                  |
| **Controls** | A surface colony to a planet and its orbit. An exclusive claim, ordered. | [§16.1](#161-control-planet)           |

**Position.** A player's seat in the game. A position is **active** while an account plays it and inactive once that
account leaves; its entities and their ownership are unaffected either way. Positions, not accounts, are counted by the
victory conditions ([§18](#18-victory)).

- Quantities of population are in **population units**; one population unit represents 100 individuals.
- Every unit's statistics are a function of its own `TL`. A `Mines-3` and a
  `Mines-7` are different items that may coexist in the same entity.
- Arithmetic rounding is specified per formula. Where it is not specified, it is a gap (`GAP-17`).

---

## 1. Universe model

### 1.1 Interstellar space

- Space is a three-dimensional integer grid in units of 1 `LY`.
- Interstellar coordinates are written `X,Y,Z` and may be negative.
- Each player's own **home star system** is `0,0,0` in that player's frame of reference. Coordinates reported to a
  player are always relative to that player's home system.
- Games to date use a cube `N` `LY` on a side. `N` is not specified (`GAP-01`).

### 1.2 Star systems

- A star system contains 1 to 5 **stars**.
- Each star is encircled by **orbits 1 through 10**.
- **Orbit 11** encircles the entire star system and is common to all its stars. Orbit 11 is always empty of planets.
- An orbit numbered 1–10 either contains one planet or is empty.
- A star system always has at least one star and always has orbit 11.
- An orbit is addressed by star letter plus orbit number (for example `A1`).

### 1.3 Planets

Planet types:

| Type        | Open colonies                      | Enclosed colonies | Habitability Factor |
|-------------|------------------------------------|-------------------|---------------------|
| Terrestrial | Allowed if Habitability Factor > 0 | Allowed           | May be > 0          |
| Gas Giant   | Not allowed (Habitability 0)       | Allowed           | 0                   |
| Asteroids   | Not allowed (Habitability 0)       | Allowed           | 0                   |

- **Habitability Factor** is an integer property of a planet. It bounds open colony population, farmland, power plants,
  and whether the planet counts toward victory ([§18.3](#183-solo-victory)).
- Every planet carries several **resource deposits**.

### 1.4 Resource deposits

| Property      | Description                                              |
|---------------|----------------------------------------------------------|
| Deposit ID    | Identifier, unique within the planet                     |
| Resource type | `Fuel`, `Metals`, or `Non-Metals`                        |
| Yield         | Percentage of mined ore recovered as refined resource    |
| Quantity      | Ore remaining in the deposit                             |
| Owning entity | The colony whose mine group occupies the deposit, if any |

Some home-planet deposits are unlimited in size. Which ones, and how many deposits a planet has, is not specified
(`GAP-01`).

### 1.5 Tactical space

- Each orbit contains its own tactical coordinate space, written `X/Y/Z`.
- The planet, and every surface colony on it, is at `0/0/0`.
- One tactical coordinate interval is 1 `TDU` = 10,000 miles.
- Tactical coordinates are bounded to the range −999 to 999 inclusive.
- Distance between two entities is computed from their tactical coordinates. The distance metric is not specified
  (`GAP-18`).
- Exactly six tactical coordinate points lie at distance 1 from `0/0/0`. Points at distance 1 from `0/0/0` are at least
  1.414 `TDU` from one another.

### 1.6 Discovery

- A player knows only the star systems on that player's **discovered list**.
- Systems are added to the discovered list by system probes ([§13.4](#134-system-probe))
  and by robot probe vehicles ([§13.6](#136-robot-probe-vehicles)).
- Jump orders may only target discovered systems.

### 1.7 Starting position

This section fixes the shape of the starting position. What the two colonies contain — population by Living type and
cadre, assembled items, Storage Inventory, stockpiles, and structure — is fixed by `D-21` and tabulated in
[`home-world-setup.md`](./home-world-setup.md), which is normative for it.

At game start each player has:

- One star system with one star and several planets, the player's **home system**.
- At least one terrestrial planet with Habitability Factor 25 in orbit 3, the **home planet**.
- Three deposits of unlimited size on the home planet: `Fuel` at 80% yield, `Metals` at 40%, `Non-Metals` at 40%
  (`D-23`). These are the deposits the starting mine groups occupy.
- One Open Surface Colony (`OPC`) on the home planet, at `0/0/0`.
- One Orbiting Colony (`OBC`) in the home planet's orbit, at tactical distance 1 from `0/0/0`.
- Tech Level 1 in every item type, unless the game is configured otherwise (`GAP-03`).
- A single race, comprising the whole of that home world's population (`D-15`).
- No ships.

The number of other planets in the home system, their types and orbits, and their deposits are generated like any other
system (`GAP-01`).

Both starting colonies are **home colonies**: a home colony may never be given away
([§15.3](#153-give)), including after it changes hands. The starting `OPC` controls the home planet and its orbit from
turn 1; this control is seeded and is not ordered, by exception to [§16.1](#161-control-planet).

Both are built of `STRC`. `STRL` may be manufactured only in an orbiting colony (`D-06`,
[§7.4](#74-factories-and-manufacturing)), so it cannot exist before one does.

The `OBC` produces no food. `Farms-1` may exist only in an `OPC` and `Farms-2` … `Farms-5` require a Tech Level the
player does not have, so the `OBC` is fed by transfer from the `OPC` every turn, by construction. It burns no fuel for
life support or manufacturing: it is in orbit 3, so both draw solar power ([§7.8](#78-solar-power)).

---

## 2. Time

- The unit of time is the **turn**, which is one quarter.
- Four turns are one **year**.
- Every order takes effect within the turn it is processed unless the rule for that order says otherwise.
- Jumps and moves take exactly one turn.
- Manufacturing takes exactly four turns.

Turn cadence, order submission, deadlines, and reporting are not specified (`GAP-04`).

---

## 3. Turn processing sequence

Orders are executed by stage, not in the sequence the player wrote them. Within a stage, the sequence is as given below.
Where a stage processes orders in player-written sequence, that is stated explicitly.

| #  | Stage              | Segments / steps                                                         |
|----|--------------------|--------------------------------------------------------------------------|
| 1  | Game Data Cleanup  | Delete zero-value records; remove dead entities; zero accumulator fields |
| 2  | Combat Orders      | See [§3.1](#31-combat-orders-stage)                                      |
| 3  | Permission Orders  | Permission to Colonize; Home Port Change; Diplomacy                      |
| 4  | Disassembly        | Disassemble; Scrap; Junk; Merge; Combine Factory Group                   |
| 5  | Setup              | Define Cargo Hold; Set Up; Add On                                        |
| 6  | Transfer           | Unload Cargo; Transfer; Beam; Pick Up; Load Cargo                        |
| 7  | Draft Orders       | Draft; Disband                                                           |
| 8  | Assembly           | See [§3.2](#32-assembly-stage)                                           |
| 9  | Surveys and Probes | S/C probes only; Survey; Launch Robot Probe                              |
| 10 | Pay Change         | Pay; Ration                                                              |
| 11 | Naming Orders      | Name; Note; Control Planet; Un-Control Planet; Message                   |
| 12 | Ship Travel        | Jump; Move                                                               |
| 13 | Probe              | Orbit probes; system probes                                              |
| 14 | Give               | Give                                                                     |
| 15 | Production         | See [§3.3](#33-production-stage)                                         |
| 16 | Produce Output     | Not specified (`GAP-05`)                                                 |
| 17 | Send Output        | Not specified (`GAP-05`)                                                 |

### 3.1 Combat Orders stage

| Segment            | Steps                                                                           |
|--------------------|---------------------------------------------------------------------------------|
| Prefire            | Dodge; Auto Return Fire; Close Proximity Targeting                              |
| Pre-Maneuver Fire  | Pre-maneuver energy weapon fire; pre-maneuver missile fire; allocate damage     |
| Maneuver           | Undock; Run; Tactical Maneuver; Close; Dock; allocate damage                    |
| Post-Maneuver Fire | After-maneuver energy weapon fire; after-maneuver missile fire; allocate damage |
| Ground Combat      | Withdraw; Defensive Support; Invasion; Offensive Support; cycle ground combat   |

Ordering rules inside the Maneuver segment:

1. `Undock`, `Run`, and `Tactical Maneuver` execute first, in the sequence the player wrote them.
2. `Close` and `Dock` execute afterwards, sorted by ascending tactical speed of the acting ship, so the fastest ship
   moves last. Ships attached by docking are excluded when computing that sort key.

Damage is applied at the end of each fire segment and after the maneuver segment, never mid-segment. All entities in a
fire segment fire before any damage from that segment is applied.

### 3.2 Assembly stage

| Segment                    | Steps                                                                                                                                    |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Order Processing           | Assemble; Expend (research points only); Expend (using prototypes); Factory Group Change; Build Change; Mine Change; Shut Down; Start Up |
| Non-Prototype TL Increases | Apply every Committed Research Buffer that has reached the cost of the next TL, item type by item type                                   |

Within the Order Processing segment, `Assemble` and `Expend` orders execute in the sequence the player wrote them. This
is observable: constructor shortages truncate the assemble list at the point of exhaustion, and prototype expends
consume research points committed by earlier expends in the same turn.

### 3.3 Production stage

Colonies are processed first, then ships.

Per colony, in order:

1. Sum and report professionals used to pilot transports.
2. Collect survey data.
3. Total automation capacity and life support capacity.
4. Population changes: **graduations and retirements**.
5. Production, in order: **Power → Mines → Farms → Laboratories → Factories**.
6. Food consumption.
7. Consumer goods consumption, including ships that use this colony as home port.
8. Rebel actions.
9. Population changes: **births and deaths**. Life supports consume their fuel or power at this step.
10. Statistics update.

Per ship, in order:

1. Sum and report professionals used to pilot transports.
2. Total automation capacity and life support capacity.
3. Population changes: **graduations and retirements**.
4. Farm production.
5. Food consumption.
6. Rebel actions.
7. Population changes: **deaths**. Life supports consume their fuel or power at this step. **Ships have no births.**
8. Statistics update.

Population changes are split across two steps (`D-08`): graduations and retirements are applied before production, so
the turn's new professionals are available to the turn's production, while births and deaths are applied after
consumption and rebel actions.

The order in which colonies (and ships) are selected within the stage is not specified (`GAP-06`).

---

## 4. Entities

An **Entity** (`S/C`) is a ship or a colony. Both hold items, population, resources, and stockpiles.

### 4.1 Entity types

| Code   | Type                    | Location                            | May be created at                         |
|--------|-------------------------|-------------------------------------|-------------------------------------------|
| `OPC`  | Open Surface Colony     | Planet surface                      | Planets with Habitability Factor > 0 only |
| `ESC`  | Enclosed Surface Colony | Planet surface                      | Any planet                                |
| `OBC`  | Orbiting Colony         | Any orbit, with or without a planet | Any orbit                                 |
| `Ship` | Ship                    | Any orbit                           | Any orbit                                 |

- Surface colonies are always at tactical coordinates `0/0/0`.
- An orbiting colony must be within 1 `TDU` of another entity to move goods directly to or from it.
- Colonies never maneuver and never issue maneuver, dock, or travel orders.

### 4.2 Volume, space, and structure

- **Volume** is the space an item or population unit occupies.
- **Space Available** is enclosed volume not yet occupied.
- Assembled `Structure` and `Light Structure` provide enclosed volume; they are the only items that do.
- Assembled structure must enclose the volume of all of the entity's items, resources, and population, **except** that
  in `OPC` and `ESC` colonies,
  `Metals` and `Non-Metals` are stored outside the structure and consume no enclosed volume.
- That exception is the only one (`D-22`). Goods held in the population's stockpiles ([§6.4](#64-stockpiles)) are
  enclosed exactly as the same goods in Storage Inventory are; the stockpile is a separate ledger, not a separate
  place. Cargo hold contents are enclosed at 50% of normal volume and are bounded by the ship's Space Available
  ([§4.4](#44-cargo-holds)). Whether work in process consumes enclosed volume is still open (`GAP-12`).
- Exceeding the structural limit prevents further contents from entering the entity, and can halt factory, mine, and
  farm production.

**Structure Ratio** by entity type:

| Entity type | Structure Ratio          |
|-------------|--------------------------|
| `OPC`       | 1                        |
| `ESC`       | 5                        |
| `OBC`       | 8                        |
| `Ship`      | 8                        |

Ships take the orbiting colony ratio by decision (`D-10`); no source supplies one.

**Enclosure.** One assembled `Structure` or `Light Structure` unit encloses `TL^2 / StructureRatio` volume units, where
`TL` is the tech level of that structure unit and `StructureRatio` is that of the entity it is assembled in (`D-05`).

- **Enclosure capacity** of an entity is the sum of `TL^2 / StructureRatio` over its assembled structure units.
- **Space Available** = enclosure capacity − the volume of the contents that must be enclosed.
- Structure Ratio is a property of the **entity**, not of the structure unit. The same structure unit encloses eight
  times less in an `OBC` than in an `OPC`. Structure moved between entities of different type changes the volume it
  encloses.
- `Structure` and `Light Structure` of the same `TL` enclose the same volume. They differ in mass, build cost, stowed
  volume, and where they may be manufactured (`D-06`, [§7.4](#74-factories-and-manufacturing)).
- The negative assembled volume of structure ([§19.1](#191-assembly-and-volume-model)) has magnitude
  `TL^2 / StructureRatio`. An assembled structure unit is never itself counted among the contents to be enclosed.
- Unassembled structure in Storage Inventory occupies its unassembled volume like any other item and is enclosed like
  any other item.
- Per-unit enclosure may be fractional. Rounding of the summed capacity is `GAP-17`.

Equivalent demand-side statement: an entity's **Structural Requirement** is the total volume of its enclosed contents
times its Structure Ratio, and each assembled structure unit supplies `TL^2` toward that requirement.

Volume enclosed by one assembled structure unit:

| Structure `TL` | `OPC` (ratio 1) | `ESC` (ratio 5) | `OBC` (ratio 8) |
|----------------|-----------------|-----------------|-----------------|
| 1              | 1               | 0.2             | 0.125           |
| 10             | 100             | 20              | 12.5            |
| 200            | 40,000          | 8,000           | 5,000           |

### 4.3 Mass

- Mass drives ship speed, hyper engine lift requirements, combat damage accounting, and control-planet ties.
- Every population unit is 1 `MU`.
- Item mass is given in [§19](#19-unit-characteristics).
- What is included in an entity's total mass — stockpiles, cargo hold contents, attached docked ships — is not fully
  specified (`GAP-13`).

### 4.4 Cargo holds

Ships only.

| Rule                        | Value                                                                                                         |
|-----------------------------|---------------------------------------------------------------------------------------------------------------|
| Created / resized by        | `Define Cargo Hold`                                                                                           |
| Resize restriction          | Only while docked to a colony owned by the ship's owner                                                       |
| Size bounds                 | Not more than the ship's total Space Available; not less than the space currently used by cargo hold contents |
| Access                      | `Load Cargo` and `Unload` only, and only by a colony the ship is docked to                                    |
| Space used by contents      | 50% of normal space requirement (contents are crated)                                                         |
| Contents usable by the ship | No                                                                                                            |
| Permitted contents          | Population units, non-assembly items, unassembled assembly items                                              |
| Population in cargo         | Cryogenically frozen: consume no food, need no life support, receive no pay                                   |
| Damage exposure             | Twice as likely to absorb damage as other contents                                                            |
| Cross-player load/unload    | Always fails                                                                                                  |

On unload, population is revived in the receiving colony: 10% die, and the receiving colony's current death rate is
applied in addition. Revived population cannot be employed on the turn of unload, though statistics report them as
employed.

A colony created this turn cannot unload a ship, because no ship can be docked to it until the following turn.

### 4.5 Storage Inventory

- **Storage Inventory** holds non-assembly items and unassembled assembly items.
- It is distinct from **Stockpiles** ([§6.4](#64-stockpiles)), which belong to the population, and from **assembled
  items**, which are in operation.
- Items listed in a `Set Up` or `Add On` order are drawn from Storage Inventory, never from assembled items.
- Research points accumulate in the Storage Inventory of the colony that produced them and can never leave it
  ([§8.2](#82-research-points)).

### 4.6 Home port

- Every ship has a **home port**, which is a colony.
- The home port pays the ship's crew from its consumer goods.
- A ship's initial home port is the colony that set it up; if a ship set it up, the mother ship's home port.
- Changed with `Home Port Change`; both entities must belong to the same player.
- If the home port is destroyed, captured, or becomes independent, the home port becomes the owning player's
  lowest-numbered colony on the following turn.
- An independent ship has no owning player and so has no home port. Its crew goes unpaid (`GAP-57`).

### 4.7 Entity identity

- Each entity has an `S/C ID#`, allocated at set up but not visible to the player until the following turn's report.
- Lower `S/C ID#` means older.
- Entity names are visible to all players. See [§17.7](#177-name).

### 4.8 Ownership and independence

Entities are the only objects that receive orders. An entity is owned by exactly one player, or by none, in which case
it is **independent** (`D-19`). An entity executes orders from its owner and from no one else; an independent entity
executes no orders at all.

**Independence test.** An owned entity becomes independent when it holds no Soldier and no Professional. Population
assigned to the `RBL` cadre is excluded from the test, so an entity whose only soldiers and professionals are rebels
becomes independent ([§5.7](#57-rebels)). The test is on population type alone, not on quantity: a colony of a million
Unskilled with no professional is independent, and one professional is enough to keep a colony out of independence
however large it is.

**Entering independence.** An entity that fails the test becomes independent immediately, in the stage in which it
fails. Nothing is destroyed or removed. It keeps its items, resources, stockpiles, cargo, population, planetary
control, and `S/C ID#`, and its population keeps its race (`D-16`).

**Leaving independence.** An independent entity becomes the property of the first player to place a Soldier or
Professional in it. Population already inside it cannot do this: a professional that an independent entity produces for
itself, by trainee graduation or soldier retirement, leaves it independent, because no player placed the unit.

Independent entities are not valid targets for `Transfer`, `Pick Up`, `Give`, `Load Cargo`, or `Unload`, so goods and
population cannot be moved into or out of one. Any order requiring a diplomatic status of the target's owner fails
against an independent entity, which has no owner to hold a status. Invasion is therefore the only route by which a
player places population in one, and independence confers no protection against it
([§14.16](#1416-invasion-resolution), `D-20`). An independent colony still drafts militia up to half its population
([§14.13](#1413-militia)), so a populous one is expensive to take rather than free. Professionals are not ground combat
units, so an invasion delivers soldiers; a professional enters an entity only once a player owns it again.

Invasion is the only such route **in the order set as it stands**. `Beam` is undesigned (`GAP-30`) and resolves in the
Transfer stage (`D-08`), which suggests it conveys mass; if it conveys population, a ship could beam a single
professional into an independent entity and take it without a battle, defeating both the blockade above and the militia
defence. Whether `Beam` carries population, and whether it may target an entity with no owner, must be settled when it
is designed.

**Consequences for holding.** An independent entity has no owner, so no player holds it ([§18.2](#182-holding)). A
planet is held by a player only when that player holds every colony on or orbiting it, so a single independent colony
denies the planet to every player. The independent entity's population continues to count toward its race, which may
still hold the colony and the planet.

An independent entity receives nothing from outside and issues no orders, so its food, pay, life support, and
production have no stated source (`GAP-57`).

---

## 5. Population

### 5.1 Common properties

Every population unit, of every type:

| Property                | Value                                                                   |
|-------------------------|-------------------------------------------------------------------------|
| Individuals represented | 100                                                                     |
| Mass                    | 1 `MU`                                                                  |
| Volume                  | 1 `VU`                                                                  |
| Stowable                | No (except crated in a cargo hold, [§4.4](#44-cargo-holds))             |
| Requires                | Food; consumer goods (except Unemployables); life support outside `OPC` |
| Race                    | Exactly one, fixed for the life of the unit                            |

**Races.** A race is the entire population of one home world at the start of a new game (`D-15`). Each player begins
with exactly one race, so at game start the number of races equals the number of players. The engine assigns each race
a sequential `Race ID#`. The numbering is not confidential: every player knows how many players and races a game has,
so a sequential ID discloses nothing.

Population is tracked per race. A `Pick Up` order names the population type and its race together, in the position
`Item-TL` occupies for an item ([§9.3](#93-pick-up)).

Races are identical in every statistic and differ only in identity (`D-16`). A player comes to hold population of
another race only by **capturing another player's ship or colony** ([§14.16](#1416-invasion-resolution)); until a player
takes an enemy entity, everything it owns is its own race. A unit's race never changes, including when its entity
changes owner, because race decides the Race victory ([§18.4](#184-race-victory)).

### 5.2 Living types and cadre assignments

Population is tracked by two independent mechanisms (`D-14`):

- **Category migration** moves a population unit from one Living type to another. The Living counts change. Migration
  is automatic except for the draft and disband of soldiers.
- **Cadre assignment** records what a population unit is doing. No Living count changes. What changes is how many units
  of that type are available for other work.

**Living types.** Every population unit is exactly one of these, always.

| Type          | Migrates in from                                                              | Migrates out to                                                        | Function                                                                                       |
|---------------|-------------------------------------------------------------------------------|------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| Unemployables | Births; combat wounded; surrendered soldiers; injured police; arrested rebels | Unskilled, automatically (`GAP-07`)                                    | None. Cannot work.                                                                             |
| Unskilled     | Unemployables; disbanded soldiers                                             | Soldiers, by `Draft`; Professionals, by trainee graduation             | Labor in farms, mines, laboratories, factories                                                 |
| Professionals | Trainee graduation; soldier retirement                                        | —                                                                      | Skilled labor; pilot transports; train trainees                                                |
| Soldiers      | Unskilled, by `Draft`                                                         | Unskilled, by `Disband`; Professionals, by retirement; Unemployables, by wounding or surrender | Combat; crew energy weapons, energy shields, missile launchers, assault craft, assault weapons |

**Cadre.** An assignment of Living units to a role. Assigning and releasing never changes a Living count.

| Cadre                 | Assigned from               | Assignment ends on                                              | Function                                                             |
|-----------------------|-----------------------------|-----------------------------------------------------------------|----------------------------------------------------------------------|
| Trainees `TRNE`       | Unskilled                   | `Disband`, or graduation, which migrates the unit to Professionals | Become professionals                                                |
| Constructors `CNST`   | Unskilled                   | `Disband`                                                       | Assemble, disassemble, scrap; assist add-on, set up, junk            |
| Police `POL`          | Unskilled                   | `Disband`, or injury, which migrates the unit to Unemployables  | Find and arrest rebels                                               |
| Special Agents `SPAG` | Professionals               | `Disband`                                                       | Reduce discontent; assist police against rebels                      |
| Workers `WRKR`        | Professionals and Unskilled | Reassignment                                                    | Staff a farm, mine, or factory ([§19.4](#194-operating-requirements)) |
| Rebels `RBL`          | Any Living type             | Arrest, which migrates the unit to Unemployables; or death      | None. Never work. See [§5.7](#57-rebels).                            |
| Spies `SPY`           | Not specified (`GAP-29`)    | Not specified                                                   | Not specified                                                        |

How cadre are counted (`D-12`):

- A population unit is counted **once**. Its 1 `MU`, 1 `VU`, food, and life support requirement belong to the unit, never
  to the assignment. A cadre adds no mass, volume, food, or consumer goods of its own.
- A unit keeps its Living type while assigned. Drafting 100 unskilled as police leaves the unskilled count unchanged and
  reduces the unskilled available for assignment by 100.
- Pay follows the **assignment**, at the rates in [§6.2](#62-consumer-goods-and-pay).
- An assigned unit is unavailable for any other work.
- `WRKR` records the population filling the direct labor requirements of [§19.4](#194-operating-requirements). It is a
  record of those requirements being met, never an additional requirement on top of them.
- Disbanding a cadre ends the assignment and frees the units for reassignment (`D-13`). No unit is lost or downgraded by
  being assigned and released. Disbanding **soldiers** is not this: it is a migration back to Unskilled.

### 5.3 Derived and transient population states

| State       | Nature                                                                                        |
|-------------|-----------------------------------------------------------------------------------------------|
| Malcontents | A tally, reported as a percentage of total population. Not an allocation of units.            |
| Rebels      | Population units assigned to the `RBL` cadre, drawn from any type ([§5.2](#52-living-types-and-cadre-assignments)). Do not work. |
| Militia     | Temporary. Drafted only during an invasion; returns to the source types when the battle ends. |

### 5.4 Population flow rules

| Rule                     | Value                                                                                                        |
|--------------------------|--------------------------------------------------------------------------------------------------------------|
| Births                   | Occur in colonies only, never in ships. All births enter Unemployables.                                      |
| Birth rate bounds        | Not less than 0.25% and not more than 10% of the colony's total population per turn                          |
| Birth rate falls as      | Standard of Living rises; total population approaches the sustainable maximum                                |
| Sustainable maximum      | `Habitability Factor × 10,000,000` in `OPC`; Life Support Capacity in `ESC` and `OBC`                        |
| Unemployable → Unskilled | Automatic, at an engine-determined rate (`GAP-07`)                                                           |
| Trainee graduation       | 5% of trainees per turn, provided professionals are training them                                            |
| Training ratio           | 1 professional per 100 trainees                                                                              |
| Soldier retirement       | 5% of soldiers per year (every 4 turns) become professionals                                                 |
| Combat wounded           | Become Unemployables. The share of casualties that are wounded rather than dead is not specified (`GAP-22`). |
| Police injury            | Police injured during encounters with rebels become Unemployables. The injury rate is not specified (`GAP-09`). |

Injury is not a general mechanism. Only two paths produce it, and only soldiers and police are exposed to them:
combat casualties ([§14.15](#1415-casualties)) and rebel encounters ([§5.7](#57-rebels)). Surrender and arrest also
produce Unemployables, but neither is an injury.

The exact birth rate function, the unemployable-to-unskilled draw rate, and the retirement scheduling within the
four-turn year are gaps (`GAP-07`, `GAP-08`).

### 5.5 Death rate

- A single **Death Rate** percentage applies to every population type in an entity and is applied to each type
  separately.
- The death rate is driven by Life Support Capacity, except in `OPC` colonies, where the planet's Habitability Factor
  drives it instead.
- The death rate increases for all open colonies on a planet when
  `Habitability Factor × 10,000,000` is less than the total population of all open colonies on that planet.
- Population in excess of the entity's Life Support Capacity dies immediately.
- Insufficient fuel to operate the required life supports increases the death rate.
- On any turn the operating Life Support Capacity is not exceeded, the death rate is reduced sharply.

The death rate formula and its magnitudes are not specified (`GAP-08`).

### 5.6 Malcontents

Malcontents increase when:

1. The general population is underpaid (Standard of Living below 1) or back pay exists. The increase scales with back
   pay as a proportion of total population.
2. The general population is underfed (below 0.25 food per unit per turn).
3. Starvation is occurring. This produces a larger increase than (2).

Malcontents decrease from special agent activity and from a Standard of Living above 1.0.

The formulas are not specified (`GAP-09`).

### 5.7 Rebels

| Rule           | Value                                                                                                                                                                       |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Source         | Units drawn from any population type                                                                                                                                        |
| Growth drivers | Quantity of malcontents; the three malcontent conditions in [§5.6](#56-malcontents); recruitment by existing rebels                                                         |
| Behavior       | Consume food like any population; never work; steal consumer goods and food from the population stockpiles                                                                  |
| Removal        | Arrest by police, assisted by special agents. Arrested rebels become Unemployables. Some rebels are killed during arrest; some police are injured and become Unemployables. |

Rebel activity escalates with rebel numbers and malcontent percentage:

| Activity       | Effect                                                                   |
|----------------|--------------------------------------------------------------------------|
| Demonstrations | None mechanically; a warning signal only                                 |
| Strikes        | Partial or complete work stoppage in randomly selected production groups |
| Riots          | Destruction of randomly selected units, plus deaths                      |

Rebels are excluded from the independence test, so an entity whose every soldier and professional is assigned to `RBL`
becomes independent and passes out of its owner's hands ([§4.8](#48-ownership-and-independence), `D-19`). Whether
rebels count toward the **race** holding test is still open (`GAP-56`).

Thresholds, rates, magnitudes, durations, theft quantities, police effectiveness, and special agent effectiveness are
all unspecified (`GAP-09`). The handbook describes rebel activities as intended rather than implemented (`GAP-40`).

---

## 6. Basic needs

### 6.1 Food

| Rule                               | Value                                                                                 |
|------------------------------------|---------------------------------------------------------------------------------------|
| Full ration                        | 0.25 food per population unit per turn                                                |
| Full ration corresponds to         | Ration Rate 100%                                                                      |
| Minimum consumption when available | 0.0625 (1/16) food per population unit per turn                                       |
| Starvation trigger                 | Less than 1/16 food per population unit available                                     |
| Starvation deaths                  | Approximately 30% of the starving population per turn, each turn starvation continues |

Ration bounds are stated inconsistently (`CONFLICT-02`): one statement caps ration orders at 100% and floors them at
25%; the `Ration` order description permits values above 100% but caps the stockpile at a 5-turn supply and consumption
at 0.25 per unit per turn.

### 6.2 Consumer goods and pay

Default pay rates, in consumer goods per population unit per turn:

| Population type | Rate  | Composition              |
|-----------------|-------|--------------------------|
| Unskilled       | 0.125 | base                     |
| Trainees        | 0.125 | unskilled rate           |
| Soldiers        | 0.250 | base                     |
| Professionals   | 0.375 | base                     |
| Constructors    | 0.500 | unskilled + professional |
| Special Agents  | 0.625 | professional + soldier   |
| Unemployables   | 0     | not paid                 |

- Pay orders are standing orders and persist until changed.
- Constructor and special agent rates are derived from the rates they are composed of; whether they track changes to
  those rates is not specified (`GAP-43`).
- A ship's crew is paid by the ship's home port colony.
- The table is complete as the sources give it. Pay follows the assignment (`D-12`), and no rate is stated for the
  `WRKR`, `POL`, `RBL`, or `SPY` cadre (`GAP-58`).
- Standard of Living is the pay rate times 4 ([§6.5](#65-standard-of-living)), so at these rates an entity reaches 1.0
  only if its average pay across its whole population, unemployables included, is 0.25.

### 6.3 Back pay

- Consumer goods owed but not delivered accrue as **Back Pay**.
- Back pay increases malcontents; the increase grows with back pay as a proportion of total population.
- Whether back pay is ever cleared or decays is not specified (`GAP-43`).

### 6.4 Stockpiles

- The population of each colony maintains its own **stockpile** of food and of consumer goods, separate from the
  player's Storage Inventory.
- The population targets a 1-year (4-turn) supply of each.
- The full rationed food amount and the full paid consumer goods amount are added to the stockpiles.
- The population consumes up to 25% of each stockpile per turn.
- Food consumption may exceed 25% of the stockpile when required to prevent starvation, but never exceeds 0.25 food per
  population unit per turn.
- The population will not accept food that would take its stockpile above a 5-turn supply.

### 6.5 Standard of Living

```
StandardOfLiving = (consumer goods consumed this turn / total population) × 4
```

Standard of Living drives malcontent increase and decrease, and modifies the birth rate.

### 6.6 Life support

| Rule                                | Value                                                                       |
|-------------------------------------|-----------------------------------------------------------------------------|
| Capacity per assembled life support | `TL^2` population units                                                     |
| Fuel consumption                    | `1 × TL` fuel per turn                                                      |
| Required in                         | `ESC`, `OBC`, and ships                                                     |
| Not required in                     | `OPC`                                                                       |
| Solar substitution                  | Life supports in an `OBC` in orbits 1–5 consume solar power instead of fuel |
| Over capacity                       | Unsupported population dies immediately                                     |
| Under-fuelled                       | Death rate increases                                                        |

---

## 7. Production

### 7.1 Common production rules

- Farms, mines, laboratories, and factories must be **assembled** before they operate.
- Each requires labor and fuel or power per turn.
- **Automation** substitutes for unskilled labor ([§7.6](#76-automation)).
- Shortages of fuel, power, or labor reduce or stop production, in proportion to the severity of the shortage.
- Solar power substitutes for fuel for farms, factories, laboratories, and life supports in an `OBC` in orbits 1–5.
- Production runs in the order given in [§3.3](#33-production-stage).

The algorithm that allocates scarce labor, fuel, and power across an entity's production groups is not specified
(`GAP-14`, `GAP-15`).

### 7.2 Farms

| Rule                            | Value                                                                                     |
|---------------------------------|-------------------------------------------------------------------------------------------|
| Grouping                        | All farms of one `TL` in an entity form one **Farm Group**; one group per `TL` per entity |
| Output, `Farms-1`               | 25 food per farm per turn                                                                 |
| Output, `Farms-2` … `Farms-200` | `5 × TL` food per farm per turn                                                           |
| Labor                           | 1 professional + 3 unskilled per farm, at any `TL`                                        |
| Fuel, `Farms-1`                 | 0.5 per turn                                                                              |
| Fuel, `Farms-2` … `Farms-5`     | `0.5 × TL` per turn                                                                       |
| Fuel, `Farms-6` … `Farms-200`   | `1 × TL` per turn                                                                         |
| Shut down / start up            | `Shut Down` and `Start Up` orders; farms are assembled in production mode                 |
| Overflow                        | Food produced with no Space Available to hold it is discarded                             |

Placement restrictions:

| Farm `TL` | Permitted location                                       |
|-----------|----------------------------------------------------------|
| 1         | `OPC` only                                               |
| 2–5       | Colonies in orbits 1–5 only. Not orbits 6–11. Not ships. |
| 6–200     | Any entity                                               |

Quantity limits:

| Scope                                  | Limit                                                          |
|----------------------------------------|----------------------------------------------------------------|
| `Farms-1` in open colonies on a planet | `Habitability Factor × 100,000`                                |
| `Farms-2` … `Farms-200`                | No limit beyond Space Available and the placement restrictions |

### 7.3 Mines

| Rule                 | Value                                                                                                     |
|----------------------|-----------------------------------------------------------------------------------------------------------|
| Grouping             | Mines are assembled into a **Mine Group** targeted at one specific deposit                                |
| `TL` uniformity      | All mines in a group share one `TL`                                                                       |
| Ore extracted        | `25 × TL` ore per mine per turn                                                                           |
| Refined yield        | `ore × deposit yield%` is added to the resource type in Storage Inventory                                 |
| Labor                | 1 professional + 3 unskilled per mine                                                                     |
| Fuel                 | `0.5 × TL` per mine per turn                                                                              |
| Processing order     | Mine Group 1 first, then ascending group number                                                           |
| Shut down / start up | `Mine Change` order. Shut-down mines stay assembled, produce nothing, consume no fuel, employ no workers. |

Quantity limits:

| Scope                              | Limit                      |
|------------------------------------|----------------------------|
| Mines per deposit                  | 1,000,000                  |
| Mines per mine group               | 1,000,000                  |
| Mine groups per colony             | 6                          |
| Mine groups per deposit            | 1, held by one colony only |
| Mine groups per colony per deposit | 1                          |

Location:

- Mines operate only in surface colonies (`OPC`, `ESC`), because deposits exist only on planets.
- Unassembled mines may be held in the Storage Inventory of any entity.

Space effects:

- If Space Available is insufficient to hold the fuel that would be mined, mines extract only what fits.
- Metals and non-metals are stored outside the colony structure, so Space Available does not limit their extraction in
  `OPC` and `ESC`.

### 7.4 Factories and manufacturing

| Rule               | Value                                                                                                       |
|--------------------|-------------------------------------------------------------------------------------------------------------|
| Grouping           | Factories are assembled into **Factory Groups**; all factories in a group share one `TL`                    |
| Product            | One item-`TL` at a time per group                                                                           |
| Not manufacturable | Food, resources, population                                                                                 |
| Restricted product | `Light Structure` may only be manufactured in an orbiting colony (`D-06`)                                   |
| Location           | Colonies only. Factory groups cannot be assembled in ships; unassembled factories may be carried in a ship. |
| Labor              | 1 professional + 3 unskilled per factory                                                                    |
| Fuel               | `0.5 × TL` per factory per turn                                                                             |
| Groups per colony  | 40                                                                                                          |
| Batch duration     | 4 turns (1 year). No item can be manufactured faster.                                                       |
| Destination        | Finished items enter the colony's Storage Inventory                                                         |

Capacity:

```
factoryOutputPerTurn  = 5 × TL MU per factory
factoryOutputPerYear  = 20 × TL MU per factory
batchQuantityPerYear  = (20 × TL × factoryCount) / massPerItem
```

`docs/units.md` states factory output as `20 × TL` mass **per turn**, four times this figure, and is in error
(`CONFLICT-03`, closed by `D-03`).

**Work In Process.** A batch advances one quarter per turn through four states. The whole batch advances together; a
quarter of the batch is not finished each turn.

| Turn | State                                |
|------|--------------------------------------|
| n    | Batch started                        |
| n+1  | WIP 1/4                              |
| n+2  | WIP 1/2                              |
| n+3  | WIP 3/4                              |
| n+4  | Finished, added to Storage Inventory |

A group that starts a new batch every turn reaches steady state in the fifth turn, finishing one batch per turn
thereafter.

**Shortage handling.** The engine processes manufacturing group by group in ascending Factory Group number. Within a
group it processes WIP 3/4, then WIP 1/2, then WIP 1/4. When resources, labor, fuel, or power run short, the engine
stops processing manufacturing for that turn at that point. On a later turn, if the shortage is resolved, processing
resumes from Factory Group 1 and WIP 3/4; if not, it does not resume.

**Product changes.** A factory group manufactures its assigned item-`TL` until a
`Build Change` order or a `Disassemble` order intervenes. `Build Change` may set the product to a specific item-`TL`, to
`Nothing`, to `Shut Down`, or to
`Start Up`.

| Setting     | Existing WIP             | New batches                                          |
|-------------|--------------------------|------------------------------------------------------|
| Item-`TL`   | Continues                | Started at that item-`TL`                            |
| `Nothing`   | Continues to completion  | None                                                 |
| `Shut Down` | Frozen; does not advance | None. No resources consumed, no population employed. |
| `Start Up`  | Resumes                  | Resumes                                              |

Specifying a `TL` of 200, or any `TL` above the player's current knowledge, makes the group build at the highest `TL`
the player has achieved for that item, and to track future increases automatically.

### 7.5 Laboratories

| Rule                 | Value                                                        |
|----------------------|--------------------------------------------------------------|
| Output               | `0.25 × TL` research points per laboratory per turn          |
| Location             | Colonies only                                                |
| Labor                | 3 professionals + 1 unskilled per laboratory                 |
| Fuel                 | `0.5 × TL` per laboratory per turn                           |
| Shortages            | Laboratories produce as much as fuel, power, and labor allow |
| Shut down / start up | `Shut Down` and `Start Up` orders                            |

### 7.6 Automation

| Rule            | Value                                                                 |
|-----------------|-----------------------------------------------------------------------|
| Substitutes for | Unskilled labor in farms, mines, laboratories, and factories          |
| Capacity        | `TL^2` unskilled equivalents per assembled automation unit            |
| Assignment      | Pooled across the entity; not assigned to a specific production group |
| Fuel / power    | None                                                                  |

Automation does not substitute for professionals.

### 7.7 Power plants

| Rule                         | Value                                                        |
|------------------------------|--------------------------------------------------------------|
| Location                     | `OPC` only, on habitable planets                             |
| Functioning limit per planet | `100,000 × Habitability Factor`                              |
| Output                       | `1 × TL` power per turn per plant, for immediate consumption |
| Storage                      | Power cannot be accumulated or stored                        |
| Inputs                       | No resources, no labor                                       |

Power may substitute for fuel in: life supports, farms, factories, mines, laboratories, sensors.

Power may **not** substitute for fuel in: hyper engines, space drives, energy shields, energy weapons.

### 7.8 Solar power

Farms, factories, laboratories, and life supports in an **orbiting colony** in orbits 1–5 consume solar power instead of
fuel. Solar power is unmetered.

Whether solar power is available to surface colonies or ships in orbits 1–5 is not specified (`GAP-16`).

---

## 8. Technology

### 8.1 Tech Levels

| Rule           | Value                                                                                                    |
|----------------|----------------------------------------------------------------------------------------------------------|
| Range          | `TL-1` through `TL-200`                                                                                  |
| Starting level | `TL-1` in every item type, unless the game is configured otherwise                                       |
| Advancement    | One `TL` at a time, in consecutive order; multiple steps may be taken in one turn                        |
| Scope          | A player's `TL` is knowledge: the highest `TL` of an item type that player can manufacture               |
| Possession     | A player may hold and use items at a `TL` above the player's own knowledge, acquired by capture or trade |
| Immutability   | An item's `TL` is fixed when it is manufactured and never changes                                        |
| Factory `TL`   | Does not limit the `TL` of what a factory can build; it only sets the factory's output capacity          |

`docs/units.md` states the `TL` range as integers 0–10 and is in error (`CONFLICT-04`, closed by `D-02`).

### 8.2 Research points

| Rule         | Value                                                                                       |
|--------------|---------------------------------------------------------------------------------------------|
| Produced by  | Laboratories, in colonies only                                                              |
| Stored in    | The Storage Inventory of the producing colony                                               |
| Transferable | Never. Cannot be transferred, picked up, loaded, given, or carried. Cannot exist in a ship. |
| Consumed by  | `Expend` orders only                                                                        |

Cost to advance an item type from `TL n` to `TL n+1`:

```
cost(n → n+1) = 1,000,000 × n
```

| From → To | Cost            | Cost with 20 prototypes |
|-----------|-----------------|-------------------------|
| 1 → 2     | 1,000,000       | 200,000                 |
| 2 → 3     | 2,000,000       | 400,000                 |
| 3 → 4     | 3,000,000       | 600,000                 |
| n → n+1   | `1,000,000 × n` | `200,000 × n`           |

Advancing from `TL a` to `TL b` costs the sum of the individual steps.

### 8.3 Committed Research Buffer

- Each player has one **Committed Research Buffer** per item type.
- An `Expend` order without prototypes moves research points from the acting colony's Storage Inventory into that item
  type's buffer.
- Research points in a buffer are committed to that item type permanently and cannot be recalled or redirected.
- Buffered points that do not complete the next `TL` persist across turns.
- Multiple colonies may contribute to the same item type in the same turn, each with its own `Expend` order.

### 8.4 Prototypes

A **prototype** is an ordinary item held at a `TL` above the player's current knowledge for that item type. It is
consumed by the `Expend` order.

| Rule                     | Value                                                                                                                                           |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Maximum per expend order | 20                                                                                                                                              |
| Discount per prototype   | 4% of the research point cost of reaching the prototype's `TL`                                                                                  |
| Maximum discount         | 80%                                                                                                                                             |
| Origin                   | Prototypes cannot have been manufactured by the expending player. They must come from capture, transfer from a friend, or pick up from an ally. |
| Colony restriction       | All prototypes for one item type in one turn must come from a single colony                                                                     |
| Research point sourcing  | Research points may come from any number of colonies and from the Committed Research Buffer                                                     |
| Failure                  | If the available prototypes and research points do not reach the target `TL`, the order aborts                                                  |
| Transportability         | Prototypes are ordinary items and can be transported between entities                                                                           |

### 8.5 Expend processing

Within the Assembly stage ([§3.2](#32-assembly-stage)):

1. **Order Processing segment**, in player-written order:
    - Non-prototype `Expend`: move the stated research points to the buffer.
    - Prototype `Expend`: check the acting colony's Storage Inventory and the item type's Committed Research Buffer for
      enough research points to complete the increase to the prototype's `TL`. If sufficient, apply the increase and
      consume the prototypes.
2. **Non-Prototype TL Increases segment**: walk every Committed Research Buffer, item type by item type, and apply every
   `TL` increase the buffer can fund. Any remainder stays in the buffer.

Consequence: prototype expend orders should be written after the research-only expend orders that fund them.

---

## 9. Transport, transfer, and cargo

### 9.1 Transports

| Rule               | Value                                                                            |
|--------------------|----------------------------------------------------------------------------------|
| Assembly           | Non-assembly item; operates from Storage Inventory                               |
| Crew               | 1 professional per 10 transports                                                 |
| Range              | 1 `TDU`                                                                          |
| Inter-orbit travel | Not possible. Transports move goods only within one orbit.                       |
| Life support       | Self-contained. Population conveyed in transports needs no food or life support. |
| Combat exposure    | Transports are never fired upon during an invasion                               |

Capacity:

```
transferCapacity = transportCount × TL^2 × 20     MU per turn
combatCapacity   = 3 × TL^2                        MU per turn per transport
```

Transports being transferred or picked up convey themselves and consume no capacity. The sending entity retains full use
of them on the turn they are transferred; the receiving entity cannot use them until the following turn.

Whether the per-turn transfer capacity is a single pool shared by transfers, pick ups, surveys, set ups, add-ons,
junking, and combat conveyance is not specified (`GAP-23`). Invade orders explicitly do not consume transfer capacity.

### 9.2 Transfer

| Rule               | Value                                                                                                 |
|--------------------|-------------------------------------------------------------------------------------------------------|
| Transports used    | The sending entity's                                                                                  |
| Permitted contents | Population units, unassembled assembly items, non-assembly items                                      |
| Cross-player       | Fails unless the receiving player is designated `Friend` or `Ally` and has reciprocated appropriately |
| Independent entity | Never a valid sender or receiver ([§4.8](#48-ownership-and-independence))                             |
| Failure causes     | Insufficient transport capacity, fuel, or professionals; insufficient Space Available at the receiver |
| Processing         | In player-written sequence, one by one, halting at a shortage                                         |

`CONFLICT-05`: the `Transfer` order requires both entities to be at the same tactical coordinates in the same orbit; the
maneuver chapter states transfers may be performed at a tactical distance of 1 or less.

**Jettison.** `Transfer` accepts `Jettison` as its destination. Jettisoned items cease to exist and consume no transport
capacity. Population may never be jettisoned.

### 9.3 Pick Up

Identical to `Transfer` except that the **receiving** entity provides the transports. All entities involved must be in
the same orbit and within 1 `TDU`.

`CONFLICT-06`: the `Pick Up` order states that a pick up from another player's entity always fails; the diplomacy
chapter states that mutual `Ally` status enables pick ups.

### 9.4 Load Cargo and Unload

| Rule               | Value                                                                             |
|--------------------|-----------------------------------------------------------------------------------|
| Actor              | The colony, always                                                                |
| Ship state         | Docked to that colony                                                             |
| Capacity cost      | One fifth (0.2×) of the capacity an equivalent `Transfer` or `Pick Up` would cost |
| Cross-player       | Always fails, regardless of diplomatic status                                     |
| Independent entity | Never a valid actor or target ([§4.8](#48-ownership-and-independence))            |
| Permitted contents | Population units, unassembled assembly items, non-assembly items                  |

See [§4.4](#44-cargo-holds) for cargo hold semantics and revival losses.

---

## 10. Construction

### 10.1 Constructors

| Operation   | Constructor cost                                                        | Loss                     |
|-------------|-------------------------------------------------------------------------|--------------------------|
| Assemble    | 1 constructor per 500 `MU` assembled                                    | none                     |
| Disassemble | 1 constructor per 500 `MU` disassembled                                 | 10% of items             |
| Scrap       | 1 constructor per 300 `MU` scrapped                                     | 30% of mass              |
| Set Up      | 1 constructor per 500 `MU` assembled                                    | none                     |
| Add On      | 1 constructor per 500 `MU` assembled                                    | none                     |
| Junk        | Uses the junked entity's constructors first, then the ordering entity's | not specified (`GAP-25`) |
| Merge       | None required                                                           | none                     |

When constructors are exhausted, orders are processed in player-written sequence and processing halts at the point of
exhaustion.

### 10.2 Assemble

- Moves items from Storage Inventory into operation.
- Factories may be assembled only if a factory group number or a producible item-`TL` is specified.
- Mines may be assembled only in surface colonies, and only onto a named deposit.
- Factories and laboratories may not be assembled in ships.

### 10.3 Disassemble

- Returns items to Storage Inventory, with 10% lost as waste.
- Single items disassembled are lost entirely; no fractions are retained.
- Disassembling factories or mines requires the group number.
- A factory group emptied of factories keeps its number and its WIP through the Assembly stage, so factories of any `TL`
  may be assembled into it in the same turn to continue production. If none are, the WIP is lost.
- Factory groups with no factories are removed at the end of the turn.

### 10.4 Scrap

- Converts unassembled or non-assembly items into resources.
- 30% of mass is lost. Each constructor therefore recovers at most 210 `MU`.
- Recovered resources are in the same proportions as the items' build costs.

### 10.5 Junk

- Disassembles an entire entity into the ordering entity's Storage Inventory.
- The junked entity ceases to exist.
- Both entities must belong to the same player and be within 1 `TDU`.
- The junked entity's transports and constructors are used first; the ordering entity's are used if needed.
- The junked entity's population joins the ordering entity's.
- Life supports recovered from the junked entity are **not** automatically reassembled in the destination.

### 10.6 Merge

- Merges two ships owned by the same player, at the same tactical coordinates in the same orbit.
- The absorbed ship ceases to exist; its items and population move to the surviving ship.
- The merged ship takes the higher of the two death rates.
- Requires no transports and no constructors.

### 10.7 Set Up

Creates a new entity.

| Rule                   | Value                                                                  |
|------------------------|------------------------------------------------------------------------|
| Abort condition        | The order includes no structure units                                  |
| Assembly order         | Items are assembled or transferred in the sequence listed in the order |
| Recommended first item | `Structure` or `Light Structure`                                       |
| Halt condition         | Shortage of constructors, fuel, transport capacity, or Space Available |
| Completion             | All assembly and transfer work in a set up happens in one turn         |
| New ships              | Are docked to the entity that set them up                              |
| ID visibility          | The new `S/C ID#` is allocated now, reported next turn                 |

Placement:

| Entity type created | Placement rule                                                                                                                                                                                    |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Surface colony      | The setting-up ship must be within 1 `TDU` of the planet (`0/0/0`)                                                                                                                                |
| Orbiting colony     | Created at the setting-up entity's location, except that if that location is `0/0/0` it is created 1 `TDU` away. A surface colony therefore cannot create an orbiting colony at its own location. |
| Ship                | Docked to the setting-up entity                                                                                                                                                                   |

Quantity restrictions:

| Scope                                                            | Limit                                         |
|------------------------------------------------------------------|-----------------------------------------------|
| Colonies of one type, one player, one tactical location          | 1                                             |
| Ships per `Set Up` order at one location                         | 99                                            |
| Orbiting colonies of one player suppliable from a surface colony | 6 (the six points at distance 1 from `0/0/0`) |
| Orbiting colonies at identical tactical coordinates              | More than one, if owned by different players  |

Constructor handling:

- Constructors listed in the order do as much assembly as they can and then remain in the new entity, whether or not
  they were all needed.
- Shortfalls are covered by constructors from the setting-up entity, which return to it when done.
- If the order lists no constructors, all work is done by the setting-up entity's constructors, which all return.
- Items that cannot be assembled for lack of constructors remain in the setting-up entity and consume no transport
  capacity.

Transport handling:

- Transports listed in the order remain in the new entity.
- Surplus listed transports may be used by the setting-up entity for other work in the same turn.
- Shortfalls are covered by transports from the setting-up entity, which return.
- Items that cannot be conveyed remain in the setting-up entity.

Assembly semantics:

- Non-assembly items go into the new entity's Storage Inventory immediately.
- Unassembled assembly items are assembled in the new entity and do not enter Storage Inventory, **except** mines,
  factories, and (in ships) farms.
- Appending the unassembled flag leaves an item in Storage Inventory instead.
- Mines are assembled only if a deposit ID or deposit resource type is given; they begin mining the following turn.
- Factories are assembled only if a product is specified; they begin manufacturing the following turn.
- `Farms-1` are assembled only in an `OPC`. `Farms-2` … `Farms-5` are assembled only in colonies in orbits 1–5.
- Population units listed come from the setting-up entity's population and enter the new entity's population groups.
- A ship design ID may be substituted for an item list; the design system is not specified (`GAP-34`).

### 10.8 Add On

Adds items, resources, and population to an **existing** entity, using the ordering entity's transports, constructors,
and inventory.

| Rule               | Value                                                                                                                          |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------|
| Range              | Within 1 `TDU` of the target                                                                                                   |
| Cross-player       | Requires the target's owner to be set to `Ally` by the acting player                                                           |
| Quantity limit     | None, beyond transport capacity, constructors, and Space Available                                                             |
| Failure causes     | Shortage of constructors, transport fuel, transport pilots, transport capacity, or Space Available                             |
| Structural effect  | Adding assembled structure increases the target's Space Available by `TL^2 / StructureRatio` per unit, using the target's Structure Ratio ([§4.2](#42-volume-space-and-structure)) |
| Unassembled option | Assembly-type items may be left unassembled in the target's Storage Inventory                                                  |
| Actor              | Need not be the entity that originally set the target up                                                                       |
| Group targeting    | Factories, mines, and farms may be added to an existing factory group, mine group, or farm group instead of creating a new one |

Added constructors and transports add their capacity to the target immediately, except to the extent the add-on itself
consumes them; only the excess is available to the target this turn.

All `Set Up` assembly rules apply to `Add On`.

### 10.9 Factory Group Change

One order performs three distinct operations:

| Operation | Effect                                                                                                                                                      |
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Renumber  | Changes a group's number. Group number determines the order in which groups receive workers and resources under shortage; lower numbers are supplied first. |
| Split     | Moves a stated quantity of factories into a new group number. All WIP stays with the original group.                                                        |
| Move      | Moves factories into another existing group. Both groups must be the same factory `TL`.                                                                     |

A vacated group number becomes available for reuse, subject to order sequence.

### 10.10 Combine Factory Group

Merges two factory groups.

| Mode             | Requirement                                                   | Effect                                                                            |
|------------------|---------------------------------------------------------------|-----------------------------------------------------------------------------------|
| Full combine     | Same factory `TL`, same product item-`TL`, same WIP item-`TL` | Factories and WIP move to the absorbing group; the absorbed group number is freed |
| WIP-only combine | Same WIP item-`TL`; factory `TL` may differ                   | Only WIP moves; the absorbed group's factories stay in place                      |

The WIP-only mode may be restricted to specific quarters (1/4, 1/2, 3/4) of WIP.

### 10.11 Mine Change

| Mode      | Effect                                                                                                                                   |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|
| Redeploy  | Moves a stated quantity of mines from one mine group to a different deposit. With no quantity, the whole group moves.                    |
| Shut Down | The group stays assembled but produces no ore, extracts no resources, consumes no fuel, and employs no workers. A quantity may be given. |
| Start Up  | Resumes a shut-down group. A quantity may be given.                                                                                      |

### 10.12 Shut Down and Start Up

| Item type    | Shut down / start up mechanism                      |
|--------------|-----------------------------------------------------|
| Farms        | `Shut Down` / `Start Up` orders                     |
| Laboratories | `Shut Down` / `Start Up` orders                     |
| Factories    | `Build Change` with the shut down / start up option |
| Mines        | `Mine Change` with the shut down / start up option  |

Shut-down items stay assembled, require no fuel, and employ no population.

---

## 11. Travel

### 11.1 Hyper engines

| Rule                  | Value                                                                     |
|-----------------------|---------------------------------------------------------------------------|
| Purpose               | Interstellar `Jump` and inter-orbit `Move`                                |
| Jump range            | `sqrt(TL) × 3` `LY`                                                       |
| Lift capacity         | `1045 × TL` `MU` per engine                                               |
| Minimum count         | A ship with fewer than 100 hyper engines cannot move or jump, at any size |
| Fuel                  | 40 fuel per `LY` travelled                                                |
| Crew                  | 1 professional per 100 engines                                            |
| Mixed `TL`            | Permitted. The highest `TL` engines are used first.                       |
| Range with mixed `TL` | Set by the **lowest** `TL` engine actually needed to lift the ship        |
| Engines charged       | Only those needed to lift the ship consume fuel and require crew          |
| Self-mass             | Hyper engine mass counts toward the mass being lifted                     |
| Partial travel        | Never. If lift, fuel, or crew is insufficient, the whole order fails.     |

`docs/units.md` gives hyper engine range as `sqrt(TL) + 4` `LY` and is in error (`CONFLICT-07`, closed by `D-03`).

### 11.2 Jump

| Rule          | Value                                                              |
|---------------|--------------------------------------------------------------------|
| Scope         | Star system to star system only                                    |
| Origin        | Any orbit                                                          |
| Destination   | Orbit 11 of the target system                                      |
| Duration      | 1 turn                                                             |
| Fuel distance | The actual distance in `LY`                                        |
| Target        | Must be a discovered star system                                   |
| Docking       | A jumping ship undocks automatically from anything it is docked to |

### 11.3 Move

| Rule          | Value                                                             |
|---------------|-------------------------------------------------------------------|
| Scope         | Orbit to orbit within one star system                             |
| Duration      | 1 turn                                                            |
| Fuel distance | 0.1 `LY`, always                                                  |
| Docking       | A moving ship undocks automatically from anything it is docked to |

### 11.4 Arrival distance

Both `Jump` and `Move` accept an arrival distance band, measured from `0/0/0` of the destination orbit:

| Band   | Distance        |
|--------|-----------------|
| Close  | 5 to 10 `TDU`   |
| Medium | 10 to 50 `TDU`  |
| Long   | 50 to 100 `TDU` |

The distribution within a band, and the choice of direction, are not specified (`GAP-18`).

### 11.5 Travel with docked ships

- Ships docked **to** the travelling ship are carried with it.
- The travelling ship's hyper engines are used first.
- Docked ships owned by the **same player** contribute their hyper engines' lift if needed.
- Docked ships owned by **other players** contribute mass but no lift.
- If combined lift is insufficient, the order fails.

---

## 12. Maneuver

### 12.1 Space drives

| Rule                  | Value                                 |
|-----------------------|---------------------------------------|
| Thrust                | `3000 × TL^2` per assembled drive     |
| Crew                  | 1 professional per 100 drives         |
| Fuel                  | `1 × TL` per maneuver at 100% speed   |
| Dodge fuel            | 10% of the normal maneuver rate       |
| Maximum fuel per turn | `1.1 × TL` (full maneuver plus dodge) |
| Power substitution    | Not permitted; fuel only              |

### 12.2 Speed

```
speed = floor(total thrust / mass)     TDU per turn
```

- Total thrust counts only assembled space drives that have fuel and crew.
- A colony's speed is always 0.
- A ship with ships docked to it computes speed from its own thrust and the combined mass of itself and everything
  docked to it. Docked ships never contribute thrust, regardless of owner.

### 12.3 Orbit stability

- A ship needs at least one assembled space drive, with fuel and crew, to hold a **stable orbit**.
- Without one, the ship is in a **decaying orbit** with a 50% chance per turn of terminating, destroying the ship.
- A ship connected by docking to a ship with space drives, or to a colony, is not at risk.

`CONFLICT-08`: one statement says empty orbits do not cause decay; another says decay applies in any orbit whether or
not it contains a planet. The handbook also records the docking-protection rule as not implemented (`GAP-41`).

### 12.4 Maneuver orders

| Order               | Effect                                                                                                 |
|---------------------|--------------------------------------------------------------------------------------------------------|
| `Tactical Maneuver` | Move toward stated tactical coordinates in the current orbit, as far as speed and fuel allow           |
| `Run`               | Move as far from a named entity as speed and fuel allow                                                |
| `Close`             | Move to the target ship's coordinates, or to a stated distance from it, as far as speed and fuel allow |
| `Dock`              | Close with the target and dock to it                                                                   |
| `Undock`            | Undock from whatever the ship is docked to                                                             |
| `Dodge`             | Reserve a percentage of speed for evasion. Standing order.                                             |

`Dodge` reserves its percentage of speed; only the remainder is available for other maneuvers. `Dodge` is a standing
order, cancelled by a `Dodge 0%` order. A
`Dock` order negates `Dodge` for that turn.

### 12.5 Docking

Definitions:

| Term                   | Meaning                                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Dock`                 | The order, or the act of executing it                                                                                                                                            |
| `Docked To`            | The relationship held by the ship that issued the `Dock` order. Only ships can be docked to something. A colony is never docked to anything, but may be docked to **by** a ship. |
| `Connected by Docking` | The transitive closure of docking relationships; potentially a chain of entities                                                                                                 |

Effects:

| Situation                                                                     | Effect                                                                                                 |
|-------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| Acting ship ordered to `Close`, `Tactical Maneuver`, `Run`, `Move`, or `Jump` | Automatically undocks from whatever it was docked to                                                   |
| Ships docked **to** the acting ship                                           | Do not undock; they are carried, adding their mass                                                     |
| Bombardment defense                                                           | All entities connected by docking combine energy shields, energy weapons, and anti-missiles in defense |
| Bombardment damage, ship target                                               | Distributed proportionally across all ships in the connected group                                     |
| Bombardment damage, colony target                                             | Absorbed entirely by the colony; connected ships take none                                             |
| Damage propagation through a colony                                           | Blocked. A colony severs damage sharing between the ship groups docked to it.                          |
| Invasion                                                                      | Troops moving between docked entities need no transports and no assault craft                          |
| Collision                                                                     | Ships never collide. There is no ramming.                                                              |

---

## 13. Reconnaissance

### 13.1 Sensors

| Rule               | Value                                                                   |
|--------------------|-------------------------------------------------------------------------|
| Probe capacity     | `1 × TL` probes per assembled sensor; summed over all assembled sensors |
| Fuel               | `0.05 × TL` per sensor                                                  |
| Power substitution | Permitted                                                               |

Capacity is consumed per probe as listed below.

### 13.2 Orbit probe

| Rule      | Value                                              |
|-----------|----------------------------------------------------|
| Cost      | 1 probe capacity per orbit probed                  |
| Own orbit | Free and automatic every turn for every entity     |
| Timing    | Performed after ship travel                        |
| Wildcards | The star, the orbit number, or both may be `ALL`   |
| Scope     | Orbits of the star system the probing entity is in |

Reported per orbit: planet type; and per entity present, the log base 10 of its mass, its `S/C ID#`, its name, and its
tactical coordinates. For the probing entity's own orbit, habitability is also reported and mass is reported to one more
significant digit.

Mass is reported as a magnitude: 0 for 0–9, 1 for 10–99, 2 for 100–999, and so on.

Entity ownership is reported as "owned by aliens" unless the owner has designated the probing player as `Friend` or
`Ally`.

### 13.3 S/C probe (intensive probe)

| Rule           | Value                                                                                                                                                                                     |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cost           | 1 probe capacity per entity probed                                                                                                                                                        |
| Range          | Probing and probed entity must be in the same star system, evaluated before ship travel                                                                                                   |
| Precondition   | The target must appear in a current orbit probe                                                                                                                                           |
| Automatic case | A ship that did not move or jump this turn, and any colony, automatically intensive-probes any ship arriving in its orbit, if it has spare probe capacity and the fuel to run its sensors |

Reported: log base 10 of mass, population, assembled structure, Space Available, and the six most massive item **types**
with each type's percentage of total mass. Item `TL` is not reported.

### 13.4 System probe

| Rule              | Value                                   |
|-------------------|-----------------------------------------|
| Cost              | 1 probe capacity per point of magnitude |
| Maximum magnitude | 10                                      |
| Range             | Magnitude in `LY`                       |
| Timing            | Performed after ship travel             |

Reported: the planet type in every orbit of every star of the probing entity's current system; and the coordinates and
distances of every star system within
`magnitude` `LY` of it. Coordinates are reported relative to the probing player's home system.

### 13.5 Survey

| Rule               | Value                                                              |
|--------------------|--------------------------------------------------------------------|
| Actor              | An entity in the same orbit as the planet and within 1 `TDU` of it |
| Duration           | 1 turn                                                             |
| Transport capacity | 200 `MU`                                                           |
| Fuel               | 1                                                                  |
| Crew               | Professionals as required by the transports' `TL`                  |

Reported per deposit: deposit ID, owning entity ID, resource type, yield percentage, ore quantity. Reported per planet:
habitability factor, quantity of assembled `Farms-1`, total population of all open colonies.

### 13.6 Robot probe vehicles

A robot probe vehicle (`RPV`) performs a probe or survey without sensors and without moving the entity. Each `RPV` is
expended by use, regardless of `TL`. If
`RPV`s of several `TL`s are present, the lowest `TL` is used.

Range by mission:

| Mission      | Maximum distance                                    | Required `TL` for a given distance |
|--------------|-----------------------------------------------------|------------------------------------|
| System probe | `3 × sqrt(TL)` `LY`                                 | `(distance / 3)^2`                 |
| Orbit probe  | `2 × sqrt(TL)` `LY`                                 | `(distance / 2)^2`                 |
| S/C probe    | `1 × sqrt(TL)` `LY`                                 | `distance^2`                       |
| Survey       | Must be in the target star system (orbit 11 counts) | —                                  |

Additional rules:

- `RPV`s launch **before** move and jump orders.
- A system probe by `RPV` travels to the target system and then executes a probe of magnitude up to the lesser of the
  ordered magnitude, the `RPV`'s `TL`, and 10.
- An `RPV` used for an S/C probe attaches a near-massless component to the target. That component sends an orbit probe
  every turn it survives, and an S/C probe with roughly 33% probability per turn.
- The attached component's chance of being discovered and removed each turn is
  `1 / TL`.
- One `RPV` survey order may survey many planets in the system, consuming one
  `RPV` per planet.

---

## 14. Combat

**Targeting.** Any entity may be named as the target of a bombardment or an invasion order. Ownership does not restrict
targeting and neither does diplomatic status: no status protects an entity from attack, and none is required to attack
one. A player may fire on a `Friend` or an `Ally` without warning, and the attack does not itself change the status,
since [§15.1](#151-diplomatic-status) states exhaustively how each status is established and combat is not among the
ways. [§15.2](#152-rights-conferred) governs cooperation only. An entity owned by no player is targetable on the same
terms, and independence confers no protection ([§4.8](#48-ownership-and-independence), `D-20`).

The constraints on attacking are physical rather than political: sensors ([§14.9](#149-sensor-requirements-for-firing)),
range and delivery ([§14.11](#1411-ground-combat-units)), and the bar on a surface colony firing energy weapons at
another surface colony ([§14.7](#147-energy-weapons)).

### 14.1 Execution sequence

See [§3.1](#31-combat-orders-stage). Player order-writing sequence does not affect combat resolution except where
stated.

### 14.2 Standing combat orders

| Order                         | Effect                                                                                                                                                                                           | Cancellation                   |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| `Dodge %`                     | Reserves that percentage of speed for evasion                                                                                                                                                    | `Dodge 0%`                     |
| `Auto Return Fire %`          | Fires that percentage of weapons at any entity that fires at it **and causes damage**. Attacks that do no damage do not trigger it.                                                              | `Auto Return Fire 0%`          |
| `Close Proximity Targeting %` | Fires that percentage of weapons at each ship attempting to dock with it. Fires once per attacker. Resolved in the maneuver segment. Does not fire at ships owned by the player being docked to. | `Close Proximity Targeting 0%` |

An entity never fires more weapons than are required to destroy the target.

Excluding friends and allies from close proximity targeting is not implemented (`GAP-40`).

### 14.3 Bombardment orders

Four bombardment orders exist: pre-maneuver energy weapon fire, pre-maneuver missile fire, after-maneuver energy weapon
fire, after-maneuver missile fire.

| Parameter           | Meaning                                                                         |
|---------------------|---------------------------------------------------------------------------------|
| Target              | Target entity ID                                                                |
| Percentage          | Percentage of the relevant weapons to fire                                      |
| Target category     | Optional; colonies only                                                         |
| Distance abort      | Optional maximum tactical distance; the order aborts if the target is beyond it |
| Avoid friendly fire | Optional; suppresses fire at entities the firer is docked to or docked by       |

Commitment percentage applies equally to each `TL` of missile launcher and energy weapon the entity has assembled.

Firing at an entity activates the energy shields and anti-missiles of every entity connected to it by docking, before
the round of fire resolves.

### 14.4 Target categories

| # | Category                                                                                                     |
|---|--------------------------------------------------------------------------------------------------------------|
| 1 | Population (population types selected at random)                                                             |
| 2 | Non-assembly and unassembled items (Storage Inventory)                                                       |
| 3 | Assembled items, excluding factory groups and mine groups                                                    |
| 4 | Mine groups                                                                                                  |
| 5 | Factory groups                                                                                               |
| 6 | Military units: assault craft, assault weapons, military robots, soldiers                                    |
| 7 | Assembled weaponry and munitions: energy weapons, energy shields, missile launchers, anti-missiles, missiles |

Target categories may be specified only against colonies. Against a ship, or with no category specified, damage is
allocated randomly.

`CONFLICT-09`: the effect of specifying a category is stated two ways — as a 50% increase in the chance of hitting items
in that category, and as total damage reduced to 80% with the named category absorbing four times more damage
proportionally.

### 14.5 Missiles and missile launchers

| Rule          | Value                                                         |
|---------------|---------------------------------------------------------------|
| Rate of fire  | 1 missile per launcher per fire phase; 2 fire phases per turn |
| `TL` matching | A launcher fires only missiles of its own `TL`                |
| Damage        | `100 × TL^2` `MU` per missile that lands                      |
| Range         | Same orbit only                                               |

```
divisor = ((distance + speed/10) / sensorTL) / missileTL
hits    = missilesFired / max(1, divisor)
```

- `sensorTL` is the highest `TL` among the firing entity's assembled sensors.
- The `speed/10` term applies only when the target has a `Dodge` order in effect.
- A colony's speed is 0.

### 14.6 Anti-missiles

| Rule                 | Value                                                                               |
|----------------------|-------------------------------------------------------------------------------------|
| Use                  | Defensive only, automatic, never ordered                                            |
| Launchers            | Fired from assembled missile launchers of the same `TL`                             |
| Available per attack | Equal to the count of assembled same-`TL` missile launchers in the defending entity |
| Effect of a hit      | Destroys one incoming missile, regardless of missile `TL`                           |

```
hitPct       = (antiMissileTL / missileTL) × 0.9
antiMissiles = ceil(incomingMissiles / hitPct)
```

The defender fires only as many anti-missiles as are needed to destroy the incoming volley. Anti-missiles have no effect
against energy weapons.

### 14.7 Energy weapons

Offense:

```
energyFired  = 10 × TL^2 per weapon fired
divisor      = max(1, (distance + speed/10) / sensorTL)
massDestroyed = (energyFired / divisor) − shieldsAtTarget
```

| Rule          | Value                                                                    |
|---------------|--------------------------------------------------------------------------|
| Fuel          | `4 × TL` per firing                                                      |
| Range         | Same orbit only                                                          |
| Line of sight | A surface colony may never fire energy weapons at another surface colony |
| Dodge term    | `speed/10` applies only when the target has `Dodge` in effect            |

Defense against missiles — automatic, unlimited by other firing:

```
hitPct        = (energyWeaponTL / missileTL) × 0.2
weaponsFiring = ceil(remainingMissiles / hitPct)
```

Energy weapons fire at each volley of missiles that anti-missiles did not destroy. Each hit destroys one missile.

### 14.8 Energy shields

| Rule               | Value                                                                                                 |
|--------------------|-------------------------------------------------------------------------------------------------------|
| Use                | Defensive only, against energy weapons                                                                |
| Deflection         | `10 × TL^2` energy units per shield per attack                                                        |
| Frequency          | Operates once per incoming attack. Two attackers firing twice each means four operations in one turn. |
| Fuel               | `10 × TL` per firing                                                                                  |
| Selection          | Highest `TL` shields are used first                                                                   |
| Effect vs missiles | None                                                                                                  |

### 14.9 Sensor requirements for firing

| Firing entity | Target   | Sensor required             |
|---------------|----------|-----------------------------|
| Ship          | Anything | At least 1 assembled sensor |
| Colony        | Ship     | At least 1 assembled sensor |
| Colony        | Colony   | None                        |

### 14.10 Damage allocation

| Rule              | Value                                                                                                                                                                                                                                       |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Timing            | End of each fire segment and after the maneuver segment                                                                                                                                                                                     |
| Unit              | Mass Units destroyed                                                                                                                                                                                                                        |
| Colony target     | Only the bombarded colony is damaged                                                                                                                                                                                                        |
| Ship target       | All ships connected to it by docking are damaged, proportionally                                                                                                                                                                            |
| Method            | Establish the ratio of damage to total mass; cycle through Storage Inventory, stockpiles, factory groups, and mine groups; each target category receives a randomly generated share until the total is allocated. Up to 4 iterative passes. |
| Multiplier, ships | A ship takes 10 times the mass damage a colony takes from the same hit                                                                                                                                                                      |
| Lower exposure    | Population and military items                                                                                                                                                                                                               |
| Higher exposure   | Cargo hold contents (twice the normal rate)                                                                                                                                                                                                 |

Because allocation is random, small fragments of a category may survive; this is less likely the further damage exceeds
the target's mass.

The per-category weights, the random distribution, and the pass logic are not specified (`GAP-19`).

### 14.11 Ground combat units

| Unit           | Combat Factors | Delivery                              | Usable against        |
|----------------|----------------|---------------------------------------|-----------------------|
| Soldier        | 1              | Transports, assault craft, or docking | Any entity            |
| Militia        | 1              | n/a (defensive only)                  | Any entity            |
| Military Robot | `2 × TL`       | Transports, assault craft, or docking | Any entity            |
| Assault Weapon | `2 × TL^2`     | Transports only                       | Surface colonies only |
| Assault Craft  | `10 × TL`      | Self-propelled                        | Any entity            |

| Rule                                     | Value                                                                                               |
|------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Crew per assault craft or assault weapon | 1 soldier, or 1 military robot equivalent                                                           |
| Military robot crewing capacity          | `2 × TL` assault craft and/or assault weapons                                                       |
| Assault craft speed                      | `5 × TL`                                                                                            |
| Assault craft range                      | `TL` `TDU`                                                                                          |
| Assault craft fuel                       | Consumed when sent into battle and each turn the battle continues                                   |
| Soldier supply                           | 1 combat supply per soldier per combat round (`GAP-53`)                                             |
| Transport combat range                   | 1 `TDU`                                                                                             |
| Transport combat capacity                | `3 × TL^2` `MU` per transport per turn                                                              |
| Transport exposure                       | Transports are not fired upon; they drop troops at 1 `TDU` or less and return                       |
| Invading a ship with assault craft       | Assault craft speed must exceed the target ship's speed, unless the invader is docked to the target |

Soldiers and military robots are assumed to carry small arms; no such item exists.

If there are insufficient assault craft, excess troops go by transport if the target is within 1 `TDU` and combat
transport capacity is available; otherwise they do not go. If there are insufficient troops to crew them, excess assault
craft and assault weapons do not go.

### 14.12 Anti-invasion energy weapon fire

If the invader and its supporters are not docked to the target, the target automatically fires its available energy
weapons at the incoming force.

```
invadersHit = (energyWeaponTL / invadingItemTL) × 0.2
```

Damage priority: highest `TL` assault weapons, then highest `TL` assault craft, then military robots, then soldiers.
Soldiers are treated as `TL` 1. One soldier or military robot equivalent is destroyed for each assault craft or assault
weapon destroyed.

### 14.13 Militia

If an invaded colony's combat factors do not match the invader's, the colony drafts militia: up to half its total
population, or enough to match the invader's combat factors, whichever is less. Militia cannot use assault weapons or
assault craft. Militia return to their source population types when the battle ends.

An **independent** colony drafts militia on the same terms (`D-20`). The draft is automatic and needs no order, so
having no owner does not prevent it, and a large independent colony resists in proportion to its population
([§4.8](#48-ownership-and-independence)).

### 14.14 Surrender check

If one side's combat factors exceed the other's by more than 6 to 1, the weaker side surrenders. This is checked for
both invaders and defenders, including their supporters.

### 14.15 Casualties

```
defenderLossesCF = attackerCF × randomInteger(1..5) / 10
attackerLossesCF = defenderCF × randomInteger(1..5) / 10
```

Both are computed from the pre-round combat factors and applied simultaneously.

Converting combat factor losses to unit losses:

```
lossRatio = combatFactorsLost / sideTotalCombatFactors
unitsLost = floor(lossRatio × unitCount)      per unit type
```

Fractions are dropped.

The handbook applies this to four unit types only: military robots, assault weapons, soldiers, and assault craft.
**Militia are excluded**, although they contribute combat factors to the side's total. Read literally, militia raise a
defender's combat factors and dilute its loss ratio without ever taking a casualty, and no population unit other than a
soldier can be wounded in ground combat. Whether that is intended is `GAP-22`.

### 14.16 Invasion resolution

| Condition                                                           | Result                                                                                                                                                                         |
|---------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Defending troops eliminated                                         | The invaded entity becomes the property of the owner of the **largest** invading force. Supporters of the invader retire from the field.                                       |
| Capture                                                             | 65% of the captured entity's malcontents become loyal; 65% of its loyal population become malcontents                                                                          |
| Primary attacker or defender surrenders, withdraws, or is destroyed | That side's supporters withdraw automatically, unless additional enemy supporting forces are present, in which case they continue to fight for the victor against those forces |
| Neither side eliminated                                             | The battle continues into the following turn                                                                                                                                   |
| Multiple simultaneous invaders                                      | One invader wins control. The others continue invading until they order a withdrawal. Supporters remain as long as invaders remain.                                            |

A captured entity's population transfers with it and keeps its race (`D-16`, [§5.1](#51-common-properties)). Capture is
the only way a player comes to hold population of a race other than its own.

**Independent entities.** An independent entity holds no soldiers by definition
([§4.8](#48-ownership-and-independence)), so it has no standing defending troops. An independent **colony** is not
undefended for that reason: it drafts militia like any other, up to half its population
([§14.13](#1413-militia), `D-20`), and a populous one is expensive to take. An independent **ship** raises no militia,
colonies alone drafting them, and falls to any force that reaches it. Either way the invader becomes the owner under
the first row, which is how a player places population in one.

How "largest invading force" is measured is not specified (`GAP-22`).

### 14.17 Support and withdrawal

| Order               | Effect                                                                     |
|---------------------|----------------------------------------------------------------------------|
| `Defensive Support` | Sends troops and weapons to help defend a named entity against invasion    |
| `Offensive Support` | Sends troops and weapons to help every invader of a named defending entity |
| `Withdraw`          | Removes all of the ordering entity's forces from a named battle            |

| Rule                   | Value                                                                                                            |
|------------------------|------------------------------------------------------------------------------------------------------------------|
| Withdraw timing        | All withdrawals resolve before any invade or support order                                                       |
| Withdraw requirement   | A destination entity within 1 `TDU` with Space Available. Without one, the order is ignored.                     |
| Withdraw cost          | None. No transports and no fuel.                                                                                 |
| Forced withdrawal      | Supporters of an invader withdraw when that invader withdraws                                                    |
| Nowhere to withdraw to | The force surrenders to the owner of the entity it is in                                                         |
| Automatic return       | When combat ends, troops return to the entity that sent them, at no cost                                         |
| Sender gone            | Troops return to their owner's largest colony at that location, or largest ship if the owner has no colony there |
| No destination at all  | Defensive supporters join the entity they were defending; offensive supporters surrender                         |

Support may come from entities owned by other players; those forces fight for the entity named in the support order.

Fuel for assault craft may be listed in an invade or support order; otherwise it is drawn automatically from the
ordering entity's Storage Inventory. Excess fuel stays with the troops for later turns. Support forces consume fuel only
if an invasion actually occurs. Transports and their fuel are never listed in these orders; they are assigned
automatically.

---

## 15. Diplomacy

### 15.1 Diplomatic status

Status is set per player, per direction, and is not automatically symmetric.

| Status       | Established by                                                           |
|--------------|--------------------------------------------------------------------------|
| (none)       | Default                                                                  |
| Acquaintance | Automatic, both directions, when either player sends the other a message |
| Friend       | Set unilaterally in the diplomacy tool                                   |
| Ally         | Set unilaterally in the diplomacy tool; effective only when reciprocated |

### 15.2 Rights conferred

| Right                                           | Requirement                                                               |
|-------------------------------------------------|---------------------------------------------------------------------------|
| Transfer goods to your entities                 | The other player has set you to `Friend` or `Ally`                        |
| See your entities identified as yours in probes | The other player has set you to `Friend` or `Ally`                        |
| Give entities to each other                     | Mutual `Friend` or better, plus a receiver entity in the same star system |
| Add On to your entities                         | Mutual `Ally`                                                             |
| Pick Up from your entities                      | Mutual `Ally` (`CONFLICT-06`)                                             |
| Load / Unload another player's ship             | Never permitted, at any status                                            |

Reciprocation rules:

- `Ally` counts as `Friend` for all purposes.
- `Friend` never counts as `Ally`.
- Only `Give` requires reciprocity of `Friend`; the other friend-level rights do not.
- The ally-level rights require reciprocal `Ally`.

There is no limit on the number of friends or allies.

Diplomacy orders take effect in the Permission Orders stage and are in force for every later stage of the same turn.

A `Master / Client` status is **not in the game** (`D-19`). The handbook described it as under consideration; it is not
on the roadmap, and the name is reserved rather than reused.

An independent entity has no owner and therefore holds no diplomatic status in either direction. Every right in the
table above fails against one ([§4.8](#48-ownership-and-independence)).

### 15.3 Give

| Rule                 | Value                                                                                   |
|----------------------|-----------------------------------------------------------------------------------------|
| Effect               | Transfers ownership of an entity to another player                                      |
| Requirement          | Mutual `Friend` or `Ally` status on the same turn                                       |
| Location requirement | The receiving player must have an entity in the same star system                        |
| Prohibited           | A home colony may never be given away, even if its original owner no longer owns it     |
| Independent entity   | Can neither be given nor receive ([§4.8](#48-ownership-and-independence))               |
| Stage                | Give stage, after ship travel                                                           |

What accompanies a given entity — its population, cargo, docked ships, home port assignments — is not specified
(`GAP-25`).

### 15.4 Messages

`Message` sends text from one entity to another. Both entities must be in the same star system, evaluated before ship
travel. Sending or receiving a message establishes `Acquaintance` status in both directions.

---

## 16. Planetary control

### 16.1 Control Planet

| Rule                 | Value                                                                                                    |
|----------------------|----------------------------------------------------------------------------------------------------------|
| Actor                | Surface colonies only. An orbiting colony can never control a planet or orbit.                           |
| Scope of control     | The planet and the orbit it is in                                                                        |
| Precondition         | No other surface colony currently controls the planet                                                    |
| Earliest issue       | The turn after the set up that created the colony                                                        |
| Tie break            | If two or more surface colonies attempt control on the same turn, the colony with the greatest mass wins |
| Automatic on set up  | No. Control must be ordered explicitly.                                                                  |
| Transfer on conquest | Capturing the controlling surface colony transfers control to the new owner                              |
| Release              | `Un-Control Planet`, issued by the controlling colony                                                    |

Effect: other players cannot establish surface colonies on the planet, or orbiting colonies in that orbit, without
permission.

A colony that becomes independent keeps the planetary control it held ([§4.8](#48-ownership-and-independence)).
`Un-Control Planet` is an order, and an independent colony issues none, so that control persists until the colony is
captured or destroyed and the planet stays closed to colonisation meanwhile.

### 16.2 Permission To Colonize

| Rule              | Value                                                                                                                  |
|-------------------|------------------------------------------------------------------------------------------------------------------------|
| Actor             | The surface colony that controls the planet/orbit                                                                      |
| Grantee           | A specific ship belonging to another player                                                                            |
| Validity          | The turn it is issued only                                                                                             |
| Not required when | The planet/orbit is uncontrolled, or the setting-up player already has a colony there                                  |
| Empty orbits      | Cannot be controlled, so never require permission                                                                      |
| Persistence       | Once a player has colonized under permission, no further permission is needed for more colonies at that location       |
| Revocation        | Not possible                                                                                                           |
| Limit             | Any number of players may colonize a location under permission, subject to the one-colony-of-each-type-per-player rule |

---

## 17. Miscellaneous orders

### 17.1 Draft

Processed in the Draft Orders stage. The order does one of two different things (`D-14`):

| Drafting into                            | Effect                                                                                              |
|------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Soldiers                                 | **Migration.** The unskilled count falls and the soldier count rises.                               |
| Constructors, police, trainees           | **Assignment.** The unskilled count is unchanged; that many unskilled become unavailable for other work. |
| Special agents                           | **Assignment.** The professional count is unchanged; that many professionals become unavailable.     |

### 17.2 Disband

The inverse of `Draft`, and likewise two different things (`D-14`). Processed in the Draft Orders stage.

| Disbanding                                     | Effect                                                                                           |
|------------------------------------------------|--------------------------------------------------------------------------------------------------|
| Soldiers                                       | **Migration.** The soldier count falls and the unskilled count rises.                            |
| Constructors, police, trainees, special agents | **Assignment ends.** No Living count changes; the units become available for reassignment (`D-13`). |

Disbanding costs nothing and destroys nothing. A disbanded special agent is an available professional again, not a
demoted one.

Both orders reference a `Race ID#` that is described as mandatory. Multiple races per player are never defined
(`GAP-33`).

### 17.3 Pay

| Form                     | Effect                                                                                         |
|--------------------------|------------------------------------------------------------------------------------------------|
| Amount + population type | Sets the consumer goods paid per unit per turn for `Unskilled`, `Professionals`, or `Soldiers` |
| Percentage + `ALL`       | Multiplies every current pay rate by that percentage                                           |

Pay rates are standing. Race IDs are not used in pay orders.

### 17.4 Ration

Sets the percentage of one turn's normal food supply provided to the population. Standing order. See [§6.1](#61-food)
and `CONFLICT-02` for bounds.

### 17.5 Survey

See [§13.5](#135-survey).

### 17.6 Probes

See [§13.2](#132-orbit-probe) through [§13.4](#134-system-probe).

### 17.7 Name

Assigns a name to a ship, colony, star system, star, planet, or player.

| Rule                  | Value                                                                                      |
|-----------------------|--------------------------------------------------------------------------------------------|
| Maximum length        | 50 characters                                                                              |
| Permitted characters  | Any printable ASCII except comma                                                           |
| Frequency             | Any entity may be named or renamed on any turn                                             |
| Visibility, entities  | Entity names are visible to all players                                                    |
| Visibility, astronomy | Star system, star, and planet names are private per player                                 |
| Visibility, players   | Player names become visible to players designated `Friend` or `Ally` who have reciprocated |

### 17.8 Note

Attaches text to an entity, displayed under its name on the owner's report only. Persists until replaced. Maximum 200
characters including spaces.

### 17.9 Non-functional orders

| Order    | Status                                                                                  |
|----------|-----------------------------------------------------------------------------------------|
| `Accept` | **Not in the game** (`D-09`). Proposed, but not on the roadmap for implementation. (`GAP-38`) |
| `Supply` | No longer functional. It formerly sent fuel to forces engaged in a battle. (`GAP-39`)   |

---

## 18. Victory

### 18.1 Total victory

A player wins total victory when no other **active** position owns any entity ([§0.5](#05-notation-and-conventions)).

A player is **eliminated** when it owns no entity (`D-19`). Elimination needs no separate trigger: a player whose
entities all fall independent — through losing their last soldiers and professionals, through rebellion, or through
capture — owns nothing and is eliminated by that fact. Entities that have gone independent are owned by no position and
so obstruct no one's total victory, though they go on denying planets under [§18.2](#182-holding).

### 18.2 Holding

**Holding** is computed from occupation every turn. It is one of the three relations distinguished in
[§0.5](#05-notation-and-conventions): it is not ownership, and it is not the exclusive claim made by the `Control
Planet` order ([§16.1](#161-control-planet)). The sources render all three with the English word "control".

| Term                     | Definition                                                                                                          |
|--------------------------|---------------------------------------------------------------------------------------------------------------------|
| Colony held by a player  | The player owns the colony and it contains at least one `SOL` or `PRO` population unit outside the `RBL` cadre      |
| Colony held by a race    | The race holds a **majority** of the colony's population, counting units of every type (`D-18`)                     |
| Planet held by a player  | The player holds **every** colony on or orbiting the planet. At least one colony must exist there.                  |
| Planet held by a race    | The race holds a **majority** of the colonies on or orbiting the planet. At least one colony must exist there.      |
| Majority                 | `floor(N × 0.5) + 1` of whatever is being counted: colonies on a planet, or population units in a colony (`D-17`)   |

A majority is unique, so **at most one race holds a colony**. A colony in which no race has a majority is held by no
race; it still counts toward `N` when a planet's colonies are counted, but toward no race's total.

The two colony tests are deliberately different. A player holds a colony by owning it and keeping soldiers or
professionals in it; a race holds it by demographic weight alone, with no unit type privileged and regardless of who
owns the colony.

**Independent entities.** An independent colony has no owner, so it is held by no player, and no player can hold a
planet carrying one, since the planet test requires every colony on it
([§4.8](#48-ownership-and-independence), `D-19`). Its population is unaffected and continues to count toward its race,
which may hold the colony and the planet outright. An abandoned colony therefore denies its planet to every player
rather than becoming a free gain for whoever arrives first. A position whose account has left the game is a separate
case: its entities keep their soldiers and professionals and are not independent, so it goes on holding through them.

Holding is counted by **position**, not by account ([§0.5](#05-notation-and-conventions)). A position whose account has
left the game keeps its entities and goes on holding through them.

Population assigned to the `RBL` cadre is excluded from the **player** test: a colony whose only soldiers and
professionals are rebels is independent, so its owner no longer holds it and neither does anyone else (`D-19`). Whether
rebels count toward the **race** test is still open (`GAP-56`).

### 18.3 Solo victory

Let `H` be the number of habitable planets in the cluster, that is, those with a Habitability Factor greater than 0. A
single player wins when both hold:

- It holds at least `floor(H × 0.5) + 1` habitable planets, **and**
- No other single **position** holds more than `ceil(H × 0.1) + 1` habitable planets.

The rival test counts positions, not active accounts (`D-19`). A position whose account has left the game is still a
rival, and blocks a solo victory on exactly the terms it did while it was played.

### 18.4 Race victory

On the same terms, applied to races. A race wins when both hold:

- It holds at least `floor(H × 0.5) + 1` habitable planets, **and**
- No other single race holds more than `ceil(H × 0.1) + 1` habitable planets.

Race holding is computed from population and not from ownership, in three steps: a race holds an entity by holding a
majority of the population in it (`D-18`); the entities it holds decide which race holds each planet; and the habitable
planets it holds are what the two conditions above count. Every step is blind to who owns the entities, so independent
entities count for their race exactly as owned ones do.

A race victory is credited to every **active** player of that race — the race each player began with (`D-15`), fixed
for the game and unchanged by capturing population of any other. Players of that race whose accounts have left the game
are credited nothing. Because the conditions are evaluated on population rather than on players, a race with no active
players at all can satisfy them, in which case the victory is credited to no one.

Because a race holds a colony by demographic weight rather than through ownership, captured population continues to
count toward its own race ([§14.16](#1416-invasion-resolution)). Conquest can therefore advance the victory of the race
conquered.

`H` depends on cluster generation (`GAP-01`). Whether victory is evaluated every turn, how the game terminates, and what
happens if two conditions are satisfied at once are not specified (`GAP-27`).

---

## 19. Unit characteristics

The per-unit table lives in [`units.md`](./units.md). It is the working unit reference and is not yet reconciled with
this manual; see
[§20](#20-source-conflicts).

### 19.1 Assembly and volume model

| Property                      | Rule                                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------|
| Assembly-type items           | Have both an unassembled and an assembled volume. Must be assembled to operate.      |
| Assembled volume              | Twice the unassembled volume, for every assembly-type item except structure          |
| Structure and light structure | Have negative assembled volume of magnitude `TL^2 / StructureRatio` ([§4.2](#42-volume-space-and-structure)): they create enclosed volume rather than consuming it |
| Non-assembly items            | Operate directly from Storage Inventory; have a single volume                        |
| Crated volume                 | 50% of normal, in a cargo hold only                                                  |
| Population and cadre          | 1 `MU` and 1 `VU`, never stowable                                                    |
| Transfer requirement          | Assembly-type items must be unassembled to be transferred                            |

### 19.2 Item classes and canonical codes

Item codes are those of `docs/units.md` (`D-03`). Names in parentheses are the handbook's, where they differ.

| Class          | Members                                                                                                                                                                                        |
|----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Structural     | `STRC` Structure, `STRL` Light Structure                                                                                                                                                       |
| Propulsion     | `HDRV` Hyper Drive (Hyper Engines), `SDRV` Space Drive                                                                                                                                         |
| Static         | `LSU` Life Support                                                                                                                                                                             |
| Weaponry       | `ESHD` Energy Shield, `EWPN` Energy Weapon, `MLNC` Missile Launcher, `ACFT` Assault Craft, `AWPN` Assault Weapons, `MRBT` Military Robot                                                       |
| Ammunition     | `MSSL` Missile, `AMSL` Anti-Missile, `CSUP` Combat Supplies (Military Supplies)                                                                                                                |
| Infrastructure | `AUTO` Automation, `FARM` Farm, `FACT` Factory, `MINE` Mine, `POWR` Power Plant, `LAB` Laboratory, `BMR` Beamer (no rules, `GAP-30`)                                                           |
| Recon          | `SENS` Sensor, `PROB` Probe (Robot Probe Vehicle)                                                                                                                                              |
| Transportation | `TRNS` Transport                                                                                                                                                                               |
| Commodity      | `FOOD` Food, `CSGD` Consumer Goods                                                                                                                                                             |
| Resource       | `FUEL` Fuel, `METL` Metallics (Metals), `NMTL` Non-Metallics (Non-Metals)                                                                                                                      |
| Technology     | `RP` Research Point, `PRTO` Prototype (no rules as a distinct unit, `GAP-31`)                                                                                                                  |
| Living         | `UEM` Unemployables, `USK` Unskilled, `PRO` Professionals, `SOL` Soldiers                                                                                                                      |
| Cadre          | `TRNE` Trainee, `POL` Police, `CNST` Construction Worker, `SPAG` Special Agent, `SPY` Spy (no rules, `GAP-29`), `WRKR` Worker (no rules, `GAP-32`), `RBL` Rebel (no rules as a unit, `GAP-32`) |

The `Living` / `Cadre` split is `units.md`'s. The handbook treats every population type as one kind of thing. Which
model governs food, consumer goods, mass, and volume accounting is `GAP-32`.

### 19.3 Key derived values

| Item                 | Value                 | Formula                |
|----------------------|-----------------------|------------------------|
| Structure, Light Structure | Volume enclosed | `TL^2 / StructureRatio` |
| Hyper Engines        | Lift                  | `1045 × TL` `MU`       |
| Hyper Engines        | Jump range            | `sqrt(TL) × 3` `LY`    |
| Space Drives         | Thrust                | `3000 × TL^2`          |
| Life Supports        | Population supported  | `TL^2`                 |
| Automation           | Unskilled replaced    | `TL^2`                 |
| Farms (`TL` 1)       | Food                  | 25 per turn            |
| Farms (`TL` ≥ 2)     | Food                  | `5 × TL` per turn      |
| Mines                | Ore                   | `25 × TL` per turn     |
| Factories            | Output                | `5 × TL` `MU` per turn |
| Laboratories         | Research              | `0.25 × TL` per turn   |
| Power Plants         | Power                 | `1 × TL` per turn      |
| Sensors              | Probe capacity        | `1 × TL` probes        |
| Transports           | Transfer capacity     | `20 × TL^2` `MU`       |
| Transports           | Combat capacity       | `3 × TL^2` `MU`        |
| Energy Weapons       | Energy per firing     | `10 × TL^2`            |
| Energy Shields       | Deflection per attack | `10 × TL^2`            |
| Missiles             | Damage                | `100 × TL^2` `MU`      |
| Military Robots      | Combat factors        | `2 × TL`               |
| Assault Weapons      | Combat factors        | `2 × TL^2`             |
| Assault Craft        | Combat factors        | `10 × TL`              |
| Assault Craft        | Speed                 | `5 × TL`               |
| Robot Probe Vehicles | Mass and volume       | `500 / TL`             |

### 19.4 Operating requirements

| Item              | Labor                         | Fuel or power                    |
|-------------------|-------------------------------|----------------------------------|
| Farms `TL` 1      | 1 professional + 3 unskilled  | 0.5 fuel                         |
| Farms `TL` 2–5    | 1 professional + 3 unskilled  | `0.5 × TL`                       |
| Farms `TL` 6–200  | 1 professional + 3 unskilled  | `1 × TL`                         |
| Factories         | 1 professional + 3 unskilled  | `0.5 × TL`                       |
| Mines             | 1 professional + 3 unskilled  | `0.5 × TL`                       |
| Laboratories      | 3 professionals + 1 unskilled | `0.5 × TL`                       |
| Life Supports     | none                          | `1 × TL`                         |
| Sensors           | none                          | `0.05 × TL`                      |
| Power Plants      | none                          | none                             |
| Automation        | none                          | none                             |
| Hyper Engines     | 1 professional per 100        | 40 fuel per `LY`, fuel only      |
| Space Drives      | 1 professional per 100        | `1 × TL` per maneuver, fuel only |
| Energy Weapons    | 1 soldier per 100             | `4 × TL` per firing, fuel only   |
| Energy Shields    | 1 soldier per 100             | `10 × TL` per firing, fuel only  |
| Missile Launchers | 1 soldier per 100             | none                             |
| Transports        | 1 professional per 10         | see `CONFLICT-11`                |

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
types to `Cadre` is adopted by the same decision. See [§5.2](#52-living-types-and-cadre-assignments).

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
| `STRC` and `STRL` Output     | Read "divided by structural requirement (based on Entity type)". The divisor is the **Structure Ratio**; the Structural Requirement is a different quantity ([§4.2](#42-volume-space-and-structure)). The `1 × TL^2` figure itself is adopted by `D-05`. | Applied |
| `STRC` and `STRL` statistics | Every statistic on both rows was shifted one item: `STRL` carried `Structure`'s figures and `STRC` a tenfold rescale of them. Corrected values per `D-07` are in the table below.                                                   | Applied |
| `HDRV` jump range            | Output read `sqrt(TL) + 4`. `CONFLICT-07`, closed by `D-03`, gives `sqrt(TL) × 3`.                                                                                                                                                  | Applied |
| `FACT` output                | Output read `20 × TL` mass per turn. `CONFLICT-03`, closed by `D-03`, gives `5 × TL` per turn, which is `20 × TL` per **year**.                                                                                                     | Applied |
| `CSUP` volume                | Volume read `0.04`, equal to its mass. The handbook's Non-Assembly chart gives volume `0.02`. Stowed volume was moved with it to keep the two equal, pending `CONFLICT-14`.                                                         | Applied |
| `RBL` output                 | Read "Tally of population willing to rebel; does not allocate the underlying units", which contradicts `D-12`: rebels *are* assigned Living units.                                                                                  | Applied |
| `BMR` statistics             | Mass `40 × (TL + 115)`, metals `10 × (TL + 210)`, non-metals `30 × TL + 2500`, fuel `20 × TL^2`, 1 professional per 25. **No source.** The Beamer appears nowhere in the handbook, in any chart or text. Provenance unknown (`GAP-30`). | **Open** |
| `PRTO` statistics            | Mass `TL` but volume `3 × TL`, and build costs `TBD`. No source; the handbook has no Prototype item (`GAP-31`).                                                                                                                     | **Open** |
| `TRNS` fuel                  | `0.1 × TL^2`. The handbook figure is truncated to "`× TL^2` fuel" and is unrecoverable (`CONFLICT-11`).                                                                                                                             | **Open** |
| `LSU` and `FACT` requirements | Both rows read "fuel or power" and omit the solar substitution of [§7.8](#78-solar-power), under which life supports, factories, farms, and laboratories in an orbiting colony in orbits 1–5 consume solar power instead. The `LAB` row omits it too. This is load-bearing rather than cosmetic: it is what makes the starting `OBC` of `D-21` viable at Tech Level 1, and a row read literally would charge it 1,200 fuel per turn it does not owe. | **Open** |
| `POWR` requirements          | The Operational Requirements column reads "Open Surface Colonies ONLY", which is a placement restriction, not an operating requirement. Power plants require no labor, no fuel, and no power ([§19.4](#194-operating-requirements)), and their functioning quantity per planet is capped at `Habitability Factor × 100,000` ([§7.7](#77-power-plants)); the row records none of this. | **Open** |
| `MRBT` volume                | `2 × (TL + 10)`, twice the handbook's `TL + 10`, as though it were an assembly item (`CONFLICT-15`).                                                                                                                                | Deferred |
| `Volume Stowed` column       | Present for every non-assembly item; the handbook gives those items no unassembled volume at all (`CONFLICT-14`).                                                                                                                   | Deferred |

Structure statistics (`D-03`, `D-07`):

| Column             | `STRC` (Structure)                 | `STRL` (Light Structure)           |
|--------------------|------------------------------------|------------------------------------|
| Mass               | `0.1 × TL`                         | `0.01 × TL`                        |
| Volume, assembled  | Negative, magnitude `TL^2 / StructureRatio` ([§4.2](#42-volume-space-and-structure)) | Negative, magnitude `TL^2 / StructureRatio` |
| Volume, stowed     | `0.05 × TL`                        | `0.005 × TL`                       |
| Metals to build    | `0.07 × TL`                        | `0.005 × TL`                       |
| Non-metals to build | `0.03 × TL`                       | `0.005 × TL`                       |

`units.md` records the assembled volume of both items as negative and states the enclosure in its Output column, since
the magnitude depends on the containing entity's Structure Ratio and so cannot be a per-item constant.

**Verification status.** Every row of `units.md` has been compared field by field against the handbook's Assembly
Items, Non-Assembly Items, and Population Units charts.

- All 15 **assembly items** agree exactly on mass, assembled volume, stowed volume, and both build costs. `units.md`
  states assembled volume where the handbook states unassembled, and the two are consistent under the doubling rule in
  [§19.1](#191-assembly-and-volume-model).
- All **population and cadre** rows agree: mass 1, volume 1, no build cost.
- All **non-assembly items** agree on mass, volume, and both build costs except `CSUP` and `MRBT`, both listed above.
- The remaining divergences are the `Volume Stowed` column and the unsourced `BMR` and `PRTO` rows.

---

## 21. Gaps

A gap is a rule the sources do not supply. Implementations must not fill a gap by invention.

### 21.1 World generation and setup

| ID       | Gap                                                                                                                                                                                                                                                                                                                     |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-01` | **Cluster generation.** How the gamemaster generates a cluster: cube size, number of star systems, their placement, star counts per system, planet placement and type distribution, habitability factor distribution, deposit counts, deposit types, yields, and ore quantities. The handbook assumes a cluster exists. Home systems are generated here too. `D-21` fixes the home planet's habitability, its orbit, and the three unlimited deposits the starting mine groups occupy ([§1.7](#17-starting-position)); how many other planets the home system holds, of what types, in which orbits, and with what deposits is the residue of the withdrawn `GAP-02` and is settled here. |
| `GAP-03` | **Player setup.** Number of players per game; how home systems are separated; whether starting `TL` above 1 is a per-game or per-player configuration.                                                                                                                                                                  |
| `GAP-04` | **Turn administration.** Turn cadence, order submission, deadlines, order validation, failure reporting, and what a player report contains.                                                                                                                                                                             |

### 21.2 Engine determinism

| ID       | Gap                                                                                                                                                                                                                     |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-05` | **Produce Output and Send Output stages.** Named in the turn sequence with no content, by both the handbook and `docs/turn-sequence.md`.                                                                                |
| `GAP-06` | **Entity iteration order.** The order in which colonies and ships are processed inside the Production stage, and inside every other stage that is not explicitly ordered.                                               |
| `GAP-14` | **Labor allocation.** How scarce professionals, unskilled, and automation are distributed across farms, mines, laboratories, and factories; whether allocation is per-group or per-item; rounding of partial operation. |
| `GAP-15` | **Fuel and power allocation.** Priority when fuel and power are insufficient for all assembled items, across production, life support, weapons, and propulsion. Whether power is consumed before fuel. `D-08` fixes only the timing of life support consumption, at the population-change step of the Production stage. |
| `GAP-17` | **Rounding.** A general rounding rule. Only ship speed (floor) and defensive weapon counts (ceiling) are specified.                                                                                                     |
| `GAP-18` | **Randomness.** Which events are random, their distributions, and seeding. The tactical distance metric. The distribution of arrival positions within a Close/Medium/Long band.                                         |
| `GAP-19` | **Damage allocation algorithm.** Per-category weights, the random distribution, the four-pass logic, and how items within a category are selected for destruction.                                                      |

### 21.3 Population and economy

| ID       | Gap                                                                                                                                                                                                                                                                                                                       |
|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-07` | **Birth rate and unskilled promotion.** The birth rate function between its 0.25% and 10% bounds; the automatic unemployable-to-unskilled rate.                                                                                                                                                                           |
| `GAP-08` | **Death rate.** The death rate formula; its relationship to life support capacity and habitability; the magnitude of the increase when over capacity or under-fuelled; the magnitude of the reduction when within capacity.                                                                                               |
| `GAP-09` | **Discontent model.** Malcontent increase and decrease formulas; the malcontent threshold that creates rebels; rebel recruitment rate; rebel food and consumer goods theft quantities; police arrest and kill rates; police injury rate; special agent effectiveness; strike and riot selection, magnitude, and duration. |
| `GAP-43` | **Back pay and composite pay.** Whether back pay decays or is ever cleared; whether constructor and special agent pay rates track changes to the rates they are composed of.                                                                                                                                              |
| `GAP-57` | **Upkeep of independent entities.** An independent entity issues no orders and can neither send nor receive goods ([§4.8](#48-ownership-and-independence)), so nothing in the manual supplies its food, consumer goods, pay, or life support fuel, and no player can supply them from outside. Whether it continues to farm, mine, and manufacture on its existing assignments, whether its population starves and dies on the ordinary rates ([§5.5](#55-death-rate), [§6.1](#61-food)), and whether an independent ship's unpaid crew accrues back pay ([§6.3](#63-back-pay)) are all unstated. The blockade on `Transfer` and `Give` is deliberate — it is what stops an independent colony from being resupplied into someone's hands — but it also denies a player any way to feed one it intends to capture. `D-21` sharpens this: the starting `OBC` can grow no food at all and is fed by transfer from the `OPC` every turn, so an orbiting colony that goes independent starves by construction rather than by neglect, and every orbiting colony founded before `Farms-2` is reachable inherits the same dependency. |
| `GAP-58` | **Pay rates for the remaining cadre.** Pay follows the assignment (`D-12`), and [§6.2](#62-consumer-goods-and-pay) supplies rates for `TRNE`, `CNST`, and `SPAG` only. `WRKR`, `POL`, `RBL`, and `SPY` have none. `WRKR` is the pressing case, because every staffed farm, mine, and factory has one and the assignment is not optional; the reading that fits `D-12` is that a `WRKR` is paid its Living type's rate, since `WRKR` records a labor requirement being met and adds nothing of its own, but that is not stated. `POL` needs a rate of its own, being drafted rather than merely recorded. Whether rebels are paid at all, and what a spy costs, follow the designs those units still lack (`GAP-29`, `GAP-09`). `D-21` avoids the question at setup by assigning none of the four. |

### 21.4 Entity model

| ID       | Gap                                                                                                                                                                                                      |
|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-12` | **Space accounting, work in process.** Whether a batch under manufacture consumes enclosed volume, and when its resources leave Storage Inventory. `D-05` settles the Space Available formula and the semantics of negative assembled volume; `D-22` settles stockpiles, which are enclosed, and records that cargo holds were never open, [§4.4](#44-cargo-holds) having bounded them by Space Available all along ([§4.2](#42-volume-space-and-structure)). A four-turn batch that occupies volume from the turn it starts and one that occupies none until it finishes differ by a full year of a colony's factory output, so the two readings are not close. |
| `GAP-13` | **Mass accounting.** What is included in an entity's total mass for speed, lift, probe reporting, and control-planet ties: stockpiles, cargo hold contents, WIP, docked ships.                           |
| `GAP-16` | **Solar power scope.** Whether solar power is available to surface colonies or ships in orbits 1–5, or only to orbiting colonies. Whether solar power is unlimited.                                      |
| `GAP-23` | **Transport capacity pool.** Whether transfer capacity is one per-turn pool shared by transfers, pick ups, loads, unloads, surveys, set ups, add-ons, junking, and combat conveyance, or separate pools. |
| `GAP-25` | **Junk and Give details.** Constructor cost of junking. What accompanies a given entity: population, cargo hold, docked ships, home port reassignment, mine group and factory group state.               |

### 21.5 Combat

| ID       | Gap                                                                                                                                                                                                                               |
|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-22` | **Ground combat details.** How "largest invading force" is measured; the wounded-versus-dead split of casualties; how multi-turn battle state is stored; how combat factors map back to specific unit `TL`s when applying losses. Whether **militia** take casualties at all: they contribute combat factors but are absent from the loss-distribution rule ([§14.15](#1415-casualties)). If they do take casualties, which Living types and cadre are eligible for the militia draft, and how losses distribute across them, must also be settled. `D-20` settles that an independent colony drafts militia like any other ([§14.13](#1413-militia)), so the eligibility and casualty questions above apply to it unchanged. |
| `GAP-40` | **Unimplemented combat features.** Excluding friends and allies from close proximity targeting. Rebel activities described as intended rather than implemented.                                                                   |
| `GAP-41` | **Orbit decay.** The docking-protection rule for ships without space drives is recorded as not implemented.                                                                                                                       |
| `GAP-53` | **Military supplies status.** The handbook's item chart annotates `CSUP` "to be deleted", meaning its assembly and operational status should be re-examined, not that the item is removed (`D-11`). Whether `CSUP` should be an assembly item, what its operating requirements are, and whether rebels consume it as well as soldiers, is unreviewed. Not a blocker and not on the roadmap; the item stands as written until then. |

### 21.6 Systems named but not designed

| ID       | Gap                                                                                                                                                                                                                                                                                                                                                                                            |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-56` | **Rebels and the race holding test.** `D-19` settles the player side: rebels are excluded from it, so a colony whose only soldiers and professionals are rebels goes independent and is held by no player ([§18.2](#182-holding)). The race side is untouched. Rebels are population units of their race, and `D-18` counts every type, so as written a race holds a colony through people who have stopped working and are stealing its food — and an independent colony can be held outright by the race whose rebels emptied it. Whether `D-18`'s "counting every type" is deliberate here or merely unexamined must be decided; the type-blindness of the race test is on record as intended, which argues for admitting rebels, while nothing on record shows the rebel case was in view when it was written. Bears on `GAP-09`, which leaves rebel numbers unspecified, and on `D-16`, since captured population arrives with its malcontents. |
| `GAP-27` | **Victory evaluation.** Whether victory is checked every turn; how the game ends; what happens when two conditions are satisfied on the same turn, which the handbook addresses only in passing, in the Diplomacy chapter, with "there can only be one Winner". `H`, the number of habitable planets in the cluster, follows from `GAP-01`, as does whether players are told what `H` is. |
| `GAP-28` | **Markets.** The orders grammar contains `SELL` and `BUY`. No market, price, or trading mechanic exists in any source.                                                                                                                                                                                                                                                                         |
| `GAP-29` | **Espionage.** `SPY` is adopted by `D-04` with no rules. The handbook describes a superspy concept explicitly as not implemented. An espionage mechanic must be designed: what a spy does, where it operates, how it is detected, and how it interacts with police, special agents, and rebels.                                                                                                |
| `GAP-30` | **Beamer.** `BMR` is adopted by `D-04` with a mass, a cost, an operating requirement, and an output of `5000 × TL^2` `MU` beamed. `D-08` places the `Beam` order in the Transfer stage, between `Transfer` and `Pick Up`, which suggests beaming conveys mass rather than damaging a target. What beaming does, what it targets, its range, and whether it draws on transport capacity must still be designed. Two questions are now load-bearing rather than incidental: whether `Beam` conveys **population**, and whether it may target an entity with **no owner**. If both were yes, a ship could beam a single professional into an independent entity and take it without a battle, bypassing the order blockade of [§4.8](#48-ownership-and-independence) and the militia defence of `D-20`, and making independence trivially reversible at range by whoever arrives first — the outcome `D-19` exists to prevent. |
| `GAP-31` | **Prototype as a unit.** `PRTO` is adopted by `D-04`. Whether it is a distinct manufacturable unit, or a marker for any item held above the player's `TL` as in [§8.4](#84-prototypes), must be decided; the two models are not compatible as written.                                                                                                                                         |
| `GAP-32` | **Cadre model, residual cases.** `D-12` settles the model itself ([§5.2](#52-living-types-and-cadre-assignments)). `D-13` settles how a cadre is released and `D-14` separates migration from assignment. Two cases remain. (a) `units.md` scopes `WRKR` to `FACT`, `FARM`, and `MINE`, while [§19.4](#194-operating-requirements) also gives laboratories a labor requirement; whether laboratory staff are `WRKR` is undecided. (b) `SPY` has no rules at all (`GAP-29`). |
| `GAP-33` | **Race in order syntax.** `D-15` defines a race and `D-16` settles its statistics and how one is acquired. What remains is the order syntax, which the handbook states three incompatible ways: `Pick Up`'s format carries a `Race ID#` bound to the population unit; `Draft` and `Disband` say a `Race ID#` is mandatory while their formats have no field for it; `Pay` says race IDs are not used at all. Which orders take a race, and where in the syntax, must be settled with the order set (`GAP-51`). |
| `GAP-34` | **Ship designs.** `Set Up` accepts a design ID in place of an item list, and supports a multiplier on a design. The design system is undefined.                                                                                                                                                                                                                                                |
| `GAP-36` | **Glossary.** The handbook references a glossary of terms throughout and contains only the term list, not the definitions.                                                                                                                                                                                                                                                                     |
| `GAP-38` | **`Accept` order.** Proposed but not on the roadmap for implementation (`D-09`). It has no rules and no place in the turn sequence. Recorded so the name is not reused for something else.                                                                                                                                                                                                     |
| `GAP-39` | **`Supply` order.** No longer functional. Fuel resupply to forces in a multi-turn battle therefore has no mechanism.                                                                                                                                                                                                                                                                           |
| `GAP-51` | **Order set.** The order set for this game is not decided. `docs/orders-grammar.txt` describes a different order vocabulary (`BOMBARD`, `RAID`, `SPY`, `SELL`, `BUY`, `COLONIZE`, `PERMIT`, `NEWS`, `MININGCHANGE`) than the handbook's. Until the order set is decided, the grammar file is unusable and [§22](#22-order-catalogue) is provisional.                                           |

---

## 22. Order catalogue

This catalogue records the handbook's order set, plus orders added or removed by decision. It is provisional; see
`GAP-51`.

Legend for **Actor**: `S/C` any entity, `Ship`, `Colony`, `Surface` surface colony only.

Every actor below is an entity acting for its owner, except `Diplomacy`, which a player issues directly. An independent
entity has no owner and is the actor of no order ([§4.8](#48-ownership-and-independence)).

| Order                               | Actor   | Stage                       | Standing | Parameters                                                                                     |
|-------------------------------------|---------|-----------------------------|----------|------------------------------------------------------------------------------------------------|
| `Accept`                            | S/C     | —                           | no       | Not in the game (`D-09`)                                                                       |
| `Beam`                              | S/C     | Transfer                    | no       | Undesigned (`GAP-30`). Resolves between `Transfer` and `Pick Up`                               |
| `Add On`                            | S/C     | Setup                       | no       | quantity, item-`TL`, target entity, unassembled flag                                           |
| `After-Maneuver Energy Weapon Fire` | S/C     | Combat / post-maneuver fire | no       | target, percentage, target category, distance abort                                            |
| `After-Maneuver Missile Fire`       | S/C     | Combat / post-maneuver fire | no       | target, percentage, target category, distance abort                                            |
| `Assemble`                          | S/C     | Assembly                    | no       | quantity, item-`TL`; plus factory group or product; plus deposit for mines                     |
| `Auto Return Fire`                  | S/C     | Combat / prefire            | **yes**  | percentage                                                                                     |
| `Build Change`                      | Colony  | Assembly                    | no       | factory group, new item-`TL` or `Nothing` / `Shut Down` / `Start Up`, optional factory count   |
| `Close`                             | Ship    | Combat / maneuver           | no       | target ship, optional standoff distance                                                        |
| `Close Proximity Targeting`         | S/C     | Combat / prefire            | **yes**  | percentage                                                                                     |
| `Combine Factory Group`             | Colony  | Disassembly                 | no       | absorbing group, absorbed group, WIP-only flag with optional quarters                          |
| `Control Planet`                    | Surface | Naming                      | no       | —                                                                                              |
| `Defensive Support`                 | S/C     | Combat / ground             | no       | defended entity, then a list of quantity + item-`TL`, terminated                               |
| `Define Cargo Hold`                 | Ship    | Setup                       | no       | quantity of space                                                                              |
| `Diplomacy`                         | Player  | Permission                  | **yes**  | target player, status                                                                          |
| `Disassemble`                       | S/C     | Disassembly                 | no       | quantity, item-`TL`; plus factory group or mine group                                          |
| `Disband`                           | S/C     | Draft                       | no       | quantity, population type                                                                      |
| `Dock`                              | Ship    | Combat / maneuver           | no       | target entity                                                                                  |
| `Dodge`                             | Ship    | Combat / prefire            | **yes**  | percentage of speed                                                                            |
| `Draft`                             | S/C     | Draft                       | no       | quantity, population type                                                                      |
| `Expend`                            | Colony  | Assembly                    | no       | research point quantity + item type; or prototype quantity + prototype item-`TL`               |
| `Factory Group Change`              | Colony  | Assembly                    | no       | existing group, new group, quantity of factories                                               |
| `Give`                              | S/C     | Give                        | no       | receiving entity                                                                               |
| `Home Port Change`                  | Ship    | Permission                  | no       | colony                                                                                         |
| `Invade`                            | S/C     | Combat / ground             | no       | target entity, then a list of quantity + item-`TL`, terminated                                 |
| `Jump`                              | Ship    | Ship Travel                 | no       | system coordinates, arrival distance band                                                      |
| `Junk`                              | S/C     | Disassembly                 | no       | entity to be junked                                                                            |
| `Launch Robot Probe`                | S/C     | Surveys and Probes          | no       | probe type, system coordinates, star letter, orbit, magnitude                                  |
| `Load Cargo`                        | Colony  | Transfer                    | no       | quantity, item-`TL`, ship                                                                      |
| `Merge`                             | Ship    | Disassembly                 | no       | ship to be absorbed                                                                            |
| `Message`                           | S/C     | Naming                      | no       | target entity, text                                                                            |
| `Mine Change`                       | Colony  | Assembly                    | no       | mine group, target deposit, quantity; or mine group + shut down / start up + optional quantity |
| `Move`                              | Ship    | Ship Travel                 | no       | star letter, orbit, arrival distance band                                                      |
| `Name`                              | S/C     | Naming                      | no       | entity kind, text (≤ 50 chars, no comma)                                                       |
| `Note`                              | S/C     | Naming                      | no       | text (≤ 200 chars)                                                                             |
| `Offensive Support`                 | S/C     | Combat / ground             | no       | defending entity, then a list of quantity + item-`TL`, terminated                              |
| `Pay`                               | Colony  | Pay Change                  | **yes**  | amount + population type, or percentage + `ALL`                                                |
| `Permission To Colonize`            | Colony  | Permission                  | no       | ship being granted permission                                                                  |
| `Pick Up`                           | S/C     | Transfer                    | no       | quantity, item-`TL` or population type + race ID (`GAP-33`), source entity                     |
| `Pre-Maneuver Energy Weapon Fire`   | S/C     | Combat / pre-maneuver fire  | no       | target, percentage, target category, distance abort                                            |
| `Pre-Maneuver Missile Fire`         | S/C     | Combat / pre-maneuver fire  | no       | target, percentage, target category, distance abort                                            |
| `Probe` (orbit)                     | S/C     | Probe                       | no       | star letter or `ALL`, orbit number or `ALL`                                                    |
| `Probe` (system)                    | S/C     | Probe                       | no       | magnitude (≤ 10)                                                                               |
| `Probe` (S/C)                       | S/C     | Surveys and Probes          | no       | target entity                                                                                  |
| `Ration`                            | S/C     | Pay Change                  | **yes**  | percentage                                                                                     |
| `Run`                               | Ship    | Combat / maneuver           | no       | target entity                                                                                  |
| `Scrap`                             | S/C     | Disassembly                 | no       | quantity, item-`TL`                                                                            |
| `Set Up`                            | S/C     | Setup                       | no       | entity type, quantity, then an item list; factories need a product, mines need a deposit       |
| `Shut Down`                         | S/C     | Assembly                    | no       | quantity, `Farms-TL` or `Laboratories-TL`                                                      |
| `Start Up`                          | S/C     | Assembly                    | no       | quantity, `Farms-TL` or `Laboratories-TL`                                                      |
| `Supply`                            | S/C     | —                           | no       | Defunct (`GAP-39`)                                                                             |
| `Survey`                            | S/C     | Surveys and Probes          | no       | —                                                                                              |
| `Tactical Maneuver`                 | Ship    | Combat / maneuver           | no       | tactical coordinates                                                                           |
| `Transfer`                          | S/C     | Transfer                    | no       | quantity, item-`TL`, receiving entity or `Jettison`                                            |
| `Un-Control Planet`                 | Surface | Naming                      | no       | —                                                                                              |
| `Undock`                            | Ship    | Combat / maneuver           | no       | —                                                                                              |
| `Unload`                            | Colony  | Transfer                    | no       | quantity, item-`TL`, ship                                                                      |
| `Withdraw`                          | S/C     | Combat / ground             | no       | defending entity                                                                               |

---

## 23. Constants and limits

| Constant                                              | Value                                    |
|-------------------------------------------------------|------------------------------------------|
| Tactical distance unit                                | 10,000 miles                             |
| Tactical coordinate bounds                            | −999 to 999                              |
| Orbits per star                                       | 10, plus the shared orbit 11             |
| Stars per system                                      | 1 to 5                                   |
| Turns per year                                        | 4                                        |
| Tech level range                                      | 1 to 200                                 |
| Population unit size                                  | 100 individuals                          |
| Sustainable population per habitability factor, `OPC` | 10,000,000                               |
| `Farms-1` per planet                                  | `Habitability Factor × 100,000`          |
| Power plants per planet                               | `Habitability Factor × 100,000`          |
| Mines per deposit                                     | 1,000,000                                |
| Mine groups per colony                                | 6                                        |
| Factory groups per colony                             | 40                                       |
| Farm groups per entity                                | one per `TL`                             |
| Manufacturing batch duration                          | 4 turns                                  |
| Minimum hyper engines to travel                       | 100                                      |
| Hyper engine fuel                                     | 40 per `LY`                              |
| Move distance for fuel purposes                       | 0.1 `LY`                                 |
| Transport range                                       | 1 `TDU`                                  |
| Assault craft range                                   | `TL` `TDU`                               |
| System probe maximum magnitude                        | 10                                       |
| Maximum prototypes per expend                         | 20                                       |
| Maximum prototype discount                            | 80%                                      |
| Research cost, `TL n` to `n+1`                        | `1,000,000 × n`                          |
| Constructor throughput, assemble / disassemble        | 500 `MU`                                 |
| Constructor throughput, scrap                         | 300 `MU`                                 |
| Disassembly waste                                     | 10%                                      |
| Scrap waste                                           | 30%                                      |
| Cargo hold space factor                               | 50%                                      |
| Cargo load/unload capacity factor                     | 20% of an equivalent transfer            |
| Cryogenic revival loss                                | 10% plus the colony death rate           |
| Ships per `Set Up` order at one location              | 99                                       |
| Colonies of one type per player per location          | 1                                        |
| Ship damage multiplier                                | 10× a colony's                           |
| Surrender ratio                                       | 6 : 1                                    |
| Malcontent conversion on capture                      | 65% each way                             |
| Militia cap                                           | Half the colony's total population       |
| Full ration                                           | 0.25 food per population unit per turn   |
| Starvation floor                                      | 0.0625 food per population unit per turn |
| Starvation death rate                                 | ~30% per turn                            |
| Stockpile target                                      | 4 turns                                  |
| Stockpile consumption cap                             | 25% per turn                             |
| Birth rate bounds                                     | 0.25% to 10% per turn                    |
| Trainee graduation rate                               | 5% per turn                              |
| Soldier retirement rate                               | 5% per year                              |
| Training ratio                                        | 1 professional per 100 trainees          |
| Entity name length                                    | 50 characters                            |
| Note length                                           | 200 characters                           |
