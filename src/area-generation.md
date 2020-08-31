> `WorldAreas.dat` contains Topologies referencing dgrfile (Digital Graph Document) which points to MasterFile (`.tsi`) containing a `.rs` filename which in turn maps to `.arm` files.

- WorldAreas
    ```
    ID: 2
    Name: The Twilight Strand
    Topologies: 0, 1, 2
    ```

- Topologies
    ```
    ID: 0
    DGR: Metadata/Terrain/Act1/Area1/Graphs/macro_beach4.tgr
    ```

- `Metadata/Terrain/Act1/Area1/Graphs/macro_beach4.tgr`
    ```
    MasterFile: "Metadata/Terrain/Act1/Area1/master.tsi"
    ```

- `Metadata/Terrain/Act1/Area1/master.tsi`
    ```
    RoomSet                 "room_tiles.rs"
    ```

- `Metadata/Terrain/Act1/Area1/room_tiles.rs`
    ```
    version 2
    "Metadata/Terrain/Act1/Area1/Rooms/town_entrance_exclude.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/washed_up3.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/largecliff_wall_1a.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/largecliff_wall_2a.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/largecliff_wall_3a.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/largecliff_wall_4a.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/stream_shore_1.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/stream_shore_2.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/cliff_nests.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/cliff_nests2.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/cliff_nests3.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/cliff_beach.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/streams.arm"
    "Metadata/Terrain/Act1/Area1/Rooms/tutorial_01.arm"
    ```