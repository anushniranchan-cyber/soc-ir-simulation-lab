# 📋 Playbook: Phishing Response

**Severity:** P2 (escalates to P1 on credential submission or malware detonation)
**SLA:** Triage in 15 min · Contain in 1 hour

## 1. Triage (0–15 min)

- [ ] Pull the original `.eml` — **never** work from a forwarded copy (headers get mangled)
- [ ] Run it through [`phishing-email-analyzer`](https://github.com/anushniranchan-cyber/phishing-email-analyzer) for automated header forensics + IOC extraction
- [ ] Check verdict:
  - 🔴 **MALICIOUS** → proceed to containment
  - 🟠 **SUSPICIOUS** → analyst review, check user interaction below
  - 🟢 **BENIGN** → close with notes, monitor sender

## 2. Scope (15–30 min)

- [ ] Search mail gateway logs: who else received it? (`sender IP + subject + hash`)
- [ ] Did anyone click? Check proxy/DNS logs for the extracted URLs
- [ ] Did anyone submit creds? Check IdP logs for anomalous logons from recipients
- [ ] Pull attachment hashes → VirusTotal / sandbox detonation

## 3. Contain (30–60 min)

- [ ] Block sender domain + IPs at the mail gateway
- [ ] Block extracted URLs at proxy/firewall; push hashes to EDR blocklist
- [ ] Purge the message from all mailboxes (mailbox search-and-destroy)
- [ ] Force password reset for any user who clicked/submitted creds
- [ ] Isolate any endpoint that executed the attachment (see `malware_containment.md`)

## 4. Eradicate & recover

- [ ] Confirm no persistence: review EDR timeline on affected hosts
- [ ] Revoke active sessions/tokens for compromised accounts
- [ ] Re-enable accounts only after MFA re-enrollment

## 5. Lessons learned

- [ ] Document IOCs in threat intel platform
- [ ] Was this a known campaign? Check OSINT (URLScan, AbuseIPDB, X/Twitter)
- [ ] Feed missed-detection feedback into mail gateway rules
