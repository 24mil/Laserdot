# Credits and provenance

This is a documentation and curated-visual portfolio. It does not bundle pretrained weights, training datasets, raw recording archives, runtime dependencies or Arduino binaries. The notices below describe the local project archive; they are not a blanket license for every source asset.

| Source | Contribution / recorded notice |
| --- | --- |
| [Ultralytics YOLO11](https://github.com/ultralytics/ultralytics) | Detection/pose architectures and training/export software; AGPL-3.0 notices retained locally |
| [sakura-lj/valorant-ai-aim](https://github.com/sakura-lj/valorant-ai-aim/tree/ef1f29d6c0f87539df6a245a360e9a2e8a8f4fbd) | Initial externally game-trained YOLO11n checkpoint; pinned commit. Repository and model metadata notices differ (GPL-3.0 / AGPL-3.0); no weights redistributed here |
| [ONNX Runtime](https://github.com/microsoft/onnxruntime), [PyTorch](https://github.com/pytorch/pytorch) | Inference and training frameworks |
| [DXcam](https://github.com/ra1nty/DXcam), [OpenCV](https://github.com/opencv/opencv) | Screen capture and image processing |
| [Arduino Mouse](https://github.com/arduino-libraries/Mouse), [USB Host Shield Library 2.0](https://github.com/felis/USB_Host_Shield_2.0) | Mouse output and boot-mouse host input |
| Riot Games / Valorant | Characters, environments and effects visible in the project's own recorded game crops; no ownership or affiliation claimed |

## Dataset records

The archive preserves source notices, revisions and recording-group information. Public source labels can be imperfect; exact agent/pose/distance/ability coverage is not comprehensively annotated.

- **[Dasun01 / Valorant-Object-Detection-Dataset](https://huggingface.co/datasets/Dasun01/Valorant-Object-Detection-Dataset)**: revision `bb397ed31b4722adb251a28e525254970d21cce6`; local provenance records [original Roboflow Valorant enemy/head v2](https://universe.roboflow.com/valorant-enemy-enemy-head-detection/valorant-iuuzs/dataset/2) and CC BY 4.0.
- **[luoym525 / Valorant-Player-Detection](https://github.com/luoym525/Valorant-Player-Detection)**: revision `6c1eaceb844b31023a6daf181d5b64916df2fe0a`; repository MIT notice, embedded dataset Public Domain notice. Imported data overlaps Santyasa; deduplication and original notices are retained.
- **[Santyasa v4 / alfin scifo](https://universe.roboflow.com/alfin-scifo/santyasa/dataset/4)**, mirrored by JustinLuu: embedded CC BY 4.0 notice retained.
- **KwanLi object-detection2 v7**: embedded CC BY-NC-SA 4.0 disagrees with a mirror MIT tag; excluded from training export.
- **haqi001 Hugging Face data**: no supplied license; excluded from training export.
- **Local game recordings and reviewed labels**: project-specific debugging/training material. Related crops and augmentations stay in their source partition; generated image counts are not independent session counts.

## Development attribution

The project was iteratively directed and tested on local hardware, using pretrained models and existing libraries. AI-assisted coding and research contributed to implementation, investigation, tests and this documentation. The portfolio distinguishes pretrained components from local adaptations and reports measured limitations alongside results.

Diagrams and chart layouts were created for this showcase. Recorded imagery retains its game/source attribution. No repository-wide MIT/other license is asserted over third-party models or imagery.
