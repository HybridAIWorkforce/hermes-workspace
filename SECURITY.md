# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in Hermes Workspace, please report it responsibly.

**Do not open a public GitHub issue for security vulnerabilities.** Public issues are for non-sensitive bugs only.

Preferred disclosure channels:

1. [GitHub Private Vulnerability Reporting / Security Advisories](https://github.com/outsourc-e/hermes-workspace/security/advisories/new)
2. Email: [security@buildingthefuture.io](mailto:security@buildingthefuture.io)

If GitHub reports that private vulnerability reporting is disabled, use the email address above and include enough detail for maintainers to reproduce the issue safely.

Maintainer action required: GitHub private vulnerability reporting is controlled in repository settings under **Settings → Code security and analysis → Private vulnerability reporting**. That setting must be enabled by a repository administrator; this policy file provides the public fallback channel but does not change the repository setting.

Please include:

- affected version, commit, or deployment mode
- reproduction steps or a proof-of-concept, if safe to share
- expected impact and affected data/systems
- any suggested remediation or mitigation

We will acknowledge your report within 48 hours and aim to provide a fix within 7 days for critical issues.

## Scope

- Hermes Workspace web application code
- API routes and Hermes communication
- Authentication and session management
- Client-side data handling and rendering
- Exec approval and human-in-the-loop controls
- Browser proxy and screenshot endpoints

## Out of Scope

- Hermes Agent itself (report to the Hermes Agent project)
- Third-party dependencies (report to the respective maintainer)
- Social engineering attacks

## Security Measures (v3.0.0+)

**Authentication**

- All API routes require authentication as of v3.0.0
- Session tokens use timing-safe comparison to prevent timing attacks
- httpOnly + SameSite=Strict cookies
- Token revocation on logout

**Network**

- `Access-Control-Allow-Origin` restricted to localhost — no wildcard CORS
- Browser proxy and screenshot endpoints locked to same-origin only
- Rate limiting on high-risk endpoints (file access, debug, exec)

**Data & File Access**

- Path traversal prevention on all file and memory routes (`ensureWorkspacePath()`)
- `.md`-only restriction on memory write routes
- No API keys or secrets ever exposed to client-side code
- Hermes tokens are server-side only
- Diagnostic output scrubbed of sensitive data

**Agent Safety**

- Exec approval workflow — sensitive Hermes exec commands require explicit human approval via in-UI modal
- Skills security scanning — every skill from the marketplace is scanned for suspicious patterns before install

**Configuration**

- Environment files are gitignored
- Config endpoints redact credentials in responses
- Example configs use placeholder keys only

## Security Audit Passes

### SEC-3 (2026-02-25)

- Completed full API audit for auth coverage; no new private route auth gaps found (`/api/config-get`, `/api/debug-analyze`, `/api/context-usage`, `/api/paths` verified authenticated).
- Added CSRF content-type enforcement (`requireJsonContentType`) to remaining POST handlers, including auth and terminal management endpoints.
- Tightened rate limiting to 10 requests/minute per IP (sliding window) on high-risk endpoints:
  - `/api/terminal-input`
  - `/api/terminal-stream`
  - `/api/restart`
  - `/api/update-check` (POST)
  - `/api/update-check` (POST)

## Supported Versions

| Version     | Supported              |
| ----------- | ---------------------- |
| v3.x (main) | ✅ Active              |
| v2.x        | ⚠️ Security fixes only |
| < v2.0      | ❌ Unsupported         |
