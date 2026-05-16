# setup-violet

GitHub Action that installs the [Violet](https://codeberg.org/dannypsnl/violet)
compiler on an Actions runner.

## Usage

```yaml
- uses: dannypsnl/setup-violet@v1
  with:
    violet-version: main  # default
- run: violet --help
```

## Inputs

| Name             | Default | Description                                                                 |
| ---------------- | ------- | --------------------------------------------------------------------------- |
| `violet-version` | `main`  | Git ref of `codeberg.org/dannypsnl/violet` — branch, tag, or commit SHA.    |
| `cache`          | `true`  | Cache the built `violet` binary keyed on the resolved commit SHA.           |

## Outputs

| Name             | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| `violet-version` | The resolved 40-character commit SHA of the build.           |
| `cache-hit`      | `true` when the binary was restored from cache.              |

## Supported runners

- `ubuntu-latest`
- `macos-latest` (Apple Silicon)

Windows is not supported in v1.

## How it works

The action installs OCaml via `ocaml/setup-ocaml@v3`, clones Violet from
Codeberg at the requested ref, builds it with opam + dune, and places the
resulting `violet` binary on `PATH`. The built binary is cached, keyed on
the resolved commit SHA, so subsequent runs at the same ref are fast.

## Example: matrix across multiple Violet versions

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        violet: [main, v0.1.0]
    steps:
      - uses: actions/checkout@v4
      - uses: dannypsnl/setup-violet@v1
        with:
          violet-version: ${{ matrix.violet }}
      - run: violet build
```

## License

MIT
