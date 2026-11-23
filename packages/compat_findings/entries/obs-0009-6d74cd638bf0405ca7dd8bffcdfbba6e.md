---
id: obs-0009-6d74cd638bf0405ca7dd8bffcdfbba6e
type: observation
timestamp: 2026-11-23T17:22:06Z
last_updated: 2026-11-23T17:22:06Z
author: Claude Coffer Maker
contributors: ["Claude Coffer Maker"]
tags: []
---

## Compat finding 9: the satisfy link as drawn in Capella 6.1, compared with the patched link

**Result:** the corrected link reads back cleanly and differs from the patched one in type and in what the read-back shows. Two questions are left open for the tool maintainer.

1. **Tool and calls:** `pull_model_changes`, then `resolve_model_uuids` on the 16 requirements, `generate_fabric`, and `verify_model(phase="OA")`.
2. **Context:** Capella 6.1 model `K Cup Coffee Maker.aird`. Incoming commit `45acd73` ("Reversed link.", committed in Capella 6.1 by the engineer, 2026-11-23, 12:19 US Eastern). The engineer deleted the reversed link created by patch (Compat finding 7) and drew a replacement in Capella.
3. **What the read-back shows now**
   - **UR-B-04** (`d3a7b156-986c-43e0-b9df-019d4ba8a963`) has exactly **one** relation, UUID `c90cce26-1f23-4ad3-957f-525809f17d6d`. The patched relation (`1a869ad3-880c-46ad-994d-5864d0162076`) is gone.
   - The new relation has **name "Satisfy"**, type **`CapellaOutgoingRelation`**, empty short name and description, and **relation type "None"**. The read-back lists `source` = the requirement (shown by its long name, "Coffee preparation cycle time") and `target` = the capability "Prepare a serving of hot coffee on demand" (`b9407bbc-98fa-4479-bdf4-7d82b519bcd8`).
   - No other requirement has a relation yet; the other 15 are unchanged.
   - `verify_model(OA)`: no findings (0 issues). The pull was a clean fast-forward.
4. **Comparison with the patched link**
   | | Patched link (finding 7) | Link drawn in Capella |
   |---|---|---|
   | Type read back | `CapellaIncomingRelation` | `CapellaOutgoingRelation` |
   | Type requested in the patch | `CapellaOutgoingRelation` | n/a |
   | Name | empty | "Satisfy" |
   | Source and target in read-back | **not shown** | shown |
   | Direction (engineer, in Capella) | reversed | as intended (satisfy) |
5. **Observation A, type names:** the patch asked for `CapellaOutgoingRelation` and produced a `CapellaIncomingRelation`; the link drawn in Capella as intended is `CapellaOutgoingRelation`. The type names therefore look **swapped between the patch input and the stored result**. **Hypothesis, not tested:** requesting `_type: CapellaIncomingRelation` from the requirement's side may produce the satisfy-direction link. This would also fit the error in finding 7, where the message named the Incoming class although Outgoing was requested.
6. **Observation B, read-back detail differs by type:** for the Capella-drawn (outgoing) relation, `generate_fabric` shows name, short name, relation type, source and target. For the patched (incoming) relation it showed only type and description. Showing the same fields for both would let the tools confirm direction.
7. **Observation C, `source` and `target` labels:** the read-back lists the requirement as `source` and the capability as `target` for a link the engineer describes as running from the element to the requirement. So the `source` and `target` labels in the read-back **should not be taken as proof of direction** without checking in Capella. Which field the labels come from is a question for the tool maintainer.
8. **Observation D, link name and long name in the UI:** the read-back shows the relation's name as "Satisfy". The engineer reports that **a link's long name is not visible in the Capella UI**, so where "Satisfy" comes from (a default set by Capella, or entered by the engineer) is not known from here. Worth checking which field the UI shows for a link and which one the tools read.
9. **Relation type "None":** the relation has no relation type set, though it is named "Satisfy"; the tools do not show whether Capella's own relation types were involved.
10. **Reproduction:** open a session at `c3c7b804`, pull `45acd73`, then run `generate_fabric` with UR-B-04 resolved.
11. **Scope:** none observed beyond the points above; likely about library accessors rather than 6.1.
12. **Workaround in use:** satisfy links are drawn in Capella.
13. **Check in Capella 6.1:** done by the engineer for the direction. Still to confirm: which link field the UI shows (observation D).
14. **Tool version or build:** not known (open item in DEC-0007).

**Suggested follow-ups, if the engineer agrees:** test the hypothesis in observation A on one requirement and capability pair (a local patch, then the engineer checks in Capella); then, if it works, create the remaining links by patch.
