# Bundle file scheme

As of patch 3.11.2 for Harvest preparing for the launch of Heist the distribution method and patching of Path of Exile changed from the previous monolithic `Content.ggpk` file to a system of multiple bundles more akin to what has previously been deployed for console game clients.

On Steam there are tens of thousands of bundle files each containing related assets which makes it easier for Steam to do its write-a-new-file type of atomic patching as files will be smaller. In the Standalone client the bundles are contained in a `Content.ggpk` with a bogus node count of 3 but retaining the existing `PDIR` and `FILE` structure for patching.

## Bundle index format
At the top level of the bundle tree there are some files beginning with underscores that seem to be detached from the typical directory structure, two of which seem to form the root of the hierarchy: `_.index.bin` and `_.index.txt`.

The UTF-8 encoded text file contains more than half a million lines of path-like strings. The file seems to be loosely delineated by a bundle basename followed by a sequence of files that might belong to that bundle.

The contents of the corresponding `.bin` file is not yet known but is likely to correlate with the lines of the `.txt` file.

## Bundle file format
Bundles start with a fixed header indicating among other things what appears to be the uncompressed(?) bundle size and the total compressed size of payloads in the bundle. There's a list of absolute sizes for compressed chunks in the file, whose length sum up to the total compressed payload size.

A work-in-progress 010 Editor template for the bundle file:
```
uint32 uncompressed_size;
uint32 total_payload_size;
uint32 head_payload_size;
struct head_payload_t {
    enum <uint32> {Kraken_6 = 8, Mermaid_A = 9, Leviathan_C = 13 } first_file_encode;
    uint32 unk10;
    uint64 uncompressed_size2;
    uint64 total_payload_size2;
    uint32 entry_count;
    uint32 unk28[5];
    uint32 entry_sizes[entry_count];
} head;
local int i <hidden=true>;
for (i = 0; i < head.entry_count; ++i) {
    struct entry_t {
        byte data[head.entry_sizes[i]];
    } entry;
}
```

File chunks seem to start with octet pairs like `8C 0C` and `8C 06`, with some slivers of plaintext like `"DDS "` occurring in the payload. These are Oodle headers for the Kraken and Leviathan compression methods. All decompressed chunk sizes but the last one seem to be 256k in size, which incidentally seems to be the value in `unk28[0]`.