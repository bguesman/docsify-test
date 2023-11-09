# Creative Sun

> Implemented as class `Expanse.CreativeSun` in `blocks/creative/CreativeSun.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/sun.jpg"/></div>
    </div>
</div>

This component is a more intuitive alternative to directly working with [Celestial Bodies](editor/blocks/celestial_body_block.md).

#### Sun

**C# member variable:** `Expanse.CelestialBody m_sunBlock` \
[Celestial Body](editor/blocks/celestial_body_block.md) that this creative sun controls. To set this up, you can create a Celestial Body and set it to the preset "Sun".

#### Use Time Of Day

**C# member variable:** `bool m_useTimeOfDay` \
Whether or not to use time of day to control the sun's direction.

#### Date Time Controller

**C# member variable:** `Expanse.DateTimeBlock m_dateTimeBlock` \
[Date Time Controller](editor/blocks/date_time_block.md) that will control this sun, if this sun is set to use time of day for its direction.

#### Direction

**C# member variable:** \
The direction of the sun in the sky is controlled by the respective `GameObject`'s rotation. It is essentially the same system you'd use for adjusting a directional light.

#### Size

**C# member variable:** `float m_size` \
Size of the sun in the sky. 1 is physical.

#### Light Brightness

**C# member variable:** `float m_lightBrightness` \
Brightness of the sunlight, in Lux. 150000 is roughly physical.

#### Light Tint

**C# member variable:** `Color m_lightTint` \
Tint to the light.

#### Disc Brightness

**C# member variable:** `float m_discBrightness` \
Brightness of the sun disc itself.

#### Disc Tint

**C# member variable:** `Color m_discTint` \
Tint to the sun disc.

#### Above Horizon

**C# function:** `bool AboveHorizon()` \
Returns true when the celestial body is above the horizon, and false otherwise. This is the best way to adjust which celestial body's light casts directional shadows, and to script other visibilty-dependent things that are generally tied to time of day in an ad hoc way.
