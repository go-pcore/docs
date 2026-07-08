# Contributing

Contributions are welcome under the project's **BSD-3-Clause** licence.

## Ground rules

- **Pure Go, zero cgo.** The library must cross-compile to all six 64-bit Go
  targets (amd64, arm64, riscv64, loong64, ppc64le, s390x).
- **100% coverage**, enforced as a CI gate — including every parse-error,
  assignability arm and serialization path. Prefer table-driven tests.
- **`gofmt` + `go vet` clean.**
- **Round-trip discipline.** Any new type must have a canonical `String()` that
  parses back through `Parse`.
- **Fidelity to Pcore.** Match Puppet's `Puppet::Pops::Types` names and
  semantics; note anything intentionally staged.

## Running the suite

```sh
go vet ./...
COVERPKG=$(go list ./... | paste -sd, -)
go test -race -coverpkg="$COVERPKG" -coverprofile=cover.out ./...
go tool cover -func=cover.out | tail -1   # expect 100.0%
```

## Documentation

Docs live in the [docs](https://github.com/go-pcore/docs) repo (MkDocs Material
+ mike). Preview locally with `mkdocs serve`.
