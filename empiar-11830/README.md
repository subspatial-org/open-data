# EMPIAR-11830 — *Chlamydomonas reinhardtii* In Situ Cryo-ET

Public S3 mirror of the cryo-plasmaFIB-milled *in situ* cryo-electron tomography dataset of *Chlamydomonas reinhardtii* (Kelley, Khavnekar, Righetto et al., [EMPIAR-11830](https://www.ebi.ac.uk/empiar/EMPIAR-11830/)).

| Field | Value |
|---|---|
| **S3 Prefix** | `s3://subspatial-opendata/empiar-mirror/empiar-11830/` |
| **Release Manifest** | `s3://subspatial-opendata/manifests/empiar-11830.manifest.json` |
| **AWS Region** | `eu-north-1` (Stockholm) |
| **Access** | Public, anonymous read (`--no-sign-request`) |
| **License** | [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/) |
| **Tutorial Notebook** | [`get-to-know-a-dataset.ipynb`](get-to-know-a-dataset.ipynb) |

---

## Directory Layout

All objects below live under `s3://subspatial-opendata/empiar-mirror/empiar-11830/`:

| Sub-prefix | Contents | Format & Scale |
|---|---|---|
| `(root / tilt series)` | Raw tilt-series frames and tilt series (~1,871 acquisition positions) | Raw cryo-ET frames & tilt series |
| `data/must_bin8/` | 1,805 bin8 reconstructed tomograms | MRC2014 (`.rec`), ~256 MiB each (~485 GB total) |
| `data/must_bin4/` | bin4 reconstructed tomograms | MRC2014 |
| `data/cryocare_bin4/` | 1,829 cryoCARE denoised bin4 tomograms | MRC2014 (`.mrc`), ~2 GB each (~3.9 TB total) |
| `data/ctf3d_bin4/` | 3D-CTF corrected bin4 tomograms | MRC2014 |

### MRC2014 File Format Notes

Reconstructed `.rec` and `.mrc` files follow the standard MRC2014 binary specification: a 1,024-byte fixed header (`nx`, `ny`, `nz`, `mode`, cell dimensions in Angstroms, and extended header size `nsymbt`) followed by contiguous `(Z, Y, X)` voxel data. Clients can read the header (`bytes=0-1023`) and individual Z-slices directly via HTTP `Range` requests without downloading entire volumes.

---

## Quick Access

- **Interactive walkthrough:** Run [`get-to-know-a-dataset.ipynb`](get-to-know-a-dataset.ipynb) to inspect the prefix anonymously, parse an MRC header, and plot a central Z-slice (~1 MiB transfer from a 256 MiB volume).
- **List bin8 tomograms:**
  ```bash
  aws s3 ls --no-sign-request s3://subspatial-opendata/empiar-mirror/empiar-11830/data/must_bin8/
  ```
- **Download a single volume with `s5cmd`:**
  ```bash
  s5cmd --no-sign-request cp \
    "s3://subspatial-opendata/empiar-mirror/empiar-11830/data/must_bin8/01082023_BrnoKrios_Arctis_WebUI_Position_11_dose-filt.rec" .
  ```
- **Mount read-only via `mountpoint-s3` (Linux):**
  ```bash
  mount-s3 --no-sign-request --region eu-north-1 --cache /tmp/s3-cache \
    subspatial-opendata /mnt/subspatial-opendata
  ```

---

## Citation

Please cite the original EMPIAR-11830 deposition, accompanying preprint, and the Registry of Open Data on AWS:

- Kelley, Khavnekar, Righetto et al., *In situ cryo-ET dataset of Chlamydomonas reinhardtii prepared using cryo-plasmaFIB milling*, EMPIAR-11830: https://www.ebi.ac.uk/empiar/EMPIAR-11830/
- Kelley, Khavnekar, Righetto et al., *Towards community-driven visual proteomics with large-scale cryo-electron tomography of Chlamydomonas reinhardtii*, bioRxiv: https://www.biorxiv.org/content/10.1101/2024.12.28.630444v1
