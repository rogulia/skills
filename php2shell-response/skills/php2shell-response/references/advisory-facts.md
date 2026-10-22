# Advisory facts

Read 2026-10-21 (GHSAs, release advisory) and 2026-10-22 (exploitation sources). Source: php-src advisories GHSA-w8hw-w9fx-j79w (CVE-2026-109618) and GHSA-6wmf-4rv5-p3rp (CVE-2026-109619),
and the PHP release advisory, all published 2026-10-21. Anything marked **[unverified]** is not stated there.

## Versions

| Branch | Affected | Fixed |
|---|---|---|
| 8.5 | < 8.5.12 | 8.5.12 |
| 8.4 | < 8.4.27 | 8.4.27 |
| 8.3 | < 8.3.36 | 8.3.36 |
| 8.2 | < 8.2.35 | 8.2.35 (branch EOL 2026-12-31) |
| 8.6 | pre-release | 8.6.0 RC4 includes the fixes |
| 8.1, 8.0, 7.4 | no PHP release | source backport branches `PHP-<ver>-CVE-2026-109618-109619` in php-src |

- The GHSAs list affected ranges for 8.2–8.5 only. The UK NCSC says "organisations using any version of PHP are
  affected". Google Cloud and Wiz put the range at PHP 5.4 and later; watchTowr says PHP 5 onward and notes this is not
  confirmed by an official advisory. No PHP 5 patch exists. None of these is in the GHSAs.
- Linux distributions and LTS vendors received the patches in advance. Their packages may be fixed under an older
  version string.
- Both CVEs: CVSS v4.0 9.2, vector `AV:N/AC:H/AT:P/PR:N/UI:N`, high impact on confidentiality, integrity,
  availability. Attack complexity is rated High and attack requirements Present.

## Exploitation (as of 2026-10-22)

- **Amazon Threat Intelligence** (AWS Security Blog, 2026-10-21): first attempt against its MadPot honeypots on
  **2026-10-17** from a commercial anonymization network, four days before disclosure. More attempts from about
  02:00 UTC on 2026-10-21 (before the 14:00 UTC release), through 08:00, from other anonymized infrastructure; more
  proof-of-concept exploits appeared within hours of the release and were used against the sensors almost at once.
  Payloads and infrastructure differ, which suggests several actors. Amazon cannot yet say whether the attempts would
  have succeeded against real systems, and says the flaws are now broadly exploited.
- **watchTowr** reports exploitation in its honeypot network as of 2026-10-21, with no detail of what was seen. Its FAQ
  is internally inconsistent about whether a vendor patch existed when it was written.
- **CISA:** reported (NBC News, via secondary summary) as not having observed exploitation on 2026-10-21. Neither
  CVE was in the CISA KEV catalog in the sources read. **[secondary]**
- **Huntress:** had not observed exploitation on its partner systems as of 16:30 ET on 2026-10-21 **[secondary,
  search summary]**.
- **Published indicator (Amazon):** POST requests with form uploads containing the string
  `PHP_SESSION_UPLOAD_PROGRESS`. Source IPs, shared anonymization infrastructure: 103.216.220.92 (2026-10-17),
  31.223.184.166, 37.19.200.5, 134.209.159.64, 103.114.163.188, 95.217.98.37 (all 2026-10-21). Amazon advises
  investigating activity involving them rather than blocking them outright, and starting investigations from at least
  2026-10-17. No file hashes or paths were published. The string is the documented name of PHP's session upload
  progress feature, so it is not specific to exploitation **[inference]**.
- PHP's advance notice (2026-10-20) reserved the right to release early if details became public or exploitation
  appeared. Details leaked before the release.

## CVE-2026-109618: RCE via OOM corruption

PHP handles out-of-memory with `longjmp()`, which leaves the current context and aborts the request. In some contexts
request memory is reachable before it is fully initialized. If an allocation fails there, request memory stays
invalid, and request shutdown later reads or branches on it. The failed allocation need not relate to that memory.

OOM strategies the advisory lists:
1. A multipart request without a usable declared content length passes the initial `post_max_size` check and keeps
   supplying body data.
2. A multipart part with an unlimited number of headers: the PHP representation can use far more memory than the
   wire form, so a request within `post_max_size` can exhaust `memory_limit`.
3. Empty file controls do not count toward `max_file_uploads` but still create file metadata arrays; nested field
   names multiply the work.
4. Application-specific: attacker-controlled input causing a large allocation or repeated memory use.

The patch removes `longjmp()` for OOM. A soft OOM stops the request at the next VM safe point; a hard OOM calls
`_exit()` at a second, higher threshold.

## CVE-2026-109619: RCE via stale global state

PHP keeps error, session, object and extension state in globals of a long-lived worker. That state can point into
memory owned by one request and must be reset before the request heap is freed. Two things defeat the reset: a
fatal error (including OOM and "Maximum execution time exceeded") that stops the operation before the reset runs,
and a non-fatal diagnostic that repopulates the state after shutdown cleared it. A later request on the **same
worker** then uses the dangling pointer in an operation that runs before normal request initialization.

Consumers the advisory names:
- **Diagnostics emitted while INI values are processed before request initialization.** The diagnostic need not be
  displayed, logged or fatal; `error_reporting`, `@`, `display_errors` and `log_errors` do not prevent it. Examples
  listed: `auto_detect_line_endings=1`, non-default `filter.default`, legacy `assert.*` (8.3+), legacy session
  settings (8.4+), `report_memleaks=0` or nonzero `intl.error_level` (8.5); explicit `mbstring.http_input`,
  `mbstring.http_output`, `mbstring.internal_encoding`, `iconv.input_encoding`, `iconv.output_encoding`,
  `iconv.internal_encoding`; invalid or conflicting `zend.assertions`, `memory_limit`, `date.timezone`, and DBA, EXIF,
  mail, OpenSSL, zlib and extension values; malformed or overflowing values for memory limit, body size, input,
  timeout, stack and extension settings (8.2+); log, mail, session or SOAP cache paths rejected under `open_basedir`.
- **Session upload progress handling**, which can run before ordinary session initialization and consume state left
  by an interrupted request. It needs no configuration diagnostic and no `session_start()`. Enabled by default.

Confirmed cases: PHP 8.5 fatal error backtraces with `fatal_error_backtraces` enabled (strongest case; basis of the
confirmed mod_php exploitation), and the session upload progress lifecycle (RCE validated against mod_php and FPM).
Other error, exception, object and extension state is conditional: impact is established only once a concrete
producer and consumer are shown.

Scope statement in the release advisory: "all PHP installations serving web requests, including PHP-FPM, Apache with
mod_php, php-cgi, FrankenPHP, and LiteSpeed", risk highest for mod_php.

## SAPI reachability

| SAPI | CVE-2026-109618 | CVE-2026-109619 |
|---|---|---|
| Apache `mod_php` | Application-independent. Shutdown runs even when startup failed; body parsing at startup can exhaust `memory_limit`. Full RCE validated over HTTP with default limits and normal prefork | `.htaccess` and Apache `php_value`, `php_flag`, `php_admin_*` apply before `php_request_startup()` and act as an early consumer. Normal `php.ini` is not reapplied per request |
| PHP-FPM | The child exits when startup fails, which blocks the direct path. After normal startup OOM can still reach the flaw; source analysis supports it, no complete FPM exploit demonstrated | Exposed if the frontend (nginx, Apache `mod_proxy_fcgi`) sends `PHP_VALUE` or `PHP_ADMIN_VALUE` that trigger an early diagnostic. Pool values apply at worker start and `.user.ini` after initialization, so neither is the same early consumer. Session lifecycle RCE validated against FPM with a normal dynamic pool |
| FrankenPHP | Source analysis: calls shutdown unconditionally like mod_php, likely susceptible | No equivalent early consumer in the default configuration; not a statement of immunity |
| LSAPI | Modeled on FPM; application-specific exploits likely, unvalidated | Can apply special environment INI values early; conditional candidate |
| php-cgi | Listed as affected by the release advisory; **[unverified]** no detail in the two GHSAs | same |

Exploitation needs the producer and consumer requests to reach the same worker. More workers and worker recycling
lower the success rate per attempt but do not remove the condition. Reliability depends on the PHP build, extensions,
frontend behavior and allocator layout.

## Mitigation list in the advisories

All partial. "Updating is the only complete mitigation."

For CVE-2026-109618:
- `memory_limit=-1`, enforced administratively (FPM pool `php_admin_value`, Apache `php_admin_value`), and verify no
  pool, directory or application setting restores a finite limit. Not complete: if `mmap()` or another allocation
  fails, `longjmp()` can still run.
- Conservative `post_max_size`; enforce decoded request size at the frontend, including for requests without a
  usable `Content-Length`. The release advisory gives `post_max_size = 8M` when no uploads are needed and
  `32M` with `upload_max_filesize` below it when uploads are needed.
- Lower `max_input_nesting_level`; bound input variables, multipart parts, headers per part and uploads.
- Block multipart requests at the frontend if the application does not use them.
- Container or cgroup `memory.max` as an availability boundary, ideally per worker; it is not complete and killing a
  container can kill the master and all workers.
- Do not use `RLIMIT_AS` or `RLIMIT_DATA`.

For CVE-2026-109619:
- `session.upload_progress.enabled=0` administratively, restart every worker. Not `ini_set()`, not `.user.ini`.
  Blocks the confirmed session paths, not other variants. The advisory suggests keeping it off after patching if the
  feature is unused.
- PHP 8.5: `fatal_error_backtraces=0`, restart every worker. Removes the confirmed fatal-backtrace producer only.
- Remove invalid and deprecated per-request settings that emit diagnostics before initialization. Prefer startup
  configuration over per-request overrides.
- Remove fatal errors on attacker-reachable paths (upload, import, error page, shutdown, destructor, serialization,
  output handler, plugin, extension) and attacker-triggerable "Maximum execution time exceeded".
- `pm.max_requests=1` if performance allows (breaks the same-worker condition at a performance cost).
