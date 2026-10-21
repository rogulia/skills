# Remediation

Recommend, do not execute, unless the user instructs a specific action. Fixed versions: 8.2.35, 8.3.36, 8.4.27,
8.5.12 (8.6.0 RC4).

## 1. Update routes

Pick by what the diagnostics found. Stay on the same minor branch unless the branch is out of support: a patch
update within a branch is the lowest-risk change.

| Platform | Route |
|---|---|
| Debian / Ubuntu packages | `apt update`, then upgrade the PHP packages; confirm the changelog names CVE-2026-109618/109619. If the distribution has not published a fix, say so and apply the interim measures |
| Third-party repository (Sury, Remi, Ondrej PPA) | Upgrade from that repository; check its changelog for the fixed upstream version |
| RHEL family | `dnf upgrade` the `php*` packages, or the module stream; check `rpm -q --changelog` |
| Alpine | Rebuild the image on a base that ships a fixed `php` package; verify with `apk info` |
| Official Docker image | Rebuild on a fixed tag (for example `php:8.4.27-fpm`); `docker pull` alone does not change a running container. Rebuild, redeploy, check the running container |
| Self-built / source | Download a fixed release from php.net; rebuild with the original configure flags |
| Managed platform (Cloud Run, App Engine, GKE, Heroku-style, PaaS) | Follow the platform's runtime update note. Some patch automatically; many need a rebuild and redeploy of your image |
| Coolify / Nixpacks / buildpacks | The PHP version comes from the build configuration or base image. Change the pinned version, redeploy with a fresh build (no cached layer holding the old PHP), then verify in the running container |
| Shared hosting / cPanel | Ask the host for the fixed version or switch the account to a fixed PHP version in the panel; verify with a one-time `phpversion()` check that is removed afterwards |
| PHP 8.1 and older | Move to a supported branch (8.2 itself reaches end of life on 2026-12-31). Source backports exist in php-src branches `PHP-8.1-`, `PHP-8.0-`, `PHP-7.4-CVE-2026-109618-109619` for builds you maintain yourself |

Major or minor branch changes carry application risk: run the test suite and check deprecations before switching.
Do not bundle an unrelated PHP upgrade into an emergency patch if a same-branch fix is available.

Update order: internet-facing `mod_php` and FrankenPHP, then internet-facing FPM/cgi, then LSAPI, then internal web
services, then CLI-only runtimes. Rebuild every container image that contains PHP; images that nobody redeploys keep
the old version.

## 2. If the update cannot happen now

State first that the runtime stays vulnerable. Apply what the advisories list, restart **every** worker after each
change, and verify the setting took effect in the running SAPI.

| Measure | Covers | Cost / caveat |
|---|---|---|
| `session.upload_progress.enabled=0` in `php.ini`, FPM pool (`php_admin_value`) or server config before request parsing | The confirmed session upload-progress paths of CVE-2026-109619 | Breaks upload progress bars that read session data. Not `ini_set()`, not `.user.ini` |
| `fatal_error_backtraces=0` (8.5 only) | The confirmed fatal-backtrace producer | Lose backtraces in fatal errors. Other state families remain |
| `memory_limit=-1` set with `php_admin_value`; confirm no pool, directory or app setting restores a limit | The main configuration mitigation for CVE-2026-109618 | Removes PHP's own memory protection: a runaway request can exhaust host memory. Pair it with a per-worker cgroup limit that kills only the worker, and request-size limits at the frontend. Not complete |
| Low `post_max_size` (for example 8M with no uploads), `upload_max_filesize` below it | Shrinks the OOM surface | May reject legitimate large requests |
| Frontend limits on decoded request size, field count, multipart parts, headers per part, nesting depth, including requests without `Content-Length` | CVE-2026-109618 OOM strategies | Must be enforced at the proxy/web server; PHP's own checks are the part that is bypassable |
| Block `multipart/form-data` at the frontend if the application does not use it | CVE-2026-109618 | None if truly unused |
| `pm.max_requests=1` | Breaks the same-worker condition | Large performance cost; worker per request |
| Remove obsolete per-request ini values and `PHP_VALUE`/`PHP_ADMIN_VALUE` frontend parameters | Early-diagnostic consumers of CVE-2026-109619 | Check what the application relies on first |
| Fix attacker-reachable fatal errors and timeouts | Producers of CVE-2026-109619 | Application work; not a substitute for the update |
| Take the service offline, or put it behind authenticated network access (VPN, ZTNA) | Removes unauthenticated reachability | Availability cost. Listed by Sophos and CERT-EU as interim measures, not in the PHP advisories |
| WAF rules (for example Cloudflare's) | Known exploit patterns only | A stopgap: traffic that bypasses the WAF is unprotected. Vendor claim, **[unverified]** |

Never: `RLIMIT_AS`, `RLIMIT_DATA`. No "fix" through `error_reporting`, `display_errors`, `log_errors` or `@`: the
advisory says they do not prevent the early diagnostic.

After the update, the advisory suggests keeping `session.upload_progress.enabled=0` if the feature is unused.

## 3. Verify

1. **Restart.** Every PHP worker and master process restarted after the update and after each config change:
   `systemctl restart php8.x-fpm` / the web server for `mod_php` / redeploy of the container. Use a full restart rather than a reload for `mod_php`;
   a reload replacing the loaded module is not something this skill verified.
2. **Version of the serving process.** Check the binary that the service uses (`php-fpm -v` from the unit's
   `ExecStart` path, `docker exec <container> php -v`, the module package version) and the process start time
   (`ps -o lstart= -p <pid>`) is after the update.
3. **Image provenance.** For containers: the running container's image ID equals the freshly built image, and that
   image's `php -v` shows a fixed version. A stale layer cache or a pinned digest keeps the old PHP.
4. **Settings in effect.** Re-run `references/diagnostics.md` §4–5 against the running SAPI if mitigations were applied.
5. **Distribution builds.** For a package with an older version string, the evidence is the vendor changelog or
   advisory naming both CVEs, not the string.
6. **Record the result** in the finding format of `SKILL.md`, including what was not checked.

If a host was reachable from the internet while vulnerable and the user wants to know if it was attacked, follow
`references/diagnostics.md` §8: the answer is "cannot be determined from published indicators".
