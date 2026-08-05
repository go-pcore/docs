# The type calculus

Pcore types are parsed from their canonical string form with
`pcore.Parse(string) (pcore.Type, error)`. Every `Type.String()` is canonical
and round-trips: `pcore.Parse(t.String())` reproduces `t`.

## Scalar

| Type | Meaning |
|------|---------|
| `Any` | every value, including `undef` |
| `Scalar` | `Numeric`, `String`, `Boolean`, `Regexp`, `Timestamp`, `Timespan` |
| `ScalarData` | `Integer`, `Float`, `String`, `Boolean` |
| `Data` | `ScalarData`, `Undef`, `Array[Data]`, `Hash[String, Data]` |
| `Numeric` | `Integer` or `Float` |
| `Integer[min, max]` | an integer in the (inclusive) range; bounds may be `default` |
| `Float[min, max]` | a float in the range |
| `String[min, max]` | a string whose length is in the range |
| `Boolean` | `true` or `false` |
| `Undef` | the `undef` value |
| `Default` | the literal `default` |

`Integer[3]` is `min = 3`, unbounded max; `Integer[default, 10]` is unbounded
min, `max = 10`.

## Collection

| Type | Meaning |
|------|---------|
| `Array[T, min, max]` | an array of `T` with a size in the range |
| `Hash[K, V, min, max]` | a hash with keys `K`, values `V`, sized in the range |
| `Tuple[T1, T2, …, min, max]` | a positional array; trailing integers give the size |
| `Struct[{ 'k' => V, Optional['o'] => W }]` | a hash with named, typed members |
| `Collection[min, max]` | any array or hash sized in the range |

`Array` alone is `Array[Any, 0, default]`; `Hash` alone is
`Hash[Any, Any, 0, default]`. A `Struct` is **closed** — a matching hash has
exactly the declared keys. A member is optional to provide when its key is
wrapped in `Optional[…]` **or** its value type accepts `Undef`.

## Abstract

| Type | Meaning |
|------|---------|
| `Variant[A, B, …]` | any of the member types (a union) |
| `Optional[T]` | `T` or `Undef` |
| `NotUndef[T]` | `T` excluding `Undef` |
| `Enum['a', 'b', …]` | one of the given strings; a trailing `true` makes it case-insensitive |
| `Pattern[/re/, …]` | a string matching any of the regexps |
| `Regexp[/re/]` | a regexp value (or, unparameterized, any regexp) |
| `Type[T]` | a type value assignable to `T` |
| `Sensitive[T]` | a value of `T` wrapped so it is redacted |
| `Init[T, args…]` | a value that constructs a `T` (optionally with extra arg types) |
| `Iterable[T]` | an iterable producing `T` |
| `Iterator[T]` | an iterator producing `T` |
| `Callable[params…, block]` | a callable with the given parameter (and block) types |

## Rich data

| Type | Meaning |
|------|---------|
| `Timestamp[from, to]` | an instant in time, optionally range-bounded |
| `Timespan[from, to]` | a duration, optionally range-bounded |
| `Binary` | a byte string |
| `SemVer[ranges…]` | a semantic version within any of the ranges |
| `SemVerRange` | a semantic-version range value |
| `Runtime['go', name]` | a runtime (foreign) object, e.g. a Go value |
| `URI[scheme]` | a URI, optionally constrained by scheme |
| `Error[kind, issue_code]` | an error value with a kind and issue code |

## Nominal / named

| Type | Meaning |
|------|---------|
| `Object[{ name => …, parent => …, attributes => {…} }]` | a nominal object type with typed attributes |
| `type X = <expr>` | a named **type alias** (forward and recursive references allowed) |
| `TypeSet[{ name => …, version => …, types => {…}, references => {…} }]` | a namespaced set of grouped type definitions |

Aliases and `TypeSet` members live in a `Loader` type environment
(`pcore.NewLoader()`) that resolves forward and recursive references
transparently through `Parse` / `IsInstance` / `IsAssignable` / `Infer`:

```go
l := pcore.NewLoader()
l.Declare("type Tree = Hash[String, Variant[Tree, Integer]]")
tree, _ := l.Parse("Tree")
pcore.IsInstance(tree, map[string]pcore.Value{
	"a": int64(1),
	"b": map[string]pcore.Value{"c": int64(2)},
}) // true
```

!!! note "Timespan textual form"
    `Timespan` bounds are expressed with Go's duration syntax (for example
    `Timespan['1s', '1m0s']`) and round-trip within this library; the
    Pcore-exact `Timespan` string form (`D-HH:MM:SS.fff`) is not yet accepted.
