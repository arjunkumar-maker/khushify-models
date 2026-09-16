# Khushify AI — model packs

Weights for [Khushify AI](https://github.com/arjunkumar-maker), downloaded by the app on first use
rather than bundled into the APK.

That split is the point: the app installs at **72 MB** instead of 320 MB, and most people never
download either large pack — everything except face restoration and colourisation runs with no model
weights at all.

## What is here

| File | Size | Pack |
|---|---|---|
| `gfpgan-v1.4-512-fp16.onnx` | 170 MB | Face Restoration |
| `realesr-general-x4v3-256.onnx` | 5 MB | HD Upscale (256) |
| `realesr-general-x4v3-384.onnx` | 5 MB | HD Upscale (384) |
| `realesr-general-x4v3-512.onnx` | 5 MB | HD Upscale (512) |
| `eccv16-256-fp16.onnx` | 65 MB | Colourisation |
| `lama-fp32-512.onnx` | ~200 MB | Damage Reconstruction |

**The weights are not in this repository.** They are attached to releases. GitHub rejects any file
over 100 MB on push, and the face restorer is 170 MB, so committing them would fail rather than
merely bloat the history. `catalog.json` is committed because it is small and documents what each
pack is, including its SHA-256.

## How the app uses this

The app ships `catalog.json` *inside the APK*, so it can name a pack and its size — and say whether
it is installed — **with no network at all**. Only the download itself needs a connection.

```
https://github.com/arjunkumar-maker/khushify-models/releases/download/<tag>/<file>
```

Each download is verified against the SHA-256 in the catalogue before it is installed. A truncated
or tampered file is deleted rather than loaded: a bad model does not crash, it quietly produces
faces from the wrong weights, which is worse.

Once downloaded, inference is entirely offline.

## Releases

| Tag | Contents |
|---|---|
| `models-v1` | Face restoration, colourisation, super-resolution, and Damage Reconstruction |

A pack added to an EXISTING tag needs no app change at all beyond the regenerated
catalogue: the download URL is `<tag>/<file>`, so a new file under a tag the app
already points at is reachable the moment it finishes uploading.

Publishing a new tag does **not** require an app update, as long as `catalog.json` in the app is
regenerated to match.

`catalog.json` in this repository and the one inside the APK are both **generated**, never edited by
hand:

```
python tools/update_catalog.py --write     # from the app repository
```

It measures `bytes` and `sha256` from the files in `release-assets/` and writes both copies from one
definition. They had already drifted once when they were maintained by hand — four packs here used
bare asset paths while a fifth used a `models/`-prefixed one — and a wrong hash does not fail
loudly: the app deletes the download and reports it as corrupt, which reads as a network problem.

A pack listed in the generator with no file in `release-assets/` is simply omitted from both
catalogues, so the app never offers it. That is the correct state for a pack that has not been
exported yet.

## Licences

See [LICENSE-MODELS.md](LICENSE-MODELS.md). Every pack here is third-party work under its own terms,
and attribution is required on redistribution.

---

## DocsSeva packs

This repository also hosts the model files for **DocsSeva**. They sit under their own release tag
and carry `"app": "docsseva"` in `catalog.json`, so the two apps' packs cannot be confused.

| File | Size | Pack |
|---|---|---|
| `segment-u2netp.onnx` | 4.6 MB | Sharper background removal *(optional)* |
| `ocr-det-ppv4.onnx` | 4.7 MB | Text finder — shared by every language |
| `ocr-rec-en-ppv4.onnx` | 7.7 MB | English reader |
| `ocr-rec-en-ppv4.txt` | 192 B | English characters |
| `ocr-rec-hi-ppv3.onnx` | 9.0 MB | Hindi reader (Devanagari + Latin) |
| `ocr-rec-hi-ppv3.txt` | 510 B | Hindi characters |

Every one is Apache-2.0 on both code and weights — see `LICENSE-MODELS.md`, which also records the
one modification made to the two dictionaries and why it was necessary.

### Publishing them

The files are already staged in `release-assets/`. To make them live:

```bash
# 1. Commit the catalogue and the licences (the .onnx files are gitignored)
git add catalog.json LICENSE-MODELS.md README.md
git commit -m "Add DocsSeva model packs"
git push

# 2. Create the release and attach all six files
gh release create docsseva-v1 \
  release-assets/segment-u2netp.onnx \
  release-assets/ocr-det-ppv4.onnx \
  release-assets/ocr-rec-en-ppv4.onnx \
  release-assets/ocr-rec-en-ppv4.txt \
  release-assets/ocr-rec-hi-ppv3.onnx \
  release-assets/ocr-rec-hi-ppv3.txt \
  --title "DocsSeva models v1" \
  --notes "OCR and segmentation models for DocsSeva. Apache-2.0 throughout; see LICENSE-MODELS.md."
```

**The tag must be exactly `docsseva-v1`.** DocsSeva builds each download URL as
`<base>/<filename>` where the base is

```
https://github.com/arjunkumar-maker/khushify-models/releases/download/docsseva-v1
```

and that string is compiled into the app. A different tag means every download 404s.

### After publishing

```bash
cd ../../docsseva_app
python scripts/fetch-models.py --check      # every SHA-256 still matches
```

Then, in the app, Settings → Downloads should offer all three packs with a real size and an
**Install** button instead of "Not available yet".

Nothing needs rebuilding for a file added to an EXISTING tag. Replacing a file does: its SHA-256
changes, the app will reject the new bytes against the old digest, and
`src/services/models/registry.ts` has to be updated to match — which is the point of pinning them.
