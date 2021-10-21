# Creative Fog

> Implemented as class `Expanse.CreativeFog` in `blocks/creative/CreativeFog.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/with_isotropic_fog.jpg"/></div>
    </div>
</div>

This block is a more intuitive alternative to directly working with [Atmosphere Layers](editor/blocks/atmosphere_layer_block.md).

#### Fog Block
**C# member variable:** `Expanse.AtmosphereLayer m_fogBlock` \
[Atmosphere Layer](editor/blocks/atmosphere_layer_block.md) that this creative fog controls. To set this up, you can create an Atmosphere Layer and set it to the preset "Screenspace Aerosols".

#### Color
**C# member variable:** `Color m_color` \
Color of the fog. Keeping this relatively grey is a good move if you're going for realism.

#### Density
**C# member variable:** `float m_density` \
Density of the fog.

#### Radius
**C# member variable:** `float m_radius` \
How far away from the player the fog extends.

#### Thickness
**C# member variable:** `float m_thickness` \
How high off the ground the fog extends.

#### Smog
**C# member variable:** `float m_smog` \
How smoggy the fog looks. When this is zero, the fog is clean and bright. When this is one, the fog is dark and smoggy.

#### Glare
**C# member variable:** `float m_glare` \
How intense the sun glare from the fog is.

#### Physical Lighting
**C# member variable:** `bool m_physicalLighting` \
Whether or not to use physical lighting. Makes little to no difference except in scenarios where the camera is outside of the fog volume.

#### Geometry Shadows
**C# member variable:** `bool m_geometryShadows` \
Whether or not to render volumetric geometry shadows.

#### Cloud Shadows
**C# member variable:** `bool m_cloudShadows` \
Whether or not to render volumetric cloud shadows.