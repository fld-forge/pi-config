# Security Policy

## Supported versions

Only the latest [GitHub release](https://github.com/fld-forge/pi-config/releases)
is supported. `main` is the development branch; fixes land there first and ship
with the next release.

## Reporting a vulnerability

Do not open a public issue. Use GitHub's private reporting:
**Security** tab -> **Report a vulnerability**
([GitHub Security Advisories](https://github.com/fld-forge/pi-config/security/advisories/new)).

You will receive an initial response within 7 business days.

## Automated controls

Every PR and every push to `main` (plus a weekly scheduled run) is scanned by
gitleaks (full git history), `uv audit --locked`, pip-audit, Semgrep CE
(`p/python` on first-party Python), and zizmor. Pull requests also run GitHub's
Dependency Review Action with moderate-or-higher findings blocking. Weekly
CodeQL analysis, GitHub secret scanning with push protection, and weekly
Dependabot updates run on top. The pinned Semgrep CLI limits tool drift, but
its remote rules pack can evolve independently.

The same gitleaks version runs locally as a pre-commit hook. CI verifies the
SHA-256 of the gitleaks binary it downloads; the local hook is built by
pre-commit, which on a machine without Go first fetches an unpinned Go
toolchain without a checksum check. Local and CI supply-chain assurances are
therefore not equivalent, and CI remains the authoritative scan.

## Verifying release assets

Each release ships the wheel, the sdist, a CycloneDX SBOM exported from
`uv.lock` (`sbom.cdx.json`), an SPDX SBOM from the dependency graph
(`sbom.spdx.json`), SHA-256 checksums (`SHA256SUMS`) and the GitHub
build-provenance attestation bundle (`attestation.intoto.jsonl`).
To verify a downloaded asset:

```bash
gh attestation verify pi_config_tools-<version>-py3-none-any.whl \
  --repo fld-forge/pi-config
sha256sum --check SHA256SUMS   # inside the folder holding the downloaded assets
```

The bundle attests every other asset of the release, so the same check can run
against the downloaded file instead of the GitHub attestations API:

```bash
gh attestation verify pi_config_tools-<version>-py3-none-any.whl \
  --repo fld-forge/pi-config --bundle attestation.intoto.jsonl
```

That is not a fully offline verification: `--bundle` removes the call to the
attestations API, but `gh` still resolves the Sigstore trust root unless it is
cached or pinned with `--custom-trusted-root` (see `gh attestation
trusted-root --help`). Any other network access is the CLI's, not ours.
