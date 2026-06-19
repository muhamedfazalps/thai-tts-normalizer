# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this
project aims to follow [Semantic Versioning](https://semver.org/). The current
version lives in `app.py` (`version="..."`).

## 0.1.0 — 2026-06-19

Initial release.

### Added

- **Reverse proxy** (`app.py`): OpenAI-compatible proxy that normalizes the
  `input` field on `POST /audio/speech` and `POST /v1/audio/speech` before
  forwarding to an upstream TTS server such as OmniVoice. All other paths
  (voices, models, clone, design, web UI, swagger) are forwarded transparently,
  and the audio response is streamed back untouched.
- **Thai text normalization** (`thai_normalizer.py`):
  - Arabic digits → Thai words (`123` → `หนึ่งร้อยยี่สิบสาม`).
  - Thousands separators stripped before conversion (`1,200` → `หนึ่งพันสองร้อย`).
  - ๆ (mai yamok) expansion (`ดีๆ` → `ดีดี`).
  - Independent toggles via the `NORMALIZE_NUMBERS` / `NORMALIZE_MAIYAMOK` env
    vars.
- **Vendored normalization logic** from [PyThaiTTS](https://github.com/PyThaiNLP/PyThaiTTS)
  (`pythaitts.preprocess`, Apache-2.0): pure-Python, depends only on the stdlib
  `re` module, pulls no TTS model weights.
- **Operational bits**: `Dockerfile`, `.env.example`, and an end-to-end test
  suite (`tests/test_proxy_e2e.py`) covering normalization, transparent
  forwarding, and edge cases (missing/`non-JSON` bodies).

### Known limitations

- Long digit strings (e.g. phone numbers) are read as a single large number,
  not digit-by-digit — inherited from PyThaiTTS's digit-to-word logic.
- Thai numerals (๑๒๓) are not converted; only Arabic digits (123) are.
