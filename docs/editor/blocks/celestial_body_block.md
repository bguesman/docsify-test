# Celestial Body

> Implemented as class `Expanse.CelestialBody` in `blocks/CelestialBody.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/celestial_body_splash.jpg"/></div>
    </div>
</div>

This block is what you'll use to render any planet, star, or other spherical object in the sky.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Modeling

These parameters describe the shape, orientation, and pose of the celestial body.

#### Direction

The direction of the celestial body in the sky is controlled by the respective `GameObject`'s rotation. It is essentially the same system you'd use for adjusting a directional light.

#### Angular Radius

**C# member variable:** `float m_angularRadius` \
The size of the celestial body in the sky, specified as an angular radius in degrees. The angular radius of the sun and moon are both about `0.5` degrees, but this can often appear too small, for a number of reasons related to the psychology of visual perception.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/angular_radius_0.5.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/angular_radius_2.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/angular_radius_8.jpg"/></div>
    </div>
    <p>Left: angular radius of 0.5. Middle: angular radius of 2. Right: angular radius of 8.</p>
</div>

#### Penumbra

**C# member variable:** `float m_penumbra` \
Multiplier on the angular radius used to compute the celestial body's penumbra. Good for smoothing out the sky shadow for smaller bodies, or for reducing artifacts for very large bodies. 1 is a "physical" value, though the way Expanse computes sun disc illumination is an approximation anyway.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/penumbra_1.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/penumbra_5.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/penumbra_11.jpg"/></div>
    </div>
    <p>Left: penumbra multiplier of 1, so "physically accurate". Middle: penumbra multiplier of 5, which is a bit more visually pleasing. Right: penumbra multiplier of 11, which is perhaps too extreme.</p>
</div>

#### Distance

**C# member variable:** `float m_distance` \
Celestial body's distance from the center of the planet, in meters. The distance from the Earth to the sun is `1.5 x 10^11` meters. The distance to the moon is `3.8 x 10^8` meters.

#### Tidally Locked

**C# member variable:** `bool m_tidallyLocked` \
Whether or not the celestial body is tidally locked. This means that the same side of the celestial body always faces the planet---for instance, like Earth's moon, Luna.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- LIGHTING --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Lighting

These parameters determine the way the celestial body illuminates the scene.

#### Directional Light

**C# member variable:** `UnityEngine.Light m_directionalLight` \
The Unity directional light that backs this celestial body.

#### Light Intensity

**C# member variable:** `float m_lightIntensity` \
Light intensity of the celestial body, in lux. In particular this is the illuminance on the ground when the body is at the zenith position.

A typical value for the sun is `150000`, and a typical value for the moon is between `1-3`. These values do not always integrate well with existing material workflows that are not physically-based, and can be challenging to accomodate with auto-exposure.

#### Light Color

**C# member variable:** `Color m_lightColor` \
Color of the celestial body's light.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/light_color_white.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/light_color_green.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/light_color_red.jpg"/></div>
    </div>
    <p>Left to right: light colors of white, green, and red. Notice how the light color affects the color of the sky. Also, notice that using a pure white light doesn't quite give the right result for modeling sunlight. It's better to use temperature mode, with a value of 5778K.</p>
</div>

#### Temperature Mode

**C# member variable:** `bool m_useTemperature` \
Allows light color to be specified by temperature in Kelvin, via an approximation to the blackbody spectrum.

#### Temperature

**C# member variable:** `float m_lightTemperature` \
Temperature of the celestial body, used to calculate its light color when operating in [temperature mode](/editor/blocks/celestial_body_block?id=temperature-mode). The temperature is converted to a color using an approximation to the physical phenomenon of blackbody radiation. The sun's temperature is 5778K.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/light_temperature_5778.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/light_temperature_2000.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/light_temperature_20000.jpg"/></div>
    </div>
    <p>Left: temperature of the sun, 5778K. Middle: temperature of 2000K, a value you might expect for a red giant. Right: temperature of 20000K, a value you might expect for a white dwarf, or a particularly hot main sequence star.</p>
</div>

#### Filter

**C# member variable:** `Color m_lightColor` \
Filter color applied over the color calculated via temperature, when operating in [temperature mode](/editor/blocks/celestial_body_block?id=temperature-mode). Shares member variable with [light color](/editor/blocks/celestial_body_block?id=light-color).

#### Transmittance Multiplier

**C# member variable:** `bool m_transmittanceMultiplier` \
Artistic override for light transmittance. Good for introducing more direct light in heavily fogged environments.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/tmult-0.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/tmult-1.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/tmult-5.jpg"/></div>
    </div>
    <p>Left: transmittance multiplier of zero---effectively turns off the directional light. Middle: transmittance multiplier of 1. This is physically accurate. Right: transmittance multiplier of 4. This isn't physically accurate, but it looks nice!</p>
</div>

<!---------------------------------------------------------------------------------------->
<!------------------------------------- INTERACTION -------------------------------------->
<!---------------------------------------------------------------------------------------->

### Interaction

These parameters control how the celestial bodies interact with Expanse's volumetrics and with scene geometry.

#### Above Horizon

**C# function:** `bool AboveHorizon()` \
Returns true when the celestial body is above the horizon, and false otherwise. This is the best way to adjust which celestial body's light casts directional shadows, and to script other visibilty-dependent things that are generally tied to time of day in an ad hoc way.

#### Light Interaction

**C# member variable:** `Expanse.LightDatatypes.LightInteraction m_lightInteraction` \
Which sorts of volumetric components of Expanse this celestial body will light. Any subset of

- `LightInteraction.Atmosphere`: lights atmosphere layers with [regular distributions](/editor/blocks/atmosphere_layer_block/regular-distributions).
- `LightInteraction.Fog`: lights atmosphere layers with [fog distributions](/editor/blocks/atmosphere_layer_block/fog-distributions).
- `LightInteraction.Clouds`: lights clouds of all types.

If a celestial body has no appreciable affect on lighting conditions, disabling interaction will improve performance. If possible, it's best to have only one celestial body actively lighting the atmosphere/fog/clouds at a time.

#### Cast Cloud Shadows

**C# member variable:** `bool m_castCloudShadows` \
Whether or not the celestial body will cast cloud shadows.

#### Volumetric Shadows

**C# member variable:** `Expanse.LightDatatypes.VolumetricShadows m_volumetricShadows` \
Controls which sorts of scene entities can cast volumetric shadows on Fog (i.e., [Atmosphere Layers](/editor/blocks/atmosphere_layer_block) with [Fog Distributions](/editor/blocks/atmosphere_layer_block/fog-distributions)). Any subset of,

- `VolumetricShadows.Geometry`: shadows from the scene geometry.
- `VolumetricShadows.Clouds`: shadows from Expanse's clouds.

#### Shadowmap Volumetric Shadows

**C# member variable:** `bool m_shadowmapVolumetricShadows` \
When this is enabled, Expanse uses the geometry and cloud shadowmaps to compute volumetric shadows. This checkbox only shows up if the celestial body [renders cloud shadows](/editor/blocks/celestial_body_block/cast-cloud-shadows)), and if its corresponding directional light renders a shadowmap.

It is cheaper and more accurate to have this enabled. Unfortunately, HDRP currently only allows one directional light to render a shadow map. This means this sort of super volumetric shadowing can only be used on one celestial body at a time.

This is ok for a sun/moon setup, since you can script this to be on/off based on whether the sun is above the horizon.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/no-volumetric-shadows.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/depth-buffer-volumetric-shadows.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/shadowmap-volumetric-shadows.jpg"/></div>
    </div>
    <p>Left: no volumetric shadows. Middle: screenspace volumetric shadows, traced using the depth buffer. Right: true volumetric shadows, using the shadow map. The depth-buffer shadows get the idea across, but far too much of the geometry is supposedly in shadow. The shadow map shadows are geometrically correct---large swaths of the fog that were unlit using the depth buffer are correctly lit when using the shadow map.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!---------------------------------------- ALBEDO ---------------------------------------->
<!---------------------------------------------------------------------------------------->

### Albedo

These parameters determine the way the body reflects the light of other bodies.

#### Receives Light

**C# member variable:** `bool m_receivesLight` \
Whether or not this celestial body receives light from other celestial bodies. If this is unselected, no albedo parameters will be displayed, since the body will not be illuminated.

#### Albedo Texture

**C# member variable:** `TextureCube m_albedoTexture` \
Celestial body's albedo texture, specified as a cubemap to avoid stretching at the poles. If no texture is given, the celestial body's albedo will be its [albedo tint](/editor/blocks/celestial_body_block?id=albedo-tint).

#### Albedo Texture Rotation

**C# member variable:** `Vector3 m_albedoTextureRotation` \
Rotation of celestial body's albedo texture, specified as euler angles.

#### Albedo Tint

**C# member variable:** `TextureCube m_albedoTint` \
Tint to celestial body's albedo texture, or just celestial body's diffuse color if no texture is specified. Perfect grey `(127, 127, 127)` specifies no tint. Values above perfect grey may be not be energy-conserving, depending on the brightness of the albedo texture.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/moon_no_tint.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/moon_accurate_tint.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/moon_lavender_tint.jpg"/></div>
    </div>
    <p>Left: no tint to moon albedo. Middle: a slight reddish tint. Right: an exaggerated purple tint, resulting in a lavender color.</p>
</div>

#### Moon Mode

**C# member variable:** `bool m_moonMode` \
Instead of using a Lambertian (diffuse) BRDF, uses a retrodirective "Luna BRDF" specifically tailored for modeling Earth's moon, Luna. This is absolutely crucial for getting the flat disc look of the moon in the night sky.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/moon_diffuse_material.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/moon_accurate_tint.jpg"/></div>
    </div>
    <p>Left: without moon mode enabled. Notice that it looks... well, it looks wrong! The edges are darkened and the moon looks noticeably round. Right: with moon mode enabled. The moon now has the correct "flat disc" look.</p>
</div>

#### Retrodirection

**C# member variable:** `bool m_retrodirection` \
Retrodirection of Luna BRDF. A good value for the moon is `0.6`. Only applicable when [moon mode](/editor/blocks/celestial_body_block?id=moon-mode) is enabled.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/retrodirection_0.001.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/moon_accurate_tint.jpg"/></div>
    </div>
    <p>Left: retrodirection of 0.001, the minimum value. Right: retrodirection of 0.6, the recommended value for the moon.</p>
</div>

#### Anisotropy

**C# member variable:** `bool m_anisotropy` \
Anisotropy of Luna BRDF. A good value for the moon is `0.1`. Only applicable when [moon mode](/editor/blocks/celestial_body_block?id=moon-mode) is enabled.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/anisotropy_negative_1.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/moon_accurate_tint.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/anisotropy_1.jpg"/></div>
    </div>
    <p>Left: anisotropy of -1, the minimum value. This results in full back-scattering, so the moon appears dimmer when fully illuminated. Middle: 0.1 the recommended value for the moon. Right: 1, the maximum value. This results in full forward-scattering, so the moon appears brighter.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- EMISSION --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Emission

These parameters describe the light the surface of the celestial body emits.

#### Emissive

**C# member variable:** `bool m_emissive` \
Whether or not the surface of this celestial body is emissive. If this is unselected, no emission parameters will be displayed, since the body's surface will not emit light.

#### Emission Texture

**C# member variable:** `TextureCube m_emissionTexture` \
Celestial body's emission texture, specified as a cubemap to avoid stretching at the poles. Multiplied by the body's light intensity to get the final color. If no texture is given, the celestial body's emission will be its [emission tint](/editor/blocks/celestial_body_block?id=emission-tint).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/emissive_texture.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/emissive_and_diffuse.jpg"/></div>
    </div>
    <p>Left: example of an emissive texture for a star. Right: it's possible to use both an emissive and diffuse texture to create some interesting effects.</p>
</div>

#### Emission Texture Rotation

**C# member variable:** `Vector3 m_emissionTextureRotation` \
Rotation of celestial body's emission texture, specified as euler angles.

#### Emission Tint

**C# member variable:** `Color m_emissionTint` \
Tint to celestial body's emission texture, or just celestial body's emissive color if no texture is specified. Perfect white `(255, 255, 255)` specifies no tint.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/emissive_texture.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/sun_green_tint.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/sun_magenta_tint.jpg"/></div>
    </div>
    <p>Left to right: no tint, green tint, magenta tint. Notice that tinting doesn't always look all that appealing. It's better to achieve the look you desire in the texture authoring process, and use the tint value only for animations and slight tweaks.</p>
</div>

#### Emission Multiplier

**C# member variable:** `float m_emissionMultiplier` \
Multiplier on emissive color/texture. Somtimes, emission textures will be too blown out if their actual physical light values are used. Sometimes the reverse is true---the physical luminance of the celestial body disc won't be enough to trigger effects like bloom and lens flares. This is an artistic override to correct thes issues.

#### Limb Darkening

**C# member variable:** `float m_limbDarkening` \
Adjustable limb-darkening effect that darkens the edges of the celestial body in accordance with the reduced light flux through its sides. A physically-accurate value is `1`, but higher values are often needed for the effect to be noticeable. This is however crucial for modeling the appearance of the sun disc accurately.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/limb_darkening_0.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/limb_darkening_1.jpg"/></div>
        <div class="img-col"><img src="img/celestial_bodies/limb_darkening_4.jpg"/></div>
    </div>
    <p>Left to right: no limb darkening, physically accurate limb darkening (value of 1), exaggerated limb darkening (value of 4). When physically accurate, it is a subtle effect, but it makes all the difference.</p>
</div>
