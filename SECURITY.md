# Security Policy

## Supported Versions

| Version | Supported |
|---------|-----------|
| 1.0.x   | Yes       |
| < 1.0   | No        |

Only the latest supported major release line (`vN.x`) is actively supported with security fixes.

## Reporting a Vulnerability

**Do not open a public issue for security vulnerabilities.**

Instead, please report them through GitHub's private security advisory feature:

1. Go to the [Security Advisories page](https://github.com/ggfevans/hardcover-json-bourne/security/advisories)
2. Click **"New draft security advisory"**
3. Fill in the details of the vulnerability

You should receive an initial response within 72 hours. If the vulnerability is confirmed, a fix will be developed privately and released as a patch before the advisory is made public.

## Scope

This action runs as a composite GitHub Action using bash scripts. It makes authenticated HTTP requests to the Hardcover GraphQL API and writes a JSON file to the caller's repository.

Security-relevant areas include:

- **Authentication** -- Bearer token passed via environment variable, never logged or echoed
- **Input validation** -- all action inputs (user_id, limit, token, output_path) are validated before use
- **Path traversal** -- the output path is checked against `GITHUB_WORKSPACE`
- **Command injection** -- inputs are restricted to safe character sets
- **Temporary file handling** -- temp files are scoped per run and cleaned up

## Out of Scope

- Vulnerabilities in the Hardcover API itself (report those to [Hardcover](https://hardcover.app))
- Vulnerabilities in GitHub Actions runner infrastructure
- Issues requiring the caller to have already misconfigured their workflow permissions
