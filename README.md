# phishwatch-simulations

Harmless browser-based simulations of phishing attack mechanics detected by [PhishWatch](https://phishwatch.io) — a Chrome extension that intercepts browser-native attacks at the moment of user interaction.

These simulations do not steal credentials, make network requests, or execute malicious code. They reproduce only the **structural browser mechanics** that distinguish real attacks — the same signals PhishWatch hooks into.

---

## Why this exists

Most phishing defences operate at delivery: email filters, URL reputation checks, blocklists. They inspect the link before you click it.

A growing class of attacks bypasses all of that. **AiTM proxies, ClickFix, Browser-in-the-Browser, and ConsentFix** activate *after* delivery — inside the browser, at the moment of interaction. They don't need a malicious link. They abuse legitimate browser mechanics.

This repo lets you:
- **Verify** that PhishWatch fires correctly on each attack class
- **Understand** the specific browser mechanic being exploited in each case
- **Test** your own detection tooling against realistic simulations

---

## Quick start

No build step, no dependencies. Just a local HTTP server.

```bash
git clone https://github.com/phishwatch/phishwatch-simulations
cd phishwatch-simulations
python3 -m http.server 8765
```

Then open: [http://localhost:8765/index.html](http://localhost:8765/index.html)

> **Why a server?** Chrome blocks certain APIs (clipboard, cross-origin frames) on `file://` URLs. A local server is required for the simulations to behave as real attacks do.

---

## Simulations

| File | Attack class | What it simulates | PhishWatch module |
|------|-------------|-------------------|-------------------|
| `simulations/clickfix.html` | ClickFix | Fake CAPTCHA or error page that instructs the user to paste a command into Run/Terminal. Writes a malicious payload to clipboard. | ClickFix detector |
| `simulations/aitm.html` | AiTM / Tycoon 2FA | Credential relay flow: login form that proxies credentials to a remote server while maintaining a valid session appearance. MFA bypass mechanics. | Treadmill (AiTM) detector |
| `simulations/bitb.html` | Browser-in-the-Browser | Fake browser chrome rendered inside a webpage to simulate a trusted popup (e.g. OAuth login window). | BitB detector |
| `simulations/consentfix.html` | ConsentFix | Abusive OAuth consent flow requesting excessive scopes to hijack account access without stealing passwords. | ConsentFix detector |
| `simulations/fakeupdate.html` | Fake Update | Browser or software update prompt used to deliver malware under the appearance of a legitimate update. | Fake Update detector |
| `simulations/ailure.html` | AI Lure | AI-generated, personalised phishing lure designed to bypass template-based detection heuristics. | AI Lure detector |

---

## Test harness

`index.html` is the PhishWatch test harness — a structured 22-test checklist across 4 sections:

- **Section 1** — Client-side detection (no backend required)
- **Section 2** — Backend-assisted detection (requires PhishWatch backend running)
- **Section 3** — Browser-native attack simulations (the files above)
- **Section 4** — False positive regression tests

Run it locally with the same server command above. Results are saved in `localStorage` so you can resume across sessions.

---

## What PhishWatch detects (and what these simulations do not do)

Each simulation reproduces the **mechanical signature** of the attack — not the malicious payload. Specifically:

- **ClickFix**: writes a benign string to clipboard, no command execution
- **AiTM**: simulates the credential relay flow structure, no real credentials transmitted
- **BitB**: renders a fake window frame, no credential harvesting
- **ConsentFix**: presents an OAuth-style consent UI, no real OAuth requests made
- **Fake Update**: displays the update prompt UI, no file download
- **AI Lure**: presents the lure copy and structure, no exfiltration

PhishWatch detects the mechanics, not the payload. These simulations are safe to run.

---

## Installing PhishWatch

[Chrome Web Store →](https://chromewebstore.google.com/detail/phishwatch/odpemcfgjbgkkpklcfgcgaglmogjbghf)

Install PhishWatch before running the simulations. Each simulation should trigger a PhishWatch warning. If one does not, please [open an issue](https://github.com/deborshiseal/phishwatch-simulations/issues) with the browser version and OS.

> **Note on the install warning**: New Chrome extensions with under ~1,000 installs show an Enhanced Safe Browsing prompt. This is a volume threshold, not a safety verdict — PhishWatch has passed Google's full review process. Click *Continue to install*.

---

## Reporting false positives or missed detections

If a simulation **does not** trigger PhishWatch, or if PhishWatch fires on a site it shouldn't:

1. Open an issue with the title format: `[FP] <site>` or `[MISS] <attack class>`
2. Include your Chrome version and OS
3. Describe what happened

False positives are fixed within 24 hours.

---

## Related

- [phishwatch.io](https://phishwatch.io) — project site
- [Chrome Web Store](https://chromewebstore.google.com/detail/phishwatch/odpemcfgjbgkkpklcfgcgaglmogjbghf) — install the extension
- [security@phishwatch.io](mailto:security@phishwatch.io) — responsible disclosure

---

*These simulations are provided for security testing and educational purposes. They do not contain malicious code and are safe to run in any browser.*
