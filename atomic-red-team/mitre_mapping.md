# 🗺️ Atomic Red Team → Detection Mapping

Technique → detection mapping: each Atomic Red Team technique below is **covered by** the example SPL in `../detections/`. Treat these as a starting point — execute the technique in your own lab and validate the detection fires before relying on it.

| # | Atomic Test | ATT&CK | What it simulates | Covered by |
|---|-------------|--------|-------------------|------------|
| 1 | `T1110` Test 1 | **T1110.001** | Password guessing via Hydra against SSH | `brute_force.spl` |
| 2 | `T1110` Test 3 | **T1110.003** | Password spraying across multiple accounts | `brute_force.spl` (`targeted_accounts ≥ 5` → critical) |
| 3 | `T1134` Test 5 | **T1134** | Access token manipulation (potato family) | `privilege_escalation.spl` (tool name match) |
| 4 | `T1059.001` Test 12 | **T1059.001** | Encoded PowerShell download cradle | `privilege_escalation.spl` (encoded_ps) |
| 5 | `T1021.002` Test 1 | **T1021.002** | PsExec lateral movement to admin share | `lateral_movement.spl` |
| 6 | `T1047` Test 3 | **T1047** | WMI remote process creation | `lateral_movement.spl` (WMI branch) |
| 7 | `T1021.006` Test 1 | **T1021.006** | WinRM remote session | `lateral_movement.spl` (WinRM branch) |

## Tuning notes

- **Brute force threshold (10 / 5 min):** a starting threshold — tune it against your own environment's noise. In production, start at 10 and adjust per asset criticality — service accounts with bad password hygiene are your biggest false-positive source.
- **Encoded PowerShell regex (`{80,}`):** catches real obfuscation while ignoring short legitimate `-EncodedCommand` usage. Legit admin scripts get allowlisted by hash.
- **Lateral movement allowlist:** the lab's admin subnet (`192.168.56.0/24`) downgrades to `high`; anything outside it escalates to `critical`. In production, replace with your jump-host inventory.
