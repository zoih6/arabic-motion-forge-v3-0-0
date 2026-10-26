# Creative direction — identity before motion

Direction is a set of decisions, not the name of a style. A direction must be defensible in one
sentence: *"the film shows X by doing Y, and refuses Z."* If you can swap the topic and the
direction still works unchanged, it is a template.

## Method: three directions, four-axis minimum

Build three candidate directions by combining options across these axes. Two of the three must
differ on **at least four axes**, otherwise you generated one idea three times.

| Axis | Options (seeds of contrast, not templates) |
|---|---|
| `story_device` | question-first · object-biography · countdown-escalation · split-world · second-person-walk · evidence-board |
| `visual_metaphor` | living-material · blueprint-world · specimen-glass · constellation-data · single-transformation · weather-of-numbers |
| `material_language` | riso-flat · matte-depth · engraving-lines · glass-layers · cut-silhouette · terminal-precision |
| `typography_architecture` | monolith-headline · stacked-kinetic-lines · annotation-field · margin-notes · number-hero · call-and-response |
| `camera_grammar` | locked-stage · drift-attention · push-into-detail · scroll-world · whip-relations · rack-reveal |
| `rhythm_policy` | voice-locked · pulse-grid · syncopated-holds · breath-first · accelerando-funnel |
| `accent_system` | single-accent-color · drawn-underlines · spotlight-wipes · ink-bleed-marks · mechanical-indicators |
| `transition_logic` | hard-cut-on-beat · occluder-handoff · shape-correspondence · camera-through-window · type-bridge |

**Definition of an option** (write it, don't just name it):
- *behavior* — what it makes the viewer do or feel, in one clause.
- *implementation* — the concrete mechanic in code (which layer, which channel, which curve).
- *refusal* — the thing this direction will not do (this is what kills generic output).

An axis without a mechanism is decoration. Before style lock, every chosen option needs a line
in `layer-plan.json` naming how it is built.

## Presentation to the user (compact)

```
1) «<title>» — <spine in one line>
   أول إطار: <what the first 1.5 s looks like>
   الحركة المميزة: <the one move repeated through the film>
   يرفض: <what it refuses>
```
No images unless asked, no essays. If the user says "you choose", choose, justify in three
lines, say that you chose.

## Style families — steal structure, never identity

| Family | Serves | Build it by | The failure to prevent |
|---|---|---|---|
| Editorial explainer (Vox-like) | explaining a question with evidence | map/graph as protagonist; camera travels to it instead of cutting; archives + drawn layers under one texture; numbers with physical weight | paper-yellow paint over a generic template; 12 fps applied to everything |
| Flat vector systems (Kurzgesagt-like) | relations and systems | original metaphors; flat shapes + one depth trick; micro-motion keeps idle states alive (≤2 % amplitude) | copying character design or the rounded-card UI system; cuteness in serious subjects |
| Bold print typography | a short message carried by language | hierarchy, mattes, independent words, rhythm and holds; type set like print (measure, baseline rhythm, one accent) | a word sliding as one block with no build |
| Brand motion | product character and values | material behavior, custom paths, one signature move executed everywhere | reusing a previous project's palette or template |
| Data-driven | quantities, trends, comparisons | read the table; constant visual encoding; axes and units recorded | decorated or invented numbers |
| Photo / 2.5D | place, person, matter | independent cutouts, alpha, separate depth and shadow layers | a flat pan across a photo labeled "detailed motion" |
| Product / UI | explaining a real action | real screenshots + callouts + attention order | a fictional interface presented as the product |
| Audio + type | song, narration, rhythm | onsets from the actual file, shaped text, cue map | random waveforms, late words, unrequested music |
| Generative / parametric | behavior, equation, transformation | real path correspondence, temporal sampling | particles filling time; fake morphs |

Selection test: write the question the film answers, what the viewer must see to be convinced,
the origin of the image/data, the material language, the hierarchy, and the camera the argument
needs. Try the hardest shot. Refuse the direction if the motion adds no understanding and no
character.

## Hooks (first 2 seconds decide the film)

Pick one and commit; do not stack them. Seeds to think from:

- cold open on one diegetic sound, visual answers it mid-motion
- drop into the action at 70 %, then rewind 1 s to explain
- extreme macro on a real texture that pulls back to reveal context
- open on a statistic framed as false; motion proves it true or exposes the twist
- one Arabic sentence alone in frame; drama is entirely internal timing
- a tidy grid that progressively breaks as the argument gets bolder
- two claims racing in split RTL/LTR columns; the winner takes the frame
- hold 1.5 s on one perfect poster frame before anything moves (confidence as hook)

## Cliché blacklist (each is an automatic re-do)

fade-in headline over bokeh · particles + lens flare as "cinematic" filler · zoom-pulse on every
beat · triangular split-screen comparison by default · glitch on non-digital subjects ·
underline-sweep-and-fade as the only text animation · stock isometric corporate figures ·
letter-by-letter Arabic popping (also contract-illegal) · 3D gold text for "premium" · infinite
white-background product spin · identical easing on entrance and exit for every layer · stock
whoosh on every cut.

## Typography system (locked in `style.json.type_system`)

- One display + one body + at most one mono/numeric face. A third family needs a written reason.
- Size ladder ≥ 1.5× between hierarchy levels; **one** deliberate break per film, recorded in
  `broken_rules` with its reason.
- Weight contrast (900 vs 300) is the primary emphasis tool — not color, glow or outline.
- Arabic display at hero sizes ≥ 96 px at 1080 width; small print uses a naskh-leaning body face
  for tashkeel legibility.
- Never fake weight with a stroke: it breaks Arabic joins. Fetch the real Bold face.
- Declare one numeral system (Arabic-Indic ٠١٢٣ or Western 0123) and keep it everywhere.
- Starting pairs (check availability and license before shipping): Rakkas + Tajawal · Lalezar +
  Vazirmatn · Reem Kufi + IBM Plex Sans Arabic · Aref Ruqaa + Noto Sans Arabic · Amiri + IBM
  Plex Sans Arabic · Blaka + any thin face (shout frames only). Latin counters: Archivo Black
  (numerals), Space Mono (labels/timecodes), Fraunces (editorial serif).

## Originality rules

1. Learn **rules** from references: transitions, hierarchy, rhythm, pacing. Never files,
   palettes, compositions, motifs or scene code.
2. Keep an originality note in `style.json`: what was studied and what is different here.
3. If you cannot name the difference in one sentence, you are echoing — re-decide.
4. A reference is not a target. "Like them" means "solve this class of problem as well as they do".
