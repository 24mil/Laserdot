# Evidence and limitations

The showcase separates **software behavior**, **recorded-scene model behavior** and **live output**. An accepted point is not necessarily anatomically correct; a curated demonstration is not an accuracy benchmark. Exact values and source-report hashes are retained in [evidence.json](evidence.json).

## What the saved reports establish

| Experiment | Result | Scope |
| --- | --- | --- |
| Recovery regression suite | 156 tests passed | Saved run, not rerun for this portfolio |
| Two-character association | 119 → 0 identity switches over 120 frames, head and body | Constructed detections; actual current points, no averaging |
| Sole-target reacquisition | Unnecessary wait 100 → 0 ms | Synthetic 50 FPS dropout/pan test; not total latency |
| Body processing | p95 **33.217 ms**, median 25.563 ms; baseline p95 27.836 ms | 100 RTX 2070 saved-scene samples while app/game ran; capture, USB and physical movement excluded |
| Designated negatives | 0 selections across 664 checks | Correlated reused development/regression images/settings |
| Designated teammates | 0 selections across 1,376 checks | Correlated crops/scales/settings, not independent encounters |
| Body acquisition | 404 → 449 selections across 1,480 checks | Reused enemy crops; selection count does not establish correct anatomy |
| Distant bot recording | Slider 85/100: 0 → 17/18 frames; 175/250: 0 → 2/18 | One correlated recording; chest/stomach remain weak |
| Fresh recorded comparison | 8/154 selections before and after; existing points unchanged | Serial disabled, no live-hit claim |

The GPU measurement is **processing time**, not a capture-to-USB latency measurement. The game FPS cap was not independently verified during that benchmark. The preview publishes at up to 10 Hz and is separate from detector/game FPS.

## What remains unresolved

The body release and full-model qualification fields remain **`approved: false`**. The incremental update was installed as a user-authorized trial, not as proof that every qualification gate passed.

- **14 hidden-region selections** and **6 broader background-crop selections** remain in saved regression checks.
- Anatomically correct checks improved **52 → 57**, retaining all 52 previous correct points. Three additional accepted points still missed tiny reviewed regions by about 0.10, 0.60 and 1.00 pixels; they remain failures.
- The zero-negative result applies only to the designated set, not all scenery or teammate cases.
- Full source-separated per-region accuracy/recall, occlusion rejection, coverage and bounded-live/end-to-end gates have not passed.
- The live capture accompanying the synthetic lock test contained no accepted enemies, so it did not establish real two-enemy performance.
- No measured tenfold/twentyfold improvement, complete agent/ability coverage or proven shield/ultimate robustness is claimed.

## Visual provenance

| Asset | Origin and rendering |
| --- | --- |
| Head replay | 60 recorded 600×600 range frames; current head model/verifier on CPU, current tracker, slider 50. Only offline adaptation disables the fast search time budget. 59 frames contain accepted heads; 40 contain a selected point. Approximate source timing is quantized to GIF 10 ms ticks. [Predictions](head-replay.json), [configuration](head-replay-config.json). |
| Body replay | Contiguous frames 48–66 from the 154-frame recorded comparison; saved accepted points at slider 85, approximate capture timing quantized to GIF ticks. Both accepted and empty frames are shown. [Selected records](body-replay.json). |
| Landmark figure | Three v6 pose outputs on one compressed saved range frame. All three fall inside that frame's reviewed regions; one frame cannot establish general precision. |
| Identity animation | First 40 frames of a constructed 120-frame regression, slowed for readability; boxes/points come from the actual saved sequence. [Full sequence](identity-sequence.json). |
| Training plots | Final RGB head box-loss curve, four epochs; final v6 pose-loss curve, ten epochs. Different losses on different tasks; no cross-model ranking. |

The two recorded replays use no capture or Arduino connection during portfolio production. Screenshots are curated centre crops without visible player names/chat/scoreboards. Source reports and raw recordings remain private; the public extract uses relative experiment identifiers and hashes.

## Output behavior verified in source

A shared result supplies marker, status and movement. Missing/rejected target observations cancel correction; capture silence beyond 50 ms also cancels it. Remembered identity does not permit outputting an old point. The application age limit is 60 ms including its configured display allowance, and the serial owner rechecks age at send time. Queues retain only the latest observation.

The Leonardo firmware parses three-button USB boot-mouse input and merges correction; it is not byte-for-byte raw report forwarding. The active firmware's `TAP` action is disabled. Historical upload verification is recorded, but no new hardware or in-game test was performed for this showcase.

[README](README.md) · [Model card](MODELS.md)
