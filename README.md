# setup-violet

GitHub Action that installs the [Violet](https://codeberg.org/dannypsnl/violet)
compiler on an Actions runner.

## Usage

```yaml
- uses: dannypsnl/setup-violet@v1
  with:
    violet-version: stable  # default
- run: violet --help
```

## Inputs

| Name             | Default  | Description                                                   |
| ---------------- | -------- | ------------------------------------------------------------- |
| `violet-version` | `stable` | Which Violet version to install. See [Version values](#version-values). |
| `cache`          | `true`   | Cache the installed `violet` binary keyed on the resolved commit SHA. |

### Version values

| Value      | Behavior                                                                       |
| ---------- | ------------------------------------------------------------------------------ |
| `stable`   | Download the prebuilt binary from the latest GitHub release.                   |
| `vX.Y.Z`   | Download the prebuilt binary from that release (e.g. `v0.1.0`).                |
| `current`  | Build from source at the latest `main` commit.                                 |
| `<branch>` / `<sha>` | Build from source at that ref.                                       |

When a prebuilt binary is requested on a platform that has no release asset,
the action falls back to building from source at the corresponding commit.

## Outputs

| Name             | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| `violet-version` | The resolved 40-character commit SHA of the install.         |
| `source`         | `prebuilt` if a release asset was used, otherwise `source`.  |
| `cache-hit`      | `true` when the binary was restored from cache.              |

## Supported runners

| Runner                       | Prebuilt asset            | Source build |
| ---------------------------- | ------------------------- | ------------ |
| `ubuntu-latest` (x86_64)     | `violet-linux-x86_64`     | yes          |
| `macos-latest` (Apple Silicon) | `violet-macos-arm64`    | yes          |

Windows is not supported.

## How it works

- For `stable` / `vX.Y.Z` on a supported platform, the action downloads the
  prebuilt binary from
  [`github.com/violet-prover/violet`](https://github.com/violet-prover/violet/releases)
  and skips OCaml setup entirely.
- For `current` or any other ref, the action installs OCaml via
  `ocaml/setup-ocaml@v3`, clones Violet from Codeberg, and builds it with
  opam + dune.
- Either way, the installed binary is cached, keyed on the resolved commit SHA,
  so subsequent runs at the same ref are fast.

## Example: matrix across multiple Violet versions

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        violet: [stable, current, v0.1.0]
    steps:
      - uses: actions/checkout@v4
      - uses: dannypsnl/setup-violet@v1
        with:
          violet-version: ${{ matrix.violet }}
      - run: violet build
```

## License

MIT
