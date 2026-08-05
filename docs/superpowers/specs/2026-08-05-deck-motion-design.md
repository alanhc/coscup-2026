# COSCUP 2026 deck — motion design

Date: 2026-08-05 · Talk: 2026-08-08 14:20 TR515

## Goal

Make the deck feel dynamic without turning it into a slideshow of effects, and
without weakening the talk's own argument about evidence and verifiability.

## A. Demo walkthrough (slides 24–26): stills → real clips

Slides 24–26 narrated three beats of the demo using stills lifted from
`uploads/agent-skills-demo-45s.mp4`. They are now short, silent, looping clips
cut from that same recording — real terminal output, not a re-enactment.

The recording carries burned-in annotations: an orange step badge on the left
and a caption strip at the bottom. Each slide must show exactly one caption, or
the slide's text and the footage disagree on screen. Boundaries were read off
the video frame by frame:

| Slide | Caption | Caption spans | Clip cut |
|---|---|---|---|
| 24 | 需求: Pixel 8 刷 BP4A.251205.006 | 0.5–3.9s | 0.9s +2.8s |
| 25 | 換手到刷機 Skill: flash-pixel-aosp | 21.0–25.4s | 21.4s +3.8s |
| 26 | 刷寫全部完成，Skill 接著自動驗證開機 | 32.5–37.4s | 32.9s +4.3s |

Cuts use **output seeking** (`-i` before `-ss`). Input seeking snapped to an
earlier keyframe and pulled the preceding fast-forward caption into clips 25
and 26.

Each `<video>` keeps its original still as `poster`, so the first paint is
instant, a failed load degrades to exactly today's slide, and print/PDF export
still renders the frame.

### Playback

deck-stage hides inactive slides rather than unmounting them, so the clips
would otherwise loop forever off-screen. A delegated `slidechange` listener on
`document` (the event bubbles and composes out of shadow DOM) restarts the
active slide's clip from 0 and pauses every other video — including slide 23's
full 45s recording, which is speaker-driven and should not keep running once
you navigate away.

**The dc runtime renders the template through React, which silently drops
valueless boolean attributes.** `loop` and `muted` disappeared; `controls=
"controls"` survived because it had a value. An unmuted video may not autoplay,
so the clips sat frozen on their posters. Fixed twice over: attributes written
in value form, and the properties forced from JS on every slide change.

## B. Diagram slides: staggered entrance

Six slides — 06 pipeline, 11 timeline, 12 three loops, 15 decision tree,
17 agent+harness, 19 aosp-build. All are plain divs with flex/grid and text
arrows, so CSS is enough; nothing is redrawn.

Follows the deck-stage contract: the visible end state is the base style, and
elements animate *from* hidden, gated on `[data-deck-active]` and
`prefers-reduced-motion: no-preference`. Print, PDF, reduced-motion viewers and
the thumbnail rail (deck-stage strips `data-deck-active` from its clones) all
see the finished slide.

Markup hooks, one attribute per container — no structural selectors that break
when a slide is edited:

- `data-anim="stagger"` — container whose direct children cascade in
- `data-anim="draw"` — child that wipes in horizontally (rules, arrows)
- `data-anim="hold"` — chrome that must never animate
- `--dc-step` — per-slide gap between children

Slide 15 marks the `<section>` itself, since its flow rows are direct children;
its slido chip and footer carry `hold`.

## Constraints observed

- Purely additive: new CSS block, new attributes, new clip files. No existing
  layout or copy touched, so any part can be reverted alone.
- Setting `--dc-step` on slide 15 required merging into the section's existing
  `style` attribute. A second `style` attribute would have won and dropped the
  slide's background and flex layout entirely.
- Clips use `preload="metadata"`. With `preload="auto"` the three videos
  competed with rail thumbnail materialization at load and the rail showed
  blank white frames for several seconds.
