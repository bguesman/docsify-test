# Creative Cloud Volume

> Implemented as class `Expanse.CreativeCloudVolume` in `blocks/creative/CreativeCloudVolume.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0.6.jpg"/></div>
    </div>
</div>

This block is a more intuitive alternative to directly working with the [Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md).

#### Cloud Volume
**C# member variable:** `Expanse.ProceduralCloudVolumeBlock m_cloudVolume` \
[Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md) that this creative cloud volume controls.

#### Coverage
**C# member variable:** `float m_coverage` \
Amount that clouds cover the sky.

#### Height
**C# member variable:** `float m_height` \
Height of the clouds above the ground.

#### Thickness
**C# member variable:** `float m_thickness` \
Thickness of the cloud layer.

#### Density
**C# member variable:** `float m_density` \
Density of the clouds.

#### Shadowing
**C# member variable:** `float m_shadowing` \
How self-shadowed the clouds are. Lower values will make the clouds appear brighter. Higher values will make them darker.

#### Ambient
**C# member variable:** `float m_ambient` \
How much ambient light the clouds receive. This is crucial to adjust to make sure the clouds don't look to dark and murky (unless, of course, that's what you're going for!).

#### Silver Lining
**C# member variable:** `float m_silverLining` \
Controls the intensity of the silver lining of the clouds near the sun.

#### Silver Lining Spread
**C# member variable:** `float m_silverLiningSpread` \
How tight the silver lining is around the sun.

#### Raininess
**C# member variable:** `float m_raininess` \
Darkens the clouds to make them appear like they are saturated with rain. When this is 0, the effect is turned off. When this is 1, the effect is at its maximum.

#### Swirl
**C# member variable:** `float m_swirl` \
How swirly the clouds look. This is really good for modeling high, wispy cirrus clouds.

#### Wind
**C# member variable:** `Vector2 m_wind` \
Speed of the wind that moves the clouds across the sky, in the x and z directions.

#### Quality
**C# member variable:** `Expanse.Datatypes.Quality m_quality` \
Quality of the cloud textures. Reducing this can help improve performance.