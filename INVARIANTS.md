# Invariants

The contract for the Zero Delta Homebrew tap. Every recipe in this repository
satisfies all of these. A change that cannot hold one of them does not get
committed.

---

## INV-1 — Recipes pin immutable artifacts

Every `url` points at an artifact that cannot change after publication: a
release asset or a tag tarball, never a branch, `HEAD`, or a mutable tag.
Every `url` is paired with the `sha256` of that exact artifact.

## INV-2 — No unverified checksums are committed

A recipe reaches `main` only after its `sha256` has been computed from the
artifact the `url` actually resolves to. Placeholder digests stay in the
working tree, untracked, until the upstream release exists.

## INV-3 — Verification is local and complete

This tap runs no CI, so the gate is the maintainer's machine. Before a recipe
is pushed, all five checks pass against the published `url`:

```text
brew style dave-schmidt-dev/tap
brew audit --strict --online dave-schmidt-dev/tap/<package>
brew install --build-from-source dave-schmidt-dev/tap/<package>
brew test dave-schmidt-dev/tap/<package>
brew uninstall <package>
```

`brew test` exercises the installed binary. A `test do` block that only checks
for the file's existence does not satisfy this.

## INV-4 — Ships no GitHub Actions workflow

`.github/workflows/` stays absent. This is deliberate, not an omission: see
INV-3 for what replaces it.

## INV-5 — macOS only

Formulae declare `depends_on :macos`. Nothing here is expected to build or run
on Linux, and no recipe claims otherwise.

## INV-6 — Recipes expose only intended binaries

An install puts on `PATH` exactly the commands the project means to publish.
Generic or ambiguous names that upstream uses for a local launcher are removed
during `install` rather than shipped to every user.

## INV-7 — One recipe, one upstream project

Each file in `Formula/` or `Casks/` packages a single upstream project.
Recipes do not bundle, vendor, or shim a second project.

## INV-8 — Nothing local, generated, or secret is committed

No secrets, credentials, build output, or machine-local state enters the
repository. `HISTORY.md` and `TASKS.md` are operational records for this
checkout only; the remote is public, so they stay gitignored and are never
force-added.

## INV-9 — The README describes what is actually installable

The catalog and status reflect the current contents of `Formula/` and
`Casks/`. An install command appears in the README as a live instruction only
when running it succeeds.
