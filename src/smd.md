# SMD Layout

The version described here is version 1. The file consists of two parts, the geometry with shapes and their attributes, with a possible tail of three variants, 2, 3 and 4 which contain skeleton-like things. There is no prior information indicating whether the tail is present or not before trying to read it.

The format has changed over time with Path of Exile releases, listed chronologically:

 * 2.5 introduces an optional tail section, always with tail version 2 if present.

 * 3.0 compresses the file with Brotli, where the compressed payload is prefixed by a seven byte header of `CMP` followed by the unpacked payload size as a little-endian `u32`.

 * 3.0 makes all tails mandatory.

 * 3.3 has the first occurence of tail version 3 alongside version 2.

 * 3.9 introduces tail version 4.

## Geometry section

The section starts with a fixed-size header with counts and sizes of the following parts.

```
ubyte version;
uint32 triangle_count;
uint32 vertex_count;
byte const_04;
uint16 shape_count;
uint32 total_shape_name_cb;

float bbox[6];

struct {
    uint32 name_cb, triangle_offset;
} shape_info[shape_count];
```

The shape name list can be cheaply skipped by using the byte count from `total_shape_name_cb`.

```
local int i;
for(i = 0; i < shape_count; i++)
{
    struct {
        wchar_t name[shape_info[i].name_cb / 2];
    } shape;
}
```

Vertex indices are either 32-bit or 16-bit based on the vertex count.

```
if (vertex_count > 0xFFFF) {
    uint32 indices[triangle_count * 3];
}
else {
    uint16 indices[triangle_count * 3];
}

struct {
    float p[3];
    ubyte unk[8];
    hfloat uv[2];
    ubyte bones[4];
    ubyte weights[4];
} vertex[vertex_count];
```

## Tail section

The tail has two mapped versions, version 2 and version 3 and a largely unknown version 4.
The blocks in version 2 seem to be a subset of the blocks in version 3.
Both versions have some counts up-front and blocks of varying sizes with that many elements, with the following data types:

```
uint32 tail_version;

typedef struct {
    float f0[15];
    uint32 u60;
} tail_0_t;

typedef struct {
    float f0[4];
    uint32 u16;
} tail_1_t;

typedef struct {
    uint32 u0, u4;
} tail_2_t;

// tail_3_t is unknown, never occured in files

typedef struct {
    float vec0[3];
    uint32 index12[4];
    float weight28[4];
} tail_4_t;

typedef uint32 tail_5_t;
typedef uint32 tail_6_t;
```

```
if (tail_version == 3) {
    uint32 tail_counts[7];
    if (tail_counts[0] > 0) {
        tail_0_t sec0[tail_counts[0]];
    }
    if (tail_counts[1] > 0) {
        tail_1_t sec1[tail_counts[1]];
    }
    if (tail_counts[2] > 0) {
        tail_2_t sec2[tail_counts[2]];
    }
    // tail_counts[3] is always zero in all files, type unknown
    if (tail_counts[4] > 0) {
        tail_4_t sec4[tail_counts[4]];
    }
    // Sections 5 and 6 are both u32:s, might be swapped
    if (tail_counts[5] > 0) {
        tail_5_t sec5[tail_counts[5]];
    }
    if (tail_counts[6] > 0) {
        tail_6_t sec6[tail_counts[6]];
    }
}
```

Note that version 2 has the blocks in a strange order, both count-wise and compared to version 3.

It is possible that the mapping from v2 to v3 blocks I've made is not accurate, as the two u32-type blocks are hard to tell apart.

```
if (tail_version == 2) {
    uint32 tail_counts[4];
    if (tail_counts[1] > 0) {
        tail_4_t sec4[tail_counts[1]];
    }
    if (tail_counts[2] > 0) {
        tail_5_t sec1[tail_counts[2]];
    }
    if (tail_counts[0] > 0) {
        tail_0_t sec0[tail_counts[0]];
    }
    if (tail_counts[3] > 0) {
        tail_6_t sec3[tail_counts[3]];
    }
}
```
