# Atmosphere Layer

> Implemented as class `Expanse.AtmosphereLayer` in `blocks/AtmosphereLayer.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/atmosphere_splash.png"/></div>
    </div>
</div>

This component allows you to add layers of participating media to Expanse's atmosphere model. It can model everything from air, to aerosols, to height and rain fog, to the ozone layer, etc.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Modeling

These parameters describe the distribution of the layer's participating media---thickness, density distribution, etc.

#### Density

**C# member variable:** `float m_density` \
The density of the participating media. This together with the scattering/extinction coefficients determines the way the media absorbs and scatters light. As such its units/scale depend on the choice of units for the scattering/extinction coefficients.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/aerosol_density_0.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/aerosol_density_1.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/aerosol_density_10.jpg"/></div>
    </div>
    <p>Left to right: aerosol densities of 0, 1, and 10. All of these represent plausible Earth atmospheres, since the aerosol density varies greatly across Earth biomes. In the middle of a densely populated city, it can be very high. On a sunny day in a grassland, it might be close to zero.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/rayleigh_density_0.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/rayleigh_density_1.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/rayleigh_density_10.jpg"/></div>
    </div>
    <p>Left to right: rayleigh densities of 0, 1, and 10. The density of Earth's rayleigh scattering layer is pretty much constant across the planet, so only the middle image represents a plausible Earth environment. With a density of 0, we're pretty much in empty space, so the sky is black! With a density of 10, the absorptive characteristics of the media take over, and the sky appears a reddish orange, like the sunset. It really is a beautiful coincidence that Earth's atmosphere is just dense enough to display a wide range of colors throughout the day/night cycle.</p>
</div>

#### Density Distribution

**C# member variable:** `Expanse.AtmosphereDatatypes.DensityDistribution m_densityDistribution` \
The density distribution of the participating media---in other words, how the gases in this atmosphere layer are distributed around the Earth. Expanse supports four density distributions, split into two categories based on how they are rendered and what features they support: [Regular Distributions](/editor/blocks/atmosphere_layer_block?id=regular-distributions) and [Fog Distributions](/editor/blocks/atmosphere_layer_block?id=fog-distributions).

The parameterizations for these distributions are discussed in their subsections.

#### Regular Distributions

Expanse provides two efficient density distribution models that will suit most of your needs.

> Exponential

Exponential layers are, most likely, the bread-and-butter building blocks you'll be working with to build your atmosphere. They are very dense near the surface of the earth, and then get less dense as you get farther up into the atmosphere, until at some distance they are basically zero. Many phenomena in nature exhibit this kind of pattern, and atmosphere gas distributions are no exception.

Earth's atmosphere is modelable as two exponential layers: one that exhibits "rayleigh" scattering---responsible for the sky's blue color---and another that exhibits "mie" scattering---responsible for the bright air glow around the sun disc.

More sophisticated models may take into account the ozone layer, which is modelable in Expanse as a tent distribution (see the next density distribution type).

This distribution is parameterized by:

##### Thickness

**C# member variable:** `float m_thickness` \
How far out into space the atmosphere layer extends.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/just_rayleigh.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/rayleigh_and_exponential_aerosols.jpg"/></div>
    </div>
    <p>Left: one exponential layer, setup to mimic Earth's nitrogen/oxygen/carbon dioxide atmosphere. Right: the same setup as left, but with an additional exponential layer that models aerosols like smoke and smog, creating the characteristic air glow around the sun disc.</p>
</div>

> Tent

The tent distribution has a maximum value at a particular altitude, its "height" parameter, and then decays above and below that value. It's good for modeling "bands" of gas that are concentrated at a particular height above the ground.

The ozone is one of those layers. Modeling the ozone is not strictly necessary, but is key to making the sky appear properly blue in the daytime, and appropriately purple in the evening.

This distribution is parameterized by:

##### Height

**C# member variable:** `float m_height` \
Height of the band center.

##### Thickness

**C# member variable:** `float m_thickness` \
How far away from the center height band the layer extends.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/rayleigh_and_exponential_aerosols.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/rayleigh_aerosols_and_tent_ozone.jpg"/></div>
    </div>
    <p>Left: atmosphere with just exponential rayleigh and mie layers. Right: with the ozone added in. Notice how it better approximates the purple twilight we are all familiar with.</p>
</div>

#### Fog Distributions

Normally, Expanse renders atmosphere layers to a low-resolution spherical texture, and then composites them into the camera's rectangular framebuffer. This is, more or less, why it can afford to use an expensive but physically accurate rendering algorithm in realtime. However, this strategy is limiting in that it cannot resolve sharp, volumetric shadows---crepuscular or "god" rays.

To address this, Expanse introduces the concept of fog layers---layers whose density distribution can be rendered in a less expensive (but still physically-based) fashion. Their main benefits are:

- They are free of some of the artifacts present in the spherical texture
- They support a falloff distance from the player
- They support point and spot lights
- They support volumetric shadows from fog and clouds

> Uniform Fog

A fog layer that is distributed uniformly within a constant radius around the camera. This is particularly useful for modeling atmospheric effects like rain fog and smoke.

This distribution is parameterized by:

##### Radius

**C# member variable:** `float m_height` \
The radius around the camera where the density is non-zero. To minimize the data members in the atmosphere layer class, the variable is overloaded with the height parameter.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/aerosol_density_1.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/uniform_rain_fog_density_300.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/uniform_rain_fog_density_3000.jpg"/></div>
    </div>
    <p>Uniform "rain fog" at varying densities. Left to right: 0, 300, 3000.</p>
</div>

> Height Fog

A fog layer that is distributed exponentially from the plane aligned to the ground where the camera is placed, extending outward to a specified attenuation radius. This layer can mimic the regular exponential layer, though not perfectly, as it does not quite capture the curvature of the planet (sorry flat-earthers).

As its name suggests, it's good for modeling height fog. It can also be a duplicate of the usual exponential aerosol layer, set up to provide some volumetric shadowing effects.

This distribution is parameterized by:

##### Radius

**C# member variable:** `float m_height` \
The falloff radius around the camera. If this value is low, the height fog will be concentrated right around the camera. If it's high, the height fog will extend far out away from the camera. To minimize the data members in the atmosphere layer class, the variable is overloaded with the height parameter.

##### Thickness

**C# member variable:** `float m_thickness` \
How far out into space the atmosphere layer extends.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/height_fog_density_0.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/height_fog_density_300.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/height_fog_density_1500.jpg"/></div>
    </div>
    <p>Height fog at varied densities. Left to right: zero, 300, 1500.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/rayleigh_and_exponential_aerosols.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/screenspace_height_fog_aerosols.jpg"/></div>
    </div>
    <p>Left: atmosphere set up with a regular exponential aerosol layer. Right: atmosphere set up with a height fog aerosol layer, with the density cranked up to make the volumetric shadowing effect visible.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MATERIAL --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Lighting

These parameters describe the color and illumination characteristics of the atmosphere layer.

#### Extinction and Scattering Coefficients

**C# member variables:** `Color m_extinctionCoefficients` and `Color m_scatteringCoefficients` \
Volumetric coefficients can be challenging to understand if you are unfamiliar with volumetric rendering theory. Their main function is to determine the **color** of the sky. One easy way to think about them is to describe what they do to the sky color at different times of the day.

**Scattering coefficients** determine the color of the sky at mid-day. Whatever color you choose for the scattering coefficients will be the color of the sky for the majority of the day.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/blue_scattering.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/teal_scattering.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/magenta_scattering.jpg"/></div>
    </div>
    <p>Different scattering coefficient values. Left to right: earth's familiar blue scattering, a cool teal, and an intense fuschia. Extinction coefficients are blue in all cases.</p>
</div>

**Extinction coefficients** determine the color of the sky at sunset. The sunset color will be **the opposite** of the color you choose for the extinction coefficients. So, if your extinction coefficients are blue, then your sunset will be orange, which is opposite from blue on the color wheel. This is why sunsets on Earth are orange.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/blue_extinction.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/red_extinction.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/magenta_extinction.jpg"/></div>
    </div>
    <p>Varying the extinction coefficients while keeping the scattering coefficients at a constant blue. Notice how in the right two images, where the scattering and extinction coefficients differ from each other, the result looks pretty funky. Left to right: earth's coefficients (so, blue), red (resulting in a blue sunset), magenta (resulting in a green sunset).</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/blue_extinction.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/yellow_scattering_extinction.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/magenta_scattering_extinction.jpg"/></div>
    </div>
    <p>Varying the scattering and extinction coefficients in parallel. Notice how the resulting skies, while alien, look more physically plausible, or at the very least more interesting, than the previous examples where the scattering and extinction coefficients differ from each other. Left to right: earth's coefficients, yellow (resulting in a deep indigo sunset), and magenta (resulting in a lime green sunset).</p>
</div>

A more technical description of these coefficients reveals why they influence the sky color in this way: extinction coefficients tell you what color light gets **absorbed**, and scattering coefficients tell you what color light gets **scattered** or **reflected**. At mid-day, light doesn't have to go through very much atmosphere, so not much blue light gets absorbed, and the scattering color, blue, dominates. At sunset, light has to go through much more atmosphere, so most of the blue gets absorbed, leaving orange behind.

To keep your skies physically realistic, follow these rules:

1. **Keep your scattering coefficients less than or equal to your absorption coefficients.** This ensures that your atmosphere layer is energy-conserving. Intuitively, it means your atmosphere will never scatter more light than it receives from the sun.
2. **Use coefficients on the order of `1e-6`**. This isn't strictly necessary but will allow you to use more reasonable density values in the range of 1-10.
3. **Use the same hue for both your scattering and extinction coefficients**. In other words, keep the hue the same, and only adjust the value and saturation. Again, this isn't necessary, but real-life examples where these hues are different are pretty rare.

The following table gives some common real-world examples of scattering and extinction coefficients.

| Example        | Extinction Coefficients          | Scattering Coefficients          | Density   |
| -------------- | -------------------------------- | -------------------------------- | --------- |
| Earth Rayleigh | `(5.802e-6, 13.558e-6, 33.1e-6)` | `(5.802e-6, 13.558e-6, 33.1e-6)` | `1`       |
| Earth Aerosols | `(4e-6, 4e-6, 4e-6)`             | `(3.996e-6, 3.996e-6, 3.996e-6)` | `0-10`    |
| Earth Ozone    | `(0.65e-6, 1.881e-6, 0.085e-6)`  | `(0, 0, 0)`                      | `0-1`     |
| Earth Clouds   | `(4e-6, 4e-6, 4e-6)`             | `(4e-6, 4e-6, 4e-6)`             | `0-50000` |
| Mars Rayleigh  | `(1.961e-2, 1.176e-2, 0.392e-2)` | `(1.992e-2, 1.357e-2, 0.575e-2)` | `0.001`   |

#### Phase Function

**C# member variable:** `Expanse.AtmosphereDatatypes.PhaseFunction m_phaseFunction` \
The phase function of the participating media, which determines the directionality of the light scattering. Expanse provides support for three different phase functions.

> Isotropic

The isotropic phase function scatters light evenly in all directions. This is good for fog and general haze.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/without_isotropic_fog.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/with_isotropic_fog.jpg"/></div>
    </div>
    <p>Left: scene without isotropic rain fog. Right: with an isotropic rain fog layer. Really adds a lot of mood!</p>
</div>

> Rayleigh

The Rayleigh phase function models scattering where the participating media particles are around the same size as the wavelength of scattered light. This is useful for modeling the bulk of the Earth's atmosphere---given that it's mostly nitrogen, oxygen, and carbon dioxide.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/isotropic.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/rayleigh.jpg"/></div>
    </div>
    <p>Comparison between using an isotropic phase function to model Earth's main atmosphere (left) and using the correct Rayleigh phase function (right). Notice the sky in the right image is brighter around the sun disc, a result of subtle forward scattering.</p>
</div>

> Mie

The Mie phase function models scattering where the participating media particles are bigger than the wavelength of scattered light. This is good for modeling smoke, fog, and aerosols.

Such scattering follows the complicated laws of Mie theory. In order to make this feasible to render in realtime, Expanse uses the Henyey-Greenstein approximation, which is parameterized by one parameter, anisotropy, documented below.

In order to keep this physical, it's best to use a grey color for the scattering and extinction coefficients, since this sort of scattering is not wavelength-dependent in the range of visible light.

##### Anisotropy

**C# member variable:** `float m_anisotropy` \
Anisotropy controls the directionality of the scattering behavior modeled by the Mie phase function. A maximum value of `1` means that light will mostly scatter in the direction of the light, so-called "forward scattering". A minimum value of `-1` means that light will scatter in the direction opposite of the light, referred to as "backward scattering". A value of `0` will be nearly identical to the isotropic phase function, with no preference for either direction.

Aerosols due to pollution on Earth typically have an anisotropy of around `0.76`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/anisotropy_0.2.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/anisotropy_0.76.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/anisotropy_0.99.jpg"/></div>
    </div>
    <p>Varied anisotropy values for a layer modeling Earth aerosols. Left to right: 0.2, 0.76, 0.99. The density has been increased beyond reasonable Earth values to make the effect of the anisotropy very obvious.</p>
</div>

#### Tint

**C# member variables:** `Color m_tint` \
Tint to the layer's color. Perfect grey, `(127, 127, 127)`, specifies no tint. **This parameter is non-physical, and is meant only as an artistic override.** If you'd like to tweak the color of the atmosphere, you'll probably get a better result by changing the [extinction and scattering coefficients](/editor/blocks/atmosphere_layer_block?id=extinction-and-scattering-coefficients).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/no_tint.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/tint.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/tint_via_coefficients.jpg"/></div>
    </div>
    <p>Comparison of tint versus adjusting the scattering and extinction coefficients. Left: no tint. Middle: using the tint parameter to adjust the sky color to look more "tropical". Right: using the scattering/extinction coefficients to achieve the same effect. The point of this comparison isn't necessarily to gawk at how much better it looks using the coefficients, but to prove that it's possible to get the result in a way that respects physical accuracy.</p>
</div>

#### Multiple Scattering Multiplier

**C# member variables:** `float m_multipleScatteringMultiplier` \
Expanse computes multiple scattering in atmosphere layers. This parameter allows you to adjust that contribution to be nonphysical. A value of `1` is neutral/physical. Any other value is non-physical. In other words, this parameter is purely an artistic override.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/no_ms.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/ms_1x.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/ms_5x.jpg"/></div>
    </div>
    <p>Different values for the multiple scattering multiplier. Left to right: no multiple scattering, 1x multiple scattering (physical), 5x multiple scattering. Cranking this parameter can create some pretty dreamy effects!</p>
</div>

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- SHADOWS ---------------------------------------->
<!---------------------------------------------------------------------------------------->

### Shadows

Expanse can compute volumetric shadows for [fog layers](/editor/blocks/atmosphere_layer_block?id=fog-distributions) in two ways:

1. By ray marching the directional light's shadow map. This produces true volumetric shadows, and can be used for modeling a variety of interesting effects. I'm pretty sure, based on some investigation, that Unity's volumetric fog uses the same strategy.
2. As a screenspace post-process using the depth buffer. The resulting shadow effects are only an approximation, so it's best to use them as a subtle effect. This strategy has been employed with great results in a number of AAA games---in particular, Mirror's Edge: Catalyst, which was a big inspiration for this product.

Which method you want to use can be set with the [Shadowmap Volumetric Shadows](/editor/blocks/celestial_body_block?id=shadowmap-volumetric-shadows) parameter on each of your Celestial Bodies.

Only true volumetric shadows are supported for point and spot lights.

A note: to adjust the streakiness of the screenspace shadows, you can tweak the [depth buffer downsample factor](/editor/blocks/quality_settings_block?id=screenspace-depth-downscale) in your [quality settings](/editor/blocks/quality_settings_block). This is a global setting, so it cannot be set per layer.

For [regular atmosphere layers](/editor/blocks/atmosphere_layer_block?id=regular-distributions), two settings ([occlusion bias](/editor/blocks/atmosphere_layer_block?id=occlusion-bias) and [occlusion spread](/editor/blocks/atmosphere_layer_block?id=occlusion-spread)) are exposed to help with scattering bleeding through geometry in an unwanted way.

#### Occlusion Bias

**C# member variable:** `float m_occlusionBias` \
Provides a way of offsetting the attenuation of aerial perspective as a consequence of approximate volumetric shadowing (for non-fog layers). To see the effect, put the sun behind a big piece of geometry (like a mountain) and play around with this parameter. Expanse does not accurately model atmospheric volumetric shadows due to the performance cost, and instead uses this approximation to avoid visual artifacts.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_ground_truth.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_bias_1.jpg"/></div>
        <p></p>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_bias_0.25.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_bias_0.jpg"/></div>
        <p>Comparison of different occlusion biases. In all cases, occlusion spread is set to 1. Top left: "ground truth" result using a screenspace height fog layer with pseudo-volumetric shadows. Top Right: occlusion bias of 1, so aerial perspective is not attenuated at all around the sun disc. Bottom Left: occlusion bias of 0.25, so aerial perspective is attenuated by some amount around the sun disc. This approximates the ground truth reasonably, though not perfectly. Bottom Right: occlusion bias of 0, so aerial perspective is fully attenuated around the sun disc.</p>
    </div>
</div>

#### Occlusion Spread

**C# member variable:** `float m_occlusionSpread` \
How aggressively aerial perspective due to non-fog layers is attenuated as a consequence of approximate volumetric shadowing. To see the effect, put the sun behind a big piece of geometry (like a mountain) and play around with this parameter. Expanse does not accurately model atmospheric volumetric shadows due to the performance cost, and instead uses this approximation to avoid visual artifacts.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_ground_truth.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_spread_0.jpg"/></div>
        <p></p>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_spread_0.25.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_spread_1.jpg"/></div>
        <p>Comparison of different occlusion spreads. In all cases, occlusion bias is set to 0.5 to make the effect as noticeable as possible. Top left: "ground truth" result using a screenspace height fog layer with pseudo-volumetric shadows. Top Right: occlusion spread of 0, so maximum spread. Bottom Left: occlusion spread of 0.25, so some the spread is tighter. Bottom Right: occlusion spread of 1, so aerial perspective is minimally spread.</p>
    </div>
</div>

<!---------------------------------------------------------------------------------------->
<!------------------------------------- INTERACTION -------------------------------------->
<!---------------------------------------------------------------------------------------->

### Interaction

#### Receive Density Particles

**C# member variable:** `bool m_receiveDensityParticles` \
If this layer uses a [fog distribution](/editor/blocks/atmosphere_layer_block?id=fog-distributions), enabling this allows the layer to receive density from [fog particle systems](/editor/blocks/fog_particle_system_block).
