# gosocialcheck with Flox

This fork of [`AkihiroSuda/gosocialcheck`](https://github.com/AkihiroSuda/gosocialcheck)
adds a [Flox](https://flox.dev) environment in `.flox/`. Everything else
tracks upstream.

## Use it

```bash
git clone https://github.com/justincastilla/gosocialcheck-flox
cd gosocialcheck-flox

flox activate
go test ./...
go install ./cmd/gosocialcheck
gosocialcheck run ./...
```

To build a package instead, run `flox build`. The binary lands in
`./result-gosocialcheck/bin/gosocialcheck`.

## Hidden dependencies

| Upstream README / CI | Hidden dependency | In the manifest |
|---|---|---|
| `go install ...@latest` | `go.mod` says `go 1.26.0`; an older `go` silently downloads a newer toolchain | `go_1_26`, `GOTOOLCHAIN=local` (as upstream CI does) |
| Not mentioned | `git` is called at runtime to clone the remote cache and diff `go.sum` for `--gha` | `git`, also a `runtime-packages` entry of the build |
| "export `GITHUB_TOKEN`=..." | An undocumented env var, without which `update` hits the rate limit | Hook reads `gh auth token`, warns if neither is set |
| `zizmor-action` in CI | Linter only available in CI | `zizmor` |
| `go install` to `~/go/bin` | Global `GOPATH`, binaries shared across projects | `GOPATH` and `GOBIN` under `$FLOX_ENV_CACHE` |

The gosocialcheck data cache stays in `$XDG_CACHE_HOME/gosocialcheck`, so
it is shared with any copy of gosocialcheck you install outside this environment.

## Syncing with upstream

```bash
git remote add upstream https://github.com/AkihiroSuda/gosocialcheck
git fetch upstream
git merge upstream/master
```
