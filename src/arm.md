# ARM Layout

ARM files are line-based text files describing composable "rooms". There are several variable-length lists where the length is derived from numbers occuring earlier in the file.

They describe a rectangular area filled with rectangular tiles of varying sizes and with a long list of per-tile attributes. There are also lists of things like doodads and decorative decals.

Examples below mostly taken from Glacier's `hideout.arm`.

## Header
Literal `"version"` followed by integer, ex. `version 29`.

## Entity table
Integer count followed by that many quoted strings.

## Dimensions?
* v14-v21: Two integers, ex. `5 5`.
* v22-v29: Three integers, ex. `27 27 1`.

## Counted apa?
Two integers, ex. `2 3`.

## Name?
A possibly empty literal like `"hideout"`.

## Bepa table?
v14-v15: Single integer.

v16+: Two integers.

## K-entry (solo)
One k-entry `k 27 27 0 0 0 0 81 81 40 46 81 81 25 31 1 1 6 6 0 0 0 0 0 0`. The first two values indicate the width and the height of the whole thing.

The solo K-line has been observed to also be a `s` entry.

## Apa entries
`2 * (apa.0 + apa.1)` lines of integer pairs.

Followed by nine (v14) or six (v29) counted lists of various kinds of entities:

Integer count followed by a line like `364 482 0.872667 "default"`. X/Y/Z position + an identifier?

## K-lines
Integer count of lines, which contains mixes of `f`, `k`, `n`, `o`, `s` and numbers at least `22` large.

* `s` is probably empty or some sort of base;
* `n` is a filler tile that is covered by a larger `k` tile;
* `o` is unknown, appears in later versions;
* `f` is followed by a single number.

`k` is followed by 23 numbers in v14-v18; 24 numbers in v19-29. The first two are the dimensions of that k-tile, the rest are possibly negative numbers.

A `k`-tile of a size larger than 1x1 covers neighbouring `n`-tiles or may cover area outside of the width/height indicated in the solo k-entry.

In versions (v14-v18) lacking the 24ths field the `k`-tile covers tiles in +X and +Y directions.

In versions (v19+) the direction is indicated by the 24th field:

 * 0: +X and +Y, tile is in southwest corner of area - same as old versions;
 * 1: -X and +Y, tile is in southeast corner of area;
 * 2: -X and -Y, tile is in northeast corner of area;
 * 3: +X and -Y, tile is in northwest corner of area.

Example of line:
```
k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 3 3 5 5 0 0 4 9 4 9 9 9 9 9 1 0 1 1 0 0 0 0 0 0 n n k 3 9 0 14 8 14 9 9 4 22 8 9 4 22 0 0 0 0 0 0 0 0 15 0 n n k 3 3 5 0 0 5 4 9 9 9 9 9 4 9 0 1 1 1 0 0 0 0 0 0 n n k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0 k 1 1 0 0 0 0 3 3 3 3 3 3 3 3 1 1 1 1 0 0 0 0 0 0
```

## Assorted footer lists

A sequence of counted lists follow, varying between versions.

v14:
* doodads
* decals

v15:
* doodads
* decals
* zero?

v29:
* doodads
* always zero?
* decals
* zero?
* zero?
* zero?

### Doodads
Integer count of lines, which contains a sequence of eleven numbers and two quoted strings, like:
```
300 470 1.5708 -5.47648e-13 -1.23634e-07 0.707107 0.707107 0 0 0 1 "Metadata/Terrain/Doodads/Encampment/guildstash_v01_01.ao" "Metadata/MiscellaneousObjects/GuildStash"
609 330 -0 -0 0 -0 1 0 0 0 3.7 "Metadata/Terrain/Doodads/Tools/invisible_blocker.ao" "Metadata/MiscellaneousObjects/Doodad"
```
The first two numbers seems to be integers, followed by three reals in scientific notation, where the first number often is pi or half-pi. 
This is followed by a pair of numbers whose magnitude is around 1, probably an 2D orientation.

Finally three numbers (always 0 thus far) occur, followed by a single real that's often 1 but sometimes small values like 3.7.

### Decals
To be described.

### Trailing line
A line that often but not always has a number of elements that is related to the k-area extents like (width - 1) * (height - 1), that's to say, the number of 4-connections between tiles.

It's often as sequence of small integers - typically zero and one more than the number of gtets.
Sometimes this line has a lot of quoted strings - typically empty and sometimes with short contents.
Sometimes this line has twice the number of elements and then has the strings first and numbers later.
In some rare case the line has a few more elements than the predicted extents.