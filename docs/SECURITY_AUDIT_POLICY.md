# Security Audit Policy

This document describes the automated security scanning policy for the alvinmunk project.

## Automated Checks

Security scans run automatically on:
- **Every PR and push** to ensure new code doesn't introduce vulnerabilities
- **Weekly schedule** (Mondays at 9:00 UTC) to catch newly published advisories

## Rust Contract Dependencies

### cargo-audit (RustSec Advisories)
Scans `contracts/Cargo.lock` against the [RustSec Advisory Database](https://rustsec.org/).

**Current threshold:** Fails on security vulnerabilities  
**Warnings allowed:** Unmaintained crates and yanked versions (reviewed manually)

### cargo-deny
Comprehensive policy enforcement covering:
- **Advisories:** Security vulnerabilities (deny), unmaintained crates (warn), yanked versions (deny)
- **Licenses:** Only permissive licenses allowed (MIT, Apache-2.0, BSD, Unicode-3.0, etc.)
- **Bans:** Prevents banned crates and warns on duplicate versions
- **Sources:** Restricts crates to crates.io registry

Configuration: `contracts/deny.toml`

## JavaScript/TypeScript Dependencies

### pnpm audit
Scans production npm dependencies for known vulnerabilities.

**Current threshold:** `--audit-level=critical`  
- Fails CI on critical severity advisories in production dependencies
- Lower severity issues are visible but don't fail the build

**Planned escalation:** After pending dependency upgrades land, tighten to `--audit-level=high`

## Response Process

### When CI Fails

1. **Investigate the advisory:**
   - Review the CVE/advisory details
   - Assess impact on alvinmunk (does it affect our usage?)
   - Check for available patches or workarounds

2. **Remediate:**
   - **Preferred:** Update to patched version
   - **If no patch:** Document in `contracts/deny.toml` ignore list with reason
   - **For npm:** Update dependencies or temporarily suppress with documented justification

3. **Document:** Note the decision in git commit message and any relevant tracking issues

### Weekly Scan Results

Review the weekly scheduled scan results even if passing:
- New warnings may indicate dependencies requiring attention
- Plan updates for unmaintained dependencies

## Manual Security Review

Automated scans complement but don't replace:
- Code review focused on security
- The full security review documented in `docs/SECURITY_REVIEW.md`
- Pre-mainnet professional audit (see `docs/DEPLOY_MAINNET.md`)

## Policy Updates

This policy may be updated as the project matures:
- Tightening audit levels as dependencies stabilize
- Adding additional scanning tools
- Adjusting response procedures based on experience

Last updated: 2026-09-29
