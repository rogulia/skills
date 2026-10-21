# Sources

Fetched 2026-10-21, the day of the release. Primary sources first.

## Primary (decide conflicts)
- PHP security advisory, 2026-10-21 (php.net home page announcement): CVEs, fixed releases, backport branches,
  workaround list.
- php-src GHSA-w8hw-w9fx-j79w, CVE-2026-109618, "RCE via OOM corruption":
  https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w
- php-src GHSA-6wmf-4rv5-p3rp, CVE-2026-109619, "RCE via stale global state":
  https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp

## Secondary (context, checked against the primary sources)
- Google Cloud: https://cloud.google.com/blog/products/identity-security/responding-to-core-php-vulnerabilities-cve-2026-109618-cve-2026-109619
  (states PHP 5.4 and later affected; platform patch routes; mentions Google Cloud bulletin GCP-2026-070).
- JFrog Security Research: https://research.jfrog.com/post/php2shell-all-you-need-to-know-about-cve-2026-109618-and-cve-2026-109619/
- Tenable FAQ: https://www.tenable.com/blog/php2shell-cve-2026-109618-cve-2026-109619-frequently-asked-questions-about-critical-php-remote
  (written before the release).
- Sophos: https://www.sophos.com/en-us/blog/proof-of-concept-exploit-released-for-php-vulnerabilities-109618-109619
  (interim network measures; exploitation not reported at publication).

## Conflicts between sources
- **Do INI settings help?** Tenable, citing a PHP release manager before the release, says disabling session
  upload progress is not a fix. The PHP advisories list it as a partial measure for the confirmed session paths and
  say updating is the only complete solution. Both agree on the conclusion that matters: update.
- **Affected versions.** The advisories list 8.2–8.5. Google and CERT-EU say all versions from 5.4 or 5 are affected;
  the PHP project published backport branches for 7.4, 8.0 and 8.1. The skill treats older versions as affected.
- **CVSS.** The PHP advisories say 9.2 (CVSS v4.0). Some early reports said 10.0; ignore them.
- **Exploitation status.** At the time of the sources, active exploitation was not reported by the vendors read
  here, and public proof-of-concept claims were unverified; fake PoCs were reported. Check the current state before
  asserting either way.

## Staleness
Verified 2026-10-21. Exploitation status, distribution package versions and published indicators change fastest;
re-check them before relying on this file.
