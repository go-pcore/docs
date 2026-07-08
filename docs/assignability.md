# Assignability & inference

Assignability is the load-bearing operation of a type system.
`pcore.IsAssignable(a, b)` answers **is `b` a subtype of `a`** — is every
instance of `b` also an instance of `a`?

```go
a, _ := pcore.Parse("Integer[0,10]")
b, _ := pcore.Parse("Integer[2,5]")
pcore.IsAssignable(a, b) // true
```

## The lattice

- `Any` is the top: everything is assignable to it.
- `Scalar` ⊇ `ScalarData` ⊇ `Numeric` ⊇ `Integer` / `Float`, and `String` ⊇
  `Enum` / `Pattern`.
- `Data` is `Variant[ScalarData, Undef, Array[Data], Hash[String, Data]]`, checked
  structurally and recursively.
- `Collection` accepts any `Array`, `Hash`, `Tuple` or `Struct` within its size
  range.
- Numeric and string ranges use containment: `Integer[0,10]` accepts
  `Integer[2,5]`; `String[1,3]` accepts `Enum['a','bb']` (each value's length is
  in range).
- `Variant` distributes: `b` is assignable when every member of `b`'s union is
  assignable, and `a` (a `Variant`) accepts `b` when some branch does.
- `Optional[T]` is `Variant[Undef, T]`; `NotUndef[T]` accepts a non-undef type
  assignable to `T`.
- `Struct` is closed: assignability checks every required key is present and
  compatible, with no extra keys.

The assignability arms are covered exhaustively by the test suite.

## Inference

`Infer(v)` returns the most specific type of a value; ranges collapse to the
observed value:

```go
pcore.Infer(int64(5))                       // Integer[5, 5]
pcore.Infer("hello")                        // String[5, 5]
pcore.Infer([]pcore.Value{int64(1), "x"})   // Array[ScalarData, 2, 2]
```

`Generalize` widens a type by dropping range/size constraints; `CommonType`
returns the narrowest single supertype of two types:

```go
pcore.Generalize(pcore.Infer(int64(5)))     // Integer
i1, _ := pcore.Parse("Integer[1,3]")
i2, _ := pcore.Parse("Integer[5,7]")
pcore.CommonType(i1, i2)                     // Integer[1, 7]
```
