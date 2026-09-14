# Painting to 3D-Printed Relief — a build diary

Turning a painting into physical objects on a desktop FDM printer: monocular depth
estimation, a conditioned heightfield, and a mesher — documented as a diary of what
worked, **what failed in plastic, and why**. The worked example throughout is Pieter
Bruegel the Elder's *Hunters in the Snow* (1565).

**→ [Read it](https://fromanalysis.github.io/bruegel-relief/)** · **→ Make one in your browser: [a lamp](https://fromanalysis.github.io/bruegel-relief/painting2lamp.html) or [an ornament](https://fromanalysis.github.io/bruegel-relief/painting2ball.html)**

| page | what it covers |
|---|---|
| **[The build guide](https://fromanalysis.github.io/bruegel-relief/)** | The whole pipeline, end to end: depth model → heightfield conditioning → watertight mesh → slicer. Fourteen sections, every parameter explained, every failed print kept. |
| **[From Panel to Cylinder](https://fromanalysis.github.io/bruegel-relief/painting_to_cylinder.html)** | The same heightfield on a *cylindrical* base surface — a carved column and a lithophane lamp. Includes the print verdict: which face the relief belongs on, and the measurements that settled it. |
| **[painting2lamp](https://fromanalysis.github.io/bruegel-relief/painting2lamp.html)** | Not a page but a tool: drop in a painting, get the STL of the lamp shell. One self-contained file — no install, no server, no network, and the image never leaves your browser. |
| **[painting2ball](https://fromanalysis.github.io/bruegel-relief/painting2ball.html)** | The same, on a sphere: a lithophane Christmas ball in three sizes that all take one printed cap. Choose the projection, choose whether the picture reflects or wraps, and get the STL. Also one offline file. |
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
- **The lamp recipe as a browser app**, ported from the Python and checked against it field by
  field: worst deviation anywhere on the object 1.1e−5 mm. The comparison found a defect in the
  *reference* on the way — `np.argsort` defaults to a non-stable sort, so the Python's own output
  is not bit-reproducible, and it disagrees with itself 170× more than the port disagrees with it.
- **The sphere, and a neck that stops scaling.** Three ornament sizes share one printed bayonet cap
  because the aperture plane moves instead of the neck — set by whichever of wall, overhang or
  bed-contact binds first at that diameter. The booleans run on the same CSG library the Python
  uses, compiled to WebAssembly, and the exported ball matches the one that came off the printer to
  0.001 % by volume.

## How it is built

Hand-written Python — numpy, scipy, trimesh, Pillow, Depth Anything V2 — on an Apple
Silicon Mac, printed on a Bambu Lab A1 mini in white PLA. The pages are single
self-contained HTML files with the figures inlined; there is no build step.

The exceptions are `painting2lamp.html` and `painting2ball.html`, which are apps rather than
pages: JavaScript throughout — ports of the lamp and ball paths, with Pillow's LANCZOS
reimplemented to the letter so the resize is bit-exact — and each still ships as a single file
that runs offline from a `file://` URL. The ball app also carries its CSG library as inlined
WebAssembly. They are the only things here that need a browser to do more than render text.

Corrections and questions are welcome via issues.
