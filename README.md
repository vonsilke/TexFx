[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/sinsofseven)

# TexFx
> GIMI Texture Extention Shaders

[GameBanana Link](https://gamebanana.com/mods/485763)

### Usage Notes
TexFx textures encode effects in the ps-t69 and ps-t70 slot.

#### Config.ini
Inside the TexFx folder, there is a config.ini change some major settings which can impact some mods. The `$seizure` setting can be set to 1 or 0 to prevent mods which might have flashing or strobing effects with RGB. Please make sure to review this setting if you do or don't want it. It is set to `0` off by default.

#### TexFx Effects Mask (t69):

Transparency (Opacity) on the RED channel

Emissives (Brightness) on the GREEN channel

Bloom Intensity (Glow) on the BLUE channel.

HSV Shift on the ALPHA channel.

Channel | Value |Effect
:-      |:-     |:-
RGBA    | 0     | Do nothing
Red     | 1-254 | Transparent
Red     | 255   | Discard (Skip Rendering)
Green   | 1-255 | Shadow - Bright
Blue    | 1-255 | Bloom
Alpha   | 1-63  | Variable Color Replace

`$\TexFx\glow_intesity` and `$\TexFx\bloom_intesity` may be used to change the intesity of Green and Blue, or `$\TexFx\hue`, `$\TexFx\sat`, `$\TexFx\val` of the Alpha lower segment.

**run = CommandList\TexFx\SetIV** now required for effects for \T.

It is recommended to set unaffected parts to `ps-t69 = null`

```ini
; Example:
[TextureOverrideHead]
hash = 1234abcd
match_first_index = 0
ib = ResourceIB
ps-t0 = ResourceHeadNormalMap
ps-t1 = ResourceHeadDiffuse
ps-t2 = ResourceHeadLightMap
ps-t69 = ResourceTexFxMap
run = CommandList\TexFx\T.1
;run = CommandList\TexFx\SetIV
;   \Transparency.0 (2.9-) Part has no Normal Map
;   \Transparency.1 (3.0+) Part has a Normal Map
;
;   -- Version 1.04, shorthand Aliases are prefered. --
;   commandlist\texfx\T.0
;   commandlist\texfx\T.1
```

#### TexFx Custom Outlines (t70):
> Thanks to Annplan for Sponsoring this feature!

The RGB channels will be directly assigned to the outline color if there is a texture in ps-t70. Applicable to Characters, Weapons, NPCs, Monsters and More!

```ini
[TextureOverrideFavoniusGreatsword]
hash = 945cd6d3
match_priority = 0
ps-t70 = ResourceFaveSwordOutline

[ResourceFaveSwordOutline]
filename = FaveSwordOutline.dds
```

```ini
; Notable TexFx Words and Aliases.

; TexFx CommandLists for Default Transparency.
run = CommandList\TexFx\T
run = CommandList\TexFx\T.0
run = CommandList\TexFx\Transparency
run = CommandList\TexFx\Transparency.0
;-slot 0 normal maps
run = CommandList\TexFx\T.1
run = CommandList\TexFx\Transparency.1

; TexFx CommandLists for Default Component based Transparency.
run = CommandList\TexFx\C
run = CommandList\TexFx\C.0
run = CommandList\TexFx\Component
run = CommandList\TexFx\Component.0
;-slot 0 normal maps
run = CommandList\TexFx\C.1
run = CommandList\TexFx\Component.1

; Disable the built in "Hull Hack" which ignores vertex colors which offsets transparency.
run = CommandList\TexFx\Shh
run = CommandList\TexFx\SupHH
run = CommandList\TexFx\SupressHullHack

; Force Outline for Transparent parts on/off
run = CommandListFO.E
run = CommandListFO.Enable
run = CommandListForceOutline.Enable
run = CommandListFO.D
run = CommandListFO.Disable
run = CommandListForceOutline.Disable

; Use when setting the hsv or glow/bloom if using \T
run = CommandList\TexFx\SetIV

; To multiply your Green and Blue Channels
; note, the 2nd options are shorthands.
$\TexFx\bloom_intesity
$\TexFx\bloom
$\TexFx\glow_intesity
$\TexFx\glow
; TexFx Alpha Channel, Variable Color Replace.
$\TexFx\hue
$\TexFx\sat
$\TexFx\val
; TexFx version number
$\TexFx\version
; Draw Indexed Carriers for Components
$\TexFx\_1
$\TexFx\_2
```

#### Keybindings
Rename `DISABLED_Keybindings.ini`, it is disabled by default to preserve your custom keybindings between updates.

#### Censorship Patch
TexFx conflicts with Silent's ShaderFixes mod, so it includes it's own version.
Press `|` (pipe key) To toggle the censorship patch (Remove Transparency Filter)

#### Hull Hack (Vertex Color Fix)
Hold `|+Shift`to preview the HullHack Effect (Enabled->Disable while holding).
If you prefer the old way, you can add **one** of these commands to your mod. (Alliases)
`run = CommandList\TexFx\SupressHullHack`
`run = commandlist\texfx\suphh`
`$\texfx\hull_hack = 0`

#### TexFx for Components (OutfitCompiler)
`hic_sunt_dracones/ComponentFriendlyBuiltinShaders.ini`
This introduces some new concepts that allow us to use TexFx in a new and exciting way, we can use it to create "Materials" or "Sub Parts" like a pair of sunglasses which which can have thier own draw passes! We can create other custom shaders and effects, but for now I've included two default ones which use normal TexFx behavior.

The syntax is verbose, but it's a price we must pay for power.

I recommend using the `.0` version even for 3.0+ characters so it is compatable with ORFix, due to a technical limit, it would be too hard to hard to fix it like `\Transparency.1`
```ini
; Use Example
if $Hood == 1
    ps-t0 = ResourceDiffuse
    ps-t1 = ResoourceLightmap
    ps-t69 = ResourceTexFxMask
    $\texfx\glow_intensity = $VAR1
    $\texfx\bloom_intensity = $VAR2
    $\TexFx\_1 = <DRAWINDEX>
    $\TexFx\_2 = <DRAWOFFSET>
    run = CommandList\TexFx\Component.0
endif
```

### Troubleshooting

#### If you're having problems with Shader Mods:
Try setting the remaining body parts like this `ps-t69 = null`. You do not need to call the commands, texfx is suppose to do this automatically, but it isn't always able to do it properly, and this can help prevent unexpected behaviors, even with unrelated mods. In 4.5 there is a known issue with some mods in the some menus which can be fixed with this.

The `[Rendering]` setting `cache_shaders` from the `d3dx_overrides.ini` can sometimes prevent shaders from properly reloading. It will drop your FPS drop some when you first visit new areas after installing or updating the mod.

You may also clear your ShaderFixes folder of unwanted shaders. If experienceing issues, you can rename this folder and reload to see if it replairs the issue. In which case you may investigate further.

#### If this mod crashes your game:
Please share with me as many details as possible, like what GPU you have, and if you're using the latest GitHub or GameBanana version.
You can leave a comment on GameBanana or make a GitHub Issue.

#### If you have texture issues (wrong textures):
Just try changing which version of the command you're using.
> \Transparency.1 -> \Transparency.0 or the other way around.

#### Incompatible
> [!WARNING]
> Severe: SilentNightSounds [Remove Transparency Filter](https://gamebanana.com/mods/406659)
> This will likely cause transparent parts to be drawn over the original, resulting in no transparency and a slight glow.

#### Recommended
[ORFix](https://github.com/leotorrez/LeoTools/blob/main/releases/ORFix.ini)
[Underwater Uncensor](https://gamebanana.com/mods/462790)

### Extensions
Extensions are add-on type mods that are intended to be used with TexFx Main, but will not guarantee compatibility between versions. For example, I already regret the name of the first extension, because the numbers are backwards for the name. The exensions folder `denn_die_todten_reiten_schnell` may be renamed in the near future as well.

The intent is to allow anyone to make extensions for the main mod. This could include new effect shaders, new pre-made effects, or utilities. As I add features this should be a lot more appealing, but for now I have just added a very early example and the first utility mod.

#### Int Opacity (Deprecated)
> [!TIP]
> This Extension will be removed in favor of a different tool called [FakeLightmap](https://github.com/SinsOfSeven/SliderImpact/tree/main/FakeLightmap), a Virtual Texture mod which can be used to quickly manipulate texture values in game!
The `int_opacity` extension is zipped in `denn_die_todten_reiten_schnell`
Just unzip and it will enable the following commands. (Requires Main)
```ini
; X is any number from 0-255, 
; 0.X and 1.X follow the same convention as TexFx.
run = CommandList\int_opacity\0.X
run = CommandList\int_opacity\1.X
```