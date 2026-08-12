# Prompt for Claude Design — COSCUP 2026 deck fixes

Copy everything below the line.

---

Edit `COSCUP_2026_____.pptx` (COSCUP 2026, "Firmware Dev & Agentic AI", 33 slides).
Two jobs: fill three empty slides, and correct five factual errors. Change nothing else.

**Ground rule: every value below is verified against real logs and real source code.
Do not invent, round, or embellish any number, path, or command. If something seems
to need a value I have not given you, leave it out rather than guessing.**

---

## Job 1 — Slides 24, 25, 26 are empty and must not be

Slide 23 promises "The next three slides are the playback fallback," but slides 24–26
contain only text boxes — zero images, zero media relationships. If the demo video
fails to play, the speaker is describing a screen the audience cannot see.

Fix by **typesetting the real terminal output below as three dark-terminal panels**,
one per slide. No screenshots needed — set the text as a styled code panel.

Keep each slide's existing title and caption text. Add the panel as the visual.

### Style for all three panels

- Dark panel, near-black background (`#0D1117`), monospace, **at least 18pt** so the
  back row can read it
- Colour meaning, used consistently: **green** = verified/pass, **yellow** = handoff,
  **red** = danger, **blue** = informational, grey = de-emphasised
- Rounded corners, generous padding, no drop shadows or gradients
- One accent chip per panel (see below), not a row of decorative badges

### Slide 24 — panel content (verbatim)

```
$ flash-manual.sh ~/shiba-aosp-images

[flash 17:56:40] device identity OK:
                 product=shiba
                 expected=ripcurrent|husky|shiba   (android-info.txt)
[flash 17:56:40] bootloader unlocked = yes
[flash 17:56:40] target slot = b
```

Colour: the three `[flash …]` lines green; `expected=` and `(android-info.txt)` grey.
Accent chip, green: `PRECONDITIONS VERIFIED`
Small caption under the panel, grey: `a mismatch exits 4 · a locked bootloader exits 5 · it never unlocks for you`

### Slide 25 — panel content (verbatim)

```
About to flash:
  product-out      : ~/shiba-aosp-images
  device           : shiba
  identity         : verified — matches android-info.txt
  bootloader       : unlocked
  slot             : b   (slot a is left untouched)
  wipe /data       : no — /data preserved
  verity           : disabled (userdebug/test-keys)
  bootloader/radio : NOT flashed (reusing current)
  recovery         : re-flash, or fastboot set_active a

Proceed? [y/N] y
```

Give this panel a **yellow border** — it is the handoff moment.
Colour: `verified`, `unlocked`, `no — /data preserved`, `NOT flashed` green;
`recovery` value blue; `Proceed? [y/N] y` yellow and visually emphasised — the `y`
is the human's actual answer and is the point of the slide.
Accent chip, yellow: `HANDOFF POINT`

### Slide 26 — panel content (verbatim)

```
[ 0s] state=none    boot_completed=?
[30s] state=device  boot_completed=1

Home screen reached.
  resumed  : com.android.launcher3/.uioverrides.QuickstepLauncher
  awake    : yes
  build id : BP1A.250505.005.B1
verify rc=0

this run's audit trail
  shiba-flash-20260806-175632.txt              commands, gates, approval
  shiba-flash-20260806-175632-dmesg.txt        8,868 lines
  shiba-flash-20260806-175632-logcat-all.txt  23,374 lines
```

Colour: `boot_completed=1`, `Home screen reached.`, `verify rc=0` green; file names
white; line counts grey.
Accent chip, green: `BOOT VERIFIED`

---

## Job 2 — Five factual corrections

### Slide 19 — repository name

`GITHUB.COM/ALANHC/AOSP-BUILD` → `GITHUB.COM/ALANHC/AOSP-BUILD-SKILLS`

The repo is `aosp-build-skills`. As printed, the audience cannot find it.

### Slide 24 — wrong build ID

`BP4A.251205.006` → `BP1A.250505.005.B1`

Every artefact — the build server, the device, and both recorded runs — reports
`BP1A.250505.005.B1` (AOSP tag `android-15.0.0_r34`). Nothing anywhere corresponds to
`BP4A.251205.006`. Replace it wherever it appears in slide 24's body **and speaker notes**.

### Slide 25 — speaker notes contradict the slide

The notes currently say the skill's default recommendation is `--wipe`
(「它的預設建議是 --wipe（factory reset /data）」). That is false and contradicts this
same slide's own body text. The script defaults to `WIPE=0`; `/data` is preserved
unless `--wipe` is passed explicitly.

Delete that clause and replace with:

> 這一步不可逆，所以 skill 預設就保留 /data —— 要 wipe 必須明確指定，而且它會停下來等我確認。

### Slide 26 — "the human appears once"

`Two skills chained; the human appears once.`
→ `Two skills chained; the human appears at defined handoff points.`

There is more than one: the vendor licence halt, the build-reuse question, and the
flash approval. "Once" also contradicts slide 25's own speaker notes.

### Slide 26 — "every run writes a receipt"

`And every run writes a receipt:` → `And this run left a receipt:`

The receipt, dmesg and logcat are real, but no script in any of the three public repos
generates them automatically — they were produced by the session. Keep the line counts
(8,699 / 31,512) **only if** you also keep the original wording elsewhere; if you use the
new slide 26 panel above, use its figures (8,868 / 23,374) instead and do not mix the
two runs' numbers on one slide.

---

## Do not

- Do not add a `--wipe` example anywhere. `--wipe` is destructive and off by default.
- Do not write that the agent accepts the vendor licence. It halts and hands back.
- Do not describe the flow as `fastboot flashall` — neither public script uses it.
- Do not add device serials, IP addresses, usernames, or `/Users/...` paths to any slide.
- Do not restyle the rest of the deck. Match the existing typography and palette.
