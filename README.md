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
| `models-v1` | Initial packs — face restoration, colourisation, super-resolution |

Publishing a new tag does **not** require an app update, as long as `catalog.json` in the app is
regenerated to match.

## Licences

See [LICENSE-MODELS.md](LICENSE-MODELS.md). Every pack here is third-party work under its own terms,
and attribution is required on redistribution.
