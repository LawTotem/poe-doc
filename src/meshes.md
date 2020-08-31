Objects in the game are specified by `.ao/.aoc` files which may reference other files for fixed meshes, rigs and skinned meshes. Most `.ao` files have similarly named `.aoc` files which contain client-only information about an item that the server does not need like their visuals and sounds. The `.ao` files often appear in lists in `.dat` tables to connect the logical ID of an item with their drop models and 3D art.

An example relationship for the Starforge item:

`ItemVisualIdentityID` is `UniqueTwoHandSword12` referencing `Metadata/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.ao` for the drop model and `Metadata/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdge.ao` for the held 3D art.

The objects/meshes involved are:
```
Art/Models/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.amd
Art/Models/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.ast
Art/Models/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.sm
Art/Models/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdge.fmt
Metadata/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdge.ao
Metadata/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdge.aoc
Metadata/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.ao
Metadata/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.aoc
```

The held model's appearance is governed by these components:
```
FixedMesh
{
	fixed_mesh = "Art/Models/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdge.fmt"
}

ParticleEffects
{
	continuous_effect = "blade Metadata/Effects/Microtransactions/Weapon_effects/uniques/StarForge/blade.pet"
}

TrailsEffects
{
	continuous_effect = "blade Metadata/Effects/Microtransactions/Weapon_effects/uniques/StarForge/blade.trl"
}
```

The drop model's appearance is goverened by these components:
```
ClientAnimationController
{
	skeleton = "Art/Models/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.ast"
}

SkinMesh
{
	skin = "Art/Models/Items/Weapons/TwoHandWeapons/TwoHandSwords/uniques/StarforgedEdgeDrop.sm"
}
```