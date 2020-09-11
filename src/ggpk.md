# GGPK Layout

The pack file is made up of tagged chunks with [Type-Length-Value](https://en.wikipedia.org/wiki/Type-length-value) structure, where a fixed-size header of chunk length and chunk type preceeds an amount of data particular to the type of the chunk.

Positions in the file are indicated by 64-bit offsets from the file start, there is no relative addressing and there are no implicit chunk sequences, if there is a relation to another chunk it's always by explicit offset.

The file begins with such a chunk of type `GGPK` which holds a list of byte locations in the file where top-level chunks of type `FREE` and `PDIR` may be found.

For the Standalone game in patch 3.11.2 the value previously thought to describe the number of direct child chunks of the `GGPK` chunk has increased from `2` to `3` without another chunk being introduced, which means that it's probably a version number. In that new scheme, the GGPK file mostly contains [bundles](bundles.md) of files instead of loose files, next to some video and audio assets.

Not all of the file contents are in active use. A singly-linked list of `FREE` chunks accounts unused space for use by the patcher or file compaction algorithms. Over time the pack may become fragmented as free space gets reused by chunks of varying size.

The root directory of the pack is stored in the `PDIR` chunk referenced by the `GGPK` chunk. A directory contains offsets to chunks of either `PDIR` or `FILE` type.

There are two hash algorithms used in the pack file, 32-bit Murmur2 accelerates pack entry lookups while [SHA-256](https://en.wikipedia.org/wiki/SHA-2) is used to hierarchically represent the contents of a directory tree for integrity checks and patching.

## Directory entries

A directory contains a sequence of entries that are either files or directories. The type of the entry cannot be determined from within the directory itself, that information resides in the referenced chunk.

The entries are stored in ascending 32-bit hash order according to [Murmur2](https://en.wikipedia.org/wiki/MurmurHash#MurmurHash2) over the bytes of the lowercased UTF-16 name of the entry, not including the 16-bit terminator. These hashes can be used to quickly determine if a name does not exist in a directory, and as they are ordered they can be used to perform a binary search for entries in a large directory.

## Signature computation

The 32-byte signature occurring in the `PDIR` and `FILE` chunks is computed with the SHA256 algorithm.

For a file, it is the SHA-256 signature of the file contents.

For a directory, the signature is the SHA-256 signature of the signature bytes of its entries in the order they occur in the directory.

For example, given this directory:
```
D              e0d86aeb850da3b98eae86ec84113b12bea463a2c551e847d92dea626672deda Art/2DArt/Cursors/
F        22000 8e2de0dada2f538b6b931575507ff3acfd4ac59fc08d17704e87a6669125a22e Art/2DArt/Cursors/cursor3.dds
F        22000 20fdff6eb128b1c4c343be9c08842345683bdf824ea65107b64503fb7083ddd3 Art/2DArt/Cursors/cursor2.dds
F        22000 4274f7e2ed49d9a3cbf1ff5455d03757fa51cc47d81d3750cf9b874d162dd3c9 Art/2DArt/Cursors/cursor1.dds
```
the signature is computed as such:
```
SHA256(
    8e2de0dada2f538b6b931575507ff3acfd4ac59fc08d17704e87a6669125a22e
    20fdff6eb128b1c4c343be9c08842345683bdf824ea65107b64503fb7083ddd3
    4274f7e2ed49d9a3cbf1ff5455d03757fa51cc47d81d3750cf9b874d162dd3c9
) = e0d86aeb850da3b98eae86ec84113b12bea463a2c551e847d92dea626672deda
```

## Chunk layouts

All chunks start with a header of 32-bit chunk length and four-octet tag, followed by a variable amount of data.

### GGPK
```cpp
u32 rec_len
char tag[4]
u32 version
u64 child_offsets[2]
```

### FREE
```cpp
u32 rec_len
char tag[4]
u64 next
u8 data[rec_len-16]
```

### FILE
```cpp
u32 rec_len
char tag[4]
u32 name_len
u8 signature[32]
u16 name[name_len] // terminated UTF-16LE
u8 data[rec_len - 12 - 32 - name_len*2]
```

### PDIR
```cpp
u32 rec_len
char tag[4]
u32 name_len
u32 child_count
u8 signature[32]
u16 name[name_len]
{
  u32 hash // murmur2_32 hash of lowercased wide leaf, without terminator
  u64 offset
}[child_count]
```
