# Day 3 — Phishing Page Anatomy & Detection

**Program:** Sqrock IT Solution — Alpha 2 Cybersecurity Internship
**Difficulty:** Beginner
**Category:** Week 1 — Recon & OSINT

## Objective

Analyze the anatomy of real phishing URLs and build a **detector**, not
a phishing page. This script only scores and flags URLs — it does not
create, host, or serve any phishing content.

## Theory

Phishing pages rely on two things: visual cloning of a legitimate site,
and urgency triggers (e.g. "your account will be suspended"). Since
visual cloning happens on the page itself, the URL is often the only
clue available before a user clicks. Common URL-level tricks include:

- **Homograph attacks** — lookalike characters mimicking a real domain
- **Subdomain abuse** — placing a trusted brand name in the subdomain
  of an attacker-controlled domain (e.g. `paypal.login.evil.com`)
- **Missing HTTPS** — legitimate services almost universally encrypt
  traffic today

## What This Script Does

`phish_scorer.py` parses a URL and assigns a 0–100 risk score based on
five weighted indicators:

| Factor | Points | Signal |
|---|---|---|
| No HTTPS | +30 | Unencrypted connection |
| Suspicious keyword in domain (`login`, `verify`, `secure`, `update`, `account`, `bank`) | +20 | Common phishing trigger words |
| Brand impersonation | +35 | Known brand name in domain, but not the brand's real domain |
| Excessive subdomains (>3 dots) | +25 | Possible subdomain abuse |
| Raw IP address instead of domain | +40 | Common in hastily-set-up malicious hosts |

Scores map to risk bands: **0** Likely Safe · **1–39** Low · **40–69**
Medium · **70–100** High.

## Requirements

No external packages — uses only Python's standard library
(`re`, `urllib.parse`).

## Usage

```bash
python phish_scorer.py
```

Or in Jupyter, paste each function into its own cell (imports/constants
→ `phish_score` → `classify` → test loop), then run.

## Sample Output

```
https://paypal-login.evil.com/verify   -> Score: 55, Risk: MEDIUM RISK
    - Suspicious keyword in domain: 'login'
    - Impersonates brand 'paypal' (not on paypal.com)

https://accounts.google.com/signin     -> Score: 20, Risk: LOW RISK
    - Suspicious keyword in domain: 'account'
```

## Key Findings & Limitations

- **Brand impersonation check matters**: without it, `paypal-login.evil.com`
  scores only 20 (LOW) — a dangerous false negative for an obvious fake.
  Adding the check raises it to 55 (MEDIUM).
- **False positives on real login pages**: legitimate URLs like
  `accounts.google.com` and `login.microsoftonline.com` still score 20
  (LOW) purely from keyword matches, since the detector has no whitelist
  of trusted root domains.
- **URL analysis alone isn't enough**: real-world phishing detection
  combines URL scoring with domain age, SSL certificate validation, and
  visual page-similarity analysis for higher accuracy.

## Ethics Note

This tool is defensive only. It analyzes and flags URLs — it does not
generate, clone, or host phishing pages.

## Files

- `phish_scorer.py` — the scoring script
- `Day3_Phishing_Detection_Report.md` — full write-up with 10-URL test results
- This README
