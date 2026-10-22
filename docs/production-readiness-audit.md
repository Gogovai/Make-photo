# Make-photo production-readiness audit

## Current verified baseline

- Repository: `Gogovai/Make-photo`
- Audit branch: `make-photo/production-audit`
- Baseline main commit: `03d0c7e3b39cfb401cdbba590431141ee4c6c8fa`
- Stack: Rust workspace, edition 2024, declared minimum Rust version 1.95.
- Existing CI workflow: `.github/workflows/ci.yml`; it checks formatting, Clippy, crate layering, generated scorecard, tests, and selected platform/web builds.
- CI status for the baseline commit was not returned by the connected GitHub status/workflow query. Treat the baseline as **unverified**, not passing or failing.
- Upstream `storytold/photocraft` has moved substantially since this fork's baseline. The reported 1,267 commits are upstream commits ahead of the fork point; they are not Make-photo-specific commits.

## Confirmed product-identity conflict

The repository is named **Make-photo**, but the current `README.md` markets it as **PhotoCraft**, uses ArtCraft logos and community links, and `AGENTS.md` requires the upstream product name in user-facing text. This must be resolved before a public/commercial launch.

Do not perform a blind global replacement. First establish which upstream source, trademark, logo, and community assets may be reused under their applicable licenses/terms. Keep the upstream attribution and license notices intact. Then make a deliberate Make-photo identity pass over README, application/window/about labels, icons, installers, package identifiers, docs, and links, while retaining lowercase internal crate names until a separately tested rename is approved.

## Safe remediation order

1. **Establish a reproducible baseline:** run the checks below on this branch and save complete logs and platform/toolchain versions.
2. **Fix confirmed failures first:** address compile, lint, test, packaging, and security failures from actual logs. Every defect fix must include a focused regression test where practical.
3. **Check file safety:** prioritize PSD/PSB import/export round-trips, malformed-file handling, save/reopen, layer/mask preservation, and non-destructive editing.
4. **Check runtime reliability:** launch the native app; open/edit/save/reopen representative files; exercise command/CLI/MCP paths; verify failures are surfaced without losing documents.
5. **Review dependency and supply-chain risks:** inspect locked dependencies, license notices, bundled assets/fonts, and build scripts before release.
6. **Review upstream changes by topic:** compare architecture, parser/format changes, UI, GPU, security, and test changes separately. Cherry-pick or merge only after reviewing conflicts and running the full suite. Do not merge thousands of upstream commits blindly into `main`.
7. **Product identity and release gates:** resolve branding/licensing, create Make-photo-owned visual assets, verify Windows packaging and signatures, and publish only after checks are green on supported platforms.

## Windows baseline commands

Run from the repository root in PowerShell. Keep the output of each command, especially the first failure.

```powershell
git status --short --branch
git log -1 --oneline
rustc --version
cargo --version
cargo fmt --all -- --check
cargo check --workspace --locked
cargo clippy --workspace --all-targets --locked --features heif -- -D warnings
cargo xtask layers
cargo xtask scorecard --check
cargo test --workspace --locked
```

The complete CI-equivalent path is `cargo xtask ci` (see `docs/development.md`). It can take a long time and may require the platform build prerequisites. For web validation, install `wasm32-unknown-unknown` and follow the documented Trunk/web packaging process. CI and local results must be recorded separately.

## Exit criteria

- [ ] Formatting passes.
- [ ] Workspace check passes with the committed lockfile.
- [ ] Clippy passes with warnings denied.
- [ ] Layering and scorecard checks pass.
- [ ] Workspace tests and doctests pass.
- [ ] CI is green for the proposed commit on Linux and Windows; web build is green where relevant.
- [ ] PSD/PSB regression and malformed-input tests pass.
- [ ] Product naming, logos, and third-party assets have a documented licensing review.
- [ ] No known critical/high security issue or data-loss bug remains open.
- [ ] A clean release build and a real end-to-end smoke test pass.
