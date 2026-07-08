# Usage & API

The module path is `github.com/go-pcore/pcore`; the package is `pcore`.

## Values

`pcore.Value` is an alias for `any`. Values use their natural Go types:

- scalars — `bool`, `int64`, `float64`, `string`
- arrays — `[]pcore.Value`
- hashes — `*pcore.Hash` (ordered, arbitrary keys) or a `map[string]Value`
- the rest — wrapper types: `Undef`, `Default`, `*Sensitive`, `*Regexp`,
  `*Binary`, `*Timestamp`, `*Timespan`

A Go `nil` is treated as `Undef`; the integer and float widths are canonicalized
to `int64` / `float64`.

```go
h := pcore.NewHash(
	pcore.HashEntry{Key: "name", Value: "puppet"},
	pcore.HashEntry{Key: "port", Value: int64(8140)},
)
secret := pcore.NewSensitive("hunter2")
fmt.Println(secret) // Sensitive[value redacted]
```

## The type parser

```go
t, err := pcore.Parse("Struct[{'name' => String, Optional['port'] => Integer[1,65535]}]")
```

`Parse` is the Go rendering of Puppet's `Type(string)` — Go cannot name a
function and the `Type` interface identically, so the parser is `Parse`. Every
`Type` has a canonical `String()` that round-trips through `Parse`.

## Operations

| Function | Purpose |
|----------|---------|
| `IsInstance(t Type, v Value) bool` | is `v` an instance of `t`? |
| `IsAssignable(a, b Type) bool` | is `b` a subtype of `a`? |
| `Infer(v Value) Type` | the most specific type of `v` |
| `Generalize(t Type) Type` | drop range/size constraints |
| `CommonType(a, b Type) Type` | narrowest supertype of both |
| `ToData(v Value) (Value, error)` | rich-data representation |
| `FromData(v Value) (Value, error)` | reconstruct from rich data |

```go
t, _ := pcore.Parse("Integer[0,10]")
pcore.IsInstance(t, int64(3)) // true

a, _ := pcore.Parse("Data")
b, _ := pcore.Parse("Array[Integer]")
pcore.IsAssignable(a, b) // true — Array[Integer] <: Data

pcore.Infer("hello")           // String[5, 5]
pcore.Generalize(pcore.Infer("hello")) // String
```

## Constructors

Types can also be built programmatically without the parser:
`NewInteger`, `AnyInteger`, `NewFloat`, `AnyFloat`, `NewString`, `AnyString`,
`NewEnum`, `NewArray`, `NewHashType`, `NewVariant`, `NewOptional`, and the
singleton accessors (`AnyT`, `ScalarT`, `DataT`, `BooleanT`, `UndefT`,
`DefaultT`, `NumericT`, `BinaryT`, `TimestampT`, `TimespanT`).
