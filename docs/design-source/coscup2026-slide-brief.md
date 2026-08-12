# COSCUP 2026 Slide Brief — for Claude (design mode)

> **How to use this file:** This is a complete build spec for the slide deck. Read the whole thing, then generate the deck slide-by-slide following the per-slide spec in Section 6. Do not invent facts beyond what's here — every claim and citation is pre-verified. Ask before changing the core structure.

---

## 1. Hard constraints (from COSCUP 2026 organizers — non-negotiable)

- **Aspect ratio: 16:9, resolution 1920×1080.** Design to this exact canvas.
- **Projection is HDMI only**, presenter's own laptop. No podium PC. → deck must render correctly from the speaker's own machine (export a **PDF fallback** in case of software issues).
- **No venue Wi-Fi.** Any demo that needs network MUST be a **pre-recorded video embedded/linked locally** (not a live network call). Design a slide slot for the demo video.
- **Upload target:** deck will be linked on Pretalx (Google Slides / Speaker Deck / GitHub / HackMD). → keep a shareable exported version.
- **IP / copyright clean:** no unlicensed video, music, images, or fonts. Use only open-license or self-made assets. Icons: use an open set (e.g. Lucide, Simple Icons). State font choice explicitly so it can be embedded.
- **Session:** 2026/8/8, 14:20–14:50, room TR515, Open Source Firmware track, level = Intermediate.
- **Timing is strict** (30 min incl. Q&A). Content budget below is built for ~26 min talk + ~4 min Q&A.

## 2. Talk metadata

- **Title:** 韌體開發與 Agentic AI:讓 AI 跑 Build,你來解難題
  *(English subtitle on title slide: "Firmware Development & Agentic AI: Let AI Run the Builds, You Solve the Hard Parts")*
- **Speaker:** alanhc (Alan Tseng), Embedded Software Engineer @ an IC/chip vendor
- **Repo shown:** https://github.com/alanhc/aosp-build
- **Companion blog:** HackMD English post (link to be added on final slide)

## 3. Language & audience

- **Slides in English**, spoken delivery in Mandarin. (English slides = deliberate: audience reference value + doubles as a portfolio piece for international/FAANG hiring managers.)
- Audience: intermediate firmware/embedded devs at an open-source conference. Assume they know `repo sync`, build systems, CI — do NOT over-explain basics. Assume healthy skepticism toward "AI hype" — win them with a concrete framework and real numbers, not enthusiasm.

## 4. Positioning & tone (the reason this deck exists)

The differentiator vs. typical "my AI journey" talks: this is a **design/methodology** talk, not a tool demo or adoption story. Core thesis:

> The bottleneck in firmware dev is not writing code — it's the `repo sync → build → flash → verify` loop. The real question is not "what can AI do" but **"where do you draw the human/agent boundary."**

Tone: confident, engineer-to-engineer, dry humor allowed (the long build time is a running joke). Intellectually honest — the speaker's own 3-criteria framework is presented as *his operationalization built on established theory*, not as a coined buzzword. Avoid marketing language.

## 5. Visual design direction

- **Theme:** dark, technical, terminal-inspired but clean. High contrast. Think "developer tooling landing page," not corporate.
- **Palette:** dark charcoal background (#0D1117-ish), one accent (electric cyan or green — pick one and stay consistent), white/light-grey body text. Use accent ONLY for emphasis and the "human decision" points.
- **Typography:** one sans for headings (e.g. Inter / IBM Plex Sans), one mono for code/commands/pipeline steps (e.g. JetBrains Mono / IBM Plex Mono). Mono is load-bearing here — pipeline steps and commands should read as real terminal text.
- **Consistent visual language for the core idea:** pick two glyphs and use them everywhere — one for "delegate to agent" (e.g. robot/gear) and one for "human decides" (e.g. hand/pause). The whole deck is really about which steps get which glyph.
- **Density:** low. One idea per slide. Big type. No paragraph dumps. Speaker notes carry the detail (put full talking points in the notes field of every slide).
- **Motion:** minimal. If the format supports builds, reveal the pipeline steps one at a time on the hook slide. No gratuitous transitions.
- **Every slide:** small footer with talk title + "COSCUP 2026" + slide number. Keep it subtle.

## 6. Slide-by-slide spec (~18 slides, ~26 min)

Each entry: **[purpose] — on-slide content — speaker-notes seed.** Keep on-slide text terse; push detail to notes.

1. **Title** — Talk title (zh) + English subtitle + speaker + role + COSCUP 2026 / TR515 / Aug 8. — *Notes: 15-sec intro, who I am, one line on working at a chip vendor doing multi-project firmware.*

2. **The hook / the loop** — Big mono block: `repo sync → build → flash → verify → repeat`. One line: "The bottleneck was never writing code." — *Notes: everyone talks about AI writing code; that's not where firmware time goes.*

3. **The real pipeline (AOSP/Pixel 8, public example)** — The 7 steps as a vertical mono list: (1) `adb shell getprop ro.build.id` (2) look up AOSP source tag (3) download vendor blob + verify SHA-256 (4) `repo init` / `repo sync` (30–90 min) (5) extract + accept license (6) `lunch` + `m` (1–3 hrs) (7) `fastboot flashall`. — *Notes: all public, reproducible. Point at the two long waits.*

4. **Which steps need a human brain?** — Same 7 steps, but now tag each with the agent-glyph or human-glyph. Only 3 stay human (version mismatch, unexpected build error, hardware anomaly). — *Notes: this is the whole talk in one slide. Everything else is waiting + fixed procedure.*

5. **The real question** — Full-bleed statement: "The question isn't *what can AI do* — it's *where do you draw the boundary*." — *Notes: reframe. Set up the framework.*

6. **Section divider: the theory it stands on** — timeline visual. — *(next 2 slides)*

7. **A short history of "who does what" (timeline)** — horizontal timeline, 4 nodes:
   - **1978** Levels of Automation — Sheridan & Verplank (10-level scale)
   - **2000** Types & Levels of Automation — Parasuraman, Sheridan & Wickens (4 stages × degree; *objective basis for deciding what to automate*)
   - **2024** Agentic Design Patterns — Andrew Ng (Reflection / Tool Use / Planning / Multi-agent)
   - **2026** Loop Engineering (trigger → verifiable goal → execute → validate → stop; + *how humans intervene*)
   — *Notes: I'm not chasing buzzwords; this problem has a 45-year lineage. Reflection = the engine of my pipeline. Loop engineering's 5th element is "how humans intervene" — everyone mentions it, nobody specifies it. That gap is my talk.*

8. **The gap I'm filling** — Quote-style: "Loop engineering says *design human intervention*. It never says *where*." → "This talk = where, for firmware." — *Notes: precise, humble, sharp. This is the intellectual contribution.*

9. **My 3 criteria (the framework)** — 3 cards. Honesty line at top: "Not a paper's formula — my operationalization for placing each build step on the automation scale." 
   - **Reversibility** → irreversible (bootloader unlock, `fastboot flashall`) = low automation, human confirms
   - **Verifiability** → objectively checkable (SHA-256, build pass/fail, CI) = high automation, agent owns it
   - **Context judgment** → needs context (no matching tag, unexpected compile error, HW anomaly) = stop, hand to human
   — *Notes: map each onto Parasuraman's 4 stages. This is the take-home slide — audience should photograph this.*

10. **The skill in practice** — `aosp-build`: input = one Build ID (`BP4A.251205.006`) → runs the whole pipeline. Show the pipeline diagram with agent/human glyphs applied. — *Notes: one input, autonomous run, pauses at the boundaries from slide 9.*

11. **How it handles failure** — two columns: *Predictable → agent auto-handles* (network drop = retry, `repo sync` = lower parallelism) vs *Unexpected → agent stops + notifies* (no tag, compile error). — *Notes: predictable failure handling is what makes it trustworthy.*

12. **DEMO (pre-recorded video slot)** — embed the local video; caption: "Recorded — because a build takes 1–3 hours. That's the point." — *Notes: NO live run (no Wi-Fi + too slow). Video shows Build ID in → pipeline running → a boundary pause. Have a static screenshot fallback in case video fails.*

13. **Role shift: executor → supervisor** — before/after: "1 build at a time, fragmented attention" vs "3 pipelines in parallel, I handle only exceptions." — *Notes: the actual value isn't speed, it's holding N projects' state at once.*

14. **Numbers (placeholder — speaker to fill)** — KPI cards: engineer-hours reclaimed / week, intervention rate (# human stops ÷ # runs), parallel projects. **[ALAN: insert real figures before final]** — *Notes: even rough real numbers beat none. Flag clearly as your own measurements.*

15. **What happens at team scale — accountability** — Statement: "Responsibility doesn't vanish. It slides to whoever clicks *approve*." — *Notes: this is the FAANG-manager slide. Agent can automate finding bugs (verification), never automate signing off (accountability).*

16. **Stripe Minions (public case)** — 1,300+ PRs/week with zero hand-written code, still every one human-reviewed before merge. Punchline: "What made it scale wasn't a smarter model — it was solid verification gates (sandboxes, tests, CI)." — *Notes: ties back to my 3 criteria — verifiability is the load-bearing wall.*

17. **Takeaways** — 3 bullets: (1) bottleneck = the loop, not the code (2) design the boundary: reversibility / verifiability / context (3) at scale, the review gate is the accountability anchor. Big: **"Let AI handle the builds. You solve the hard parts."** — *Notes: land the thesis.*

18. **Thanks / links** — repo QR (aosp-build), HackMD English blog link, contact (GitHub/LinkedIn). "Continue in the Hacking Room →". — *Notes: per COSCUP tip, invite deep Q&A to Hacking Room to protect the clock.*

**Optional backup slide (after 18, only if asked):** comparison table — journey-style talks vs this talk (axis: thesis / evidence / context / endpoint). Don't present unless a question invites it.

## 7. Pre-verified facts & citations (do not alter)

- Sheridan, T. B. & Verplank, W. L. (1978). *Human and Computer Control of Undersea Teleoperators.* — 10-level automation scale.
- Parasuraman, R., Sheridan, T. B. & Wickens, C. D. (2000). *A Model for Types and Levels of Human Interaction with Automation.* IEEE Trans. SMC-A, 30(3), 286–297. doi:10.1109/3468.844354 — 4 stages (acquisition → analysis → decision → action) × degree of automation.
- Ng, A. (2024). Four agentic design patterns: Reflection, Tool Use, Planning, Multi-Agent Collaboration (DeepLearning.AI / The Batch).
- Loop Engineering (2026, industry term, no single academic source): agentic loop = trigger + verifiable goal + execute + validate + stop condition + human intervention. (IBM; Data Science Dojo.)
- Stripe "Minions": internal autonomous coding agent, 1,300+ zero-hand-written-code PRs/week, all human-reviewed before merge; scaling credited to dev infra (sandboxed devboxes, test suites, CI gates). — public case.
- AOSP/Pixel 8 pipeline steps + `aosp-build` skill: from speaker's own HackMD post and open-source repo (github.com/alanhc/aosp-build).

## 8. Deliverables checklist

- [ ] 1920×1080 deck, dark technical theme, English
- [ ] Speaker notes filled on every slide (detail lives here, not on-slide)
- [ ] Demo video slot + static screenshot fallback
- [ ] PDF export (projection fallback, HDMI-safe)
- [ ] Shareable export for Pretalx link
- [ ] Repo QR + blog link on final slide
- [ ] Real numbers inserted on slide 14 (speaker action)
- [ ] Fonts embedded / open-license; icons from open set
