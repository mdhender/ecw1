# Turn Sequence

1. Game Data Cleanup Stage
   1. Zero value records are deleted.
   2. Dead ships and colonies are removed.
   3. Accumulator fields are zeroed out.

2. Combat Orders Stage
   1. Prefire Segment
      1. Dodge
      2. Auto Return Fire
      3. Close Proximity Targeting
   2. Pre-Maneuver Fire Segment
      1. Pre-maneuver Energy Weapon Fire
      2. Pre-maneuver Missile Fire
      3. Allocate Damage
   3. Maneuver Segment
      1. Undock
      2. Run
      3. Tactical Maneuver
      4. Close
      5. Dock
      6. Allocate Damage
   4. Post-Maneuver Fire Segment
      1. After-maneuver Energy Weapon Fire
      2. After-maneuver Missile Fire
      3. Allocate Damage
   5. Ground Combat Segment
      1. Withdraw
      2. Defensive Support
      3. Invasion
      4. Offensive Support
      5. Cycle Ground Combat

3. Permission Orders Stage
   1. Permission to Colonize
   2. Home Port Change
   3. Diplomacy

4. Disassembly Stage
   1. Disassemble
   2. Scrap
   3. Junk
   4. Merge
   5. Combine Factory Group

5. Setup Stage
   1. Define Cargo Hold
   2. Set Up
   3. Add On

6. Transfer Stage
   1. Unload Cargo
   2. Transfer
   3. Beam
   4. Pick Up
   5. Load Cargo

7. Draft Orders Stage
   1. Draft
   2. Disband

8. Assembly Stage
   1. Order Processing Segment
      1. Assemble
      2. Expend (Research Points only)
      3. Expend (using Prototypes)
      4. Factory Group Change
      5. Build Change
      6. Mine Change
      7. Shut Down
      8. Start Up
   2. Non Prototype TL Increases Segment
      1. Expend Research Points only from the Committed Research Buffer

          It does not process any orders it just goes through the committed research buckets and upgrades tech levels and removes used research from the buckets.

9. Surveys and Probes Stage
   1. S/C Probes Only
   2. Survey
   3. Launch Robot Probe

10. Pay Change Stage
    1. Pay
    2. Ration

11. Naming Orders Stage
    1. Name
    2. Note
    3. Control Planet
    4. Un-control Planet
    5. Message

12. Ship Travel Stage
    1. Jump
    2. Move

13. Probe Stage
    1. Probes

14. Give Stage
    1. Give

15. Production Stage
    1. Cycle Through Colonies
        1. Sums and reports Professionals used to pilot transports.
        2. Collects data for surveys.
        3. Totals automation capacity and life support capacity.
        4. Population Changes (Graduations & Retirements)
        5. Does production in the following order:
           1. Power Production
           2. Mine Production
           3. Farm Production
           4. Laboratory Production
           5. Factory Production
        6. Food Consumption
        7. Consumer Goods Consumption (includes ships calling this colony home port)
        8. Rebel Actions
        9. Population Changes (Births, Deaths)

           Note: LFS consume FUEL/POWER at this point

        10. Statistics updates

    2. Cycle Through Ships
       1. Sums and reports Professionals used to pilot transports.
       2. Totals automation capacity and life support capacity.
       3. Population Changes (Graduations, Retirements)
       4. Farm Production
       5. Food Consumption
       6. Rebel Actions
       7. Population Changes (Deaths)

          Note: LFS consume FUEL/POWER at this point

       8. Statistics updates

16. Produce Output Stage

17. Send Output Stage
