# Night Sky

> Implemented as class `Expanse.NightSky` in `blocks/NightSky.cs`

This block exposes settings that allow you to tweak the look of the night sky. This includes light pollution, artistic scattering overrides, and global rotation of stars/nebulae.

#### Intensity
**C# member variable:** `float m_intensity` \
Overall intensity of the night sky. Multiplies star/nebulae relative intensities.

#### Rotation
**C# member variable:** `Vector3 m_rotation` \
Overall rotation of the night sky.

#### Tint
**C# member variable:** `Color m_tint` \
Overall tint to the night sky.

#### Light Pollution Intensity
**C# member variable:** `float m_lightPollutionIntensity` \
Intensity of light pollution coming from the planet, in Lux.

#### Light Pollution Tint
**C# member variable:** `Color m_lightPollutionTint` \
Color of light pollution coming from the planet.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/night_sky/no_lp.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/lp_orange_30.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/lp_blue_30.jpg"/></div>
        <p></p>
    </div>
    <p>Various light pollution settings. Left: no light pollution. Middle: orange light pollution at an intensity of 30 Lux, similar to what you might find in a dense suburb. Right: blue light pollution at an intensity of 30 Lux. Maybe what it could look like on a planet whose oceans are swimming with bioluminescent algae!</p>
</div>

#### Scatter Intensity
**C# member variable:** `float m_scatterIntensity` \
Expanse computes sky scattering using the average color of the sky texture. There are so many light sources in the night sky (stars) that this is really the only computationally tractable option. However, this can sometimes result in scattering that's too intense, or not intense enough, depending on your use case. This parameter is an artistic override to help mitigate that issue.

To emulate reality, it's probably best to keep this value very low. Stars are very dim and don't contribute much to the sky color at all, compared to the moon.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/night_sky/no_night_scatter.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/scatter_0.3.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/scatter_1.jpg"/></div>
        <p></p>
    </div>
    <p>Various scatter intensities. Left: scatter intensity 0. Middle: scatter intensity 0.3. Right: scatter intensity 1.</p>
</div>

#### Scatter Tint
**C# member variable:** `Color m_scatterTint` \
An additional tint applied on top of the night sky tint, but only to the scattering. This is useful as an artistsic override for if the average color of your sky texture doesn't quite get you the scattering behavior you want. For instance, you may want the scattering to be bluer.