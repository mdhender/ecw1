# Home World Setup

The starting assets of a new player, in seed form, and the arithmetic that closes them.

> **Normative for the contents of the two starting colonies**, which `D-21` fixes and which are stated here rather than
> in the manual. [§1.7](./rules-manual.md#17-starting-position) fixes the *shape* of the starting position — the home
> system, the two colonies, and the facts about them that other rules turn on — and governs wherever it and this
> document overlap. Everything else in [`rules-manual.md`](./rules-manual.md) governs unconditionally: this document
> states a starting state, never a rule. Section references below are to the manual.

Every figure here is at Tech Level 1, because every game starts at `TL-1` in every item type (`D-24`, §8.1). One population unit
is 100 individuals. Volumes are `VU`, masses `MU`.

---

## 1. What a new player receives

| Asset                   | Quantity                                                          |
|-------------------------|-------------------------------------------------------------------|
| Star systems            | One, the home system, at `0,0,0` in the player's own frame        |
| Stars                   | One                                                               |
| Home planet             | Terrestrial, Habitability Factor 25, orbit 3                      |
| Unlimited deposits      | Three on the home planet: `Fuel` at 80% yield, `Metals` at 40%, `Non-Metals` at 40%  |
| Colonies                | One `OPC` at `0/0/0`; one `OBC` at tactical distance 1 from it    |
| Ships                   | None                                                              |
| Population              | 51,000 units (5,100,000 individuals), all of one race             |
| Tech Level              | 1 in every item type, in every game (`D-24`)                      |
| Discovered systems      | The home system only                                              |

Both colonies are **home colonies** and may never be given away (§15.3). The `OPC` controls the home planet and its
orbit from turn 1 (§16.1). Everything else about the home system — how many other planets it holds, of what types, in
which orbits, with what deposits — is world generation (`GAP-01`).

---

## 2. Statistics of the items seeded

Derived from §19 and `units.md`, at `TL` 1. `Metals` and `Non-Metals` are the build costs, given for reference; nothing
in the starting position is manufactured.

| Item     | Mass | Volume, assembled | Volume, stowed | `METL` | `NMTL` |
|----------|------|-------------------|----------------|--------|--------|
| `STRC-1` | 0.1  | negative, `1 / StructureRatio` | 0.05 | 0.07 | 0.03 |
| `STRL-1` | 0.01 | negative, `1 / StructureRatio` | 0.005 | 0.005 | 0.005 |
| `FARM-1` | 8    | 8                 | 4              | 5      | 3      |
| `MINE-1` | 12   | 12                | 6              | 6      | 6      |
| `FACT-1` | 14   | 14                | 7              | 9      | 5      |
| `LAB-1`  | 10   | 10                | 5              | 6      | 4      |
| `POWR-1` | 12   | 12                | 6              | 6      | 6      |
| `SENS-1` | 3,000 | 3,000            | 1,500          | 1,000  | 2,000  |
| `LSU-1`  | 8    | 8                 | 4              | 3      | 5      |
| `TRNS-1` | 4    | 4                 | 4              | 3      | 1      |
| `FOOD`   | 6    | 3                 | —              | —      | —      |
| `CSGD`   | 0.6  | 0.3               | —              | 0.2    | 0.4    |
| `FUEL`, `METL`, `NMTL` | 1 | 0.5          | —              | —      | —      |
| Any population unit | 1 | 1              | not stowable   | —      | —      |

---

## 3. Open Surface Colony

Structure Ratio 1, so one assembled `STRC-1` encloses 1 `VU` (§4.2).

### 3.1 Population

| Living type | Units      |
|-------------|------------|
| `UEM`       | 2,500      |
| `USK`       | 42,500     |
| `PRO`       | 4,000      |
| `SOL`       | 1,000      |
| **Total**   | **50,000** |

### 3.2 Cadre assignments

An assignment changes no Living count (`D-12`, `D-14`).

| Cadre  | Assigned                  | Drawn from        |
|--------|---------------------------|-------------------|
| `WRKR` | 3,260 `PRO` + 9,780 `USK` | Farms, mines, factories |
| `CNST` | 500 `USK`                 | Unskilled         |
| `TRNE` | 1,000 `USK`               | Unskilled         |
| `POL`  | 0                         | —                 |
| `SPAG` | 0                         | —                 |
| `SPY`  | 0                         | —                 |
| `RBL`  | 0                         | —                 |

Not recorded as cadre: 300 `PRO` + 100 `USK` staffing the laboratories (`GAP-32` leaves the record open and no count
turns on it), 20 `PRO` piloting transports, 10 `PRO` training the trainees.

Unassigned and available: 410 `PRO`, 31,120 `USK`.

### 3.3 Assembled items

| Item     | Quantity | Group                                     | Volume  |
|----------|----------|-------------------------------------------|---------|
| `STRC-1` | 350,000  | —                                         | encloses 350,000 |
| `FARM-1` | 600      | Farm group, `TL` 1                        | 4,800   |
| `MINE-1` | 150      | Mine Group 1, unlimited `Fuel` deposit    | 1,800   |
| `MINE-1` | 450      | Mine Group 2, unlimited `Metals` deposit  | 5,400   |
| `MINE-1` | 500      | Mine Group 3, unlimited `Non-Metals` deposit | 6,000 |
| `FACT-1` | 960      | Factory Group 1, product `CSGD`           | 13,440  |
| `FACT-1` | 600      | Factory Group 2, product `STRC-1`         | 8,400   |
| `LAB-1`  | 100      | —                                         | 1,000   |
| `POWR-1` | 1,750    | —                                         | 21,000  |
| `SENS-1` | 10       | —                                         | 30,000  |

Neither factory group holds work in process. Each starts its first batch on turn 1 and finishes it on turn 5 (§7.4).

### 3.4 Storage Inventory

| Item     | Quantity | Volume  |
|----------|----------|---------|
| `TRNS-1` | 200      | 800     |
| `FUEL`   | 10,000   | 5,000   |
| `METL`   | 20,000   | 0, stored outside the structure (§4.2) |
| `NMTL`   | 20,000   | 0, stored outside the structure (§4.2) |
| `CSGD`   | 30,000   | 9,000   |
| `RP`     | 0        | 0       |

### 3.5 Stockpiles

At the four-turn target (§6.4).

| Commodity | Quantity | Volume  |
|-----------|----------|---------|
| `FOOD`    | 50,000   | 150,000 |
| `CSGD`    | 29,000   | 8,700   |

### 3.6 Totals

| Quantity            | Value       |
|---------------------|-------------|
| Enclosure capacity  | 350,000 `VU` |
| Enclosed contents   | 315,340 `VU` |
| Space Available     | 34,660 `VU`  |
| Total mass          | 563,040 `MU` |

---

## 4. Orbiting Colony

Structure Ratio 8, so one assembled structure unit of either kind encloses 0.125 `VU` (§4.2). In orbit 3, so its life
supports and its factories draw solar power and consume no fuel (§7.8).

It is built of `STRC`, not `STRL`. `STRL` may be manufactured only in an orbiting colony (`D-06`), so it cannot exist
before one does; seeding this colony with it would give it structure that only it can make. The restriction is on
manufacture alone — `STRL` may be held, transported, and assembled anywhere, and both items enclose
`TL^2 / StructureRatio` — so the colony converts to light structure out of its own factory group's output, and the same
route refounds an orbiting colony later from `STRC` made in the `OPC`.

### 4.1 Population

| Living type | Units     |
|-------------|-----------|
| `UEM`       | 50        |
| `USK`       | 700       |
| `PRO`       | 200       |
| `SOL`       | 50        |
| **Total**   | **1,000** |

### 4.2 Cadre assignments

| Cadre  | Assigned                | Drawn from |
|--------|-------------------------|------------|
| `WRKR` | 150 `PRO` + 450 `USK`   | Factories  |
| `CNST` | 50 `USK`                | Unskilled  |

5 `PRO` pilot the transports. Unassigned and available: 45 `PRO`, 200 `USK`.

### 4.3 Assembled items

| Item     | Quantity | Group                             | Volume |
|----------|----------|-----------------------------------|--------|
| `STRC-1` | 200,000  | —                                 | encloses 25,000 |
| `LSU-1`  | 1,200    | —                                 | 9,600  |
| `FACT-1` | 150      | Factory Group 1, product `STRL-1` | 2,100  |

No work in process.

### 4.4 Storage Inventory

| Item     | Quantity | Volume |
|----------|----------|--------|
| `TRNS-1` | 50       | 200    |
| `FUEL`   | 1,000    | 500    |
| `METL`   | 1,500    | 750    |
| `NMTL`   | 1,500    | 750    |

`Metals` and `Non-Metals` are exempt from enclosure only in an `OPC` or `ESC`, so here they occupy volume.

### 4.5 Stockpiles

| Commodity | Quantity | Volume |
|-----------|----------|--------|
| `FOOD`    | 1,000    | 3,000  |
| `CSGD`    | 775      | 232.5  |

### 4.6 Totals

| Quantity              | Value        |
|-----------------------|--------------|
| Enclosure capacity    | 25,000 `VU`  |
| Enclosed contents     | 18,132.5 `VU` |
| Space Available       | 6,867.5 `VU` |
| Life Support Capacity | 1,200 population units |
| Total mass            | 43,365 `MU`  |

---

## 5. Player state on turn 1

| Property                      | Value                                                            |
|-------------------------------|------------------------------------------------------------------|
| Tech Level                    | 1 in every item type, in every game (`D-24`)                     |
| Races held                    | One, `Race ID#` assigned sequentially by the engine (`D-15`)     |
| Ships                         | None                                                             |
| Discovered systems            | The home system only                                             |
| Committed Research Buffers    | 0 in every item type                                             |
| Research points held          | 0                                                                |
| Diplomatic status             | None, either direction, with every other player (§15.1)          |
| Malcontents, rebels, back pay | 0                                                                |
| `Pay` standing orders         | `USK` 0.125, `SOL` 0.250, `PRO` 0.375 — the defaults of §6.2     |
| `Ration` standing order       | 100%, within bounds under both readings of `CONFLICT-02`         |
| Standing combat orders        | None set                                                         |
| Planetary control             | The `OPC` controls the home planet and orbit 3                   |

Every item type not listed for a colony in §3 or §4 is zero in that colony. Neither colony starts with automation,
weapons, ammunition, propulsion, robot probe vehicles, or combat supplies.

---

## 6. The arithmetic

Every balance closes with a margin. The sections that follow derive each line.

| Balance             | Supply                        | Demand                                                  | Margin      |
|---------------------|-------------------------------|---------------------------------------------------------|-------------|
| Food                | 15,000 (`OPC` farms)          | 12,750 (both colonies)                                  | +2,250      |
| Consumer goods      | 8,000 (`OPC` factory group 1) | 7,443.75 (both colonies)                                | +556.25     |
| `METL`              | 4,500 (`OPC` mine group 2)    | 4,075                                                   | +425        |
| `NMTL`              | 5,000 (`OPC` mine group 3)    | 4,475                                                   | +525        |
| `FUEL`              | 3,000 (`OPC` mine group 1)    | Transports only (`CONFLICT-11`)                         | accumulates |
| Power               | 1,750 (`OPC` power plants)    | 1,680.5                                                 | +69.5       |
| `OPC` `PRO`         | 4,000                         | 3,590                                                   | +410        |
| `OPC` `USK`         | 42,500                        | 11,380                                                  | +31,120     |
| `OBC` `PRO`         | 200                           | 155                                                     | +45         |
| `OBC` `USK`         | 700                           | 500                                                     | +200        |
| Life support, `OBC` | 1,200 population units        | 1,000                                                   | +200        |
| Transport, `OPC`    | 4,000 `MU`                    | 2,366.25 `MU` to the `OBC`                              | +1,633.75   |
| Transport, `OBC`    | 1,000 `MU`                    | 750 `MU` to the `OPC`                                   | +250        |

All figures are per turn.

### 6.1 Food

| Step                              | Figure                                        |
|-----------------------------------|-----------------------------------------------|
| `OPC` consumption at full ration  | 50,000 × 0.25 = 12,500 per turn               |
| `OBC` consumption at full ration  | 1,000 × 0.25 = 250 per turn                   |
| Total demand                      | 12,750 per turn                               |
| `Farms-1` output                  | 600 × 25 = 15,000 per turn                    |
| Surplus                           | 2,250 per turn                                |
| Farm labor                        | 600 × (1 `PRO` + 3 `USK`) = 600 `PRO` + 1,800 `USK` |
| `Farms-1` cap on the planet       | 25 × 100,000 = 2,500,000; 600 used            |

One `Farms-1` feeds 100 population units and employs 4, so farms occupy 4% of the population they feed. `Farms-1` may
be assembled only in an `OPC` (§7.2), which is why the `OBC` grows nothing.

Stockpile: 4 turns × 12,500 = 50,000 `FOOD`, which is the four-turn target and also exactly 4 × the 25% per turn the
population may draw from a stockpile (§6.4). Five turns, the cap on what the population will accept, would be 62,500.

### 6.2 Consumer goods and pay

`OPC`, at the default rates of §6.2:

| Paid group                                  | Units  | Rate  | Per turn |
|---------------------------------------------|--------|-------|----------|
| `UEM`                                       | 2,500  | 0     | 0        |
| `SOL`                                       | 1,000  | 0.250 | 250      |
| `PRO`                                       | 4,000  | 0.375 | 1,500    |
| `USK`, including `TRNE` at the unskilled rate | 42,000 | 0.125 | 5,250    |
| `CNST`                                      | 500    | 0.500 | 250      |
| **Total**                                   |        |       | **7,250** |

`OBC`:

| Paid group | Units | Rate  | Per turn |
|------------|-------|-------|----------|
| `UEM`      | 50    | 0     | 0        |
| `SOL`      | 50    | 0.250 | 12.5     |
| `PRO`      | 200   | 0.375 | 75       |
| `USK`      | 650   | 0.125 | 81.25    |
| `CNST`     | 50    | 0.500 | 25       |
| **Total**  |       |       | **193.75** |

Combined demand 7,443.75 per turn. Factory Group 1 supplies `960 × 20 / 0.6 = 32,000` `CSGD` per year, 8,000 per turn,
a surplus of 556.25.

That output first reaches Storage Inventory on turn 5, because a batch takes four turns (§7.4). The 30,000 `CSGD` in
the `OPC`'s Storage Inventory covers `4 × 7,443.75 = 29,775`, four turns of both colonies' pay, with 225 spare.

Stockpiles are at the four-turn target: 29,000 in the `OPC`, 775 in the `OBC`. The 25% per turn the population may draw
from a stockpile at target is exactly one turn's pay in each case.

Standard of Living is pay × 4 (§6.5), computed over the whole population including unemployables:

| Colony | Consumed per turn | Population | Standard of Living |
|--------|-------------------|------------|--------------------|
| `OPC`  | 7,250             | 50,000     | 0.580              |
| `OBC`  | 193.75            | 1,000      | 0.775              |

Both are below 1.0, which §5.6 makes a malcontent driver of unspecified magnitude (`GAP-09`). The default rates cannot
reach 1.0 for any population that is mostly unskilled: 1.0 requires an average of 0.25 consumer goods per population
unit per turn and the unskilled rate is 0.125. Raising pay is a `Pay` order (§17.3).

The low opening Standard of Living is intended rather than incidental. It is the pressure toward rapid development: the
colony is solvent but not comfortable, and the discontent it accrues is what a player spends the opening turns
outrunning. Nothing in the rules says this — it is design intent recorded here, and an engine reads only the seeded
`Pay` rates and the §5.6 response to them.

### 6.3 Labor

Requirements are 1 professional + 3 unskilled per farm, mine, and factory, and 3 professionals + 1 unskilled per
laboratory (§19.4); 1 professional per 10 transports (§9.1); 1 professional per 100 trainees (§5.4).

`OPC` professionals:

| Use                        | `PRO`     |
|----------------------------|-----------|
| 600 farms                  | 600       |
| 1,100 mines                | 1,100     |
| 1,560 factories            | 1,560     |
| 100 laboratories           | 300       |
| 200 transports             | 20        |
| 1,000 trainees             | 10        |
| **Committed**              | **3,590** |
| Held                       | 4,000     |
| Spare                      | 410       |

`OPC` unskilled:

| Use                        | `USK`     |
|----------------------------|-----------|
| 600 farms                  | 1,800     |
| 1,100 mines                | 3,300     |
| 1,560 factories            | 4,680     |
| 100 laboratories           | 100       |
| `CNST`                     | 500       |
| `TRNE`                     | 1,000     |
| **Committed**              | **11,380** |
| Held                       | 42,500    |
| Spare                      | 31,120    |

`OBC`: 155 of 200 `PRO` committed (150 factories, 5 transports); 500 of 700 `USK` committed (450 factories, 50 `CNST`).

No automation is seeded. An `AUTO-1` replaces `TL^2` = 1 unskilled and occupies 4 `VU` assembled, four times the volume
of the unit it replaces (§7.6).

### 6.4 Power and fuel

Power substitutes for fuel in life supports, farms, factories, mines, laboratories, and sensors (§7.7). Power plants
require no labor, no fuel, and no resources.

| Consumer            | Rate           | Per turn |
|---------------------|----------------|----------|
| 600 `FARM-1`        | 0.5 each       | 300      |
| 1,100 `MINE-1`      | `0.5 × TL`     | 550      |
| 1,560 `FACT-1`      | `0.5 × TL`     | 780      |
| 100 `LAB-1`         | `0.5 × TL`     | 50       |
| 10 `SENS-1`         | `0.05 × TL`    | 0.5      |
| **Total demand**    |                | **1,680.5** |
| 1,750 `POWR-1`      | `1 × TL` each  | 1,750    |
| Surplus             |                | 69.5     |

Power plants are capped at `Habitability Factor × 100,000` = 2,500,000 per planet (§7.7); 1,750 are used. Power cannot
be stored.

The `OPC` therefore consumes no fuel for production. Its only fuel demand is its transports, whose rate is
`CONFLICT-11` and unrecoverable from the sources; 10,000 `FUEL` is seeded and 3,000 more is mined per turn, against
hyper engine consumption of 40 fuel per light year once the player has ships.

The `OBC` consumes no fuel either: its life supports and factories run on solar power because it is in orbit 3 (§7.8).
Outside orbits 1–5 the same colony would owe `1 × TL × 1,200 = 1,200` fuel per turn for life support alone, with no
mine of its own and no way to make it.

### 6.5 Resources

Per turn, at the seeded production:

| Draw                                    | `METL`    | `NMTL`    |
|-----------------------------------------|-----------|-----------|
| 8,000 `CSGD` at 0.2 / 0.4               | 1,600     | 3,200     |
| 30,000 `STRC-1` at 0.07 / 0.03          | 2,100     | 900       |
| 75,000 `STRL-1` at 0.005 / 0.005, shipped to the `OBC` | 375 | 375  |
| **Total**                               | **4,075** | **4,475** |

Supply, at 25 ore per mine per turn and the home planet's deposit yields:

| Mine group              | Mines | Yield | Ore    | Refined |
|-------------------------|-------|-------|--------|---------|
| 1, `Fuel`               | 150   | 80%   | 3,750  | 3,000   |
| 2, `Metals`             | 450   | 40%   | 11,250 | 4,500   |
| 3, `Non-Metals`         | 500   | 40%   | 12,500 | 5,000   |

Surplus 425 `METL` and 525 `NMTL` per turn. The metals and non-metals deposits yield 40% against fuel's 80%, so those
two groups need two and a half mines for every one the fuel group needs to deliver the same refined tonnage, and
together they are 950 of the colony's 1,100 mines. The deposits are unlimited, so nothing depletes. Mines operate only in
surface colonies (§7.3), which is why the `OBC` mines nothing and is sent its resources.

Mine group limits: 3 of 6 groups per colony used, one group per deposit, at most 1,000,000 mines per deposit (§7.3).

### 6.6 Manufacturing

Factory output is `5 × TL` `MU` per factory per turn, `20 × TL` per year, and `batchQuantityPerYear = 20 × TL ×
factoryCount / massPerItem` (§7.4).

| Group          | Factories | Product  | Mass each | Per year | Per turn |
|----------------|-----------|----------|-----------|----------|----------|
| `OPC` Group 1  | 960       | `CSGD`   | 0.6       | 32,000   | 8,000    |
| `OPC` Group 2  | 600       | `STRC-1` | 0.1       | 120,000  | 30,000   |
| `OBC` Group 1  | 150       | `STRL-1` | 0.01      | 300,000  | 75,000   |

`STRL` may be manufactured only in an orbiting colony (`D-06`, §7.4), which is what the `OBC` is for. A factory-year
buys 200 `STRC-1` or 2,000 `STRL-1`, and both enclose `TL^2 / StructureRatio`, so the orbiting colony is the cheap
source of enclosure and the surface colony is the self-sufficient one.

This is also why nothing in the starting position is made of `STRL`. Both colonies are seeded with `STRC`, the only
structure item that can exist before an orbiting colony does, and the `OBC`'s factory group is what puts `STRL` into
the game. From turn 5 it delivers 75,000 `STRL-1` per turn: 9,375 `VU` of enclosure if assembled where it is made, or
75,000 `VU` if shipped to the `OPC`.

### 6.7 Life support

Required in an `OBC`, not in an `OPC` (§6.6). Capacity is `TL^2` population units per assembled life support, so at
`TL` 1 one `LSU-1` sustains one population unit — 100 individuals.

| Figure                          | Value |
|---------------------------------|-------|
| `OBC` population                | 1,000 |
| `LSU-1` assembled               | 1,200 |
| Life Support Capacity           | 1,200 |
| Headroom                        | 200   |
| Volume of the life supports     | 9,600 `VU`, against 1,000 `VU` of people |

The headroom is load-bearing. Life Support Capacity is the sustainable maximum for an `OBC` (§5.4) and population in
excess of capacity dies immediately (§5.5), so a colony seeded at exactly capacity would lose every birth in the turn
it occurred.

Each `OBC` population unit therefore costs 1 `VU` of itself plus 8 `VU` of life support, and at Structure Ratio 8 that
is 72 structure units per population unit before anything it eats, wears, or works with.

### 6.8 Enclosure

`OPC`, Structure Ratio 1:

| Contents                       | Volume      |
|--------------------------------|-------------|
| Assembled farms, mines, factories, laboratories, power plants, sensors | 91,840 |
| Population, 50,000 units       | 50,000      |
| `TRNS-1` in Storage Inventory  | 800         |
| `FUEL` in Storage Inventory    | 5,000       |
| `CSGD` in Storage Inventory    | 9,000       |
| `FOOD` stockpile               | 150,000     |
| `CSGD` stockpile               | 8,700       |
| `METL` and `NMTL`              | 0, exempt   |
| **Total enclosed**             | **315,340** |
| 350,000 `STRC-1` × `1 / 1`     | 350,000     |
| **Space Available**            | **34,660**  |

`OBC`, Structure Ratio 8:

| Contents                       | Volume       |
|--------------------------------|--------------|
| 1,200 `LSU-1`                  | 9,600        |
| 150 `FACT-1`                   | 2,100        |
| Population, 1,000 units        | 1,000        |
| `TRNS-1` in Storage Inventory  | 200          |
| `FUEL` in Storage Inventory    | 500          |
| `METL` and `NMTL` in Storage Inventory | 1,500 |
| `FOOD` stockpile               | 3,000        |
| `CSGD` stockpile               | 232.5        |
| **Total enclosed**             | **18,132.5** |
| 200,000 `STRC-1` × `1 / 8`     | 25,000       |
| **Space Available**            | **6,867.5**  |

Stated the other way (§4.2), the `OBC`'s Structural Requirement is `18,132.5 × 8 = 145,060` and its 200,000 assembled
structure units supply `TL^2` each, so 200,000, leaving 54,940 — which is the 6,867.5 `VU` above.

Two consequences of the seeded numbers are worth reading off. The `OPC`'s food stockpile is 150,000 of its 315,340
`VU`, nearly half the colony's structure, because `FOOD` is 3 `VU` per unit and a four-turn stockpile is one food unit per
population unit. And both structure counts are chosen so the summed enclosure capacity is an integer, so no rounding
rule is needed at setup (`GAP-17`).

### 6.9 Transport

Transports carry `20 × TL^2` = 20 `MU` per transport per turn, over a range of 1 `TDU`, crewed at 1 professional per
10 (§9.1). The `OBC` is at distance 1, so both directions are in range.

`OPC` to `OBC`, per turn:

| Cargo                    | Quantity | Mass each | `MU`         |
|--------------------------|----------|-----------|--------------|
| `FOOD`                   | 250      | 6         | 1,500        |
| `CSGD`                   | 193.75   | 0.6       | 116.25       |
| `METL`                   | 375      | 1         | 375          |
| `NMTL`                   | 375      | 1         | 375          |
| **Total**                |          |           | **2,366.25** |
| Capacity, 200 `TRNS-1`   |          |           | 4,000        |

`OBC` to `OPC`, per turn: 75,000 `STRL-1` at 0.01 `MU` = 750 `MU`, against 1,000 `MU` of capacity from 50 `TRNS-1`.

The `OPC`'s spare 1,633.75 `MU` covers a survey, which costs 200 `MU` (§13.5), and set-up and add-on work. Whether all
of these draw on one pool is `GAP-23`.

Transfers resolve in stage 6 and production in stage 15 (§3), so each turn's supply run reaches the `OBC` before it
eats.

### 6.10 Reconnaissance and research

| Figure                              | Value                                                    |
|-------------------------------------|----------------------------------------------------------|
| Probe capacity, 10 `SENS-1`         | `1 × TL` each = 10 probes per turn                       |
| One magnitude-10 system probe costs | 10, the maximum magnitude (§13.4)                         |
| Research output, 100 `LAB-1`        | `0.25 × TL` each = 25 research points per turn           |
| Cost of `TL` 1 → 2                  | 1,000,000 research points (§8.2)                          |

Research at the seeded laboratory count is 40,000 turns from the next Tech Level. Laboratory output scales with
laboratory count and the colony's ceiling is `Habitability Factor × 10,000,000` = 2,500,000 population units, so the
route to a Tech Level is growth first.

### 6.11 Growth headroom

| Limit                                   | Value                                                        |
|-----------------------------------------|--------------------------------------------------------------|
| `OPC` sustainable population            | 25 × 10,000,000 = 250,000,000 individuals = 2,500,000 units; 50,000 seeded |
| `OBC` sustainable population            | Life Support Capacity, 1,200 units; 1,000 seeded            |
| `OPC` birth range                       | 0.25% to 10% of 50,000 = 125 to 5,000 units per turn (§5.4) |
| Food surplus supports                   | 2,250 / 0.25 = 9,000 further population units                |
| Space Available supports                | 34,660 / 4 ≈ 8,665 further units, at 1 `VU` each plus 3 `VU` of stockpile food |
| Trainee graduation                      | 5% of 1,000 = 50 new `PRO` per turn                          |
| Soldier retirement                      | 5% of 1,000 per year = 50 `PRO` per year                     |
| Constructor throughput, `OPC`           | 500 × 500 = 250,000 `MU` assembled per turn                  |
| Constructor throughput, `OBC`           | 50 × 500 = 25,000 `MU` assembled per turn                    |

Professionals are the binding constraint on deploying the 31,120 unassigned unskilled: every farm, mine, and factory
needs 1 professional per 3 unskilled, and professionals arrive only by trainee graduation and soldier retirement.

---

## 7. What the seed rests on

Rules the numbers depend on, and open questions they avoid.

| Item          | Bearing on the starting position                                                                                          |
|---------------|---------------------------------------------------------------------------------------------------------------------------|
| `D-05`        | One structure unit encloses `TL^2 / StructureRatio`. Sets both structure counts.                                          |
| `D-06`        | `STRL` is manufacturable only in an orbiting colony. The `OBC`'s reason to exist, and the reason both colonies are seeded with `STRC` instead. |
| `D-12`–`D-14` | Cadre are assignments, counted once. A `WRKR` adds no mass, volume, food, or consumer goods.                              |
| `D-19`        | Both colonies hold soldiers and professionals, so neither is independent on turn 1.                                       |
| `D-21`        | Fixes everything above.                                                                                                   |
| `D-22`        | Stockpiles are enclosed. Half the `OPC`'s structure exists to hold its food.                                              |
| §7.8          | Solar power in orbits 1–5. Without it the `OBC` owes 1,200 fuel per turn it cannot obtain, and is not viable at `TL` 1.   |
| `CONFLICT-02` | Ration bounds. The seeded 100% is in bounds under both readings, so the conflict is untouched.                            |
| `CONFLICT-11` | Transport fuel. Unrecoverable; the seeded 10,000 `FUEL` and 3,000 per turn cover it under any reading of a `TL` 1 rate.   |
| `GAP-01`      | The rest of the home system, and the yields of deposits other than the three named.                                       |
| `D-24`        | Every game starts at `TL` 1, so this is the seed rather than one of a family of them.                                     |
| `GAP-03`      | Player count and home system separation. Neither bears on the contents of the two colonies.                              |
| `GAP-07`, `GAP-08` | Birth and death rates. The seed sets initial populations, not the rates that move them.                             |
| `GAP-09`      | Discontent. Both colonies begin below Standard of Living 1.0; the malcontent response is unspecified.                     |
| `GAP-12`      | Work in process. Avoided: no factory group is seeded with any.                                                            |
| `GAP-13`      | Mass accounting. The totals here count everything the entity holds.                                                       |
| `GAP-17`      | Rounding. Avoided: every enclosure sum is an integer.                                                                     |
| `GAP-23`      | Whether transport capacity is one pool. Bears on the spare capacity, not on the supply run.                               |
| `GAP-32`      | Whether laboratory staff are `WRKR`. No count depends on it.                                                              |
| `GAP-57`      | Upkeep of independent entities. The `OBC` grows no food and would starve if it left play.                                 |
| `GAP-58`      | Pay rates for `WRKR`, `POL`, `RBL`, `SPY`. Avoided: none of the four is assigned at setup.                                |
