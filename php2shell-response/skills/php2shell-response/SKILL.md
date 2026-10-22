---
name: php2shell-response
description: Triage and remediation for the PHP2Shell remote code execution flaws CVE-2026-109618 (RCE via OOM corruption) and CVE-2026-109619 (RCE via stale global state), patched 2026-10-21 in PHP 8.5.12, 8.4.27, 8.3.36 and 8.2.35. Use when asked whether a server, container image, hosting account or codebase is affected by these CVEs, how to patch PHP, which interim mitigations exist, how to verify the fix, or when a PHP runtime is found below a fixed version. Covers inventory of PHP runtimes, SAPI exposure (mod_php, PHP-FPM, FrankenPHP, LSAPI), the settings the advisories name, update routes per platform, post-fix verification, and a compromise check (published indicator, exploitation timeline from 2026-10-17).
license: CC-BY-4.0
metadata:
  author: Iurii Rogulia
  source: https://iurii.rogulia.fi
  version: "1.1.0"
  verified: "2026-10-22"
---

# PHP2Shell response: CVE-2026-109618 and CVE-2026-109619

Two critical PHP core flaws (CVSS v4.0 9.2 each), unauthenticated remote code execution as the web worker account.
Patched 2026-10-21. **Updating PHP is the only complete fix.** Every configuration measure below is partial.

**Exploitation status (2026-10-22).** Attempts in the wild are documented: Amazon saw the first one on **2026-10-17**,
four days before disclosure, and reports broad exploitation since release (`references/advisory-facts.md`,
§Exploitation). So "was this host exposed" starts on 2026-10-17, not on the patch date. Neither CVE was in the CISA
KEV catalog in the sources read.

**Basis.** The two php-src security advisories and the PHP release advisory, read 2026-10-21 (`references/sources.md`).
Vendor posts written before the release disagree with them on some points; the advisories win. Exploitation facts
come from Amazon Threat Intelligence, the UK NCSC and watchTowr. Anything the
advisories do not state is marked **[unverified]**.

**Mode.** Read-only unless the user says otherwise. Diagnose, report, recommend. Do not edit PHP or web server
configuration, restart services, run package upgrades, or deploy without an explicit instruction for that action.

## Where the detail lives

| File | Use it for |
|---|---|
| [references/advisory-facts.md](references/advisory-facts.md) | Affected and fixed versions, mechanics, SAPI reachability, the mitigation list |
| [references/diagnostics.md](references/diagnostics.md) | Commands to find PHP, read the real version and effective settings, audit frontend config and logs |
| [references/remediation.md](references/remediation.md) | Update routes per platform, interim mitigations and their cost, verification |
| [references/sources.md](references/sources.md) | Source list, claims that conflict between sources |

## Rules

1. **Patched means a version, not a date.** Fixed: 8.2.35, 8.3.36, 8.4.27, 8.5.12 (8.6.0 RC4 has the fix). Below these on
   the same branch is affected. Branches 8.1 and older receive no PHP release; the PHP project published source
   backport branches only. A distribution package may carry the fix under an older version number: check the vendor
   changelog or advisory, never compare version strings alone.
2. **Find the runtime that serves requests.** `php -v` reports the CLI binary. The web SAPI (`php-fpm`, `libphp`
   module, FrankenPHP, LSAPI) can be a different build. In containers, check the image that is deployed, not the host.
3. **Updating beats mitigating.** Offer interim measures only when an update cannot happen now, and say each one is
   partial and what it costs (see `references/remediation.md`).
4. **Never use `RLIMIT_AS` or `RLIMIT_DATA`** as a memory control. The advisory says they make `mmap()` fail at the
   limit and trigger the vulnerable path.
5. **Restart is part of every configuration change.** The advisories require restarting all workers. A changed file
   with unrestarted workers has not mitigated anything.
6. **Clean logs prove nothing.** The PHP advisory says clean logs do not prove safety. One published indicator exists
   (rule 9), and it only helps where request bodies are logged. Do not tell the user a host was not compromised
   because logs look normal.
7. **Do not run or fetch public proof-of-concept code.** Fake PoCs carrying payloads were reported. The skill never
   needs exploit code to diagnose.
8. **Report only what was observed.** A version read from a command is a fact. A setting inferred from a config file
   that may be overridden is an assumption; label it.
9. **The one published indicator** (Amazon Threat Intelligence, 2026-10-21): POST requests with multipart form
   uploads containing the string `PHP_SESSION_UPLOAD_PROGRESS`, plus a list of source IPs on shared anonymization
   networks. That string is also the documented name of a legitimate PHP feature, so a hit is a lead to investigate,
   not proof of compromise **[inference: the feature name is public PHP documentation]**. Do not block the IPs on
   that basis alone; Amazon recommends investigating activity involving them.

## Procedure

### 1. Scope
Ask or detect what is in scope: this host, a repository (Dockerfiles, CI, deploy config), a fleet. Note that
the user's own application code is not where the flaw lives; the PHP runtime is.

### 2. Inventory (`references/diagnostics.md` §1–2)
For each runtime record: where it runs, branch, exact version, SAPI, and whether it is reachable from the internet.
In a repository, list every place that pins PHP: `Dockerfile` base images and tags, `docker-compose.yml`, CI
workflows, `composer.json` `require.php` and `config.platform.php`, `.tool-versions`, `.php-version`, buildpack
files, hosting panel notes. Include products that bundle their own PHP (control panels, appliances, CMS hosting
stacks): updating the system PHP does not fix them, so record the vendor's fix status.

### 3. Classify each runtime

| Result | Condition | Action |
|---|---|---|
| Fixed | Branch 8.2–8.5 at or above the fixed version, or vendor changelog names both CVEs | Verify the running process (step 6) |
| Affected | Branch 8.2–8.5 below the fixed version | Update (step 5) |
| Affected, no PHP release | 8.1 or older | Move to a supported branch. PHP published source backports only; vendor packages may exist |
| Unknown | Version not readable, or distribution build with unclear patch level | Check vendor advisory; treat as affected until shown otherwise |

PHP 7.x and 5.x: the GHSAs list affected versions for 8.2–8.5 only. The UK NCSC states "any version of PHP" is
affected, and PHP published backport branches for 7.4, 8.0 and 8.1; Google and Wiz put the range at 5.4 and later
**[secondary, not in the GHSAs]**. Treat as affected.

### 4. Exposure (`references/diagnostics.md` §3–5)
Rank affected runtimes so the update order is defensible:

- **Highest:** Apache `mod_php` and FrankenPHP. The advisory states the OOM flaw is application-independent there:
  shutdown runs on invalid state before any application code.
- **High:** PHP-FPM and php-cgi behind an internet-facing server. Exposure depends on the application and on what the
  frontend passes (`PHP_VALUE`, `PHP_ADMIN_VALUE`).
- **Conditional:** LSAPI.
- **Lower, not safe:** CLI-only, queue workers, cron. They do not serve HTTP requests. Still update.

Then read the settings the advisories name: `session.upload_progress.enabled` (default on), `memory_limit`,
`post_max_size`, `fatal_error_backtraces` on 8.5, `pm.max_requests`, and whether the frontend or `.htaccess` sets
per-request PHP values.

### 5. Plan
Give the update route for the detected platform (`references/remediation.md` §1). If an update cannot happen
immediately, list the interim measures that apply, in the order: reduce what is reachable, apply the partial
settings, restart workers. State plainly that the host remains vulnerable.

### 6. Verify after the change (`references/remediation.md` §3)
Check the version of the process that serves traffic, in the image that is actually deployed, after every worker was
restarted. For a fleet, report per host: before, after, how it was checked.

### 7. Compromise check
For internet-facing runtimes that ran an affected version on or after 2026-10-17. Update first or take the service out
of the path, then look (`references/diagnostics.md` §8): the Amazon indicator in any request-body log, WAF or proxy
capture; unexpected processes run by the web user; new or modified files in the document root; new cron jobs,
scheduled tasks and accounts. If anything is found, stop and recommend an incident response provider (the NCSC advises
one when you lack the skills). Treat credentials the web user could read as exposed. State what could not be
checked, most often request bodies.

### 8. Report
Per runtime: location, SAPI, exact version, classification, exposure, the recommended action, and what was
**not** checked, and the compromise-check result with its limits. Keep unverified statements labelled. Do not call
the result a clean bill of health.

## Finding format

```
Runtime:     <host or image>, <SAPI>
Version:     <exact, how read>
Status:      Fixed | Affected | Affected, no PHP release | Unknown
Exposure:    <internet-facing? SAPI? settings observed>
Action:      <update route or interim measure>
Not checked: <what could not be verified>
Compromise:  Not assessed | Lead found (<what>) | Nothing found in <sources>, not proof of safety
```
