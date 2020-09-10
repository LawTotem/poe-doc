# Bundle file scheme

As of patch 3.11.2 for Harvest preparing for the launch of Heist the distribution method and patching of Path of Exile changed from the previous monolithic `Content.ggpk` file to a system of multiple bundles more akin to what has previously been deployed for console game clients.

On Steam there are tens of thousands of bundle files each containing related assets which makes it easier for Steam to do its write-a-new-file type of atomic patching as files will be smaller. In the Standalone client the bundles are contained in a `Content.ggpk` with a bogus node count of 3 but retaining the existing `PDIR` and `FILE` structure for patching.

## Bundle file format
Bundles start with a fixed header indicating among other things what appears to be the uncompressed(?) bundle size and the total size of payloads in the bundle. There's a list of absolute sizes for entries in the file, whose length sum up to the total payload size.

A work-in-progress 010 Editor template for the file:
```
uint32 uncompressed_size;
uint32 total_payload_size;
uint32 unk08[3];
uint32 uncompressed_size2;
uint32 unk18;
uint32 total_payload_size2;
uint32 unk20;
uint32 entry_count;
uint32 unk28[5];
uint32 entry_sizes[entry_count];

local int i <hidden=true>;
for (i = 0; i < entry_count; ++i) {
	struct entry_t {
		byte data[entry_sizes[i]];
	} entry;
}
```

File entries seem to as far as have been observed start with the octets `8C 0C` or `8C 06`, with some slivers of plaintext like `"DDS "` occurring in the payload but with bogus values for fields, hinting at some sort of compression.