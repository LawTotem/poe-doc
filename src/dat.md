# DAT Layout

The `.dat` family of files store a lot of the metadata in tabular format. There are four variations on the format:

* `.dat` - 32-bit references and UTF-16LE strings;
* `.dat64` - 64-bit references and UTF-16LE strings;
* `.datl` - 32-bit references and UTF-32LE strings;
* `.datl64` - 64-bit references and UTF-32LE strings.

The `l` variant was introduced with the macOS client but all four variants are present on all the PC platforms.

## 32-bit DAT

A `dat` file has two parts, one fixed-width and one with free-form data.

The fixed-width section has a leading `u32` with the number of rows, followed by tightly packed rows with fixed-width fields adhering to an implicit specification that is not known.

The free-form section starts with a delimiter of eight `0xBB` bytes, making it possible to determine the expected width of a row by searching the file for the delimiter. All offsets into the free-form section include the delimiter so the first byte of valid data is at offset `8`.

The fields contain either:

* inline primitive fixed-width data,
* offsets into the free-form section with field data,
* offsets into the free-form section to a null-terminated string literal,
* offsets and count for a list of field elements,
* foreign integer keys into other tables.

Fields may represent `NULL`/`None` by repeating `0xFE`. Note that fields in the free-form section can again be references to the free-form section, a row field can for example be `list-of-string` or `list-of-list-of-int`.

A list field entry contains two `u32`, one with the element count and one with the offset into the free-form section.

A string field entry has a single `u32` with the offset into the free-form section. This offset has been observed to be to the end of the free-form section so make sure to check bounds.

## 64-bit DAT

64-bit DAT files are like 32-bit DAT files, except that some types are doubled in size to reflect the 64-bit `size_t` of the platform. These include reference offsets, foreign keys, and list counts.

## External specification sources

There are some community efforts of guessed specifications that have been built over time, these include:

 * [PyPoE](https://github.com/OmegaK2/PyPoE/), which has an extensive [`stable.py`](https://github.com/OmegaK2/PyPoE/blob/dev/PyPoE/poe/file/specification/data/stable.py) specification that can be exported into ordered JSON;
 * [PathOfBuilding](https://github.com/PathOfBuildingCommunity/PathOfBuilding/), which has similar information in [`Export/spec.lua`](https://github.com/PathOfBuildingCommunity/PathOfBuilding/blob/dev/Export/spec.lua), detailing the tables and fields they use.
