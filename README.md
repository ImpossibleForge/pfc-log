# PFC-Log — High-Ratio Log Compressor with Block-Level Random Access

> Compress Apache/Nginx/syslog files to **6.7% of original size** — with timestamp-based queries that download only the blocks you need.

[![License: Proprietary](https://img.shields.io/badge/License-Proprietary-red.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-Ready-blue.svg)](https://hub.docker.com/r/impossibleforge/pfc-log)
[![Version](https://img.shields.io/badge/Version-3.3-green.svg)]()

---

## Why PFC-Log?

| Tool | Ratio on Apache Logs | Random Access | Egress (10TB, 1h query) |
|------|---------------------|---------------|------------------------|
| **PFC-Log** | **6.7%** | ✅ Block-level | **~24 MB** |
| gzip-6 | 14.3% | ❌ Full download | ~1.43 TB |
| zstd-3 | 14.3% | ❌ Full download | ~1.43 TB |
| zstd-19 | 8.4% | ❌ Full download | ~857 GB |
| bzip2-9 | 7.4% | ❌ Full download | ~754 GB |

**10 TB archive, 1-hour query: download ~24 MB instead of 1.43 TB. That's $0.002 instead of $129 at S3 Glacier pricing.**

---

## How It Works

PFC divides logs into independent 32 MiB blocks (BWT → MTF → RLE → rANS). Each block's timestamp range is stored in a `.pfc.idx` file. To query a time range, only the relevant blocks are fetched via HTTP Range requests — no full archive download needed.

Works with **any S3-compatible storage** via pre-signed URLs: AWS S3, S3 Glacier Instant, Azure Blob (SAS), Cloudflare R2, Hetzner, MinIO, Wasabi.

---

## Quick Start

```bash
# Pull the image
docker pull impossibleforge/pfc-log:v3.3

# Compress a log file (requires license.key — see below)
docker run --rm -v $(pwd):/data impossibleforge/pfc-log:v3.3 \
    pfc compress /data/access.log /data/access.pfc

# Query a time range (from pre-signed URL)
docker run --rm -v $(pwd):/data impossibleforge/pfc-log:v3.3 \
    pfc query --from 2024-01-15T06:00 --to 2024-01-15T08:00 \
    --idx-url "https://your-presigned-idx-url" \
    "https://your-presigned-pfc-url"

# Decompress
docker run --rm -v $(pwd):/data impossibleforge/pfc-log:v3.3 \
    pfc decompress /data/access.pfc /data/restored.log
```

---

## Commands

| Command | Description |
|---------|-------------|
| `pfc compress <input> <output>` | Compress log file → `.pfc` + `.pfc.idx` |
| `pfc decompress <input> <output>` | Full decompression |
| `pfc query --from X --to Y --idx-url <url> <url>` | Fetch only matching blocks |
| `pfc seek-block N <input> [output]` | Extract single block by number |
| `pfc info <input>` | Show block table + timestamp ranges |

---

## License & Trial

PFC-Log is **proprietary software**. A free 30-day trial is available.

📧 **Trial or license inquiry:** info@impossibleforge.com

Without a valid `license.key` file, the tool displays:
```
[PFC-Log] No license.key found. Contact info@impossibleforge.com
```

---

## Documentation

- [Full Documentation](docs/PFC_LOG_DOCS.md)
- [Setup Guide](docs/SETUP_GUIDE.txt)
- [Third Party Licenses](docs/THIRD_PARTY_LICENSES.txt)

---

## White-Label & Partnership

Interested in integrating PFC-Log into your product?
We offer white-label licensing and OEM partnerships.

📧 **Contact:** info@impossibleforge.com

---

*Built by [ImpossibleForge](https://github.com/ImpossibleForge) — proprietary compression research.*
