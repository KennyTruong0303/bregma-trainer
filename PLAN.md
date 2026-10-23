# Bregma/Lambda Trainer — Plan

Interactive simulator for locating bregma and lambda on C57BL/6J mouse skulls under a surgical microscope view, with saline, drying, swab and pressing interactions.

Status: **v2a published** (2026-10-09): https://claude.ai/artifact/HswWj8N1oRW1yDkGAPJBQA. Difficulty currently only changes guidance (Easy guide, Medium hints, Hard none); the skull mix is the same at every level. v2a (Surgery mode core run) published; v2b not started.

## Decisions

| Topic | Decision |
|---|---|
| Animal | C57BL/6J mouse, age slider 4–20 weeks |
| View | Top-down surgical-microscope view (zoom/pan, scale bar) |
| Visual style | Procedural realistic (generated in code; unlimited variants; the correct bregma/lambda are always known) |
| Look reference | Research reference photos online (papers, protocols); use them only to tune colors, textures and suture appearance. Copyrighted images are never embedded. Keep refining the look as I go. |
| Modes | Quiz/practice, free exploration, stereotaxic coordinates, progress tracking |
| Difficulty | Easy / Medium / Hard |
| Bregma/lambda convention | Paxinos best-fit (bregma = best-fit coronal curve crossing the midline; lambda = midpoint of the best-fit lambdoid curve at the midline) |
| Skull variants | Offset/curved coronal sutures, ambiguous lambda, surgical noise (tilt, tissue, bleeding, glare, extra small sutures, interparietal variation) |
| Press down | Plates shift slightly at the sutures (seams show as shadow lines); bone blanches around the pressure point while the sutures keep their color |
| Moisture | 4-stage curve: wet (vessels/sinuses visible, sutures faint) → just dried/tacky (sutures at peak contrast) → over-dry (chalky, sutures fade) → re-wet resets |
| Drying tool | Cotton swab / Kimwipe drag for local drying |
| H₂O₂ | Yes: bubbles, clears tissue, briefly boosts suture contrast; warning on overuse (v2) |
| Scoring | Error in µm with an adjustable pass threshold (default 100 µm), flat-skull check, timed, best-fit explanation after each attempt |
| Progress | Just you, saved in your browser (localStorage, with try/catch), CSV export |
| Delivery | Local project here, plus a private shareable page on claude.ai (same link, updated each version) |

## Versions

### v1: find bregma and lambda
- Seeded skull generator (units in mm):
  - bone plates: frontal, parietal, interparietal
  - sutures: sagittal, left and right coronal (independent offset, curvature, interlocking zig-zags), lambdoid (with an ambiguity setting)
  - vessels: superior sagittal sinus, transverse sinuses, smaller vessels
  - surgical noise
- Age model: skull size and bregma–lambda distance scale with age; older skulls are less translucent and their sutures less distinct.
- Easy/Medium/Hard presets.
- Correct-answer engine: least-squares Paxinos best-fit on the generated sutures. The reveal draws the same construction that is scored.
- Rendering:
  - Layer maps generated once per skull, composited in a WebGL shader.
  - A live moisture map controls translucency, specular shine, vessel visibility and suture contrast.
  - Focus-blur gradient when the skull is tilted.
- Tools:
  - saline dropper (the drop spreads)
  - swab/Kimwipe (local wicking; overdoing it causes over-dry)
  - press (hold)
  - marker
  - zoom/pan
- Quiz: mark bregma → mark lambda → submit. Reports:
  - µm error for each point
  - measured vs. true bregma–lambda distance
  - time
  - pass/fail
  - animated best-fit reveal with a written explanation
- Explore mode: overlay toggles (sutures, bone names, sinuses, fit lines) and generator sliders.
- **Check-in:** you review how it looks at the end of v1.

### v1.1: match the look to the user's photos, fix saline, redo the UI (done)
Based on the user's two scope photos of a real exposed C57BL/6J skull and their v1 review.

**Look**
- Bone: warm salmon/peach, slightly paler than the photos (phone cameras over-saturate), evenly translucent, fine granular speckle. Replaces v1's mauve-cream.
- Vessels: many thin, wiggly red capillaries across the bone instead of a few thick branching trees; the sagittal sinus becomes a narrow reddish stripe instead of a wide purple band; the posterior end is redder over the transverse sinus.
- Sutures: faint, red and blood-tinged, instead of dark brown zig-zags.
  - The sagittal suture is a thin, dotted red line.
  - The coronal suture is a reddish smear-line.
  - Overall harder to see; they are clearest when tacky and pressed.
- Edges: the retracted skin edge is white/grey fascia, not a pink glowing rim; the fur is wet, spiky black; the window is a long, narrow almond shape.
- Glare: small bright specks on wet spots instead of broad sheen.

**New skull variants (random per skull)**
- Stray black hairs lying on the bone; swabbing moves or removes them.
- A glistening wet/tissue patch over lambda that hides the junction until it is swabbed clean.
- Blood along the sutures, in varying amounts.

**Saline and drying**
- Saline evaporates very slowly (minutes), so you must swab it off.
- After swabbing, the bone stays tacky for 1–2 minutes, then over-dries. The drying-speed setting still scales this.
- The skull starts with the surface wet.

**UI**
- The scope view takes most of the screen.
- A floating toolbar over the view: Mark (bregma/lambda), Saline, Swab, Press, Pan, zoom, plus the surface-state readout.
- The side panel is slimmed to the task, timer and result. Skull settings, overlays, variant sliders and settings move into collapsible drawers.
- A polish pass on spacing, type and states; the mobile layout keeps the toolbar over the view.

**Unchanged:** the answer engine, scoring, explanations, seeds, age model, and Easy/Medium/Hard (still identical). Republish to the same link.

### v1.2: vessels, harder landmarks, dry start, guidance and instrument look (done)
From the user's v1.1 review.

**Vessels**
- Structured rather than scattered: cortical vessels branch off the midline sinus and run laterally in smooth, gently curving arcs that taper and fork, plus a few that enter from the lateral edges. No random "worms".
- Softer and fainter, clearly *under* the bone: blurrier edges, lower contrast, more visible when wet and fading as the bone dries.

**Harder landmarks**
- Fainter sutures overall; you have to work the moisture and pressing to see them.
- Blood along the sutures is rarer and weaker, so it no longer gives the junctions away.
- More vessel confusion: vessels cross or run alongside near bregma and lambda, and some run parallel to the sutures and look like them.

**Moisture and hair**
- The skull starts **dry**. You add saline to wet it (vessels show) and swab back to tacky (sutures sharpest).
- Hair: saline floats a hair loose so it drifts slightly, then swabbing removes it. A dry hair stays stuck and the swab doesn't lift it.
- The tissue patch over lambda behaves as before.

**UI**
- Guidance: a step guide in the panel that follows the real workflow, with the current step highlighted and advancing on its own (wet the skull → clear the hairs → swab to tacky → press to check seams → mark bregma → mark lambda → submit). The hint line adapts to what's under the cursor, e.g. "Hair is stuck. Add saline first."
- Instrument character: the scope field gets an eyepiece-style rim with a subtle reticle and tick marks, a magnification dial feel for zoom, and a styled panel like instrument labels, keeping the current colours.

**Unchanged:** scoring, the answer engine, variants, seeds and the Easy/Medium/Hard behavior. Republish to the same link.

### v1.3: press shows every seam, vessels and sutures, dry/wet looks, guidance levels, UI polish (done)
From the user's v1.2 review.

**Press**
- Pressing shows *all* sutures inside the pressed area at once: at bregma, the left and right coronal arms and the midline together. Each bone plate under the pressure moves by a different amount, so every seam between plates shows a visible step, not just the borders of one plate.
- Larger effect area and stronger blanching: the bone around goes clearly white while the seams stay dark.
- A clearer plate-edge step/shadow when pressed.

**Vessels**
- More visible again, closer to the photos: clearer red.
- Less "drawn": irregular, with a fine network of small vessels linking the larger branches, varying widths and kinks instead of smooth arcs.

**Sutures**
- Darker grey-brown seams instead of red lines.
- More wavy/jagged: visible interlocking zig-zag.
- Read as a translucent seam (thinner bone along the line lets the darker tissue show through), not a painted line.
- Blood along sutures stays as an occasional variant.

**Dry / wet / tacky**
- Dry keeps more salmon color (less plaster-white); still paler and matte.
- Wet is glossier and more see-through: stronger specular sheen and higher translucency.
- Tacky looks clearly different from wet: matte satin, no film sheen, best suture contrast.
- Less glinting sparkle at the edges of the bone.

**Guidance and eyepiece**
- Guide by difficulty: Easy shows the full step list and hints; Medium shows short hints only; Hard shows none. (This is the first real Easy/Medium/Hard difference.)
- A "Show guide" toggle overrides it.
- Remove the reticle (crosshair and ticks) from inside the field.
- Simplify the rim to a plain eyepiece edge with no tick marks.

**UI polish**
- Panel: tighter structure with less empty space. The procedure, timer and result live in one clear block; the drawers get compact summary lines, e.g. "Skull · 10 wk · seed 8511…".
- Toolbar: more instrument character, with custom icons for the swab/dropper/press and a grouped bregma/lambda selector with marker colors.
- A general polish pass on spacing, contrast and states.

### v2: full stereotaxic injection run (v2a done 2026-10-09; v2b not started)
A new **Surgery** mode runs the whole procedure end to end. Quiz and Explore stay as they are. Each stage can also be practised on its own, and the run can start from the scalp or from an exposed skull. The real wait times are minutes long, so there is a fast-forward control; the score uses simulated time.

**Stage 1: Mount in the frame**
- Ear bars, left then right; each has a depth scale, and readings should match (one kit reads 3–4 on both sides when seated). Incisor bar under the top teeth; nose clamp optional.
- Check: rock the nose. The head should rotate up and down around the ear-bar axis with no side-to-side play, and a gentle press on the head shouldn't shift it.
- Hidden consequences: asymmetric ear bars → head roll/yaw (a side-to-side tilt the user has to catch while leveling); a loose mount → the head shifts while drilling, which invalidates every recorded coordinate.

**Stage 2: Prep and exposure**
- Checklist: eye ointment, shave, three alternating scrubs, local anesthetic, toe pinch before cutting.
- Incision: drag a 12–15 mm midline cut, then retract the skin with clips. That opens the v1 window.
- Scrape the periosteum with a new scraper tool; v1's "leftover tissue" becomes real periosteum.
- **H₂O₂ tool**: bubbles on contact, clears tissue, and briefly boosts suture contrast. A warning fires on skin, and a serious warning fires inside a burr hole, because 3% H₂O₂ damages brain tissue within minutes.

**Stage 3: Find bregma and lambda (v1, unchanged).** This feeds the rest of the run.

**Stage 4: Zero and level, using the stereotaxic arm**
- The tip is moved with AP/ML/DV knobs (keyboard, scroll or drag), not by clicking, and a readout shows the three coordinates. The view shows the tip and its shadow; touching bone gives a "contact" cue.
- Zero AP/ML at your bregma. Touch the bone at bregma and at lambda, compare DV, and adjust the incisor bar (pitch) until |ΔDV| ≤ 0.05 mm (adjustable; SOPs accept < 0.1 mm).
- Changing the pitch moves bregma under the tip, so you have to re-zero. That's realistic, and a common mistake.
- Side-to-side: touch ±2 mm lateral at the target AP and adjust ear-bar height/roll until ≤ 0.1 mm.
- Bregma–lambda check against 4.21 mm, with optional coordinate scaling (× measured BL / 4.21).

**Stage 5: Target**
- Pick a target. Example coordinates come from a published IACUC SOP (AP/ML/DV from bregma): NAc core +1.1/1.2/4.5, LV +0.5/0.75/2.5, BLA −1.5/2.75/4.75, STN −1.9/1.6/4.4, dorsal CA1 −2.1/2.0/1.4. The app labels them as examples and says to check your atlas. Custom coordinates are allowed too.
- Drive the arm to the target and mark the skull.

**Stage 6: Burr hole**
- Hand drill (~0.5–1 mm hole, 0.6 mm burr): hold to drill. The bone visibly thins (pinker and translucent), then "gives".
- Pressing too long or too hard plunges through the dura (penalty: cortical damage).
- Bleeding: a site near a vessel oozes, and you clear it with the swab or an absorbable sponge. A site over the sagittal or transverse sinus bleeds heavily; the right response is pressure and letting it clot, because flooding with saline keeps it bleeding.
- Keep the dura moist with saline.

**Stage 7: Dura and needle**
- Pierce the dura with a 30 G needle, or go through with the pipette.
- Choose a delivery device: Hamilton (30–33 G) or a pulled glass pipette with a pump such as a Nanoject. It changes how the needle looks, the clog risk and the rate options.
- Clog check before lowering: expel a small drop.
- DV zero at the skull or at the brain surface, per your protocol's convention. Using the wrong reference shifts the depth by roughly the skull thickness.
- Lowering at ~1 mm/min. Slow entry dimples the brain surface, and you see the dimple.

**Stage 8: Inject, wait, withdraw**
- Pump rate and volume, with typical values from the sources: 50–100 nl/min (one SOP uses ~75 nl/min; another ≤ 0.1 µl/min) and a total of ~0.2–1 µl.
- Optional: overshoot by 0.1 mm and come back, or retract < 1 mm during the wait to make a pocket.
- Wait 2–10 min, then withdraw slowly (~0.5 mm/min).
- Injecting too fast or withdrawing early causes backflow up the track (a visible bead at the surface and a smaller dose on target).

**Stage 9: Close**
- Clean with saline, close with sutures or clips, apply ointment, record. A short checklist.

**Scoring and results**
- The actual injection site comes from the whole chain:
  - bregma marking error
  - residual AP/ML tilt: the needle path tilts by atan(ΔDV / BL), which offsets the tip at depth
  - re-zero mistakes and head shift
  - DV reference choice and dimpling
  - backflow
- Results show a 3D error (AP/ML/DV in µm) and hit/miss against a per-target tolerance.
- A **cause breakdown** says how much error each step added, e.g. "leveling 60 µm, bregma 40 µm, DV reference 300 µm".
- Results also list complications: bleeding handled or not, H₂O₂ misuse, plunge, backflow.
- A schematic depth view shows the planned vs. actual track. It is drawn by me, not copied from an atlas.

**Progress tracking:** run history in localStorage, error over time, stats by stage and by cause, CSV export.

**Difficulty hooks for later:** tighter tolerances, more sinus-adjacent targets, a loose-mount chance, more bleeding.

**Decisions (2026-10-02):**
- **Device:** pulled glass pipette with a Nanoject. Defaults: ~20–50 nl/min equivalent, 200–500 nl total, all adjustable; tip ~20–50 µm; clog check by expelling a pulse.
- **DV convention:** use DV at bregma and lambda to flatten the skull, then re-zero DV at the dura in the burr hole before lowering. The sim scores against this convention, and zeroing at the skull instead counts as an error.
- **Targets:** the SOP examples (NAc core, lateral ventricle, BLA, STN, dorsal CA1) plus a custom entry.
- **Wait:** default 10 min before withdrawing, slow withdrawal (~0.5 mm/min or slower).
- **Time:** real time by default, with a fast-forward button for waits; the score uses simulated time.
- **Scope:** everything, mounting through closing.

**Releases**
- **v2a, the core run:**
  - Stage 4: zero, level AP and side-to-side, BL check and scaling
  - Stage 5: target
  - Stage 6: drill, bleeding, sinus
  - Stage 7: dura, DV re-zero at the dura, pipette lowering, dimpling
  - Stage 8: Nanoject inject, wait, withdraw, backflow
  - results with the 3D error, the cause breakdown and the planned vs. actual track
  - the run starts from the exposed skull with bregma and lambda already found
- **v2b, the bookends:**
  - Stage 1: mounting, ear and incisor bars, wiggle check, loose-mount consequences
  - Stage 2: prep checklist, incision, retraction, periosteum scraper, H₂O₂ tool
  - Stage 9: closing
  - progress tracking: history, charts, CSV
  - "start from scalp" option

### v3: anesthesia events (Hard difficulty only)
- Reference values from the 2026-10-02 research:
  - isoflurane: induction 3–4%, maintenance 1–2%
  - respiratory rate 55–100 breaths/min: deep and < 55 suggests too deep; shallow and > 100 suggests too light
  - mucous membranes pink, never pale or blue
  - early waking: whisker and tail twitches
  - depth checks: toe pinch and corneal reflex, recorded every 15 min
- Vital signs panel: breathing rate and pattern, mucous membrane and paw color, drifting over time
- Visible cues:
  - too light: whisker twitches, leg movement
  - too deep: slow gasping breaths, pale or blue color
- Isoflurane % dial and a toe-pinch reflex check
- Consequences: a wrong or late response ends the attempt (the mouse moves off target or dies) and counts in the score

## Layout
```
bregma-trainer/
  PLAN.md       this file
  index.html    the whole app (single self-contained file, no libraries)
```
Test in the Claude browser pane, then publish as a private page.

## Research notes
- **Definitions:** bregma is where the coronal and sagittal sutures cross; lambda is where the lambdoid and sagittal sutures cross (Paxinos & Franklin). When the sutures don't meet cleanly, extrapolate smooth best-fit lines, or use a best-fit parabola on the coronal suture crossed with the sagittal suture.
- **Bregma–lambda distance:**
  - Adult C57BL/6J (about 3 months) is about 4.21 mm.
  - It shrinks about 0.8–1.0% in aged mice (23–30 months).
  - The juvenile value (about 3.7 mm at 4 weeks) is an **estimate**, since no solid data was found; it is labeled in the UI and can be edited.
- **Coordinate scaling:** multiply atlas coordinates by (measured bregma–lambda distance / 4.2).
- **Leveling:** bregma and lambda depth should match within 0.05–0.10 mm; the side-to-side check uses points 2 mm either side of the midline.
- **Moisture:** fully dry bone goes opaque and the sutures disappear, and pooled saline or blood also hides them. A lightly damp surface gives the best contrast, and 3% H₂O₂ enhances it.
- **Human error:**
  - Students' lambda errors in one study ranged from about 200 µm to over 1 mm (an automated method averaged about 260 µm).
  - Vessels along the sagittal suture can be mistaken for the bregma junction.
  - In aged mice, lambda can become indistinct.

## Sources
- [Where do you measure the Bregma for rodent stereotaxic surgery? (IBRO Neurosci Rep)](https://www.ibroneuroreports.org/article/S2667-2421(23)00060-X/fulltext)
- [Age-related morphological changes in the C57BL/6J mouse calvaria (PMC)](https://pmc.ncbi.nlm.nih.gov/articles/PMC13142485/)
- [Automatically detecting bregma and lambda points (PLOS One)](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0244378)
- [ConductScience Bregma-Lambda Helper](https://conductscience.com/tools/stereotaxic-bregma-lambda-calculator)
- [ResearchGate: typical bregma–lambda distance in adult mice](https://www.researchgate.net/post/What_is_the_typical_distance_from_Bregma_to_Lambda_in_adult_mouse_brains)
- [Biological constraints on stereotaxic targeting (Cerebral Cortex)](https://academic.oup.com/cercor/article/33/6/3293/6644718)
- [McGill SOP #202 Rodent Stereotaxic Surgery](https://www.mcgill.ca/research/files/research/202_-_rodent_stereotaxic_surgery_approved_2025.01.13.pdf)

### v2 research sources (2026-10-02)
- [UC Berkeley OLAC — Stereotaxic Compound Administration in Mice (SOP)](https://olac.berkeley.edu/sites/private/doc/StereotaxicCompoundAdministrationinMice.pdf)
- [McGill SOP #202 — Rodent Stereotaxic Surgery (2025)](https://www.mcgill.ca/research/files/research/202_-_rodent_stereotaxic_surgery_approved_2025.01.13.pdf)
- [Stereotaxic surgery for epigenetic editing in rodent brain (PMC12883188)](https://pmc.ncbi.nlm.nih.gov/articles/PMC12883188/)
- [Adaptable angled stereotactic approach (PMC8008979)](https://pmc.ncbi.nlm.nih.gov/articles/PMC8008979/)
- [Stereotaxic surgery for SCN lesions in mice (PMC8410266)](https://pmc.ncbi.nlm.nih.gov/articles/PMC8410266/)
- [AAV injection and cannula in dorsal raphe — angled approach (PMC5633075)](https://pmc.ncbi.nlm.nih.gov/articles/PMC5633075/)
- [Miniscope wiki — cranial window surgeries (sinus bleeding)](https://miniscope.org/wiki/Guide/Cranial_window_surgeries)
- [Vital signs monitoring during anesthesia in mice (PMC4329516)](https://pmc.ncbi.nlm.nih.gov/articles/PMC4329516/)
- [U Michigan — Guidelines on Anesthesia and Analgesia in Mice](https://az.research.umich.edu/animalcare/guidelines/guidelines-anesthesia-and-analgesia-mice/)
- [ConductScience — Bregma-Lambda helper / leveling](https://conductscience.com/tools/stereotaxic-bregma-lambda-calculator)
- [Value of H2O2 in neurosurgery — brain tissue effects (Pharmaceuticals 2025)](https://doi.org/10.3390/ph18040533)
