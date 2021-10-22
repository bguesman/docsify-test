# Creative Cloud Volume

> Implemented as class `Expanse.CreativeCloudVolume` in `blocks/creative/CreativeCloudVolume.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/1-4-0/alone.jpg"/></div>
    </div>
</div>

This block is a more intuitive alternative to directly working with the [Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md).

## Backing Components

#### Cloud Volume
**C# member variable:** `Expanse.ProceduralCloudVolumeBlock m_cloudVolume` \
[Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md) that this creative cloud volume controls.

## Preset Management

As of v1.5, the preferred way to author, save, and load presets for cloud layers is via the Preset Browser, or, programmatically, the `SaveUniversal()` and `LoadUniversal()` functions.

Expanse stores cloud presets in a "universal" format---universal meaning, "unified across the different types of cloud layers" (2D, 3D, procedural, texture-based, etc.). This universal representation is serialized as a [`ScriptableObject`](https://docs.unity3d.com/ScriptReference/ScriptableObject.html) on save.

What this means for you is that, to save a preset from editor, you can just click the "Save Preset" button, and a file dialogue will pop up for you to specify the path to save the preset file to. A screen capture of the currently rendering main camera will automatically be used as preset image---so line up your shot carefully!

To then load that preset, you can browse for it in the preset browser. You may have to hit `Refresh Library` to get it to show up.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width: 80%" src="img/procedural_cloud_volume/preset-browser.jpg"/></div>
    </div>The cloud preset browser.</p>
</div>

To do this programmatically, you can use the `SaveUniversal()` and `LoadUniversal()` functions, defined in `blocks/advanced/BaseCloudLayer.cs`, which all cloud layers derive from. Each of these functions accepts a string filepath to the preset to save to/load from.

You can also use [Unity's preset system](https://docs.unity3d.com/Manual/Presets.html) to manage presets, but this has the disadvantage of not being runtime-compatible (you can only select presets in the editor). In fact, the entire reason this ScriptableObject-based preset system exists is to get around this unfortunate limitation of Unity presets.

## Modeling

These parameters control the overall shape and cover of the clouds.

#### Coverage
**C# member variable:** `float m_coverage` \
Amount that clouds cover the sky.

#### Swirl
**C# member variable:** `float m_swirl` \
How swirly the clouds look. This is really good for modeling high, wispy cirrus clouds.

#### Wind
**C# member variable:** `Vector2 m_wind` \
Speed of the wind that moves the clouds across the sky, in the x and z directions.

#### Quality
**C# member variable:** `Expanse.Datatypes.Quality m_quality` \
Quality of the cloud textures. Reducing this can help improve performance.

#### Geometry Extents and Origin
As of v1.5, the origin and size of the cloud volume are controlled via the Unity `GameObject` transform.

## Lighting

These parameters control the illumination characteristics of the clouds.

#### Density
**C# member variable:** `float m_density` \
Density of the clouds.

#### Shadowing
**C# member variable:** `float m_shadowing` \
How self-shadowed the clouds are. Lower values will make the clouds appear brighter. Higher values will make them darker.

#### Ambient
**C# member variable:** `float m_ambient` \
How much ambient light the clouds receive. This is crucial to adjust to make sure the clouds don't look to dark and murky (unless, of course, that's what you're going for!).

#### Multiple Scattering
**C# member variable:** `float m_multipleScattering` \
How much multiple scattering occurs within the clouds.

#### Silver Lining
**C# member variable:** `float m_silverLining` \
Controls the intensity of the silver lining of the clouds near the sun.

#### Silver Lining Spread
**C# member variable:** `float m_silverLiningSpread` \
How tight the silver lining is around the sun.

#### Raininess
**C# member variable:** `float m_raininess` \
Darkens the clouds to make them appear like they are saturated with rain. When this is 0, the effect is turned off. When this is 1, the effect is at its maximum.