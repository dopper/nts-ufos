# nts-ufos — UAP/UFO Release 01 reproducible mirror

A reproducible local mirror of **U.S. Department of War — UAP/UFO Release 01**
(public release dated 2026-05-08, the first tranche of the PURSUE program).

This repository ships the **recipe**, not the bytes:

- [`manifest.txt`](./manifest.txt) — every asset URL (161 entries: 119 PDFs, 14 images, 28 DVIDS video pages).
- [`download.py`](./download.py) — bulk downloader using `curl_cffi` to bypass `war.gov`'s Akamai TLS-fingerprint bot wall.
- [`SHA256SUMS-20260509.txt`](./SHA256SUMS-20260509.txt) — 128 hashes captured from a verified pull on 2026-05-09; use to confirm your local copy matches.
- [`docs/2026-05-09-pull-report.md`](./docs/2026-05-09-pull-report.md) — full reconnaissance + execution report.

The PDFs and images themselves (~2.3 GB) live on `war.gov`; running
`download.py` materializes them under `./files/` (gitignored).

## Quick start

```bash
# 1. install the one Python dep
uv sync                # if you use uv (preferred)
# OR
pip install --user curl-cffi

# 2. pull the data (~2.3 GB, ~1 min on a fast connection, resumable)
python3 download.py

# 3. verify integrity against the captured baseline
cd files && shasum -a 256 -c ../SHA256SUMS-20260509.txt
```

After step 2, files mirror the war.gov URL paths under `./files/`. The script
is idempotent — re-running skips files that already exist.

## What's in the release

| Category | Count | Approx size |
|---|---:|---|
| PDFs (FBI, Air Force, NSA, NARA records) | 119 | ~2.3 GB |
| Images (NASA Apollo UAP, FBI photos) | 14 | ~30 MB |
| Video pages (DVIDS — HTML viewers, browser-only) | 28 | — |
| **Total URLs** | **161** | **~2.3 GB on disk** |

Two manifest URLs return HTTP 404 from origin (upstream issue, not a downloader
problem) and three are duplicates that overwrite to the same path. After a
clean run you'll have **128 unique files**.

## Why this exists

Plain `wget` / `curl` get HTTP 403 from `war.gov`: the origin sits behind
Akamai bot protection that fingerprints TLS handshakes (JA3), not just headers.
`curl_cffi` impersonates Chrome's full TLS fingerprint, which gets through.
Once the origin no longer requires this, `download.py` can revert to plain
`requests`.

## Sources

- Origin: <https://www.war.gov/ufo/>
- DoW press release: <https://www.war.gov/News/Releases/Release/Article/4480582/>
- Upstream mirror this fork is based on: <https://war-gov-ufo-release-1.vercel.app/>

## License

Code/manifest/docs: MIT (see [`LICENSE`](./LICENSE)).
Underlying records: U.S. federal government works — public domain (17 U.S.C. § 105).
