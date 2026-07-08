# go-pcore documentation

**A pure-Go reimplementation of Puppet's Pcore type system** — the data-type and
value model that underpins **Puppet**, **Hiera** and **Facter** values, built
with **zero cgo**.

`go-pcore/pcore` gives a Go program the Puppet type calculus: a type model, a
type **parser**, a value model, and the load-bearing operations — instance-of,
assignability, inference and rich-data serialization. Type names and semantics
track Puppet's `Puppet::Pops::Types`, so it is a drop-in for Puppet type
expressions. The module path is `github.com/go-pcore/pcore`.

!!! success "Status: v0.1 complete"
    Type model, round-trippable parser, value model, and `IsInstance` /
    `IsAssignable` / `Infer` / `Generalize` / `CommonType` / `ToData` /
    `FromData` — at **100% coverage**, `gofmt` + `go vet` clean, CI green across
    the six 64-bit Go targets (amd64, arm64, riscv64, loong64, ppc64le, s390x).
    Type **aliases** and **`TypeSet`** are staged for v0.2.

## Install

```sh
go get github.com/go-pcore/pcore
```

## Quick taste

```go
package main

import (
	"fmt"

	"github.com/go-pcore/pcore"
)

func main() {
	t, _ := pcore.Parse("Variant[Integer[0,10], Enum['a','b']]")

	fmt.Println(pcore.IsInstance(t, int64(7))) // true
	fmt.Println(pcore.IsInstance(t, "a"))      // true
	fmt.Println(pcore.IsInstance(t, int64(99))) // false

	fmt.Println(pcore.Infer([]pcore.Value{int64(1), int64(2)}))
	// Array[Integer[1, 2], 2, 2]

	fmt.Println(t.String()) // round-trips through Parse
}
```

## Where to next

- [The type calculus](type-calculus.md) — every type and its parameters.
- [Usage & API](api.md) — the Go surface.
- [Assignability & inference](assignability.md) — the subtype lattice.
- [Rich-data serialization](serialization.md) — `ToData` / `FromData`.
