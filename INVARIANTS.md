# Invariants — homebrew-tap

> System contract. The harvest tool reads `area:` globs to map HISTORY bug entries
> to invariants.
>
> This repository holds packaging recipes, not runtime code. Its gates are
> Homebrew's own commands rather than a test suite, so each `gate_test` names the
> command that enforces the invariant. Per `plan.md`, `gate_test` text is never
> executed by tooling.

## Standing invariants (seeded on every charter)

### INV-1 — No silent blocking waits: user-facing blocking ops surface live progress
area: ["**/*.sh", "**/*.py", "**/*.rb"]
gate_test: brew install --build-from-source dave-schmidt-dev/tap/<package>
threshold: 3
rationale: Kept seeded rather than dropped, though currently vacuous — this repo
  ships no runtime code of its own, and a formula's perceptible waits belong to
  brew, which already reports them. It binds any script or install-time logic
  added here later. See AGENTS.md "Progress Visibility (No Silent Waits)".

## Project-specific invariants

### INV-2 — Every recipe pins an immutable upstream artifact and its checksum
area: ["Formula/**/*.rb", "Casks/**/*.rb"]
gate_test: brew audit --strict --online dave-schmidt-dev/tap/<package>
threshold: 3
rationale: A `url` that resolves to a branch, `HEAD`, or a movable tag makes the
  paired `sha256` meaningless and the install unreproducible. Release assets and
  tag tarballs only.

### INV-3 — No unverified checksum reaches main
area: ["Formula/**/*.rb", "Casks/**/*.rb"]
gate_test: git grep -n REPLACE_WITH -- Formula Casks
threshold: 1
rationale: A placeholder digest that ships is a recipe that fails for every user
  on first install. Recipes awaiting an upstream release stay untracked in the
  working tree until the artifact exists and its digest is computed from it.

### INV-4 — Verification is local, complete, and run before every push
area: ["Formula/**/*.rb", "Casks/**/*.rb"]
gate_test: brew style dave-schmidt-dev/tap && brew test dave-schmidt-dev/tap/<package>
threshold: 3
rationale: With no CI, the maintainer's machine is the only gate. All five checks
  pass against the published url — style, audit --strict --online, a
  build-from-source install, brew test, and uninstall. A `test do` block that only
  asserts a file exists does not satisfy this; it must exercise the binary.

### INV-5 — This tap ships no GitHub Actions workflow
area: [".github/**"]
gate_test: test ! -d .github/workflows
threshold: 1
rationale: Deliberate, not an oversight. Recorded so a later session does not
  helpfully restore CI. INV-4 is what replaces it.

### INV-6 — macOS only
area: ["Formula/**/*.rb", "Casks/**/*.rb"]
gate_test: brew audit --strict --online dave-schmidt-dev/tap/<package>
threshold: 3
rationale: Formulae declare `depends_on :macos`. Nothing here is expected to build
  or run on Linux, and no recipe may imply otherwise.

### INV-7 — Recipes expose only the binaries the project means to publish
area: ["Formula/**/*.rb"]
gate_test: brew test dave-schmidt-dev/tap/<package>
threshold: 3
rationale: Upstream often declares a console script named for a local launcher.
  Installing it puts a generic name on every user's PATH, where it collides with
  unrelated tools. Such entry points are removed during `install`.

### INV-8 — One recipe, one upstream project
area: ["Formula/**/*.rb", "Casks/**/*.rb"]
gate_test: brew audit --strict --online dave-schmidt-dev/tap/<package>
threshold: 3
rationale: Recipes do not bundle, vendor, or shim a second project. A bundle makes
  upgrades and removal unpredictable and hides one project's breakage inside
  another's version.

### INV-9 — Nothing local, generated, or secret is committed
area: [".gitignore", "Formula/**/*.rb", "Casks/**/*.rb"]
gate_test: git ls-files | grep -Ev '^(README|INVARIANTS|LICENSE)|^(Formula|Casks)/|^\.gitignore$'
threshold: 1
always_active: true
rationale: The remote is public. No secrets, credentials, build output, or
  machine-local state enters it. `HISTORY.md` and `TASKS.md` are operational
  records for this checkout only; they stay gitignored and are never force-added.

### INV-10 — The README describes what is actually installable
area: ["README.md"]
gate_test: brew install dave-schmidt-dev/tap/<package>
threshold: 3
rationale: The catalog and status reflect the current contents of `Formula/` and
  `Casks/`. An install command appears as a live instruction only when running it
  succeeds; anything aspirational is labelled as such.
