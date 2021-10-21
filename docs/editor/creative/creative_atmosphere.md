# Creative Atmosphere

> Implemented as class `Expanse.CreativeAtmosphere` in `blocks/creative/CreativeAtmosphere.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/no_tint.jpg"/></div>
    </div>
</div>

This block is a more intuitive alternative to directly working with [Atmosphere Layers](editor/blocks/atmosphere_layer_block.md).

#### Air Block
**C# member variable:** `Expanse.AtmosphereLayer m_airBlock` \
[Atmosphere Layer](editor/blocks/atmosphere_layer_block.md) that this creative atmosphere controls. To set this up, you can create an Atmosphere Layer and set it to the preset "Rayleigh".

#### Ozone Block
**C# member variable:** `Expanse.AtmosphereLayer m_ozoneBlock` \
[Atmosphere Layer](editor/blocks/atmosphere_layer_block.md) that this creative atmosphere controls. To set this up, you can create an Atmosphere Layer and set it to the preset "Ozone".

#### Daytime Color
**C# member variable:** `Color m_daytimeColor` \
Color of the sky/atmosphere at daytime.

#### Sunset Color
**C# member variable:** `Color m_sunsetColor` \
Color of the sky/atmosphere at sunset.

#### Ozone
**C# member variable:** `float m_ozone` \
Amount of ozone gas. Increasing will make the sky more purple at sunset.

#### Thickness
**C# member variable:** `float m_thickness` \
How thick and dense the atmosphere is. Good for alien planets.