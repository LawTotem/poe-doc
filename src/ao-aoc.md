# AO / AOC file structure

This describes version 2 of the file format which is the only one that has existed in public since the release of PoE 1.0.

These two closely related file types contain a set of components that describe the visuals and effects of an object in the game world.

The files are UTF-16LE with a BOM and Windows CRLF newlines. They may have C-style `/* comments */` spanning multiple lines.

They begin with a version line which here is `version 2`. This is followed by one or more `extends "x"` lines which either references another file by full path or is the special string `"nothing"` for bases.

## Components

Components are denoted by an unquoted name and the component members inside curly braces - an empty component looks like `ClientAnimationController { }`. Components may appear multiple times in the same file as "forward declarations" according to comments in the similar OT/OTC file formats.

The members of a component are indented with one tab and seem to be able to have values of string, boolean, integer and floating point types like:
```
    x = "hi there"
    y = true
    z = 3.1415f
    w = 10
```

Some component members have a list of additional lines, indented by two tabs. These include `animation` and `continuous_effect`.

```
	animation = "throw"
		2.25226 = "throw_trl Metadata/Particles/monster_effects/act10/oriath_children/apple_blast.pet"
```

```
    animation = "throw"
		2.12065,0.209613 = "throw_trl Metadata/Particles/monster_effects/act10/oriath_children/throw_trl.trl"
```

```
	continuous_effect = "wake vortex"
		size = "100.0 100.0 100.0"
		duration = 5.0
		is_persistent = true
		shape = "Wake"
		is_attached = true	
		bone_name = "root"
```

The possible components seem to be documented in the files for `Parent_AssetViewer` as follows:

`Metadata/Parent_AssetViewer.ao`
```
version 2
extends "nothing"

/*
    This file contains all components needed by an AO.
    Strictly for use in the asset viewer!
*/

BaseAnimationEvents { }
AnimationController { }
AttachedAnimatedObject { }
Hull { }
```

`Metadata/Parent_AssetViewer.aoc`
```
version 2
extends "nothing"

/*
    This file contains all components needed by an AO.
    Strictly for use in the asset viewer!
*/

ClientAnimationController { }
AnimatedRender { }
SkinMesh { }
SoundEvents { }
BoneGroups { }
ParticleEffects { }
TrailsEffects { }
DecalEvents { }
Lights { }
EffectPack { }
ScreenShake { }
WindEvents {}
SoundParams { }
```

## Observed inconsistencies and mistakes

Some files seem to bear evidence of having been hand-edited with quoting mistakes and indentation errors.

One file has an errant tab at the start of the version line:

```
    version 2
```

One file has two excess tabs before an animation block, putting it deeper than the keyframes of the surrounding animations:

```
    animation = "a"
        0.1234 = "..."
            animation = "b"
        0.1234 = "..."
    animation = "c"
        0.1234 = "..."
```

One file has unbalanced quoted strings in an animation block, where one particular string ends with two double quotes:

```
		0.432554 = "Audio/Sound Effects/Footstep/Creak/Creak_Walk_$(#).ogg%40@0.3 110 0 0.1 0.1 0 2 0 24 3.5"
		0.857914 = "Audio/Sound Effects/Footstep/Creak/Creak_Walk_$(#).ogg%40@0.3 110 0 0.1 0.1 0 2 0 24 3.5""
```

There are some lines with excess whitespace at the end.