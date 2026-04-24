# startrek360viewer

An LCARS-themed viewer with two modes:

1. **PANORAMA** — a WebGL equirectangular renderer for a 360° Enterprise-vs-fleet battle scene. Drag to look, scroll to zoom, pinch on mobile.
2. **SPLAT** — a 3D Gaussian Splat of the Transporter Lost-and-Found scene, reconstructed from a single AI-generated equirectangular panorama via Apple's [ml-sharp](https://github.com/apple/ml-sharp). Rendered in the browser with [gsplat.js](https://github.com/huggingface/gsplat.js).

Toggle with the **MODE** button in the top bar.

## How the splat was built

1. `reproject.py` reprojects the equirectangular panorama into 4 perspective
   views at 62° horizontal FOV (matching SHARP's 30mm-equivalent default):
   `hero (0°,0°)`, `right (90°,0°)`, `back (180°,0°)`, `left (270°,0°)`.
2. Each view runs through SHARP's monocular predictor, producing a 1.18M-gaussian
   PLY in its own camera frame.
3. `splice.py` rotates each PLY into a common world frame (by `rotY(yaw) · rotX(pitch)`
   on positions and quaternion pre-multiplication on orientations) and concatenates.
4. `compress.py` converts PLY → antimatter15 `.splat` format (32 bytes per
   gaussian) and keeps the highest-opacity splats to fit a size budget.

Scripts live outside this repo; the shipped artifact is `transporter.splat`
(25 MB, 781,250 gaussians — the top ~17% by opacity).
