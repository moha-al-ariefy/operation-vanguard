# Threat Emulation Brief: FIN7 (G0046)
**Threat Emulation Operator:** Mohammed Al Ariefy
**Campaign:** Operation Vanguard (Sprint 6)

## 1. Adversary Profile: FIN7
For this emulation, I selected **FIN7** (MITRE ATT&CK Group G0046). FIN7 is a highly sophisticated, financially motivated threat group that heavily targets the retail, restaurant, and hospitality sectors. Their primary objective is the theft of payment card data and Personally Identifiable Information (PII). 

I chose FIN7 because their documented Tactics, Techniques, and Procedures (TTPs) perfectly map to the OWASP Juice Shop e-commerce environment. They frequently utilize SQL injection to breach web applications, pivot to client-side attacks to capture user data, and employ automated exfiltration scripts.

## 2. Emulated Kill-Chain & Methodology
Instead of running isolated exploits, I chained the attacks to mimic FIN7's behavioral footprint:

1. **Active Scanning (T1595.002):** FIN7 typically probes internet-facing infrastructure for known vulnerabilities. I emulated this by fuzzing the `/rest/products/search` endpoint using aggressive SQL syntax lists.
2. **Exploit Public-Facing Application (T1190):** I executed FIN7's signature SQL injection payloads against the `/rest/user/login` portal. As expected, our Sprint 4 ORM hardening successfully neutralized the exploit, but I generated the necessary telemetry for the Blue Team to tune their alerts.
3. **Drive-by Compromise / XSS (T1189):** FIN7 frequently uses web-skimmers (Magecart-style attacks) to steal data directly from the client. I emulated this by exploiting the known DOM XSS vulnerability to inject a JavaScript payload designed to harvest `localStorage` JWTs, simulating a payment data scraper.
4. **Automated Exfiltration (T1020):** I wrote a lightweight Python script to iterate through the `/rest/basket/*` API endpoints sequentially, emulating FIN7's automated data scraping techniques.

## 3. Emulation Conclusion
Emulating a named actor forces an operator to think about *how* the attack is delivered (the noise, the rate, the specific payload structure) rather than just making the exploit work. Generating this realistic, chained traffic provided the exact telemetry needed for the Blue Team to tune out false positives and finalize their detection logic.
