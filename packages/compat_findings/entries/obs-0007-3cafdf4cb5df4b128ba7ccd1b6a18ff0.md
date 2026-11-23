---
id: obs-0007-3cafdf4cb5df4b128ba7ccd1b6a18ff0
type: observation
timestamp: 2026-11-23T17:00:11Z
last_updated: 2026-11-23T17:00:52Z
author: Claude Coffer Maker
contributors: ["Claude Coffer Maker"]
tags: []
---

## Compat finding 7: linking a requirement to a capability by patch (first try failed with a clear error, second try accepted; direction not verifiable from the tools)

**Result:** a relation object was created and committed; whether it points from the requirement to the capability, and in which direction, **cannot be confirmed from the tools**. **Pending:** the engineer's check in Capella 6.1. Also in this batch: two more entity involvement links, confirmed.

1. **Tool and calls**
   - **Attempt 1** (`apply_model_patch`), requirement `UR-B-04` (`d3a7b156-986c-43e0-b9df-019d4ba8a963`) as parent:
     ```yaml
     - parent: !uuid d3a7b156-986c-43e0-b9df-019d4ba8a963
       extend:
         relations:
           - _type: CapellaOutgoingRelation
             source: !uuid d3a7b156-986c-43e0-b9df-019d4ba8a963
             target: !uuid b9407bbc-98fa-4479-bdf4-7d82b519bcd8
     ```
     **Error, nothing committed:** `capellambse.extensions.reqif.capellarequirements.CapellaIncomingRelation() got multiple values for keyword argument 'source'`.
   - **Attempt 2:** the same patch without `source:` (only `_type` and `target`). Status `ok`, committed.
   - **Involvement links:** `extend: involved_entities:` adding Utilities Supply to two capabilities (Prepare a serving of hot coffee on demand, Keep the equipment clean and performing). Status `ok`, committed.
   - Read-back with `resolve_model_uuids`, `generate_fabric`, and `verify_model(phase="OA")`.
2. **Context:** Capella 6.1 model `K Cup Coffee Maker.aird`. Pushed state before this finding: `c3c7b804`. New local commits: `43e07267` (two involvement links) and `85b33268` (requirement relation test). **Neither is pushed.** Target capability: `Prepare a serving of hot coffee on demand` (`b9407bbc-98fa-4479-bdf4-7d82b519bcd8`).
3. **Expected vs actual**
   - **Attempt 1:** the `source` is supplied automatically from the parent, so giving it too causes a duplicate keyword argument. The error text names `CapellaIncomingRelation` even though `CapellaOutgoingRelation` was requested.
   - **Attempt 2:** accepted. `generate_fabric` now lists a `relations` entry on UR-B-04: UUID `1a869ad3-880c-46ad-994d-5864d0162076`, type **`CapellaIncomingRelation`**, empty name and description.
   - **Involvement links:** both confirmed in `generate_fabric` (the capabilities now list Utilities Supply, with the earlier entities unchanged).
   - `verify_model(OA)`: no findings (0 issues).
4. **Observation A, relation type:** the object reads back as `CapellaIncomingRelation` although `CapellaOutgoingRelation` was requested. It is unclear whether the tool maps the requested type, whether the parent decides the type, or whether naming differs between the call and the read-back.
5. **Observation B, target and direction not visible:** `generate_fabric` shows the relation's UUID on the requirement but not its source or target, the capability's fabric shows no requirement link, and `traceability_artifacts` is 0. So the tools **cannot confirm** that the relation points at the intended capability, or its direction (requirement to element, or element to requirement).
6. **Observation C, no relation type or label:** the relation has no name, and the test did not set a relation type (for example a satisfaction or trace kind).
7. **Reproduction:** open a session at `c3c7b804`, run attempt 2 against the UUIDs above, then `generate_fabric` and look for the `relations` entry on UR-B-04.
8. **Scope:** unknown; nothing points specifically to 6.1. Requirement relations may already be covered by the 7.0.1 suite.
9. **Workaround:** omit `source:`; if direction matters, create the link in Capella and compare.
10. **Check in Capella 6.1:** **pending** after push. To check: open UR-B-04 and see whether it has a relation to "Prepare a serving of hot coffee on demand", the direction shown, and whether the capability lists UR-B-04 among its linked requirements; also that the model opens without warnings.
11. **Tool version or build:** not known (open item in DEC-0007).

**Not yet covered:** creating the same link in Capella and comparing it with the patched one; relation types; linking requirement to requirement; linking to entities or to elements outside OA; removing a relation.

---

**Update: pushed.** With the engineer's confirmation, `push_model_changes` returned status `ok` ("Pushed to refs/heads/master") for both local commits, `43e07267` (two involvement links) and `85b33268` (requirement relation test). This is the fourth successful push on the 6.1 model. **Still pending:** the engineer's check in Capella 6.1 (item 10): whether UR-B-04's relation points at "Prepare a serving of hot coffee on demand", its direction, and whether the capability lists the requirement.
