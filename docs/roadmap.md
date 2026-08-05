# Roadmap

## v0.1 — done

- The Pcore type calculus as Go types (scalar, collection, abstract, plus
  `Timestamp` / `Timespan` / `Binary`).
- `Parse(string) (Type, error)` with a canonical, round-trippable `String()`.
- The value model, including a redacting `Sensitive`.
- `IsInstance`, `IsAssignable`, `Infer`, `Generalize`, `CommonType`.
- Rich-data `ToData` / `FromData`.
- 100% coverage, `gofmt` + `go vet` clean, CI green on amd64, arm64, riscv64,
  loong64, ppc64le and s390x.

## v0.2 — done

- **Type aliases** — named, referenceable type definitions, including forward
  and recursive references, via a `Loader` type environment.
- **`TypeSet`** — grouped, namespaced type definitions with references.
- `Timestamp` / `Timespan` **range parameters** (`Timestamp[from, to]`).
- `SemVer` / `SemVerRange`, `Init`, `Object`, `RichData`, `Runtime`, `URI`,
  `Iterable` / `Iterator`, `Error` and `Callable`.

## Later

- Pcore-exact `Timespan` textual form (`D-HH:MM:SS.fff`).
- Tighter `CommonType` for strings (length-range merging).

## Consumers

`go-pcore` is the foundational type layer for **go-puppet** (the Puppet DSL
evaluator) and for **go-ruby-puppet**, which marshals `rbgo.Value ↔ pcore.Value`
across the rich-data protocol.
