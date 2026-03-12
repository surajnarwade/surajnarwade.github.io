+++
title = "Padding in Go structs"
slug = "padding-in-go-structs"
description = "How padding and field order influence Go struct size and when to act on it."
author = "Suraj Narwade"
date = "2026-03-12"
category = "Blog"
tags = ["go", "performance", "memory"]
+++

I recently came across a simple but surprisingly rich concept in Go: padding. It is the space the compiler inserts between fields so each value aligns with the architecture's expectations. Once you see how much wasted room can live between the fields, you start to appreciate how a small reordering can shrink both the in-memory size of a struct and the amount of memory you move around.

### Why padding exists at all

Go lays out struct fields according to each type's alignment requirement. The compiler inserts padding bytes between fields so the next field starts at the proper boundary. This alignment keeps the CPU happy but can leave gaps between fields or push later fields forward so the entire struct ends up bigger than the sum of its fields.

- `bool` — 1 byte (byte-aligned, no extra padding unless followed by a multi-byte value).
- `int32` — 4 bytes (the field must begin at 4-byte multiples).
- `float64` — 8 bytes (mantissa size requires 8-byte boundaries on amd64).
- `string` — 8 bytes on amd64 (two words: pointer + length, so it follows pointer-sized alignment).

### Example 1: Bring `int64` to the front

```go
package main

import (
    "fmt"
    "unsafe"
)

type metadata struct {
    readOnly bool  // 1 byte
    // +7 padded bytes so `id` starts at offset 8
    id       int64 // aligned to 8
    flags    bool  // trailing padding keeps the struct aligned to 8
}

type compactMetadata struct {
    id       int64
    readOnly bool
    flags    bool
}

func main() {
    fmt.Println("metadata size:", unsafe.Sizeof(metadata{}))
    fmt.Println("compactMetadata size:", unsafe.Sizeof(compactMetadata{}))
}
```

Running this prints:

```
metadata size: 24
compactMetadata size: 16
```

In the first layout, `readOnly` sits at offset 0 but the following `int64` field must start at offset 8, so the compiler pads seven bytes between them. After `id`, the struct still has to align up to 8, which leaves implicit padding after `flags`. The reordered version keeps `int64` first, then packs the two `bool` fields right after it. Running the snippet shows `metadata` at 24 bytes and `compactMetadata` at 16 bytes on amd64, which matters when you are holding many of these structs in memory.

### Example 2: Mix of pointers, floats, and bytes

```go
package main

import (
    "fmt"
    "unsafe"
)

type packet struct {
    tag     byte    // 1 byte
    // +7 padded bytes so `value` can align to 8
    value   float64 // 8-byte aligned
    payload *byte   // 8-byte aligned pointer
    flag    byte    // adds another byte requiring padding
}

type compactPacket struct {
    value   float64
    payload *byte
    tag     byte
    flag    byte
}

func main() {
    fmt.Println("packet size:", unsafe.Sizeof(packet{}))
    fmt.Println("compactPacket size:", unsafe.Sizeof(compactPacket{}))
}
```

Running this prints:

```
packet size: 32
compactPacket size: 24
```

The original ordering puts the `byte` first, triggering padding before the `float64` while the trailing `flag` invites another 7 bytes of padding at the end. The pointer also enforces 8-byte alignment, so the struct ends up padded between each block. The compact version keeps the 8-byte-aligned fields together and pushes both `byte` values to the end, so padding only appears once at the struct tail instead of between several fields. That trimming reduces the footprint from 32 bytes down to 24 bytes on amd64.

### Example 3: Same size, different padding

```go
package main

import (
    "fmt"
    "unsafe"
)

type paddedBytes struct {
    tag1 byte // 1 byte
    tag2 byte // 1 byte
    // +6 padding before `id` so it can start at offset 8
    id   int64
}

type reordered struct {
    id   int64
    tag1 byte
    tag2 byte
}

func main() {
    fmt.Println("paddedBytes size:", unsafe.Sizeof(paddedBytes{}))
    fmt.Println("reordered size:", unsafe.Sizeof(reordered{}))
}
```

Running this prints:

```
paddedBytes size: 16
reordered size: 16
```

Both layouts report 16 bytes on amd64, so the overall struct size does not change. The first layout still bears the 6-byte padding before `id`, while the second keeps `id` at the start and leaves the padding at the end of the struct. Being aware of this padding helps when you are tracing struct-heavy, cache-sensitive code, even if the size stays constant.

### When to act (and when to leave it alone)

Should you always optimize for size? Not always. Readability still matters. If a struct is small, rarely allocated, or not performance critical, it is often better to keep the most meaningful field order instead of micro-optimizing the layout. Treat padding knowledge as a tool, not a rule.

A good approach:
- write the clearest struct first
- measure if the layout matters (many of the examples above are just `unsafe.Sizeof` calls away)
- reorder only when there is a real, measurable benefit and comment why you chose the odd order

For larger systems—caches, pooled objects, packet builders—these tweaks can save noticeable memory, but even then I leave a short note so the next person does not “fix” it back to a natural field order and accidentally regress size.

### Light takeaway

In Go, struct size is not just the sum of field sizes. Alignment requirements, compiler-inserted padding, and field order all matter, which means two structs with identical fields can consume different amounts of memory. Sometimes simply reversing or regrouping fields will trim a few bytes. These quiet corners of the language often yield the most interesting performance lessons when you notice them.
