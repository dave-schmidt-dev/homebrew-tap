# Zero Delta Homebrew Tap

Self-managed Homebrew distribution for Zero Delta projects. This tap will hold
recipes for projects that have a versioned release, a reproducible build or
download, and a documented installation path.

**Owner:** [dave-schmidt-dev](https://github.com/dave-schmidt-dev)

**Status:** tap scaffolded; no packages are currently available.

## Catalog

The catalog is intentionally empty while the projects for the first release
are being selected. No formulae or casks are installable from this repository
yet.

Future installation form, once a package is added and published:

```text
brew install dave-schmidt-dev/tap/<package>
```

The command above is a placeholder and does not install a current package.

## Priorities

1. Keep each recipe tied to an immutable upstream release and checksum.
2. Keep runtime dependencies explicit and minimal.
3. Run meaningful build, installation, and smoke checks before publishing.
4. Make upgrades and removal predictable on supported macOS versions.

## Layout

| Path | Purpose |
|---|---|
| `README.md` | Tap purpose, catalog, workflow, and conventions. |
| `Formula/` | Future Homebrew formulae for command-line projects. |
| `Casks/` | Future Homebrew casks for signed macOS applications. |
| `HISTORY.md` | Local record of meaningful tap changes. |
| `TASKS.md` | Local queue for pending tap work. |
| `LICENSE` | MIT license. |

`Formula/` and `Casks/` are planned locations; they are not populated in this
scaffold.

## Maintainer workflow

For each selected project, add a recipe only after its upstream release is
available. Record the exact release URL, immutable checksum, dependencies,
supported platforms, and required caveats. Run the project’s meaningful
verification checks, then run `brew audit --strict` and `brew style` against
the recipe before publishing it. Recheck installation, upgrade, and removal
on a clean supported macOS environment.

This is a personal Zero Delta tap. It is not an official Homebrew repository
or an endorsement by the Homebrew project.

## Conventions

- Keep recipes small and specific to one upstream project.
- Never commit secrets, local state, or generated build output.
- Keep operational history and task tracking local to this checkout.
- Update `HISTORY.md` with meaningful tap changes and keep `TASKS.md` as the
  current queue.
