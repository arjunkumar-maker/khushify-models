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
