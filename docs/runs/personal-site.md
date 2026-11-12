# personal-site

Personal site and its rendered hero scene (Blender).

**Repo:** Not published.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

### 2026-11-09 personal-site: hero room render polish (Blender room.py)
- Task: polish the rainy-window neon desk render toward a photoreal baked-light bar (owner's quality bar: a named reference site, in the owner's own style). Render only; baking and page rebuild not started.
- Units: U1 city view (3 review rounds), U2 glass/lighting/hologram/lamp (2), U3 props/plant/curtains (2), U4 hero props (1, folded into U5), U5 surface wear + CRT + final fixes, final review (APPROVED, no blockers).
- Agents/models: workers sonnet for U1-U2 r1 (haiku default overridden), opus from U2 r2 on (owner asked); reviewer opus; researcher unused.
- Finding kinds: U1 r1 spec mismatch / look not at bar (8 items); r2 value tuning (scale, brightness, ground); U2 r1 geometry bug (light inside opaque bulb + closed shade) + colour balance; U3 r1 occlusion (mouse hidden by mug), look; U4 dead geometry (note hidden). Same-hole rule not triggered; 3rd-round stop reached once (U1 r3, approved).
- Measured subagent tokens: workers 876,045; reviewers 400,577 (all 9 reviews measured); total 1,276,622. Lead tokens: not measured. Rows in the Cost section of this file.
- Lessons: (1) a worker's "fixed" often hid a visible regression (CRT blown out after the wear pass): the Lead must look at the render itself. (2) Check geometry before blaming wattage: lamp light was inside an opaque bulb. (3) Blender resolves relative out paths against the drive root (wrote C:\city once, deleted with the owner's yes): always absolute paths. (4) sed -i breaks CRLF in room.py: use Edit. (5) Parallel review needs frozen copies (.scratch/review). (6) Opus workers were faster per task than sonnet; long units (U3 r1 19 min) are mostly Blender render time: use 16-sample 50% previews.
- Deviations: first run wrote C:\city (outside project); deleted after the owner said "delete it". One read-only Blender listing ran without TEMP/TMP set (no files known).
- Addendum 2026-11-09 (personal-site): polish pass (window texture, label ink, desk edge, CRT text tints, plant variation) approved. Added measured tokens: worker 102,988; reviewer 34,862. Updated totals: workers 979,033; reviewers 435,439 (34,862 added for the polish review; final-review figure 34,712 was already in the 400,577).
- Outcome 2026-11-09 (personal-site): the owner rejected the finished render as too close to the reference site. Direction changed to an original Kanpeki-Plaza-from-Arasaka-Tower scene. Lesson: the quality bar was taken as the reference composition; the Lead should have checked originality of the concept (desk, window, CRT, tape stack) before spending ~1.4M tokens on polish. Ask for the concept, not only the quality bar, when a reference site is named. Art units themselves ran clean (reviews approved), but the product call was wrong.

## Cost

### personal-site hero room render (2026-11-09)
| Unit / round | Agent | Model | Tokens (measured) | Duration | Result |
|---|---|---|---|---|---|
| U1 city view r1 | worker | sonnet | 91,574 | 8m 0s | reviewer CHANGES_REQUESTED |
| U1 review r1 | reviewer | opus | 32,364 | 1m 1s | CHANGES_REQUESTED |
| U1 rework r2 | worker | sonnet | 75,818 | 8m 19s | CHANGES_REQUESTED |
| U1 review r2 | reviewer | opus | 37,707 | 1m 12s | CHANGES_REQUESTED |
| U1 rework r3 | worker | sonnet | 40,959 | 2m 57s | |
| U1 review r3 | reviewer | opus | 32,891 | 0m 39s | APPROVED |
| U2 glass/lighting r1 | worker | sonnet | 80,995 | 8m 35s | |
| U2 review r1 | reviewer | opus | 58,144 | 1m 35s | CHANGES_REQUESTED |
| U2 rework r2 | worker | opus | 59,299 | 4m 28s | |
| U2 review r2 | reviewer | opus | 31,049 | 0m 41s | APPROVED |
| U3 props r1 | worker | opus | 139,202 | 19m 30s | |
| U3 review r1 | reviewer | opus | 78,410 | 2m 21s | CHANGES_REQUESTED |
| U3 rework r2 | worker | opus | 93,540 | 9m 36s | |
| U3 review r2 | reviewer | opus | 29,945 | 0m 54s | APPROVED |
| U4 hero props | worker | opus | 106,847 | 15m 33s | |
| U4 review r1 | reviewer | opus | 65,355 | 3m 38s | CHANGES_REQUESTED (hidden note; rest folded into U5) |
| U5 surface wear | worker | opus | 151,374 | 12m 13s | |
| U5 fixes + 128 render | worker | opus | 36,437 | 5m 9s | |
| Final review | reviewer | opus | 34,712 | 1m 1s | APPROVED |
Researcher: not used. Lead (main chat) tokens: not measured.
| Polish pass + 128 render (personal-site) | worker | opus | 102,988 | 8m 4s | APPROVED by reviewer |
| Polish review | reviewer | opus | 34,862 | 0m 30s | APPROVED |
(Polish worker was the last opus worker, owner's call. Interim notice for it showed 98,684; the final figure is the one above.)
