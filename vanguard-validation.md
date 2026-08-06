# Operation Vanguard: Detection Validation & Tuning Log

**Target:** OWASP Juice Shop (Local Docker - `127.0.0.1:3000`)
**Emulated Threat Actor:** FIN7 (G0046)
**Red Operator:** Mohammed Al Ariefy
**Blue Lead:** Saif Alblooshi
**Date:** 6 August 2026

| MITRE ATT&CK ID | Emulated Behavior (FIN7) | Existing Rule Fired? (Y/N/Partial) | False Positives Seen | Tuning Applied | Result After Tuning |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **T1595.002** | Automated vulnerability scanning against `/rest/products/search` to find injection flaws. | **Partial** (Volume spike detected, but no payload context) | High (Normal user typos and aggressive browsing triggered alerts) | Tuned threshold to require 5+ SQL-specific malformed payloads within a 60-second rolling window. | High fidelity alert; zero FPs on legitimate traffic. |
| **T1190** | SQLi Probing (`' OR 1=1--`) on `/rest/user/login` to bypass authentication. | **Y** (SIGMA-01 fired) | Low (Triggered on names like "O'Connor") | Refined regex constraints to ignore isolated single quotes; rule now requires SQL logical operators (`OR`, `UNION`). | Alert triggers exclusively on executable SQL syntax. |
| **T1189** | Drive-by Compromise (DOM XSS) injected into search to hijack active user JWTs. | **N** (Known gap from Sprint 5) | N/A | Authored new SIGMA-05 rule monitoring GET parameters for `<script>`, `<iframe>`, or `javascript:` URI handlers. | XSS probing now reliably alerts the SOC. |
| **T1020** | Automated API scraping of `/rest/basket/*` to exfiltrate proprietary data. | **Partial** (SIGMA-02 fired on IDOR, but missed the automation rate) | Medium (Normal bulk item cart additions flagged) | Tuned rate-limiting detection to specifically alert on strictly sequential ID iteration (e.g., basket/1, basket/2, basket/3). | 100% detection of sequential enumeration with no FPs. |
