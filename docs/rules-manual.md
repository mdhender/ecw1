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
  and victory points.
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

At game start each player has:

- One star system with one star and several planets.
- At least one terrestrial planet with Habitability Factor 25 in orbit 3.
- Some home-planet deposits of unlimited size.
- One Open Surface Colony at the home planet.
- One Orbiting Colony in the home planet's orbit, at tactical distance 1 from
  `0/0/0`.
- Tech Level 1 in every item type, unless the game is configured otherwise.

The starting inventory, population, and structure of those two colonies are not specified (`GAP-02`).

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
- If the home port is destroyed or captured, the home port becomes the owning player's lowest-numbered colony on the
  following turn.

### 4.7 Entity identity

- Each entity has an `S/C ID#`, allocated at set up but not visible to the player until the following turn's report.
- Lower `S/C ID#` means older.
- Entity names are visible to all players. See [§17.7](#177-name).

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

### 5.2 Population types

| Type           | Recruited from                                                                                | Disbands to | Function                                                                                       |
|----------------|-----------------------------------------------------------------------------------------------|-------------|------------------------------------------------------------------------------------------------|
| Unemployables  | Births; combat wounded; surrendered soldiers; injured police; arrested rebels                 | —           | None. Cannot work.                                                                             |
| Unskilled      | Unemployables (automatic); disbanded soldiers, police, constructors, special agents, trainees | —           | Labor in farms, mines, laboratories, factories                                                 |
| Professionals  | Trainee graduation; soldier retirement                                                        | —           | Skilled labor; pilot transports; train trainees                                                |
| Trainees       | Draft from Unskilled                                                                          | Unskilled   | Become professionals                                                                           |
| Soldiers       | Draft from Unskilled                                                                          | Unskilled   | Combat; crew energy weapons, energy shields, missile launchers, assault craft, assault weapons |
| Constructors   | Draft from Unskilled                                                                          | Unskilled   | Assemble, disassemble, scrap; assist add-on, set up, junk                                      |
| Police         | Draft from Unskilled                                                                          | Unskilled   | Find and arrest rebels                                                                         |
| Special Agents | Draft from **Professionals**                                                                  | Unskilled   | Reduce discontent; assist police against rebels                                                |

Note the asymmetry: special agents are drafted from professionals but disband to unskilled.

### 5.3 Derived and transient population states

| State       | Nature                                                                                        |
|-------------|-----------------------------------------------------------------------------------------------|
| Malcontents | A tally, reported as a percentage of total population. Not an allocation of units.            |
| Rebels      | Actual units drawn from the other types. Do not work.                                         |
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
| Soldier supply                           | 1 military supplies per soldier per combat round (`CONFLICT-10`)                                    |
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

### 14.16 Invasion resolution

| Condition                                                           | Result                                                                                                                                                                         |
|---------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Defending troops eliminated                                         | The invaded entity becomes the property of the owner of the **largest** invading force. Supporters of the invader retire from the field.                                       |
| Capture                                                             | 65% of the captured entity's malcontents become loyal; 65% of its loyal population become malcontents                                                                          |
| Primary attacker or defender surrenders, withdraws, or is destroyed | That side's supporters withdraw automatically, unless additional enemy supporting forces are present, in which case they continue to fight for the victor against those forces |
| Neither side eliminated                                             | The battle continues into the following turn                                                                                                                                   |
| Multiple simultaneous invaders                                      | One invader wins control. The others continue invading until they order a withdrawal. Supporters remain as long as invaders remain.                                            |

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

A `Master / Client` status is described as under consideration and is not implemented (`GAP-42`).

### 15.3 Give

| Rule                 | Value                                                                                   |
|----------------------|-----------------------------------------------------------------------------------------|
| Effect               | Transfers ownership of an entity to another player                                      |
| Requirement          | Mutual `Friend` or `Ally` status on the same turn                                       |
| Location requirement | The receiving player must have an entity in the same star system                        |
| Prohibited           | A home colony may never be given away, even if its original owner no longer controls it |
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

Converts unskilled population into soldiers, constructors, police, or trainees; converts professionals into special
agents. Processed in the Draft Orders stage.

### 17.2 Disband

Converts soldiers, constructors, police, special agents, or trainees into unskilled population. Processed in the Draft
Orders stage.

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

Eliminate every other player.

### 18.2 Domination victory

| Rule                 | Value                                                                                                                |
|----------------------|----------------------------------------------------------------------------------------------------------------------|
| Victory point source | Open colonies on planets with a Habitability Factor                                                                  |
| Rate                 | 1 victory point per 100,000 population units in the open colony                                                      |
| Cap per planet       | The planet's Habitability Factor                                                                                     |
| Contention           | If several players have open colonies on one planet, the oldest colony (lowest `S/C ID#`) takes victory points first |
| Home planet          | Habitability Factor 25                                                                                               |
| Win condition        | More than half of all available victory points **and** more than twice the second-place player's victory points      |

How total available victory points are computed, whether victory is evaluated every turn, and how the game terminates
are not specified (`GAP-27`).

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
| `CONFLICT-10` | Military supplies                | Soldiers consume 1 per combat round; the item chart marks it "to be deleted"                   | `units.md` keeps it as `CSUP` Combat Supplies                                                      | **Open**: is the item retained?                                                                        |
| `CONFLICT-11` | Transport fuel                   | Chart text truncated: "`× TL^2` fuel"                                                          | `0.1 × TL^2` fuel                                                                                  | **Open**: the handbook figure is unrecoverable from the source                                         |
| `CONFLICT-12` | Structure scale and naming       | `Structure` mass `0.1 × TL`; `Light Structure` mass `0.01 × TL`                                | `STRC` mass `1 × TL`; `STRL` mass `0.1 × TL`. `units.md` `STRL` equals the handbook's `Structure`. | Closed by `D-07`: there is no rename, so `D-03` applies and the handbook statistics stand. Both `units.md` rows are in error                            |
| `CONFLICT-13` | Missile launcher build cost      | Assembly chart `15 × TL` metals; summary chart `15 + TL` metals                                | `15 × TL`                                                                                          | Closed by `D-03`: `15 × TL` (both sources' assembly chart)                                             |
| `CONFLICT-14` | Non-assembly stowed volume       | No stowed volume; crating is a cargo hold rule only                                            | Every non-assembly item has a stowed volume, generally half                                        | **Open**                                                                                               |
| `CONFLICT-15` | Military robot volume            | `TL + 10`                                                                                      | `2 × (TL + 10)` assembled, `TL + 10` stowed, despite not being an assembly item                    | **Open**, follows `CONFLICT-14`                                                                        |
| `CONFLICT-16` | Pre-maneuver missile fire timing | Order text says "after all ship maneuvers", contradicting its own name and the combat sequence | Combat sequence places it before maneuvers                                                         | **Open**, though the order text is plainly a copy error                                                |
| `CONFLICT-17` | Game name                        | "Empyrean Cluster Wars"                                                                        | "Epimethean Challenge" / "Empyrean Challenge"                                                      | Closed by `D-01`: **Epimethean Challenge**                                                             |

### 20.1 Units adopted without rules

`D-04` keeps these in the unit vocabulary. No source supplies rules for them.

| Code   | Name      | What is missing                                                                                                                                                                  |
|--------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `BMR`  | Beamer    | Stated to beam `5000 × TL^2` `MU`. `D-08` fixes when the `Beam` order resolves; what beaming does, what it targets, and its range remain undesigned. (`GAP-30`)                  |
| `SPY`  | Spy       | The handbook describes a "superspy" concept explicitly as not implemented. No espionage mechanic exists. (`GAP-29`)                                                              |
| `WRKR` | Worker    | A cadre representing professionals and unskilled allocated to a `FACT`, `FARM`, or `MINE`. Its relationship to the handbook's direct labor requirements is undefined. (`GAP-32`) |
| `RBL`  | Rebel     | The handbook treats rebels as a tally drawn from other types, not an allocatable unit. (`GAP-32`)                                                                                |
| `PRTO` | Prototype | The handbook treats prototypes as ordinary higher-`TL` items, not a distinct unit with its own mass and volume. Whether both models coexist is undecided. (`GAP-31`)             |

`units.md` also reclassifies `POL`, `CNST`, `TRNE`, and `SPAG` from population types to `Cadre`. See `GAP-32`.

### 20.2 Naming divergences

| Handbook                    | `units.md`                                  |
|-----------------------------|---------------------------------------------|
| Hyper Engines               | Hyper Drive (`HDRV`)                        |
| Military Supplies           | Combat Supplies (`CSUP`)                    |
| Robot Probe Vehicle         | Probe (`PROB`)                              |
| Structure / Light Structure | `STRC` / `STRL` (codes only, no rename; `D-07`) |
| Metals / Non-Metals         | Metallics (`METL`) / Non-Metallics (`NMTL`) |

### 20.3 Corrections to `units.md`

| Item                         | Correction                                                                                                                                                                                                                          | Status  |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| Tech Level range             | Stated 0–10. `D-02` sets 1–200.                                                                                                                                                                                                    | Applied |
| `STRC` and `STRL` Output     | Read "divided by structural requirement (based on Entity type)". The divisor is the **Structure Ratio**; the Structural Requirement is a different quantity ([§4.2](#42-volume-space-and-structure)). The `1 × TL^2` figure itself is adopted by `D-05`. | Applied |
| `STRC` and `STRL` statistics | Every statistic on both rows was shifted one item: `STRL` carried `Structure`'s figures and `STRC` a tenfold rescale of them. Corrected values per `D-07` are in the table below.                                                   | Applied |

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

---

## 21. Gaps

A gap is a rule the sources do not supply. Implementations must not fill a gap by invention.

### 21.1 World generation and setup

| ID       | Gap                                                                                                                                                                                                                                                                                                                     |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-01` | **Cluster generation.** How the gamemaster generates a cluster: cube size, number of star systems, their placement, star counts per system, planet placement and type distribution, habitability factor distribution, deposit counts, deposit types, yields, and ore quantities. The handbook assumes a cluster exists. |
| `GAP-02` | **Home system generation.** Exact planet count and layout; which deposits are unlimited; the starting inventory, population, structure, and assembled items of the starting `OPC` and `OBC`.                                                                                                                            |
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
| `GAP-49` | **Player elimination.** What happens when a player's population reaches zero, and what happens to that player's assets.                                                                                                                                                                                                   |

### 21.4 Entity model

| ID       | Gap                                                                                                                                                                                                      |
|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-12` | **Space accounting.** Whether stockpiles, cargo hold contents, and work in process count toward the volume that must be enclosed. The Space Available formula and the semantics of negative assembled volume are settled by `D-05` ([§4.2](#42-volume-space-and-structure)). |
| `GAP-13` | **Mass accounting.** What is included in an entity's total mass for speed, lift, probe reporting, and control-planet ties: stockpiles, cargo hold contents, WIP, docked ships.                           |
| `GAP-16` | **Solar power scope.** Whether solar power is available to surface colonies or ships in orbits 1–5, or only to orbiting colonies. Whether solar power is unlimited.                                      |
| `GAP-23` | **Transport capacity pool.** Whether transfer capacity is one per-turn pool shared by transfers, pick ups, loads, unloads, surveys, set ups, add-ons, junking, and combat conveyance, or separate pools. |
| `GAP-25` | **Junk and Give details.** Constructor cost of junking. What accompanies a given entity: population, cargo hold, docked ships, home port reassignment, mine group and factory group state.               |

### 21.5 Combat

| ID       | Gap                                                                                                                                                                                                                               |
|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-22` | **Ground combat details.** How "largest invading force" is measured; the wounded-versus-dead split of casualties; how multi-turn battle state is stored; how combat factors map back to specific unit `TL`s when applying losses. |
| `GAP-40` | **Unimplemented combat features.** Excluding friends and allies from close proximity targeting. Rebel activities described as intended rather than implemented.                                                                   |
| `GAP-41` | **Orbit decay.** The docking-protection rule for ships without space drives is recorded as not implemented.                                                                                                                       |

### 21.6 Systems named but not designed

| ID       | Gap                                                                                                                                                                                                                                                                                                                                                                                            |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `GAP-27` | **Victory evaluation.** How total available victory points are computed; whether victory is checked every turn; how the game ends; simultaneous victory.                                                                                                                                                                                                                                       |
| `GAP-28` | **Markets.** The orders grammar contains `SELL` and `BUY`. No market, price, or trading mechanic exists in any source.                                                                                                                                                                                                                                                                         |
| `GAP-29` | **Espionage.** `SPY` is adopted by `D-04` with no rules. The handbook describes a superspy concept explicitly as not implemented. An espionage mechanic must be designed: what a spy does, where it operates, how it is detected, and how it interacts with police, special agents, and rebels.                                                                                                |
| `GAP-30` | **Beamer.** `BMR` is adopted by `D-04` with a mass, a cost, an operating requirement, and an output of `5000 × TL^2` `MU` beamed. `D-08` places the `Beam` order in the Transfer stage, between `Transfer` and `Pick Up`, which suggests beaming conveys mass rather than damaging a target. What beaming does, what it targets, its range, and whether it draws on transport capacity must still be designed. |
| `GAP-31` | **Prototype as a unit.** `PRTO` is adopted by `D-04`. Whether it is a distinct manufacturable unit, or a marker for any item held above the player's `TL` as in [§8.4](#84-prototypes), must be decided; the two models are not compatible as written.                                                                                                                                         |
| `GAP-32` | **Cadre model.** `WRKR` and `RBL` are adopted by `D-04`. Whether police, constructors, trainees, special agents, workers, spies, and rebels are population types or assignments of population units must be decided, along with how their food, consumer goods, mass, and volume are counted, and how `WRKR` relates to the direct labor requirements in [§19.4](#194-operating-requirements). |
| `GAP-33` | **Races.** `Draft`, `Disband`, and `Pick Up` orders describe a mandatory `Race ID#`. Multiple races per player are never defined.                                                                                                                                                                                                                                                              |
| `GAP-34` | **Ship designs.** `Set Up` accepts a design ID in place of an item list, and supports a multiplier on a design. The design system is undefined.                                                                                                                                                                                                                                                |
| `GAP-36` | **Glossary.** The handbook references a glossary of terms throughout and contains only the term list, not the definitions.                                                                                                                                                                                                                                                                     |
| `GAP-38` | **`Accept` order.** Proposed but not on the roadmap for implementation (`D-09`). It has no rules and no place in the turn sequence. Recorded so the name is not reused for something else.                                                                                                                                                                                                     |
| `GAP-39` | **`Supply` order.** No longer functional. Fuel resupply to forces in a multi-turn battle therefore has no mechanism.                                                                                                                                                                                                                                                                           |
| `GAP-42` | **Master / Client diplomacy.** Described as under consideration: client perks equal to `Ally` minus pick up, and transfer of all the client's victory points to the master.                                                                                                                                                                                                                    |
| `GAP-51` | **Order set.** The order set for this game is not decided. `docs/orders-grammar.txt` describes a different order vocabulary (`BOMBARD`, `RAID`, `SPY`, `SELL`, `BUY`, `COLONIZE`, `PERMIT`, `NEWS`, `MININGCHANGE`) than the handbook's. Until the order set is decided, the grammar file is unusable and [§22](#22-order-catalogue) is provisional.                                           |

---

## 22. Order catalogue

This catalogue records the handbook's order set, plus orders added or removed by decision. It is provisional; see
`GAP-51`.

Legend for **Actor**: `S/C` any entity, `Ship`, `Colony`, `Surface` surface colony only.

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
| `Pick Up`                           | S/C     | Transfer                    | no       | quantity, item-`TL` or population type, source entity                                          |
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
