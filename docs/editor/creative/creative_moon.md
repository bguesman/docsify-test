# Creative Moon

> Implemented as class `Expanse.CreativeMoon` in `blocks/creative/CreativeMoon.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/moon.jpg"/></div>
    </div>
</div>

This component is a more intuitive alternative to directly working with [Celestial Bodies](editor/blocks/celestial_body_block.md).

#### Moon

**C# member variable:** `Expanse.CelestialBodyBlock m_moonBlock` \
[Celestial Body](editor/blocks/celestial_body_block.md) that this creative moon controls. To set this up, you can create a Celestial Body and set it to the preset "Earth Moon".

#### Use Time Of Day

**C# member variable:** `bool m_useTimeOfDay` \
Whether or not to use time of day to control the moon's direction.

#### Date Time

**C# member variable:** `Expanse.DateTimeBlock m_dateTimeBlock` \
[Date Time Controller](editor/blocks/date_time_block.md) that will control this moon, if this moon is set to use time of day for its direction.

#### Direction

**C# member variable:** \
The direction of the moon in the sky is controlled by the respective `GameObject`'s rotation. It is essentially the same system you'd use for adjusting a directional light.

#### Size

**C# member variable:** `float m_size` \
Size of the moon in the sky. 1 is physical.

#### Light Brightness

**C# member variable:** `float m_lightBrightness` \
Brightness of the moon's light, in Lux.

#### Light Tint

**C# member variable:** `Color m_lightTint` \
Tint to the light.

#### Texture

**C# member variable:** `Cubemap m_texture` \
Albedo texture that the moon uses.

#### Rotation

**C# member variable:** `Vector3 m_rotation` \
Rotation of the moon's texture.

#### Surface Tint

**C# member variable:** `Color m_surfaceTint` \
Tint to the moon's albedo.

#### Above Horizon

**C# function:** `bool AboveHorizon()` \
Returns true when the celestial body is above the horizon, and false otherwise. This is the best way to adjust which celestial body's light casts directional shadows, and to script other visibilty-dependent things that are generally tied to time of day in an ad hoc way.
