# wearable-interpreter

A Claude skill for interpreting health and performance data from wearables. Share your data — CSV export, screenshot, or just describe your numbers — and get a personalized breakdown of what your body is telling you.

Supports **Whoop, Oura, Garmin, Apple Watch, and Fitbit**.

---

## What it does

- Interprets recovery, HRV, sleep, respiratory rate, strain, and readiness data from any major wearable
- Benchmarks your readings against **your own baseline** — not population averages
- Flags anomalies only when they exceed your personal noise floor (not universal thresholds)
- Accounts for context: alcohol, travel, illness, stress, menstrual cycle phase
- Combines metrics to give a single clear training recommendation

---

## What makes it different

Most wearable interpretations use universal thresholds ("HRV below 50ms is low"). This skill doesn't. A 20% HRV drop is noise for one person and a red flag for another — it depends on how much your HRV typically moves day-to-day.

This skill calculates your **personal mean** and your **personal variability** before drawing any conclusions. Flags only fire when a deviation exceeds your own signal noise, expressed in standard deviations from your rolling average.

---

## Supported platforms

| Platform | Recovery score | HRV | Sleep stages | Temp | Load |
|---|---|---|---|---|---|
| Whoop | ✅ Recovery % | ✅ rMSSD | ✅ | Wrist skin | ✅ Strain (0–21) |
| Oura | ✅ Readiness | ✅ rMSSD | ✅ | ✅ Deviation | Activity score |
| Garmin | ✅ Body Battery | ✅ rMSSD | ✅ | — | ✅ Training Load |
| Apple Watch | — | SDNN* | ✅ (Series 9+) | — | Rings / cal |
| Fitbit | Daily Readiness† | ✅ rMSSD | ✅ | Wrist deviation | Active Zone Min |

*Apple Watch uses SDNN — not directly comparable to rMSSD values from other platforms.  
†Fitbit Daily Readiness requires Fitbit Premium.

---

## Input types

You don't need to export a CSV. The skill handles:

- **CSV export** pasted as text or uploaded as a file
- **Screenshot** of your app's daily view
- **Verbal description** — "my HRV was 58, recovery 71%, slept 6h40"
- **Single metric questions** — "what does a 42ms HRV mean?"
- **Trend questions** — "why has my recovery been low all week?"

For full personalized interpretation (baseline + variability), 14–30 days of data gives the best results. Single-day data still produces a useful explanation — just with lower confidence.

---

## Example interaction

> **You**: Here's my Whoop data for the past two weeks [paste CSV]. Last night I also had 2 glasses of wine.
>
> **Claude**: 
>
> **Status**: You're in moderate recovery — capable of training but not at peak.
>
> **What's driving it**: Your HRV this morning is 54ms — about 9ms below your 14-day mean of 63ms, which puts it 1.4 standard deviations below your typical range. That's meaningful but not alarming. Your RHR is 52bpm, right at your personal baseline, which is a good sign. Sleep performance was 81% — solid.
>
> **Trend**: You've had three consecutive nights averaging above your HRV baseline. Last night's dip is likely the alcohol — expect a return to baseline tomorrow if tonight's sleep is clean.
>
> **Recommendation**: Train moderate. Skip anything maximal today. A tempo run or technique-focused session is fine.
>
> **Confidence**: Moderate — 14 days of history available; variability estimates will sharpen with another two weeks.

---

## Installation

### Option 1 — .skill file (recommended)

Download `wearable-interpreter.skill` and install it through the Claude desktop app or Cowork by dragging it into your skills folder.

### Option 2 — Manual install

Clone this repo and copy the `wearable-interpreter/` folder into your Claude skills directory:

```bash
git clone https://github.com/LucianoGijon/wearable-interpreter-skill.git
cp -r wearable-interpreter-skill/wearable-interpreter/ ~/path/to/your/claude/skills/
```

---

## How it's structured

```
wearable-interpreter/
├── SKILL.md                  ← entry point: trigger, workflow, output template
└── references/
    ├── platforms.md          ← CSV schemas and column names for each wearable
    ├── metrics.md            ← physiological meaning of each metric
    └── interpretation.md     ← personalized baseline logic, flags, context modifiers
```

Claude reads `SKILL.md` first to understand the workflow, then loads only the reference files relevant to your platform and query. The reference files stay lean and targeted so Claude doesn't load information it doesn't need.

---

## Extending it

Want to add a platform or improve the interpretation logic? The reference files are plain markdown — fork the repo and edit them directly.

Useful contributions:
- Additional platform schemas (Polar, Suunto, WHOOP 5.0 columns, etc.)
- Metric additions (lactate threshold estimates, training monotony, etc.)
- Improved context modifiers with more precise effect sizes
- Translations of the output template into other languages

Open a PR or file an issue if something is wrong or missing.

---

## Limitations

- This skill interprets data — it does not diagnose medical conditions. Patterns that warrant medical attention are flagged with a recommendation to consult a doctor, not a diagnosis.
- Apple Watch HRV (SDNN) cannot be compared directly to rMSSD from other platforms. The skill accounts for this but cannot bridge the gap entirely.
- Wearable sensors have measurement error. Treat single-night readings as directional, not definitive.
- Personalized interpretation requires history. With fewer than 7 days of data, conclusions are limited.

---

## License

MIT — use it, modify it, share it.
