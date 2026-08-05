# Rich-data serialization

Pcore's **rich-data** protocol renders any value as a tree of data-only values
(`nil`, `bool`, `int64`, `float64`, `string`, `[]Value` and string-keyed `Hash`)
in which non-data values are encoded as tagged hashes carrying a `__ptype` key.

```go
d, _ := pcore.ToData(pcore.NewBinary([]byte("hi")))
// { "__ptype" => "Binary", "__pvalue" => "aGk=" }

v, _ := pcore.FromData(d) // *pcore.Binary{ "hi" }
```

`ToData` and `FromData` are inverse: any value round-trips, **except** that
`Sensitive` is redacted by design — it serializes to `{ "__ptype" => "Sensitive" }`
with no payload and reconstructs wrapping `Undef`.

## Encoding

| Value | Rich-data form |
|-------|----------------|
| `undef` | `nil` |
| `bool` / `int64` / `float64` / `string` | itself |
| `Array` | array of encoded elements |
| string-keyed `Hash` | a plain hash of encoded values |
| non-string-keyed (or reserved-key) `Hash` | `{ __ptype: "Hash", __pvalue: [k,v,…] }` |
| `Default` | `{ __ptype: "Default" }` |
| `Regexp` | `{ __ptype: "Regexp", __pvalue: "<source>" }` |
| `Binary` | `{ __ptype: "Binary", __pvalue: "<base64>" }` |
| `Timestamp` | `{ __ptype: "Timestamp", __pvalue: "<RFC 3339>" }` |
| `Timespan` | `{ __ptype: "Timespan", __pvalue: "<duration>" }` |
| `Type` value | `{ __ptype: "Type", __pvalue: "<type string>" }` |
| `Sensitive` | `{ __ptype: "Sensitive" }` (redacted) |

A plain hash that happens to contain a `__ptype` / `__pvalue` key is encoded via
the tagged `Hash` array form so it survives the round-trip unambiguously.

!!! note "Timespan textual form"
    The `Timespan` string form uses Go's duration syntax and round-trips within
    this library; the Pcore-exact `Timespan` textual form (`D-HH:MM:SS.fff`) is
    not yet emitted.
