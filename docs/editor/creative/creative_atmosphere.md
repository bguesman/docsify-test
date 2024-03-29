# Creative Atmosphere

> Implemented as class `Expanse.CreativeAtmosphere` in `blocks/creative/CreativeAtmosphere.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/no_tint.jpg"/></div>
    </div>
</div>

This component is a more intuitive alternative to directly working with [Atmosphere Layers](editor/blocks/atmosphere_layer_block.md).

#### Air Layer
**C# member variable:** `Expanse.AtmosphereLayer m_airLayer` \
[Atmosphere Layer](editor/blocks/atmosphere_layer_block.md) that this creative atmosphere controls. To set this up, you can create an Atmosphere Layer and set it to the preset "Rayleigh".

#### Ozone Layer
**C# member variable:** `Expanse.AtmosphereLayer m_ozoneLayer` \
[Atmosphere Layer](editor/blocks/atmosphere_layer_block.md) that this creative atmosphere controls. To set this up, you can create an Atmosphere Layer and set it to the preset "Ozone".

#### Smog Layer
**C# member variable:** `Expanse.AtmosphereLayer m_smogLayer` \
[Atmosphere Layer](editor/blocks/atmosphere_layer_block.md) that this creative atmosphere controls. To set this up, you can create an Atmosphere Layer and set it to the preset "Aerosols".

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

#### Smogginess
**C# member variable:** `float m_smogginess` \
How smoggy the atmosphere is. Upping this can be good for city environments.

#### Smog Saturation
**C# member variable:** `float m_smogSaturation` \
How close the smog color is to the atmosphere color. Can help reintroduce some color to otherwise very grey smog.