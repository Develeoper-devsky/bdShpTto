# Open vision models for Body Editor

Staging pack for upload to your GitHub model mirror (e.g. `bdShpTto`).
These are **not** BeautyPlus / Meitu `.manis` weights — they are open ONNX/TFLite
substitutes for attribute routing, face/body parsing, and 3D face assist.

## Layout

```
tools/models/
  attributes/
    genderage.onnx                 # age + gender (tiny)
    fairface.onnx                  # age + gender + race (7 classes)
    face_attrib_net.onnx           # eyeglasses / sunglasses / mask / eye open
  face_parsing/
    face_parsing_celebamask_quantized.onnx   # ears, nose, eyes, neck, glasses mask…
  body_parsing/
    schp-atr-18-int8-static.onnx   # fashion + body parts (18 classes)
    schp-pascal-7-int8-static.onnx # coarse head/torso/limbs (7 classes)
  3ddfa/
    tddfa_mb1_fp16.tflite          # 3DMM regressor
    tddfa_*.bin                    # BFM bases + param mean/std
  SOURCES.md
```

Already present elsewhere in `tools/` (not duplicated here):

- `tools/newModels/face_parsing_bisenet_r34.onnx`
- `tools/newModels/selfie_multiclass_256x256.tflite`

## Sources & licenses (check before shipping)

| File | Upstream | License notes |
|---|---|---|
| `genderage.onnx` | [InsightFace buffalo_l](https://huggingface.co/public-data/insightface/tree/main/models/buffalo_l) | InsightFace model packs are typically **research / non-commercial** — verify before Play Store use |
| `fairface.onnx` | [facefusion/models-3.0.0](https://huggingface.co/facefusion/models-3.0.0) · [yakhyo/fairface-onnx](https://github.com/yakhyo/fairface-onnx) | FairFace dataset/weights: **CC BY 4.0** |
| `face_attrib_net.onnx` | [yakhyo/face-attribute](https://github.com/yakhyo/face-attribute/releases/tag/weights) (Qualcomm Facial-Attribute-Detection) | Qualcomm model: **BSD-3-Clause** (see HF card) |
| `face_parsing_celebamask_quantized.onnx` | [Xenova/face-parsing](https://huggingface.co/Xenova/face-parsing) ← [jonathandinu/face-parsing](https://huggingface.co/jonathandinu/face-parsing) | Upstream card: research / educational — confirm for commercial |
| `schp-atr-18-int8-static.onnx` | [pirocheto/schp-atr-18](https://huggingface.co/pirocheto/schp-atr-18) | SCHP packaging; follow HF + original SCHP license |
| `schp-pascal-7-int8-static.onnx` | [pirocheto/schp-pascal-7](https://huggingface.co/pirocheto/schp-pascal-7) | Same as above |
| `3ddfa/*` | [litert-community/3DDFA-V2-LiteRT](https://huggingface.co/litert-community/3DDFA-V2-LiteRT) | 3DDFA_V2: **MIT**; BFM bases may have separate academic terms |

## What each model is for in our app

| Need | Use |
|---|---|
| Age / gender preset routing | `genderage.onnx` (preferred tiny) or `fairface.onnx` |
| Race / population preset routing | `fairface.onnx` only |
| Glasses on face (skip makeup on lenses) | `face_attrib_net.onnx` and/or face-parsing `eye_g` class |
| Ears, neck, nose, eye masks | CelebAMask face parsing |
| Shoulders / torso / limbs (coarse) | Pascal-7 + existing pose / multiclass |
| Clothes-aware body parse | ATR-18 (optional; LIP-20 already downloaded in-app) |
| 3D nose / jaw assist | 3DDFA LiteRT pack |

## Not included (no good open drop-in)

- Dedicated freckle detector (keep classical `FreckleMap`)
- Eyelid-fat / eyelid refine net
- Dedicated breast / shoulder / neck Meitu-style part nets

## Suggested GitHub release folder names

Mirror under your `bdShpTto` repo, for example:

```
genderage/genderage.onnx
fairface/fairface.onnx
face_attrib_net/face_attrib_net.onnx
face_parsing_celebamask/face_parsing_celebamask_quantized.onnx
schp_atr_18/schp-atr-18-int8-static.onnx
schp_pascal_7/schp-pascal-7-int8-static.onnx
3ddfa_v2_litert/...
```

## Download commands (re-fetch)

```bash
# From repo root
BASE=tools/models
curl -L -o $BASE/attributes/genderage.onnx \
  https://huggingface.co/public-data/insightface/resolve/main/models/buffalo_l/genderage.onnx
curl -L -o $BASE/attributes/fairface.onnx \
  https://huggingface.co/facefusion/models-3.0.0/resolve/main/fairface.onnx
curl -L -o $BASE/attributes/face_attrib_net.onnx \
  https://github.com/yakhyo/face-attribute/releases/download/weights/face_attrib_net.onnx
curl -L -o $BASE/face_parsing/face_parsing_celebamask_quantized.onnx \
  https://huggingface.co/Xenova/face-parsing/resolve/main/onnx/model_quantized.onnx
curl -L -o $BASE/body_parsing/schp-atr-18-int8-static.onnx \
  https://huggingface.co/pirocheto/schp-atr-18/resolve/main/onnx/schp-atr-18-int8-static.onnx
curl -L -o $BASE/body_parsing/schp-pascal-7-int8-static.onnx \
  https://huggingface.co/pirocheto/schp-pascal-7/resolve/main/onnx/schp-pascal-7-int8-static.onnx
# 3DDFA: all files under litert-community/3DDFA-V2-LiteRT/resolve/main/
```
