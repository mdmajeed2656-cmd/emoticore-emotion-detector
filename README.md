# Emoticore

**On-device expression and voice scoring, running entirely in the browser.**
*Score affect before you call it a threat.*

Emoticore fuses a live camera and a hand-written voice heuristic into one demo score. This
repository implements it as a working website: a landing page that states what the code does,
plus three real product surfaces where the analysis actually runs.

Built under four deliberate constraints:

- **No backend.** Every page is a self-contained static HTML file — no server, no database, no accounts.
- **No frameworks, no build step.** Pure HTML5 / CSS3 / vanilla JavaScript.
- **On-device analysis.** Video and audio never leave the machine. The one exception (optional speech transcription) is opt-in and labeled in the UI.
- **One design system.** A "defense-tech mission control" visual identity shared across all pages.

---

## Pages

| File | What it is |
|---|---|
| [index.html](index.html) | Landing page — animated hero, capability rack, simulated operations console, dashboard mockup |
| [demo.html](demo.html) | **Live camera demo** — real face detection, 68 landmarks, 7-class expression classification, stable subject IDs via IoU tracking, threat gauge, 90 s sparkline |
| [voice.html](voice.html) | **Voice emotion analysis** — record up to 15 s or upload a clip; on-device prosody engine scores six emotions plus a 0–100 vocal stress index |
| [console.html](console.html) | **Operations Console (flagship)** — camera + voice fusion, 4 Hz session recording on a 120 s timeline, hysteresis-gated incident engine with snapshots, JSON/CSV export, settings drawer, Ctrl+K command palette |

Supporting files: [sw.js](sw.js), [manifest.webmanifest](manifest.webmanifest),
[icon.svg](icon.svg) — the PWA layer (installable app, offline shell).

---

## Running it

Serve over **localhost or https** — for example VS Code's Live Server extension, or any static
server from the repo root:

```
npx serve .
```

Then open `index.html`.

Camera and microphone work on `file://` in most Chromium browsers, but localhost/https is the
reliable path — and it is **required** for the service worker and PWA install.

**First run needs internet** to fetch the ~4 MB face-detection engine and model weights from
jsDelivr. They're cached afterwards.

---

## Tech

| Layer | Technology |
|---|---|
| Languages | HTML5, CSS3, vanilla JavaScript — zero frameworks, zero build tools, zero npm dependencies |
| Face AI | [`@vladmandic/face-api`](https://github.com/vladmandic/face-api) (TensorFlow.js) — `tinyFaceDetector`, `faceLandmark68TinyNet`, `faceExpressionNet` |
| Audio DSP | Web Audio API — `AnalyserNode`, custom autocorrelation pitch detection, all hand-written |
| Transcription | Web Speech API (Chrome/Edge only) — strictly opt-in, cloud-routed, labeled as such |
| Graphics | Canvas 2D + inline SVG — every visual generated in code |
| Storage | `localStorage` (settings, voice history) + Blob downloads (JSON/CSV exports) |
| PWA | Web App Manifest + service worker, stale-while-revalidate |

---

## Privacy

- All video inference and all audio DSP run **on-device**. No frame or sample is ever
  transmitted — there is no upload endpoint in the codebase.
- The one exception is **transcription**: the Web Speech API routes audio through the browser
  vendor's speech service. It is off by default, opt-in per session, and labeled as
  cloud-routed at the moment of enabling.
- Cameras and microphones are released on stop and on page exit (`pagehide`).
- Exports are generated locally as Blob downloads.

---

## Limitations — read this

**This is demo-grade accuracy.** The face models are open, lightweight networks. There is no
accuracy eval in this repo, and the landing page does not invent one. The vocal stress and
voice-emotion scores are transparent DSP heuristics: prosody distinguishes arousal well, valence only
roughly. Both are labeled as heuristics, not a clinical measure, in the UI.

A production system would need serious models, validation datasets, and a legal/compliance
review — emotion recognition is regulated in some jurisdictions (e.g. the EU AI Act).

Other known limits:

- Session data lives in memory; closing the tab discards an unexported session.
- Transcription requires Chrome/Edge and an internet connection; Firefox shows "N/A".
- First run requires internet for the engine and model weights.

---

## Documentation

- [docs/project-report.html](docs/project-report.html) — full 9-section project report
  (also as [Emoticore-Project-Report.pdf](Emoticore-Project-Report.pdf))
- [docs/presentation.html](docs/presentation.html) — slide deck
  (also as [Emoticore-Presentation.pdf](Emoticore-Presentation.pdf))
- [docs/RESUME-NOTES.md](docs/RESUME-NOTES.md) — technical summary and honest scope notes
