# CIS-Benchmark-Compliance-Assessment-Windows-Server-2022
Assessment and hardening of Windows Server 2022 Account Policies against CIS Benchmark v3.0.0, closing the exact weak-password-policy gap exploited in the <a href="https://github.com/Harisankar-01/SOC-Lab-Enterprise-Monitoring/tree/main/Incidents/02-rdp-bruteforce">SOC Lab brute-force scenario</a>

* **Target:** Windows Server 2022 (Local Security Policy / secpol.msc)
* **Benchmark:** CIS Microsoft Windows Server 2022 Benchmark v3.0.0
* **Scope:** Account Policies — Password Policy & Account Lockout Policy

---

## Why This Project Matters

In the SOC lab project, a weak account lockout policy allowed an RDP brute-force attack to succeed with only 6 password attempts, no lockout ever triggered, and there was no limit on login attempts. This project addresses that root cause directly: evaluating the server's default security configuration against an industry-standard benchmark and hardening it so the same attack would be stopped at the policy level, not just detected after the fact.

---

## Password Policy

| Security Policy | Default | CIS Recommended | Final | Compliance |
|---|---|---|---|---|
| Enforce Password History | 0 remembered | 24 remembered | 24 remembered | Compliant |
| Maximum Password Age | 42 days | 365 days | 365 days | Compliant |
| Minimum Password Age | 0 days | 2 days | 2 days | Compliant |
| Minimum Password Length | 0 characters | 14 characters | 14 characters | Compliant |
| Minimum Password Length Audit | Not Defined | 14 characters | 14 characters | Compliant |
| Password Complexity | Enabled | Enabled | Enabled | Compliant |
| Relax Minimum Password Length Limits | Not Defined | Enabled | Enabled | Compliant |
| Store Passwords Using Reversible Encryption | — | Disabled | Disabled | Compliant |

**Assessment:** The default configuration allowed unlimited password reuse, no minimum length, and no minimum password age, conditions that make weak and reused passwords likely. Bringing this in line with CIS significantly raises the bar for credential-based attacks.

---

## Account Lockout Policy

| Security Policy | Default | CIS Recommended | Final | Compliance |
|---|---|---|---|---|
| Account Lockout Threshold | 0 invalid attempts | 5 invalid attempts | 5 invalid attempts | Compliant |
| Account Lockout Duration | Not Applicable | 15 minutes | 15 minutes | Compliant |
| Reset Account Lockout Counter After | Not Applicable | 15 minutes | 15 minutes | Compliant |

**Assessment:** By default, the server permitted unlimited login attempts, no lockout, no threshold, meaning an attacker could brute-force credentials indefinitely without triggering any defensive response. This is the exact condition that allowed the SOC lab brute-force scenario to succeed.

---

## Changes Implemented

**Password Policy**
- Password history set to remember the previous 24 passwords
- Maximum password age increased from 42 to 365 days
- Minimum password age set to 2 days
- Minimum password length increased from 0 to 14 characters
- Minimum password length audit value set to 14 characters
- Relax Minimum Password Length Limits enabled
- Verified Password Complexity remained enabled
- Verified reversible encryption remained disabled

**Account Lockout Policy**
- Account lockout threshold configured to 5 invalid attempts
- Account lockout duration configured to 15 minutes
- Reset account lockout counter configured to 15 minutes

---

## Challenges Encountered

- Interpreting CIS Benchmark documentation required careful reading, each recommendation comes with technical description, implementation guidance, and rationale that needed to be mapped to the correct Local Security Policy setting
- Determining which settings were already compliant (e.g., password complexity) versus which required significant change took a careful side-by-side comparison against the benchmark
- Each setting was verified post-implementation to confirm the change was applied correctly and no misconfiguration was introduced

---

## Evidence

Before/after screenshots of both policies (Local Security Policy editor) are included in [`screenshots/`](screenshots/):
- Password policy — before and after configuration
- Account lockout policy — before and after configuration

---

## Conclusion

This assessment evaluated and hardened the Password Policy and Account Lockout Policy of Windows Server 2022 against CIS Benchmark v3.0.0. The resulting configuration prevents password reuse, enforces stronger password requirements, and protects against brute-force attacks through automatic account lockout. Combined with the SOC lab project, this demonstrates the full defense-in-depth story: a weak policy enabling compromise, and a properly hardened policy closing that gap at the source rather than relying on detection alone.
