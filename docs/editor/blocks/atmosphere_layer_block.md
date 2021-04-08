# Atmosphere Layer Block

> Implemented as class `Expanse.AtmosphereLayerBlock` in `blocks/AtmosphereLayerBlock.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/atmosphere_splash.png"/></div>
    </div>
</div>

This block allows you to add layers of participating media to Expanse's atmosphere model. It can model everything from air, to aerosols, to height and rain fog, to the ozone layer, etc.

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
The density distribution of the participating media---in other words, how the gases in this atmosphere layer are distributed around the Earth. Expanse supports four density distributions, split into two categories based on how they are rendered and what features they support: [Regular Distributions](/editor/blocks/atmosphere_layer_block?id=regular-distributions) and [Screenspace Distributions](/editor/blocks/atmosphere_layer_block?id=screenspace-distributions).

The parameterizations for these distributions are discussed in their subsections.

#### Regular Distributions

Expanse provides two efficient density distribution models that will suit most of your needs.

>Exponential

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

>Tent

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

#### Screenspace Distributions

Normally, Expanse renders atmosphere layers to a low-resolution spherical texture, and then composites them into the camera's rectangular framebuffer. This is, more or less, why it can afford to use an expensive but physically accurate rendering algorithm in realtime. However, this strategy is limiting in that it cannot resolve sharp, volumetric shadows---crepuscular or "god" rays.

To address this, Expanse introduces the concept of screenspace layers---layers that are rendered in a less expensive (but still physically-based) fashion, per-camera pixel. Their main benefit is that they are free of some of the artifacts present in the spherical texture, they support a falloff distance from the player, and they support a form of screenspace volumetric shadowing. As such they are particularly useful for modeling fog, smoke, and other dense aerosols.

With volumetric shadows turned off, these layers are extremely performant. However, with volumetric shadows on, they will perform raymarching against a downsampled version of the depth buffer. This can reduce performance---the performance hit can be mitigated by choosing an appropriate [depth buffer downsample factor](/editor/blocks/quality_settings_block?id=screenspace-depth-downscale) in your [quality settings block](/editor/blocks/quality_settings_block).

>Screenspace Uniform

A screenspace layer that is distributed uniformly within a constant radius around the camera. This is particularly useful for modeling atmospheric effects like rain fog and smoke.

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
    <p>Screenspace uniform "rain fog" at varying densities. Left to right: 0, 300, 3000.</p>
</div>

>Screenspace Height Fog

A screenspace layer that is distributed exponentially from the plane aligned to the ground where the camera is placed, extending outward to a specified attenuation radius. This layer can mimic the regular exponential layer, though not perfectly, as it will fail to capture the curvature of the planet (sorry flat-earthers). 

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
    <p>Screenspace height fog at varied densities. Left to right: zero, 300, 1500.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/rayleigh_and_exponential_aerosols.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/screenspace_height_fog_aerosols.jpg"/></div>
    </div>
    <p>Left: atmosphere set up with a regular exponential aerosol layer. Right: atmosphere set up with a screenspace height fog aerosol layer, with the density cranked up to make the volumetric shadowing effect visible.</p>
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

|Example |Extinction Coefficients |Scattering Coefficients |Density |
|---------------- |------------------ |-------------------	|----------- |
| Earth Rayleigh | `(5.802e-6, 13.558e-6, 33.1e-6)` | `(5.802e-6, 13.558e-6, 33.1e-6)` | `1` |
| Earth Aerosols | `(4e-6, 4e-6, 4e-6)` | `(3.996e-6, 3.996e-6, 3.996e-6)` 	| `0-10` |
| Earth Ozone | `(0.65e-6, 1.881e-6, 0.085e-6)` | `(0, 0, 0)` | `0-1` |
| Earth Clouds | `(4e-6, 4e-6, 4e-6)` | `(4e-6, 4e-6, 4e-6)` | `0-50000` |
| Mars Rayleigh | `(1.961e-2, 1.176e-2, 0.392e-2)` | `(1.992e-2, 1.357e-2, 0.575e-2)` | `0.001` |

#### Phase Function
**C# member variable:** `Expanse.AtmosphereDatatypes.PhaseFunction m_phaseFunction` \
The phase function of the participating media, which determines the directionality of the light scattering. Expanse provides support for three different phase functions.

>Isotropic

The isotropic phase function scatters light evenly in all directions. This is good for fog and general haze.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/without_isotropic_fog.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/with_isotropic_fog.jpg"/></div>
    </div>
    <p>Left: scene without isotropic rain fog. Right: with an isotropic rain fog layer. Really adds a lot of mood!</p>
</div>

>Rayleigh

The Rayleigh phase function models scattering where the participating media particles are around the same size as the wavelength of scattered light. This is useful for modeling the bulk of the Earth's atmosphere---given that it's mostly nitrogen, oxygen, and carbon dioxide.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/isotropic.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/rayleigh.jpg"/></div>
    </div>
    <p>Comparison between using an isotropic phase function to model Earth's main atmosphere (left) and using the correct Rayleigh phase function (right). Notice the sky in the right image is brighter around the sun disc, a result of subtle forward scattering.</p>
</div>

>Mie

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

#### Multiple Scattering  Multiplier
**C# member variables:** `float m_multipleScatteringMultiplier` \
Expanse computes multiple scattering for non-screenspace layers. This parameter allows you to adjust that contribution to be nonphysical. A value of `1` is neutral/physical. Any other value is non-physical. In other words, this parameter is purely an artistic override.

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
These parameters are only valid for [screenspace layers](/editor/blocks/atmosphere_layer_block?id=screenspace-distributions), since only they are capable of volumetric shadows.

Expanse computes pseudo-volumetric shadows as a screenspace post-process using the depth buffer and the cloud transmittance buffer. Because of this, the shadow effects it computes are only an approximation. It's best to use them as a subtle effect, and defer to a more dedicated volumetrics plugin for true volumetric lighting. I'm pretty sure, based on some investigation, that Unity's volumetric fog uses the same strategy. It's also been employed with great results in a number of AAA games---in particular, Mirror's Edge: Catalyst, which was a big inspiration for this product.

A note: to adjust the streakiness of the screenspace shadows, you can tweak the [depth buffer downsample factor](/editor/blocks/quality_settings_block?id=screenspace-depth-downscale) in your [quality settings block](/editor/blocks/quality_settings_block). This is a global setting, so it cannot be set per layer.

#### Geometry Shadows
**C# member variable:** `bool m_geometryShadows` \
Whether or not scene geometry should cast volumetric shadows for this layer.

#### Cloud Shadows
**C# member variable:** `bool m_cloudShadows` \
Whether or not clouds should cast volumetric shadows for this layer.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/no_cloud_shadows.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/cloud_shadows_darker.jpg"/></div>
    </div>
    <p>Left: no cloud shadows. Right: with cloud shadows. This produces the "god rays" effect.</p>
</div>

#### Max Occlusion
**C# member variable:** `float m_maxOcclusion` \
The maximum amount that scene geometry or clouds can attenuate scattering. A value of `1` means that full occlusion is possible. A value of `0` is tantamount to turning shadows off, as geometry and clouds will have no occluding power.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/atmosphere/occlusion_0.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/occlusion_0.5.jpg"/></div>
        <div class="img-col"><img src="img/atmosphere/occlusion_1.jpg"/></div>
    </div>
    <p>Comparison of different max occlusion values. Left: value of 0---effectively turning volumetric shadows off. Middle: value of 0.5. Right: value of 1. Notice how the volumetric shadows are darker and more pronounced in the right-most image, where geometry has maximum occluding power.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- METADATA --------------------------------------->
<!---------------------------------------------------------------------------------------->
### Metadata
These parameters are metadata or references to components/objects that the block uses.

#### Name
**C# member variable:** `string m_name` \
The name for this layer used in debug and error printouts.

#### Volume
**C# member variable:** `UnityEngine.Rendering.Volume m_volume` \
The Sky and Fog Volume that backs this atmosphere layer block. In all likelihood, this should be a single global volume you set up at the start of your project. More complex workflows may have multiple local volumes, in which case you will need to create multiple atmosphere layer blocks (one for each volume), or programmatically change this parameter.
