# Models and training

Audited snapshot: **8 October 2026**. Counts below are PyTorch model parameters, including frozen parameters; ONNX initializer counts can differ after export fusion. Models use actual RGB pixels. The project specializes [Ultralytics YOLO11 detection and pose architectures](https://docs.ultralytics.com/models/yolo11/); pose predicts individually labelled [keypoints](https://docs.ultralytics.com/tasks/pose/).

## Learned components

| Component | Architecture / parameters | Input → output |
| --- | --- | --- |
| Head detector | YOLO11n / **2,590,230** | FP16 `1×3×320×320` → `1×6×2100`: box + head/teammate scores |
| Body detector | YOLO11n-pose / **2,662,458** | FP32 `1×3×320×320` → `1×18×2100`: box + enemy/teammate scores + four `(x,y,visibility)` triplets |
| Head verifier | Shared early YOLO features + head/role, support and context MLPs / **862,246** | Five RGB views, `15×64×64` → five logits combined into head/role, support and context gates |
| Primary body verifier | Early pose features + `3072→128→3` MLP / **468,579** | Body/context/overhead RGB, `9×96×96` → enemy/teammate/background |
| Secondary body verifier | Early pose features + `2048→128→3` MLP / **337,507** | Body/context RGB, `6×96×96` → enemy/teammate/background |

The selected detector runs with ONNX Runtime's [CUDA execution provider](https://onnxruntime.ai/docs/execution-providers/CUDA-ExecutionProvider.html); verifiers run on CPU. Detector exports use Ultralytics **8.4.171**, opset **17**, static 320 input and application-side NMS. Verifiers use PyTorch export with dynamic batch size and 64/96-pixel views. Only the requested branch runs per frame.

## Head lineage

The initial five-class checkpoint came from [sakura-lj/valorant-ai-aim, pinned commit](https://github.com/sakura-lj/valorant-ai-aim/tree/ef1f29d6c0f87539df6a245a360e9a2e8a8f4fbd), member `v11moudle/val_kenny_ultra_250619_v11n.pt`. It was already game-trained; this project did not create that upstream training.

```text
Community YOLO11n → gray-heads → gray-agents → gray-roles
→ gray-balanced → gray-native320 → expanded-v1-trial1
→ precision-v2 → rgb-head-v4 → FP16 ONNX
```

The final RGB specialist starts directly from `precision-v2/best`, not the separate RGB-context model. Body labels are omitted; classes become head/teammate. Its **2,703 train / 207 validation image variants** include recorded bots, agent source footage, reviewed negatives and imported crop data. Existing grouped splits are retained, but independent match-level separation is not established for every public source.

Final adaptation: **4 epochs**, batch 8, RGB 320, CPU, AdamW lr **0.0001**, weight decay **0.0005**, no frozen blocks. Box/class/DFL loss weights: **10 / 0.75 / 1.5**. Flip 0.5, rotation ±2°, translation 0.10, scale 0.35 and modest HSV augmentation; mosaic/mixup/copy-paste off. The selected `best` checkpoint is epoch 4. Successive adaptations are separate runs, not one uninterrupted training schedule.

The head verifier transfers early features from the **separate three-class RGB-context detector**. Head and role use masked binary losses, so unknown labels do not become enemy negatives. Support and wider/object context checks reject unsupported proposals. The added object-context gate can only reduce approval; it does not move coordinates.

## Body lineage and labels

```text
COCO-pretrained YOLO11n-pose → v1 → v2 → v4 → v5 → v6
```

The original checkpoint's metadata identifies `coco-pose.yaml`; its download revision was not preserved. Experimental v3 game-body transfer is **not** an ancestor of the installed v6.

Landmark order: **lower head, neck, chest, stomach**. These are reviewed screen-space surface points, not engine hitbox ground truth. Concealed/unavailable landmarks use visibility zero. The label design is intended to withhold invisible points, but current occlusion evaluations still contain failures.

v6 uses **104 reviewed training crop records** (87 enemy, 17 teammate), generating **334 correlated training images** with crop/scale variants, bot scenes and empty scenes. Earlier v2 evaluation uses 21 held-out records from one recording group; v6 adds no new independent sessions. Related derivatives stay in their parent partition.

Final v6: **10 epochs**, fixed final checkpoint, CPU, RGB 320, batch 12, AdamW lr **0.0005**, weight decay **0.0005**, first 10 blocks frozen, seed 6423, one warmup epoch. Box/class/DFL, coordinate and visibility losses are used; keypoint-objectness weight **2.0**, anatomical OKS sigmas **[0.05, 0.04, 0.07, 0.08]**. Mosaic 0.25 closes for the final three epochs; flip 0.5, scale 0.20, translation 0.10, rotation ±3°, modest HSV; mixup/copy-paste off.

**Metric caveat:** the final framework validation uses the training image list. Epoch-10 box mAP50 **0.9947** and pose mAP50 **0.43687** are training-list diagnostics. The plotted training pose loss **2.98903 → 1.47066** shows optimization only.

## Context rejection and runtime selection

The primary body-verifier dataset contains **2,805 proposal crops**: 2,212 train, 335 development, 258 reused holdout; 514 enemy, 679 teammate, 1,612 background. Crop counts include repeated proposals. The development/holdout sources have been inspected previously and are not an untouched final evaluation.

Early pose features initialize the body verifiers. The primary extends the existing two-view encoder/classifier with an overhead view. Classifier-only training is followed by encoder fine-tuning; deployed full-encoder selection is **epoch 4** of an eight-epoch candidate run. Encoder/classifier AdamW lr **0.000005 / 0.0001**, weighted cross-entropy, label smoothing 0.02 and frozen batch-normalization statistics are used.

Body targeting requires enemy classification and requested landmarks with confidence ≥**0.65**, inside the image and character box. Interpolation requires both endpoint landmarks. Verifiers leave accepted points unchanged. Friendly UI/colour checks are rejection-only. Secondary confirmation cannot override friendly evidence.

The latest recovery searches a central **192-native-pixel** view only when the wide body pass has no usable point and its time budget allows it. It imposes stronger small-target checks, including landmark confidence ≥0.70 and enemy verifier probability ≥0.95. This update changes search/selection, **not model weights**; it does not apply to head mode.

## Active artifact identities

| Runtime artifact | SHA-256 |
| --- | --- |
| `rgb-candidate-fp16.onnx` | `0d87c53ef0f791060a4b818cbd8fe304125babe47e4bfb08b3841aefe2a2e1fe` |
| `role-verifier.onnx` | `dd0942dc6e5fac7ef08b92346ffebcba759b8e41600937bc7796cd260ef8c4d8` |
| `body-landmarks.onnx` | `17cb23565cd2ffcd5a4d9e806a25345127737e271f442b5426b48e88b3da5121` |
| `body-rejection.onnx` | `a21d7227d415c12daf6af8f922021c43b4fc918a320153ccddf51ba4c4b6809c` |
| `body-rejection-secondary.onnx` | `7dbd95393eacc0e68397cca55c28149151939715354ff5155ee04c6730173a86` |

Starting head checkpoint SHA-256: `f69f9b86b221e8f4044481143fe420599568b070c39f351d6c4e7e4ceddc8d4a`; starting pose checkpoint: `869e83fcdffdc7371fa4e34cd8e51c838cc729571d1635e5141e3075e9319dc0`. Weights are not included in this showcase. [Evaluation](EVALUATION.md) · [Credits and dataset provenance](CREDITS.md) · [README](README.md)
