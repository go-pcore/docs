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

## v0.2 — planned

- **Type aliases** — named, referenceable type definitions.
- **`TypeSet`** — grouped type definitions with a namespace.
- `Timestamp` / `Timespan` **range parameters** (`Timestamp[from, to]`).
- Pcore-exact `Timespan` textual form.
- Tighter `CommonType` for strings (length-range merging).

## Consumers

`go-pcore` is the foundational type layer for **go-puppet** (the Puppet DSL
evaluator) and for **go-ruby-puppet**, which marshals `rbgo.Value ↔ pcore.Value`
across the rich-data protocol.
