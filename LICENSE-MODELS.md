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
