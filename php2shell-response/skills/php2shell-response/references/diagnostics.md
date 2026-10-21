# Diagnostics

Read-only commands. Adapt paths to the distribution. Do not install, restart or edit anything while diagnosing.

## 1. Find PHP

Host:
```bash
command -v php php-fpm php-cgi frankenphp lsphp 2>/dev/null
ls /usr/sbin/php-fpm* /usr/bin/php* /opt/remi/*/root/usr/bin/php* /usr/local/bin/php* 2>/dev/null
ps -eo pid,user,comm,args | grep -E 'php|frankenphp|lsphp|httpd|apache2' | grep -v grep
```
Apache module:
```bash
apachectl -M 2>/dev/null | grep -i php        # or: apache2ctl / httpd -M
ls /etc/apache2/mods-enabled/ 2>/dev/null | grep php
```
Containers (the deployed image, not the host):
```bash
docker ps --format '{{.Names}}\t{{.Image}}'
docker exec <container> php -v
docker run --rm --entrypoint php <image> -v
```
Repository: grep for every place PHP is pinned.
```bash
grep -rniE 'php[:@=-]?[ ]?[0-9]+\.[0-9]+|FROM .*php|php-fpm|frankenphp' Dockerfile* docker-compose*.yml .github .gitlab-ci.yml nixpacks.toml .tool-versions .php-version 2>/dev/null
grep -nE '"php"|"platform"' composer.json
```
`composer.json` states what the code needs, not what runs in production.

## 2. Read the real version

```bash
php -v                         # CLI binary
php-fpm -v                     # FPM binary, can differ from the CLI
php-cgi -v
frankenphp version
php -r 'echo PHP_VERSION, PHP_EOL;'
```
For `mod_php`, the version is the module in use: `php -v` of the same install, or the `Server`/`X-Powered-By` header
**[unreliable: often hidden]**. When the binary and the loaded module may differ, check the package that owns the
module file.

Distribution packages: the version string may stay old after a fix. Check the changelog for the CVE ids.
```bash
# Debian / Ubuntu
apt-cache policy php8.3-fpm libapache2-mod-php8.3 ; apt changelog php8.3 2>/dev/null | grep -nE 'CVE-2026-10961[89]'
# RHEL family
rpm -q php-fpm php ; rpm -q --changelog php-fpm | grep -nE 'CVE-2026-10961[89]'
# Alpine
apk info -v | grep -E '^php'
```
The vendor security tracker is the authority; the commands above can come up empty before the vendor publishes.

## 3. SAPI and process model

```bash
php-fpm -tt 2>&1                # dumps the parsed FPM config: pools, pm settings, php_admin_value lines
grep -rn 'pm.max_requests\|^pm *=' /etc/php* /usr/local/etc/php-fpm.d 2>/dev/null
```
`php-fpm -tt` parses and prints the configuration; it is a test mode and does not restart the service.

## 4. Settings the advisories name

Effective ini for the binary being checked (CLI values are not FPM values):
```bash
php -i | grep -E '^(memory_limit|post_max_size|upload_max_filesize|max_input_nesting_level|max_input_vars|max_file_uploads|session.upload_progress.enabled|fatal_error_backtraces|zend.assertions|auto_detect_line_endings|filter.default|report_memleaks|intl.error_level) '
php-fpm -i 2>/dev/null | grep -E '^(memory_limit|session.upload_progress.enabled|fatal_error_backtraces) '
php --ini                      # which files were loaded
```
Where values can be overridden (a file value is only an assumption until the layers are checked):
```bash
grep -rnE 'php_(admin_)?(value|flag)\[' /etc/php* /usr/local/etc/php-fpm.d 2>/dev/null   # FPM pool
find / -name '.user.ini' -not -path '*/node_modules/*' 2>/dev/null | head
```

Legacy and diagnostic-prone directives named by the advisory:
```bash
grep -rniE '^\s*(auto_detect_line_endings|filter\.default|assert\.|mbstring\.(http_input|http_output|internal_encoding)|iconv\.(input_encoding|output_encoding|internal_encoding)|report_memleaks|intl\.error_level)' /etc/php* /usr/local/etc/php 2>/dev/null
```
Anything it prints is a candidate for an early diagnostic; the advisory says to remove obsolete directives.

## 5. Per-request PHP values from the frontend

```bash
grep -rnE 'PHP_VALUE|PHP_ADMIN_VALUE' /etc/nginx /etc/apache2 /etc/httpd /etc/caddy 2>/dev/null
grep -rnE 'php_(admin_)?(value|flag)' /etc/apache2 /etc/httpd 2>/dev/null
find <docroot> -name .htaccess -print0 | xargs -0 grep -nE 'php_(admin_)?(value|flag)' 2>/dev/null
```
These apply before request initialization and are the early consumer the CVE-2026-109619 advisory describes for
`mod_php` and FPM.

Request-size controls at the frontend (relevant to CVE-2026-109618):
```bash
grep -rnE 'client_max_body_size|LimitRequestBody|request_body|max_request_body' /etc/nginx /etc/apache2 /etc/httpd 2>/dev/null
```

## 6. Is the application reachable and does it use multipart?

- Reachability: is the service behind a public listener, a reverse proxy, a CDN, or private only? Say which was
  observed and which was assumed.
- Multipart: look for upload forms, `$_FILES`, `multipart/form-data` clients, upload endpoints. If the application
  has none, the advisory suggests blocking multipart at the frontend as a mitigation.

## 7. Logs (supplementary, proves nothing)

The advisory's audit: in an isolated environment enable `log_errors=On`, a working `error_log`, and
`error_reporting=E_ALL` in startup configuration, then look for:
- warnings, deprecations and core errors logged before the target script runs;
- diagnostics from extension or server cleanup;
- fatal, parse and uncaught error messages, and "Maximum execution time exceeded";
- failures tied to attacker-controlled routes, content types, files, callbacks or plugin options.

```bash
grep -hE 'Maximum execution time|Allowed memory size|Out of memory|PHP Fatal|PHP Parse|PHP Deprecated' /var/log/php*/*.log /var/log/apache2/error.log /var/log/nginx/error.log 2>/dev/null | tail -100
```
Repeated fatal or out-of-memory entries from external addresses are worth reporting. Their absence does not show
the host is clean. Historical logs may exclude relevant severities and some failures occur before PHP writes a
message.

## 8. After a suspected exploitation attempt

No indicators of compromise were published with the advisories **[unverified: none found as of 2026-10-21]**. If
the user suspects exploitation, do not claim to detect it; recommend preserving logs and process listings, checking
recently modified files in the document root and writable paths, unexpected child processes of the web user, and
unusual outbound connections, and treat credentials the web user could read as exposed. State that this is generic
incident-response practice, not a vendor indicator list.
