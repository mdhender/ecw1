# Entity model

**Status: not normative.** This document is downstream of `docs/reference/rules-manual.md`, not input to it. The
manual states the rules; this states how an implementation may represent them. Where the two disagree, the manual
governs and this document is wrong. Nothing here may be cited to settle a rule, and no `GAP-nn` may be closed from here.

It exists because `D-19` keeps the Faction layer out of the rules while leaving it available to the engine.

## What the rules say

The manual recognises exactly two relations for an entity
([§4.8](./reference/rules-manual.md#48-ownership-and-independence)):

- An entity is owned by exactly one player, or by none, in which case it is independent.
- An entity executes orders from its owner and from no one else. An independent entity executes none.

Everything below is a representation choice underneath those two sentences. None of it is visible to a player, and no
rule outcome may depend on which representation is chosen.

## Why not an attribute on the entity

The direct encoding is an `owner` column on the entity, nullable for independence. It is correct and it is what the
rules describe. Its cost is that every event that moves the direction of orders — a player leaving, a player
returning, a player handing the seat to someone else for a few turns — rewrites one row per entity owned. A mature
position owns hundreds.

A second column is then needed for entities directed by something that is not a player, which leaves two nullable
columns encoding one fact, and an invariant that at most one is set.

## The indirection

    (Player | Agent) --o< Faction --o< Entity

- A **Faction** owns entities. Every entity belongs to exactly one.
- A Faction is **directed** by exactly one agency: a player, or a computer agent.
- Transfers of direction move Factions between agencies. Entities do not move and are not rewritten.

The rules never see a Faction. A player owning an entity, in manual terms, is that player directing the Faction that
owns it.

## What the agencies are

| Agency               | Directs                                                                                      |
|----------------------|----------------------------------------------------------------------------------------------|
| Player               | The Factions of a position an account is actively playing                                    |
| Vacation agent       | A position whose player has stepped out for a few turns, issuing basic orders until they return |
| Delegate (a player)  | A position whose player would rather another player issue its orders than the vacation agent  |
| Estate agent         | A position whose account has left the game                                                    |
| Independents         | Entities that have gone independent under the rules; issues no orders at all                  |

There may be many agents of a kind — one estate agent per position, rather than one shared. The rules do not
distinguish agents from one another and must not be made to.

## Invariants the engine owes the rules

1. **Independence is a rules fact, not an agency fact.** An entity is independent when it holds no `SOL` and no `PRO`
   outside the `RBL` cadre. Assigning it to an independents agency is bookkeeping that follows; it is never what makes
   it independent, and an entity may not be made independent by moving it.
2. **Independence is sticky.** An independent entity that grows its own professional stays independent. Only a player
   *placing* a soldier or professional in it ends the state, which under the current order set means invasion.
3. **One position per estate.** A position that leaves the game keeps its identity. It is a distinct rival for
   [§18.3](./reference/rules-manual.md#183-solo-victory) and its Factions are recoverable as a unit if the account
   returns — which is the reason to keep the estate whole rather than folding it into a shared pool.
4. **Delegation confers nothing.** A delegate directing another position's Factions gains no ownership, no diplomatic
   status, no holding, and no credit toward any victory condition. The position is unchanged; only the source of its
   orders differs.
5. **Holding is computed, never stored on the agency.** Both tests in
   [§18.2](./reference/rules-manual.md#182-holding) read population and ownership at evaluation time.

## Open where the rules are open

- Delegation gives a human delegate full sight of the position it plays, which routes around the visibility rights in
  [§15.2](./reference/rules-manual.md#152-rights-conferred). Out of game, so not a gap in the manual, but the
  implementation should decide it deliberately rather than by omission.
- Independent entities have no stated upkeep (`GAP-57`). Until that is settled, an engine has no defined behaviour for
  their food, pay, life support, or production, and must not invent one.
