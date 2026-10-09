# GoForge

A Go engineering workshop and public portfolio: progressive learning
implementations, reusable packages, developer tools, and production-grade
capstones, built while deepening Go expertise beyond tutorial-level material.

## What this is

`GoForge` tracks a structured curriculum of 58 projects across five phases,
from idiomatic Go fundamentals through distributed systems, observability,
and security. Each project solves a realistic problem or teaches a
transferable engineering skill — nothing here is a trivial variation padded
in for count. The goal isn't just finished code; it's engineering judgment:
knowing when to add an interface, when a mutex beats a channel, when to
reach for a library instead of hand-rolling something, and how to prove a
performance claim with a benchmark instead of a guess.

## Structure

```
GoForge/
├── go.mod                  # root module (Go 1.27.1)
├── go.work                 # workspace file, added once standalone modules exist
├── projects/                # small, independent learning implementations
│   ├── 01-string-toolkit/
│   ├── 02-inventory-cli/
│   └── ...
├── pkg/                     # reusable packages worth importing across projects
│   ├── retry/
│   ├── ratelimit/
│   ├── workerpool/
│   └── ...
├── cmd/                     # executable entry points for anything long-lived
│   ├── GoForge-cli/
│   └── urlshortener/
├── internal/                 # shared internals, not meant for external import
│   └── testutil/             # test helpers, fixtures, fake clocks
├── docs/                     # design notes, ADRs, phase retrospectives
├── scripts/                  # lint, test, bench, release automation
└── .github/workflows/        # CI: test, vet, lint, race, benchmarks
```

A project graduates out of this repo into its own, independently versioned
repository once it needs real release tags, a clean `go install` path,
independent deployment, or outside contributors. See `docs/` for the current
list of standalone candidates and their status.

## Module strategy

A single root module (`github.com/Gr8Soln/GoForge`) covers `projects/`,
`pkg/`, `cmd/`, and `internal/` for as long as possible. Multi-module /
`go.work` complexity is introduced only when a package is actually about to
split into its own repository — not preemptively.

## Requirements

- Go **1.27.1** (pinned in `go.mod`)
- Docker (for integration tests against Postgres/Redis — see below)

## Getting started

```bash
git clone https://github.com/Gr8Soln/GoForge.git
cd GoForge
go build ./...
go test ./...
```

Each `projects/NN-name/` directory is self-contained with its own short
`README.md`: what problem it solves, what it teaches, how to run it, and
how to verify it's correct.

## Testing

- **Unit tests** live beside the code (`_test.go`), run with `go test ./...`.
- **Race-sensitive packages** run under `go test -race ./...` in CI as a
  dedicated job.
- **Integration tests** (anything touching Postgres, Redis, or another
  external dependency) are tagged `//go:build integration` and run against
  services started via `docker compose` in CI — they're skipped by default
  in a plain local `go test ./...`.
- **Fuzz tests**, where present, run with `go test -fuzz=. -run=^$` and are
  not part of the default CI loop (seed corpora are committed; fuzzing
  itself is run manually/periodically).

## Linting and formatting

```bash
gofmt -l .        # must be empty
go vet ./...
golangci-lint run # introduced after Phase 1, once early noise settles
```

All three are CI gates on every push and pull request.

## Benchmarks

Any package in `pkg/` that claims a performance property ships a
`BenchmarkX` alongside its tests. Run with:

```bash
go test -bench=. -benchmem ./pkg/...
```

Profiling write-ups (flame graphs, before/after `pprof` output) for the
Phase 4 performance projects live in `docs/`.

## CI/CD

GitHub Actions runs `test` (unit + race), `lint`, `vet`, and a `build`
matrix across `linux/amd64`, `linux/arm64`, and `darwin/arm64` for anything
in `cmd/`. Standalone repositories extracted from this one (see
`docs/standalone-candidates.md`) add `goreleaser` for tagged, cross-platform
release artifacts.

## Curriculum phases

| Phase | Focus | Projects |
|---|---|---|
| 1 — Foundations | Idiomatic Go: structs, interfaces, errors, I/O, testing | 1–12 |
| 2 — Reusable packages & application design | Generics, CLIs, HTTP, databases, API design | 13–26 |
| 3 — Concurrency & networking | Goroutines, channels, TCP/UDP, backpressure, races | 27–40 |
| 4 — Advanced backend & infrastructure | Resilience, distributed processing, observability, profiling, security | 41–52 |
| 5 — Production systems & open source | Public libraries, releases, deployable services, capstones | 53–58 |

Current status: **Phase 1 — Foundations**, starting with project #1.

## License

[MIT](./LICENSE)