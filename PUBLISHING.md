# Publishing a model pack

The exact sequence for getting a new pack from a published checkpoint into the app. Written down
because the order matters in one place and is silently wrong in another: a release published before
the catalogue is regenerated leaves the app checking downloads against a hash it does not have.

Paths below assume the app repository at `../../khushify` and this repository at
`../modals/khushify-models`, which is how they sit locally.

---

## 1. Export the checkpoint to ONNX

Each pack has a converter in the app repository under `tools/convert/`. They take a published
checkpoint and produce the ONNX the app loads; they are run once, by hand, never by the app.

For Damage Reconstruction:

```bash
git clone https://github.com/advimman/lama
cd lama
curl -LO https://huggingface.co/smartywu/big-lama/resolve/main/big-lama.zip
unzip big-lama.zip

pip install torch onnx onnxruntime numpy pyyaml omegaconf

PYTHONPATH=. python ../khushify/tools/convert/export_lama.py \
    --checkpoint big-lama/models/best.ckpt \
    --config     big-lama/config.yaml \
    --out        lama-fp32-512.onnx
```

The script runs `onnx.checker` on its output and prints the size and SHA-256 it measured.

**Read `LICENSE-MODELS.md` before running this one.** LaMa's code is Apache 2.0; the `big-lama`
weights are released for non-commercial research use. That is a decision for the project owner, and
it has to be made before the file is redistributed, not after.

---

## 2. Stage the file

```bash
cp lama-fp32-512.onnx ../modals/khushify-models/release-assets/
```

`release-assets/` is git-ignored. Weights are release assets, not repository files — GitHub rejects
any push containing a file over 100 MB, so committing the 170 MB face restorer would fail outright
rather than merely bloat the history. The directory exists so the files sit in one folder to drag
into the release UI.

---

## 3. Regenerate both catalogues

```bash
cd ../khushify
python tools/update_catalog.py            # check: exits 1 if either copy is stale
python tools/update_catalog.py --write    # rewrite both
```

This measures `bytes` and `sha256` from the real files and writes:

* `android/ai/src/main/assets/models/catalog.json` — ships inside the APK
* `catalog.json` in this repository — committed as documentation

Never edit either by hand. They drifted once when they were maintained that way, and a wrong hash
does not fail loudly: the app deletes the download and reports it as corrupt, which looks like a
network problem and is not.

---

## 4. Publish the release

```bash
cd ../modals/khushify-models
git add catalog.json README.md LICENSE-MODELS.md PUBLISHING.md
git commit -m "Add Damage Reconstruction pack (LaMa)"
git push

gh release create models-v2 \
    release-assets/*.onnx \
    --title "Model packs v2" \
    --notes "Adds Damage Reconstruction (LaMa). Re-uploads the v1 packs so one tag serves them all."
```

**Attach every pack to the new tag, not only the new one.** The app builds one URL per file from a
single host property:

```
<khushifyModelHost>/<file>
```

so all files must live under the same tag. Attaching only the new pack to `models-v2` leaves the
other four resolving to 404 the moment the host is repointed.

---

## 5. Point the app at the tag

`android/gradle.properties`:

```properties
khushifyModelHost=https://github.com/arjunkumar-maker/khushify-models/releases/download/models-v2
```

This is pinned in the properties file rather than passed with `-P` for a reason: with no host the
build still succeeds, and the only symptom is the app telling a tester "this build has no model host
configured" when they tap Download. That shipped once.

---

## 6. Rebuild and verify

```bash
cd ../../khushify/android
./gradlew clean && ./gradlew assembleRelease
```

`clean` first, always, for a release build. Assets have moved in and out of this APK before, and
`merged_assets` intermediates survive an incremental build and silently inflate the artifact.

Then confirm on a device that the pack downloads and verifies — Profile lists every pack with its
real state, so a pack that installs shows "Installed on this device" and one that fails does not
appear as ready.

---

## Checklist

- [ ] Licence position resolved and recorded in `LICENSE-MODELS.md`
- [ ] ONNX exported, `onnx.checker` clean
- [ ] File copied into `release-assets/`
- [ ] `update_catalog.py --write` run, both catalogues updated
- [ ] Release created with **all** packs attached
- [ ] `khushifyModelHost` points at the new tag
- [ ] Clean release build
- [ ] Download verified on a device
