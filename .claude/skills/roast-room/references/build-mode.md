# Build mode

`/roast-room build <what>` has the board make something together instead of deciding between options: a training program, a nutrition plan, a client check-in system, an offer, a launch plan. One seat lays out the structure, the best-fit seats each write a section, every seat attacks the draft from its own lens, and the Chair merges the fixes into one finished deliverable. There are no option scores and no verdict.

Use it when the user says "build me", "write me", "design a program", "make a plan for this client", or "have the board put together...". If they are choosing between options, run a normal council instead.

Agent types and the `general-purpose` fallback work exactly as in `round-prompts.md`. Give the absolute repo root at the top of each prompt.

## Step B1: The build brief

Build it from what the user gave you. Ask for what is missing in one AskUserQuestion batch (at most four questions), offering sensible defaults. If the user says "just build it", fill gaps with stated assumptions marked `(assumed)`.

For a training or nutrition program, the brief needs:

1. **The person**: goal (muscle, fat loss, strength, sport, health), training age, sex and age if they matter, bodyweight, current lifts or a recent best, and what they have run before.
2. **The limits**: days a week, minutes a session, equipment, injuries or pain, medical conditions, sleep, and the job or schedule around it.
3. **Preferences**: exercises they love or hate, how they like to track, and what made them quit before.
4. **The deliverable**: length (for example 12 weeks), what it must include (training, nutrition, cardio, check-ins), and the format the coach hands to the client.

For anything else (an offer, a launch, a system), ask the same four things in that shape: who it is for, the limits, what has worked or failed before, and the exact deliverable.

**Safety.** If the brief mentions a medical condition, pregnancy, an eating disorder, or pain that is not diagnosed, say in the brief that the plan assumes medical clearance, and have every seat keep inside it. The board does not prescribe treatment.

Keep the brief under 300 words. Save it as `brief.md` in `sessions/YYYY-MM-DD-build-<slug>/`. Write no `council.json`: build sessions do not play in the live room, and the room ignores folders without one.

## Step B2: Split the work

Pick the seats as in "Pick the board" (a named board when one fits, for example `fitness`), four to seven seats. Then split the deliverable into sections and give each to the seat whose pillar owns it:

- **Architect**: the one seat that sets the structure everything hangs on. For a training program that is the split, the weekly layout, the phases, and the progression model.
- **Section owners**: one seat per section. For a training program, typical sections are volume and recovery (sets per muscle, deloads), exercise selection and technique, intensity and progression rules, nutrition, and adherence (making it fit the client's real week). No seat owns more than two sections.
- **Critics**: every seat at the table critiques the whole draft in Step B4, owners included.

Show the split in chat as a short list (section, seat, one line on why), then confirm it with one AskUserQuestion question (header "Build"): the recommended split (Recommended), or "Other" to swap seats. Put it in the same call as any brief questions so the user answers once. Save the split as a table at the bottom of `brief.md`.

## Step B3: Draft

1. Spawn the architect alone with the Architect prompt. Save its answer verbatim to `round-1.md` under `## <Seat> (architect)`.
2. Spawn every section owner in **one message**, in parallel, with the Section prompt. They read `round-1.md` and build on the architect's structure. Append each answer to `round-1.md` under `## <Seat> (<section>)`.
3. Assemble `draft.md`: the architect's structure with each section dropped in, in the deliverable's final format. Copy the seats' content faithfully. Where two sections clash (the volume does not fit the session length, an exercise contradicts an injury), keep both, mark the clash with `CLASH:` and the seats involved, and leave it for the critique.

## Step B4: Critique

Spawn every seat at the table in one message, in parallel, with the Critique prompt. Save the answers to `round-2.md`, one `## <Seat>` heading each. Then add `## Chair notes`: each proposed change in one line, with the seats that asked for it, the seats against it, and your call (adopt, reject, or merge). Rules for the call:

- Adopt a **must** change unless another seat gave a better-argued reason against it.
- Adopt a **should** change when no seat objects and it does not break the brief's limits.
- Take a **could** change only when it is free (no extra time, sessions, or complexity).
- When seats conflict, take the better argument, not the larger side. Record the other side for the dissent.
- Every `CLASH:` in the draft gets resolved here.
- A change that breaks the brief's limits (a fifth day on a four-day brief, a 90-minute session on a 60-minute brief) is rejected, whoever asks for it.

## Step B5: Revise and sign off

1. Apply the adopted changes to `draft.md` and save it as `program.md`. A change that rewrites most of a section goes back to that section's owner with the Section prompt plus the adopted changes. Everything else you apply yourself, as written by the seat that asked for it. You never add content of your own.
2. Spawn every seat in one message with the Sign-off prompt. Save the answers to `round-3.md`. There is no further round: a "no" goes into the dissent, and a one-line fix that every other seat would accept can be applied with a note.

## Step B6: Deliver

1. Run `python .claude/skills/roast-room/scripts/check_quotes.py <session folder>` (it scans `program.md` and `build-notes.md` too). Remove any quote it cannot find.
2. Finish `program.md` in the format below, then write `build-notes.md`: who built which section, every adopted and rejected change with its seat and reason, the dissent, and the open questions for the coach.
3. In chat, keep it short: what was built, the shape in two or three lines (for example "4 days, upper/lower, 12 weeks in three blocks, deload in weeks 5 and 10"), the two changes from the critique that mattered most (with the seat), any dissent in one line, and the path to `program.md`. Under 200 words.

## program.md format

Open with a one-paragraph summary for the client: the goal, the weekly shape, and how long it runs. Then, for a training program:

1. **The week at a glance**: a table of days and focus.
2. **Each day**: a table with `Exercise | Sets | Reps | Effort (RIR or RPE) | Rest | Notes`, plus substitutes for any exercise that needs equipment or could hurt.
3. **Progression rules**: exactly when to add weight, reps, or sets, written so the client can follow them without asking.
4. **Phases and deloads**: what changes each block, and when and how to deload.
5. **Adjusting on a bad day**: what to cut first when time, sleep, or pain gets in the way.
6. **Nutrition** (when in scope): calories or how to set them, protein, how to adjust from weekly averages.
7. **Check-ins**: what the client tracks and sends, and what the coach changes based on it.
8. **Why it's built this way**: three to six lines, each naming the seat and the framework with its source id.

End with: "Built by the Roast Room [board] board. The seats are simulations built from public work. Check it against your own coaching judgment."

Plain English throughout. No em dashes. Numbers instead of adjectives.

---

## Architect prompt

```
ROAST ROOM BUILD, STEP 1: THE STRUCTURE

Repo root: [absolute path]. You hold the [SEAT] seat ([pillar]). Read your brain's _hot.md, then at most three more brain files that fit (frameworks.md and positions.md are usually right).

Read the build brief: [session folder]/brief.md. The table at the bottom shows who owns which section.

You are the architect. Lay out the structure every other section will hang on. Do not write the other seats' sections; leave clear slots for them.

Answer in your seat's voice, plain text, under 450 words, in exactly this shape:

SEAT: [SEAT] (architect)
THE STRUCTURE: the shape in two lines (for a program: the split, days, and length).
THE LAYOUT: the week (or the parts of the deliverable), as a short list or table.
PHASES: how it changes over time, block by block.
PROGRESSION MODEL: the rule for moving forward, in one or two lines.
SLOTS FOR THE OTHERS: for each section owner, what you need from them and the limits they must fit (for example, session length or set budget).
WHY: three reasons, each naming the framework or principle you used and its source id.
WHAT WOULD BREAK THIS: the one fact about the person that would make you change the structure.

Rules: fit the brief's limits exactly (days, minutes, equipment, injuries). Use real numbers. Quote only verbatim lines from your brain, with the source id.
```

## Section prompt

```
ROAST ROOM BUILD, STEP 2: YOUR SECTION

Repo root: [absolute path]. You hold the [SEAT] seat ([pillar]). Read your brain's _hot.md, then at most three more brain files that fit.

Read: [session folder]/brief.md and [session folder]/round-1.md (the architect's structure).

Your section: [section], and what the architect asked of you in its slot.
[Only when sent back after the critique: THE CHANGES TO MAKE, one per line, with the seat that asked.]

Write your section so it drops straight into the structure. Answer in your seat's voice, plain text, under 600 words, in exactly this shape:

SEAT: [SEAT] ([section])
THE SECTION: the finished content, in the deliverable's format (for training days, the table Exercise | Sets | Reps | Effort | Rest | Notes).
WHY: two or three reasons, each naming the framework or principle you used and its source id.
WHAT I NEED FROM THE OTHERS: any dependency on another section, or "none".
WHAT I'M UNSURE OF: one line, or "nothing".

Rules: stay inside the architect's structure and the brief's limits. If the structure makes your section impossible, say so in WHAT I NEED FROM THE OTHERS instead of changing it yourself. Quote only verbatim lines from your brain, with the source id.
```

## Critique prompt

```
ROAST ROOM BUILD, STEP 3: CRITIQUE THE DRAFT

Repo root: [absolute path]. You hold the [SEAT] seat ([pillar]). Read your brain's _hot.md and any brain file you need (at most three).

Read: [session folder]/brief.md and [session folder]/draft.md (the assembled draft; lines marked CLASH: need a ruling).

Attack the draft from your pillar. Find what is wrong, missing, or weak for this person, not what you would have done differently for its own sake.

Answer in your seat's voice, plain text, under 400 words, in exactly this shape:

SEAT: [SEAT]
KEEP: the one or two things in the draft that are right, and why, in one line each.
CHANGES:
1. [must | should | could] Where: the day, section, or line. Change: exactly what to replace it with (numbers, exercise, rule). Why: one line with the framework and source id.
2. ...
(at most five, most important first)
ON THE CLASHES: your ruling on each CLASH: line that touches your pillar, or "none".
THE BIGGEST RISK FOR THIS PERSON: one line.

Rules: every change must be concrete enough to paste in. "Must" means the draft fails the brief or puts the person at risk without it. Do not ask for anything that breaks the brief's limits. Quote only verbatim lines from your brain, with the source id.
```

## Sign-off prompt

```
ROAST ROOM BUILD, STEP 4: SIGN OFF

Repo root: [absolute path]. You hold the [SEAT] seat. Read only your _hot.md if you need it.

Read: [session folder]/program.md and the Chair notes at the bottom of [session folder]/round-2.md.

Answer in your seat's voice, under 120 words, in exactly this shape:

SEAT: [SEAT]
SIGN-OFF: yes | yes with a note | no
NOTE: one or two sentences (the one-line fix, or why you would not put your name on it). Blank if yes.
ONE LINE FOR THE COACH: the thing from your pillar they must watch when the client runs this.
```
