# Creative Sun

> Implemented as class `Expanse.CreativeSun` in `blocks/creative/CreativeSun.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/sun.jpg"/></div>
    </div>
</div>

This block is a more intuitive alternative to directly working with [Celestial Body Blocks](editor/blocks/celestial_body_block.md).

#### Sun Block
**C# member variable:** `Expanse.CelestialBodyBlock m_sunBlock` \
[Celestial Body Block](editor/blocks/celestial_body_block.md) that this creative sun controls. To set this up, you can create an Celestial Body Block and set it to the preset "Sun".

#### Use Time Of Day
**C# member variable:** `bool m_useTimeOfDay` \
Whether or not to use time of day to control the sun's direction.

#### Date Time Block
**C# member variable:** `Expanse.DateTimeBlock m_dateTimeBlock` \
[Date Time Block](editor/blocks/date_time_block.md) that will control this sun, if this sun is set to use time of day for its direction.

#### Direction
**C# member variable:** `Vector3 m_direction` \
Direction of the sun in the sky.

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