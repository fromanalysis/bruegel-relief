# Painting to 3D-Printed Relief — a build diary

Turning a painting into physical objects on a desktop FDM printer: monocular depth
estimation, a conditioned heightfield, and a mesher — documented as a diary of what
worked, **what failed in plastic, and why**. The worked example throughout is Pieter
Bruegel the Elder's *Hunters in the Snow* (1565).

**→ [Read it](https://fromanalysis.github.io/bruegel-relief/)**

| page | what it covers |
|---|---|
| **[The build guide](https://fromanalysis.github.io/bruegel-relief/)** | The whole pipeline, end to end: depth model → heightfield conditioning → watertight mesh → slicer. Fourteen sections, every parameter explained, every failed print kept. |
| **[From Panel to Cylinder](https://fromanalysis.github.io/bruegel-relief/painting_to_cylinder.html)** | The same heightfield on a *cylindrical* base surface — a carved column and a lithophane lamp. Includes the print verdict: which face the relief belongs on, and the measurements that settled it. |
| **[From Relief to Sculpture](https://fromanalysis.github.io/bruegel-relief/painting_to_sculpture.html)** | Branch B — what it costs to give up one-height-per-pixel and go for real depth. |
| **[The Poisson solve, for dummies](https://fromanalysis.github.io/bruegel-relief/poisson.html)** | Why gradient-domain range compression keeps detail that a linear squash destroys. |
| **[How the depth model works](https://fromanalysis.github.io/bruegel-relief/depth_primer.html)** | A plain-language primer on Depth Anything V2: relative vs metric depth, scale-and-shift invariance, distillation. |

## What is actually in here

- **Gradient-domain range compression** (Weyrich et al. 2007) via a discrete Poisson
  solve — the step that makes a 5 mm panel read like a deep relief.
- **Carve for the nozzle.** A 0.42 mm bead with surface tension is a low-pass filter:
  raised fine detail melts, recessed detail prints crisply. The stages that follow from
  that are the difference between the failed print and the good one.
- **Wrapping the field onto a cylinder.** The heightfield assumption is about a *base
  surface*, not a plane — swap the plane for a cylinder and every conditioning stage runs
  unchanged. Only the mesher and two constraints move.
- **Lithophanes, measured rather than assumed.** Beer–Lambert for the wall, thickness
  solved from the brightness you want, and a printed calibration wedge instead of a
  guessed absorption coefficient.
- **Sixteen documented mistakes**, including two that only a printed object could find:
  watertight is not printable, and the relief belongs on the face you look at.

## How it is built

Hand-written Python — numpy, scipy, trimesh, Pillow, Depth Anything V2 — on an Apple
Silicon Mac, printed on a Bambu Lab A1 mini in white PLA. The pages are single
self-contained HTML files with the figures inlined; there is no build step and no
JavaScript.

Corrections and questions are welcome via issues.
