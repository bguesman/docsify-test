# Creative Stars

> Implemented as class `Expanse.CreativeStars` in `blocks/creative/CreativeStars.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/stars/density_0.2.jpg"/></div>
    </div>
</div>

This block is a more intuitive alternative to directly working with [Procedural Stars](editor/blocks/procedrual_stars_block.md).

#### Stars Block
**C# member variable:** `Expanse.ProceduralStars m_starsBlock` \
[Procedural Stars](editor/blocks/procedrual_stars_block.md) that this creative stars component controls.

#### Brightness
**C# member variable:** `float m_brightness` \
Brightness of the stars.

#### Direction
**C# member variable:** `float m_density` \
Density of the star field. In other words, how many stars there are in the sky.

#### Size
**C# member variable:** `float m_size` \
Average size of the stars.