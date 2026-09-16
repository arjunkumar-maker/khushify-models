# Model licences

These weights are third-party work. Hosting them here is redistribution, and each carries its own
terms. Attribution is required.

---

## Real-ESRGAN `general-x4v3` — BSD 3-Clause

`realesr-general-x4v3-{256,384,512}.onnx`

© 2021 Xintao Wang. From [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN).

> Super-resolution by Real-ESRGAN (BSD 3-Clause), © 2021 Xintao Wang

Commercial use permitted with attribution.

---

## Colourisation (Zhang et al., ECCV 2016) — BSD 2-Clause

`eccv16-256-fp16.onnx`

© 2016 Richard Zhang. From [colorization](https://github.com/richzhang/colorization).

> Colourisation by Zhang et al. (BSD 2-Clause), © 2016 Richard Zhang

Commercial use permitted with attribution. Unambiguous.

---

## GFPGAN v1.4 — Apache 2.0, WITH A CAVEAT

`gfpgan-v1.4-512-fp16.onnx`

© Tencent ARC Lab. From [GFPGAN](https://github.com/TencentARC/GFPGAN).

> Face restoration by GFPGAN (Apache License 2.0), © Tencent ARC Lab

**The commercial position is unresolved, and the ambiguity is inside GFPGAN's own LICENSE file.**

The headline is Apache 2.0. The same file then bundles NVIDIA's StyleGAN2 terms verbatim — *"The
Work and any derivative works thereof only may be used or intended for use non-commercially"* — and
GFPGAN's face prior **is** a pretrained StyleGAN2. DFDNet components are under CC-BY-NC-SA 4.0.

The `clean` architecture used here was written to remove NVIDIA's custom CUDA operators, which
weakens the code-derivation argument but says nothing about the weights.

Checked against the upstream file on 2026-08-26. Widely-repeated claims that GFPGAN is "Apache 2.0,
therefore fine commercially" omit the bundled clause.

**This is a commercial and legal decision for the project owner, not an engineering one.** It is
recorded here so it is made deliberately rather than by default.

---

## LaMa (big-lama) — Apache 2.0 CODE, NON-COMMERCIAL WEIGHTS

`lama-fp32-512.onnx`

© Samsung Research. From [lama](https://github.com/advimman/lama), Suvorov et al., WACV 2022.

> Damage reconstruction by LaMa (Suvorov et al., 2022), © Samsung Research

**The code is Apache 2.0. The `big-lama` weights are not.**

This is the same shape of problem as GFPGAN, and it needs the same deliberate decision. The
repository's LICENSE is Apache 2.0, and that covers the architecture and the training code. The
published `big-lama` checkpoint is released separately, under terms restricting it to
non-commercial research use.

So the position is:

* shipping the **architecture**, retrained on data you hold rights to — unrestricted;
* shipping the **published big-lama checkpoint** in a paid product — not covered by Apache 2.0, and
  the restriction lands on the artefact this pack actually contains.

Checked against the upstream repository on 2026-08-27. As with GFPGAN, the widely-repeated summary
"LaMa is Apache 2.0" describes the code and omits the weights.

**This is a commercial and legal decision for the project owner, not an engineering one.** It is
recorded here so it is made deliberately rather than by default. If Khushify is or becomes a paid
product the options are to retrain the architecture, to license the checkpoint, or to leave this
pack out — and the app already handles the last one correctly, because a pack absent from the
catalogue is never offered.

---

## Conversion

All files were converted from their published checkpoints by the scripts in the app repository
(`tools/convert/`). The weights are unmodified in substance; only the numeric format changed
(fp32 → fp16) and the graph format (PyTorch → ONNX). Parity against the originals is recorded in
each descriptor.

---

# DocsSeva packs

Six files added for [DocsSeva](https://docsseva.com), sharing this repository and its releases.
They are tagged `docsseva-v1` and carry `"app": "docsseva"` in `catalog.json`, so the two apps'
packs never get confused for one another.

**Every one is Apache-2.0 on BOTH the code and the weights.** That was a hard requirement rather
than a preference: DocsSeva ships commercially, and its model registry refuses any entry whose
weights are non-commercial or research-only — with a test asserting it. The two obvious
alternatives for background removal, RMBG-1.4 and MODNet, are excluded for exactly that reason and
will never appear here.

---

## Apache-2.0 obligations, and how they are met

Hosting these on a GitHub release IS redistribution, so Apache-2.0 §4 applies. Three things are
required, and all three are done here:

1. **Ship the licence.** The Apache-2.0 text accompanies this file. PaddleOCR and U-2-Net both
   publish under the standard unmodified text.
2. **Keep the copyright notices.** Reproduced per pack below.
3. **State that the files were changed.** Every ONNX here is a format conversion of a published
   checkpoint — `paddle2onnx` for the PaddleOCR models, a PyTorch export for U-2-Net. The weights
   are unmodified in substance; only the graph format changed. The two dictionaries have **one
   space entry appended**, which is stated in full below because it is a real modification.

Neither PaddleOCR nor U-2-Net ships a NOTICE file, so §4(d) does not apply.

---

## U-2-Net (u2netp) — Apache-2.0

`segment-u2netp.onnx` · 4,574,861 bytes ·
`309c8469258dda742793dce0ebea8e6dd393174f89934733ecc8b14c76f4ddd8`

© Xuebin Qin et al. From [U-2-Net](https://github.com/xuebinqin/U-2-Net).

> Background removal by U²-Net (Apache License 2.0), © Xuebin Qin et al.

**Commercial use permitted, on the code AND the published checkpoints.** This is the specific
reason it was chosen: RMBG-1.4 forbids commercial use without a BRIA agreement, and MODNet's
weights are research-only. Both are recorded as blocked in DocsSeva's `docs/06`.

ONNX conversion redistributed by [rembg](https://github.com/danielgatis/rembg) (MIT), byte-identical
across three independent mirrors. Input is statically `[1,3,320,320]`.

*Optional.* The tools that use it — background removal and passport photos — work without it, on a
GrabCut implementation that needs no weights at all. This buys quality on hair, glasses and busy
backgrounds.

---

## PaddleOCR PP-OCRv4 / PP-OCRv3 — Apache-2.0

> OCR by PaddleOCR (Apache License 2.0), © 2016 PaddlePaddle Authors

From [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR). The upstream LICENSE is the standard
Apache-2.0 text with no carve-out for weights, and the project README states the release is under
it. PaddleOCR publishes inference models in Paddle's own format; every ONNX below is a
`paddle2onnx` conversion by a third party, verified against the shape and class-count contract the
app's C++ requires before being accepted.

| File | Bytes | SHA-256 |
|---|---|---|
| `ocr-det-ppv4.onnx` | 4,745,517 | `d2a7720d45a54257208b1e13e36a8479894cb74155a5efe29462512d42f49da9` |
| `ocr-rec-en-ppv4.onnx` | 7,656,802 | `5ca59a096cf493d5fd5044b9ccc1bdea9229fbcf656bd76040ff80a00201ee16` |
| `ocr-rec-hi-ppv3.onnx` | 8,988,754 | `444426a637dd15aeb43c59f18893e3c9415cf572170396b6aa3e7112c0a6400d` |
| `ocr-rec-en-ppv4.txt` | 192 | `aab362a7748f5e47d2440f67ecbcb7aab0f1a4d43dda470edb46570fbccf7f82` |
| `ocr-rec-hi-ppv3.txt` | 510 | `1bc8477be6b59a2a8d4b88d5943fb7e2f1f2e6729bc3249bc6873a4d44062dd1` |

Detection is a DB model and is script-agnostic despite its upstream `ch_` name — it finds text
regions, not characters, so one detector serves every language pack.

### The dictionaries were modified, and here is exactly how

Both `.txt` files are PaddleOCR's own character lists (`en_dict.txt` and
`dict/devanagari_dict.txt`, at tag `v2.9.1`) **with one space entry appended**.

That is not cosmetic. Each recogniser emits one class per dictionary entry plus a CTC blank at index
zero — English 97 classes, Devanagari 169 — and PaddleOCR adds a space at load time when
`use_space_char` is set, so the files on disk are one entry short of what the models produce. Left
uncorrected, every index past the gap lands one row off and the recogniser produces fluent,
confident, entirely wrong text rather than anything that looks like a failure.

Appending it to the published file makes the artefact self-describing: `entries + 1 blank = classes`
holds for anyone who picks it up. English ends with two consecutive space entries because its
upstream file already ended with one and PaddleOCR appends regardless; both decode to a space, so
the duplication is harmless and the count is what matters.

Verified by running each model — see `scripts/verify-ocr-models.py` in the DocsSeva repository.

---

## Devanagari font

Not hosted here — bundled directly into the APK, and recorded for completeness.

**Noto Sans Devanagari**, SIL Open Font License 1.1, © 2022 The Noto Project Authors.

OFL permits embedding in a document, including commercially, and a document does not become subject
to the OFL by containing an embedded subset. The licence text ships beside the font in the app and
is shown on its licences screen.
