# darkvessel — Cross-Sensor Open-Set Dark-Vessel Detection

Flagship (P1): one open-set dark-vessel **detector** where AIS-identified track = "known",
no-AIS = "dark", demonstrated across **video+AIS (FVessel)** and **radar+AIS (Busan)**.
Replaces the PDF's 6-stage rule checker. Full plan: `~/.claude/plans/cached-snuggling-octopus.md`.

## Layout
- `common/geo.py` — haversine, angle diff, timestamp parsing (numpy only).
- `data/busan_loader.py` — Busan AIS + radar-target loaders.
- `data/pair_builder.py` — geometric radar↔AIS match (gating + cost-min, J=d+2·dψ) = the baseline.
- `data/dropout_splitter.py` — controlled AIS-dropout protocol (known-dark positives via withheld AIS).
- `data/fvessel_loader.py` — FVessel video+AIS track labels (ready; needs the download).
- `scripts/run_busan_w1.py` — W1 Busan arm (load→match→labels→splits→`outputs/`).
- `encoders/`, `p1_openset_darkdet/` — model code (W2+).

## Status (W1)
- ✅ **Busan radar+AIS arm** verified: `python -m scripts.run_busan_w1` reproduces the PDF baseline
  (point-match **0.740**, P90 **105.7 m**, bias 22.2 m = 7.4% of gate); 200 tracks → 101 matched / 99 dark
  candidates; dropout test split = 9 dark / 10 negatives. Artifacts in `outputs/`.
- ⏳ **FVessel video+AIS arm** — loader ready; **dataset download is manual** (see below).

## Get FVessel (manual — headless download is blocked)
OneDrive returns 403 (SharePoint-migrated) and Baidu is account-gated, so download via a browser:
- OneDrive: https://1drv.ms/u/s!As3rCDROnrbLeWE-RMXAGbwAMa4
- Baidu: https://pan.baidu.com/s/1-VNeZvWqYh7ESLXQxreCDg  (code: `MIPC`)

Unzip into `data/raw/fvessel/` so that video folders like `01_Video+AIS/` sit directly under it, then:
`python -m data.fvessel_loader data/raw/fvessel`
