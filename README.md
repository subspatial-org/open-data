# Subspatial Open Data on AWS

Open, cloud-accessible cryo-electron tomography (cryo-ET) and structural cell biology datasets managed by [Subspatial](https://github.com/subspatial-org) and hosted on Amazon S3 through the AWS Open Data Sponsorship Program.

| Resource | Value |
|---|---|
| **S3 Bucket** | `s3://subspatial-opendata` (`arn:aws:s3:::subspatial-opendata`) |
| **AWS Region** | `eu-north-1` (Stockholm) |
| **Access** | Public, anonymous read (`--no-sign-request`, no AWS account required) |
| **SNS Notifications** | `arn:aws:sns:eu-north-1:732227445052:subspatial-opendata-notifications` (SQS and Lambda subscriptions; fires on `manifests/*.manifest.json`) |
| **Default License** | [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/) (unless noted in a dataset manifest or directory) |
| **Contact & Issues** | [GitHub Issues](https://github.com/subspatial-org/open-data/issues) |

---

## Bucket Layout (`s3://subspatial-opendata/`)

| Top-Level Path | Purpose |
|---|---|
| `README.txt` | Plain-text bucket overview and dataset index at bucket root |
| `LICENSE` | Default CC0 1.0 Universal public domain dedication |
| `manifests/*.manifest.json` | Release manifests written after dataset uploads complete (triggers SNS notifications) |
| `empiar-mirror/empiar-<id>/` | Public mirrors of upstream EMPIAR depositions, organized by accession |
| `datasets/subspatial-<nnnnn>/v<N>/` | Immutable, versioned releases published from the Subspatial Data Platform under stable Subspatial accession IDs (e.g., `datasets/subspatial-00001/v1/`) |

### Naming, Versioning, and Manifest Convention

- **Immutable S3 paths:** Published dataset prefixes are never renamed or mutated in place so external scripts, notebooks, and citations remain stable.
- **Provenance separation:** Upstream EMPIAR mirrors live under `empiar-mirror/empiar-<id>/`. Datasets originated or curated on the Subspatial Data Platform receive a permanent sequential accession (`subspatial-00001`, `subspatial-00002`, ...) with explicit release versions (`v1/`, `v2/`, ...).
- **Release manifests (`manifests/*.manifest.json`):** Each release writes a JSON manifest (`empiar-<id>.manifest.json` for mirrors; `subspatial-<nnnnn>-v<N>.manifest.json` for versioned releases) containing `dataset_id`, `release`, `source_type`, `prefix`, `license`, and `external_ids` (such as `empiar`, `emdb`, or `doi`). If a Subspatial dataset is later deposited to EMPIAR or assigned a DOI, `external_ids` is updated in the manifest without moving any objects in S3.

---

## Available Datasets

| Dataset | S3 Prefix | License | Documentation | Tutorial |
|---|---|---|---|---|
| **EMPIAR-11830** (*Chlamydomonas reinhardtii* in situ cryo-ET) | `empiar-mirror/empiar-11830/` | CC0 1.0 | [`empiar-11830/README.md`](empiar-11830/README.md) | [`empiar-11830/get-to-know-a-dataset.ipynb`](empiar-11830/get-to-know-a-dataset.ipynb) |

See [`empiar-11830/README.md`](empiar-11830/README.md) for the EMPIAR-11830 directory layout (`must_bin8`, `must_bin4`, `cryocare_bin4`, `ctf3d_bin4`), MRC2014 Range-read notes, and full citations.

---

## Quick Start

### 1. Interactive Tutorial (No AWS Account Required)

Run [`empiar-11830/get-to-know-a-dataset.ipynb`](empiar-11830/get-to-know-a-dataset.ipynb) to list the bucket anonymously, read an MRC2014 header, and render a single Z-slice (~1 MiB transfer out of a 256 MiB tomogram) using unsigned `boto3`.

### 2. Command-Line Access

**List the bucket root and manifests with AWS CLI:**
```bash
aws s3 ls --no-sign-request s3://subspatial-opendata/
aws s3 ls --no-sign-request s3://subspatial-opendata/manifests/
```

**High-throughput bulk staging with `s5cmd`:**
```bash
s5cmd --no-sign-request cp \
  "s3://subspatial-opendata/empiar-mirror/empiar-11830/data/must_bin8/01082023_BrnoKrios_Arctis_WebUI_Position_11_dose-filt.rec" .
```

**Read-only POSIX mount on Linux (`mountpoint-s3`) for IMOD, RELION, or TOMOMAN:**
```bash
mount-s3 --no-sign-request --region eu-north-1 --cache /tmp/s3-cache \
  subspatial-opendata /mnt/subspatial-opendata
```

### 3. Compute Placement and Release Notifications

- **Run compute in `eu-north-1`:** The bucket is hosted in `eu-north-1` (Stockholm). Launching EC2 or AWS Batch analysis jobs in `eu-north-1` gives the lowest latency and highest throughput to S3.
- **Subscribe to new releases:** Attach an Amazon SQS queue or AWS Lambda function to `arn:aws:sns:eu-north-1:732227445052:subspatial-opendata-notifications`. Notifications fire only when `manifests/*.manifest.json` files are created so subscribers are not flooded by individual data objects.
