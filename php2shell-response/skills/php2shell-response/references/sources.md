# Sources

Advisories fetched 2026-10-21, exploitation sources 2026-10-22. Primary sources first.

## Primary (decide conflicts)
- PHP security advisories, 2026-10-20 (advance notice) and 2026-10-21 (advisory, release announcements):
  https://www.php.net/archive/2026.php
- php-src GHSA-w8hw-w9fx-j79w, CVE-2026-109618, "RCE via OOM corruption":
  https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w
- php-src GHSA-6wmf-4rv5-p3rp, CVE-2026-109619, "RCE via stale global state":
  https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp
- Amazon Threat Intelligence, 2026-10-21 (exploitation, the one published indicator and IPs):
  https://aws.amazon.com/blogs/security/amazon-threat-intelligence-identifies-php2shell-exploitation-prior-to-public-disclosure-in-madpot/
- UK NCSC, published 2026-10-21 (guidance: inventory, patch, mitigate, take offline, check for compromise):
  https://www.ncsc.gov.uk/news/critical-security-issue-affecting-php

## Vendor and secondary (context; checked against the primary sources where possible)
- watchTowr FAQ (exploitation observed in its honeypots, no detail; internally inconsistent about patch status):
  https://watchtowr.com/intelligence/php-cve-2026-109618-faq/
- Wiz (affected range from PHP 5.4): https://www.wiz.io/blog/php2shell-php-rce (cited secondhand)
- Google Cloud: https://cloud.google.com/blog/products/identity-security/responding-to-core-php-vulnerabilities-cve-2026-109618-cve-2026-109619
- JFrog Security Research: https://research.jfrog.com/post/php2shell-all-you-need-to-know-about-cve-2026-109618-and-cve-2026-109619/
- Tenable FAQ (written before the release): https://www.tenable.com/blog/php2shell-cve-2026-109618-cve-2026-109619-frequently-asked-questions-about-critical-php-remote
- Sophos: https://www.sophos.com/en-us/blog/proof-of-concept-exploit-released-for-php-vulnerabilities-109618-109619
- CISA and Huntress observation statements were read only as summaries of news coverage, not at the sources.
- Not fetched: the NSA press release (HTTP 403), the PHP Foundation's mitigation post, Cloudflare's own statements.

## Conflicts between sources
- **Do INI settings help?** Tenable, citing a PHP release manager before the release, says disabling session upload
  progress is not a fix. The PHP advisories list it as a partial measure for the confirmed session paths and say
  updating is the only complete solution. Amazon agrees that no configuration-only workaround covers both.
- **Affected versions.** The GHSAs list 8.2–8.5. The NCSC says any PHP version; Google and Wiz say 5.4 and later;
  watchTowr says PHP 5 onward and flags that no vendor advisory confirms it; PHP published backport branches for
  7.4, 8.0 and 8.1. The skill treats older versions as affected.
- **CVSS.** The PHP advisories say 9.2 (CVSS v4.0). Some early reports said 10.0; ignore them.
- **Is it being exploited?** Settled for "attempts in the wild": Amazon (first on 2026-10-17, more on 2026-10-21) and
  watchTowr both report it. Unsettled: how many attempts would succeed (Amazon cannot yet say), and whether
  large-scale compromise has happened. Huntress and CISA reported no observed exploitation on 2026-10-21 per news
  summaries. Neither CVE was in CISA KEV in the sources read.
- **Proof-of-concept code.** Amazon says multiple PoCs circulated within hours of release. JFrog reported fake PoCs
  carrying payloads. The skill never needs exploit code.

## Staleness
Verified 2026-10-22. Exploitation status, the CISA KEV listing, published indicators, distribution package versions and
bundled-product fixes change daily; re-check them before relying on this file.
