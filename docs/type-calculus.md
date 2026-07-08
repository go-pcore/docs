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

## Rich data

| Type | Meaning |
|------|---------|
| `Timestamp` | an instant in time |
| `Timespan` | a duration |
| `Binary` | a byte string |

!!! note "Staged for v0.2"
    Named **type aliases** and **`TypeSet`** are not yet parsed. `Timestamp` /
    `Timespan` range parameters and the Pcore-exact `Timespan` string form are
    also staged; the value model and serialization round-trip within this library
    today.
