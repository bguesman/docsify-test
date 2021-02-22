# Procedural Cloud Volume Block

> Implemented as class `Expanse.ProceduralCloudVolumeBlock` in `blocks/ProceduralCloudVolumeBlock.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="docsify-test/img/procedural_cloud_volume/banner.png"/></div>
    </div>
</div>

Procedural cloud volume...

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->
### Modeling
These parameters describe the general shape, form, and distribution of the clouds.

#### Noise Layer Array
**C# member variable:** `Expanse.ProceduralCloudVolumeBlock.LayerSettings m_noiseLayers` \
The six different noise layers that, combined together, define the cloud layer's density field. For details, see the [section on authoring noise layers](/editor/blocks/procedural_cloud_volume_block?id=noise-layers).

#### Quality
**C# member variable:** `Expanse.Datatypes.Quality m_noiseTextureQuality` \
Quality of procedural noise textures for this layer. If no procedural noises are enabled, this parameter does not change anything.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/low_qual.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/high_qual.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/rtm_qual.jpg"/></div>
    </div>
    <p>Different quality levels. Left: low quality. Middle: high quality. Right: "ripping through metaverse" quality.</p>
</div>

#### Coverage Intensity
**C# member variable:** `float m_coverageIntensity` \
Amount of cloud coverage.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage_0.3.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage_0.75.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage_1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage_1_rain_fog.jpg"/></div>
    </div>
    <p>Different coverage amounts. Left: 0.3. Middle-Left: 0.75. Middle-Right: 1. Right: 1, but with some rain fog added.</p>
</div>

#### Structure Intensity
**C# member variable:** `float m_structureIntensity` \
How much the structure noise layer erodes the base noise layer.

#### Detail Intensity
**C# member variable:** `float m_detailIntensity` \
How much the detail noise layer erodes the base noise layer.

#### Base Warp Intensity
**C# member variable:** `float m_baseWarpIntensity` \
How much the base noise layer is warped by its warp texture.

#### Detail Warp Intensity
**C# member variable:** `float m_detailWarpIntensity` \
How much the detail noise layer is warped by its warp texture.

#### Geometry Extents
**C# member variable:** `float m_XExtent`, `float m_YExtent`, and `float m_ZExtent` \
Extents of the cloud volume, so boundaries in X, Y, and Z directions.

#### Origin
**C# member variable:** `Vector3 origin` \
Origin of this layer's cloud geometry---the center of the cloud volume.

#### Curved
**C# member variable:** `bool m_curved` \
Whether or not the cloud layer is curved with the surface of the planet. This is a crucial setting to enable if you have a visible horizon line. It does not incur too much of a performance impact.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/curved.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/not_curved.jpg"/></div>
    </div>
    <p>Curved (left) vs. not curved (right). Notice how the clouds go over the horizon in the left image, with curved turned on.</p>
</div>

#### Attenuation Distance
**C# member variable:** `float m_attenuationDistance` \
Distance over which density is attenuated.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/atten_dist_50k.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/atten_dist_100k.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/atten_dist_200k.jpg"/></div>
    </div>
    <p>Comparison of different attenuation distances. Attenuation bias was set to 35000 for all examples. Left: 50000. Middle: 100000. Right: 200000.</p>
</div>

#### Attenuation Bias
**C# member variable:** `float m_attenuationBias` \
Distance before density attenuation kicks in.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/atten_dist_50k.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/atten_bias_75k.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/atten_bias_150k.jpg"/></div>
    </div>
    <p>Comparison of different attenuation biases. Attenuation distance was set to 50000 for all examples. Left: 35000. Middle: 75000. Right: 150000. Notice how the bias pushes the density out further, but, without adjusting the attenuation distance, fades out the clouds much quicker at the edge of the bias ring.</p>
</div>

#### Ramp Up
**C# member variable:** `Vector2 m_rampUp` \
Range over which density ramps up to full. Useful as a sort of soft near clipping plane for the clouds---it seems a common use case to have a minimum distance from the camera before clouds are drawn.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/no_ramp_up.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ramp_40_60.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ramp_100_150.jpg"/></div>
    </div>
    <p>Comparison of different ramp up ranges. Attenuation distance and bias are both set to 100000. Left: (0, 0), so no ramp up. Middle: (40000, 60000). The clouds directly overhead are gone. Right: (100000, 150000). Only the clouds far away remain.</p>
</div>

#### Rounding
**C# member variable:** `float m_rounding` \
How much to round off the tops of the clouds. This is a pretty important parameter to tweak to get realistic-looking cloud shapes. Many cloud systems lack this sort of capability, and, without it, their clouds look very blocky.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/no_round.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/round_3.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/round_5.5.jpg"/></div>
    </div>
    <p>Comparison of different rounding amounts. Rounding shape is set to 2.5 for each example. Left: rounding 1, so no rounding. Doesn't necessarily look bad, but the clouds are kind of boxy. Middle: rounding 3. Right: rounding 5.5.</p>
</div>

#### Rounding Shape
**C# member variable:** `float m_roundingShape` \
The curve of the rounding.

#### Height Gradient
**C# member variable:** `Vector2 m_heightGradientBottom` and `m_heightGradientTop` \
The height gradient of the clouds. The bottom height gradient determines the ramp-up of the cloud density along the height of the cloud volume. The top height gradient determines the ramp-down of the density at the tops of the clouds. Adjusting these parameters can help mitigate the blockiness of the clouds.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/hg_full.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/hg_0_0.15_0.9_1.jpg"/></div>
    </div>
    <p>Left: height gradient of (0, 0)/(1, 1), so effectively no ramp up and no ramp down---the entire cloud volume is visible. This exposes weird artifacts in the vertical in-scatter probability (the dark cloud bottoms), and additionally makes the clouds look kind of boxy. Right: height gradient of (0, 0.15)/(0.9, 1). Adjusting the height gradient to soften the ramp up and ramp down fixes these problems.</p>
</div>


#### Wind Skew
**C# member variable:** `Vector2 m_windSkew` \
Skew over height of the clouds due to wind, in the x and z directions respectively.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/no_wind.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/wind_1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/wind_2.jpg"/></div>
    </div>
    <p>Comparison of different amounts of wind skew. Left: none. Middle: 1 in the x direction. Right: 2 in the x direction.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!------------------------------------- NOISE LAYERS ------------------------------------->
<!---------------------------------------------------------------------------------------->
### Noise Layers
**C# member variable:** `Expanse.ProceduralCloudVolumeBlock.LayerSettings[] m_layers` \
Expanse models clouds using six layers of noise, authored as "noise layers". They are:
* **Coverage:** determines where there are clouds, and where there is sky. You can think of this as a "cloud map".
* **Base:** forms the base shapes and structure of the clouds.
* **Structure:** erodes the base noise, bringing in higher detail features without disturbing the basic form and distribution of the clouds.
* **Detail:** erodes the base and structure noise futher, introducing very small, granular features.
* **Base Warp:** distorts the base noise, giving the appearance of fluid motion.
* **Detail Warp:** distorts the detail noise, giving the appearance of fluid motion.

It's easiest to understand what these noises do by seeing them in action. Consider the following example, where we build up a cloud layer by enabling these noises successively. This example won't necessarily look the best, since each step is set up to be very obvious so that it can be more instructive. **The clouds are viewed from above here to make it easier to see the difference between each step.**
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/base.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage.jpg"/></div>
    </div>
    <p>Left: just the base noise. This is great, but covers the whole sky. Right: with the coverage map applied---clouds now have shapes, but they've also got some detail and variation, due to the base noise.</p>
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/structure.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/detail.jpg"/></div>
    </div>
    <p>Left: now, applying some structure erosion. This gives the clouds some more form. Right: applying some detail erosion on top of that. This creates some small-scale features that give the clouds a sense of scale. This is likely way too much detail erosion, but it's instructive to make sure it's very visible in this much example.</p>
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/done.jpg"/></div>
    </div>
    <p>The final result, viewed from the ground!</p>
</div>

Each layer provides a number of authoring controls to maximize expressivity.

#### Procedural
**C# member variable:** `bool procedural` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
Whether to use procedural noise or a texture for this noise layer.

#### Texture
**C# member variable:** `Cubemap texture` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
Noise texture that defines the distribution for this layer. Only used if [procedural is set to false](/editor/blocks/procedural_cloud_volume_block?id=procedural).

#### Noise Type
**C# member variable:** `Expanse.Datatypes.NoiseType noiseType` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
Type of procedurally generated noise used for this layer. There are seven types of noises available for use.
* `Constant`: exactly what it sounds like---just a flat, solid distribution.
* `Value`: a sharper noise, helpful for modeling more intense striated clouds.
* `Perlin`: a very soft noise, useful for modeling big, featureless clouds.
* `Worley`: Looks kind of like honeycomb. Good as detail noise---can create a good feathery effect.
* `InverseWorley`: the inverse of `Worley`---a billowy cell noise, good for modeling puffy clouds.
* `PerlinWorley`: A combination of `Perlin` and `InverseWorley` noise---so kind of like a softer version of vanilla `Worley` noise. Probably the best for cloud base noise.
* `Curl`: if you use this one for anything other than warp layers, it will look *weird*.

The following figure will give you a sense of what the different noises are like, but the best things you can do are play around with them yourself and watch the tutorial videos on modeling clouds. **Displayed here are 2D versions of the noises, since it's easier to see the difference this way.**

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_plane/constant.jpg"/><div>Constant</div></div>
        <div class="img-col"><img src="img/procedural_cloud_plane/value.jpg"/><div>Value</div></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_plane/perlin.jpg"/><div>Perlin</div></div>
        <div class="img-col"><img src="img/procedural_cloud_plane/worley.jpg"/><div>Worley</div></div>
    </div> 
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_plane/inverse_worley.jpg"/><div>Inverse Worley</div></div>
        <div class="img-col"><img src="img/procedural_cloud_plane/perlin_worley.jpg"/><div>Perlin Worley</div></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/procedural_cloud_plane/curl_warp.jpg"/><div>Curl used as base warp, with Perlin Worley used as base.</div></div>
    </div>
</div>

#### Scale
**C# member variable:** `Vector2 scale` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
Scale of 0th octave of noise.

#### Octaves
**C# member variable:** `int octaves` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
Number of octaves of noise to layer together. As a rule of thumb, the more octaves you use, the more detail the layer will have. At some point though, you can reach past what the resolution of the texture is able to express.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_plane/one_octave.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_plane/3_octaves.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_plane/8_octaves.jpg"/></div>
    </div>
    <p>Comparison of different octave counts. Structure and detail are not applied, and a 2D layer is used, to make it easier to visualize. Left: 1 octave. Middle: 3 octaves. Right: 8 octaves.</p>
</div>

#### Octave Scale
**C# member variable:** `float octaveScale` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
How much to scale each successive octave by.

#### Octave Multiplier
**C# member variable:** `float octaveMultiplier` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
How much to multiply the intensity of each successive octave by.

#### Tile
**C# member variable:** `int tile` in `Expanse.ProceduralCloudVolumeBlock.LayerSettings` \
Number of times to tile the texture across the cloud plane. Set this carefully in tandem with the scale, to ensure decent cache coherency and good performance. The higher this value, generally, the worse the performance.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- LIGHTING --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Lighting
These parameters describe the illumination characteristics of the cloud layer.

#### Density
**C# member variable:** `float m_density` \
Density of this cloud layer.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/density_800.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/density_5000.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/poofy_guys.jpg"/></div>
    </div>
    <p>Comparison of different densities. Since it's pretty challenging to compare density with all other things being equal, we use three different examples. Left: density 800. Middle: density 5000. Right: density 50000.</p>
</div>

#### Extinction and Scattering Coefficients
**C# member variables:** `Color m_extinctionCoefficients` and `Color m_scatteringCoefficients` \
Volumetric coefficients can be challenging to understand if you are unfamiliar with volumetric rendering theory. Their main function is to determine the **color** of the medium. Roughly: extinction coefficients tell you what color light gets **absorbed**, and scattering coefficients tell you what color light gets **scattered** or **reflected**.

To keep your clouds physically realistic, follow these rules:
1. **Keep your scattering coefficients less than or equal to your absorption coefficients.** This ensures that your cloud layer is energy-conserving. Intuitively, it means your clouds will never scatter more light than they receive from the sun. In the case of clouds, the scattering and extinction coefficients are usually the same.
2. **Use monochrome coefficients.** Water vapor particles are so much larger than the wavelength of visible light that scattering cannot be wavelength dependent.

**TL;DR: a good value to use for both the scattering and extinction coefficients is `(4e-6, 4e-6, 4e-6)`.**

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/scatter_0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/scatter_1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/scatter_4.jpg"/></div>
    </div>
    <p>Comparison of different scattering coefficient values. I chose an example---these sort of streaky jet trails---that looks good with each setting. That means the differences are more subtle though. Extinction coefficients are set to (4e-6, 4e-6, 4e-6) in all images. Left: (0, 0, 0). This results in no scattering, so the clouds only blot out light---that is why they appear so dark here. Middle: (1e-6, 1e-6, 1e-6). The scattering coefficients here are only 1/4th the magnitude of the extinction coefficients, so the clouds are noticeably dark. This is a good tactic to use for rain clouds. Right: (4e-6, 4e-6, 4e-6). The scattering coefficient here is the same as the extinction coefficient.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/too_dark.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/scatter_correction.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_correction.jpg"/></div>
    </div>
    <p>If the clouds you're working on look too dark, your first inclination might be to adjust your scattering coefficients to be brighter than your extinction coefficients. This is non-physical! If you find this is the case, you should first try to use the multiple scattering parameters to fix the issue. Left: clouds that are too dark. Middle: correcting the issue by upping the scattering coefficients to 4x the extinction coefficients. This looks ok but is non-physical. Right: correcting the issue by upping the multiple scattering amount. This looks a lot better and is physical! At the end of the day, it's your choice, and different style goals may call for different strategies. That said, it's a principle of mine to always know where your model deviates from physical reality, and make sure it's intentional.</p>
</div>

#### Anisotropy
**C# member variable:** `float m_anisotropy` \
Anisotropy of cloud scattering. Higher values will give more forward scattering. Lower values will give more backward scattering. A value of zero is neutral---i.e. it will produce \"isotropic\" scattering. Clouds are generally very forward-scattering, so a value of around 0.6 is a good physical approximation. However, sometimes this isn't aesthetically pleasing.

The FOV in the following images is very high to make the effect of different anisotropies more noticeable.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0.3.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0.6.jpg"/></div>
    </div>
    <p>Various anisotropy values. The effect is most visible around the sun disc. Left: 0. Middle: 0.3. Right: 0.6, a reasonable "physical" value.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0_back.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0.3_back.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0.6_back.jpg"/></div>
    </div>
    <p>It's important to consider the effect of anisotropy on the clouds opposite the side of the sky from the sun. Left: anisotropy of 0. The clouds look pretty bright. Middle: anisotropy of 0.3. The clouds are dimmer and greyer. Right: anisotropy of 0.6. The clouds are very grey. Note that using a value of zero for clouds this dense might not necessarily be non-physical, since higher orders of multiple scattering are increasingly more isotropic. So, if you like the way anisotropy 0 looks, and you add some silver lining with the silver intensity parameter, you can probably still call your clouds "physically-based".</p>
</div>

#### Silver Intensity
**C# member variable:** `float m_silverIntensity` \
Intensity of the characteristic silver lining around clouds close to the sun. This usually results from the phase function, so keeping things strictly physical would mean keeping this value at 0. However, sometimes this artistic override is required to achieve a desired look.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/anisotropy_0.3.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/silver_0.5.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/silver_1.jpg"/></div>
    </div>
    <p>Various silver intensities. Silver spread is set to 0.3 for all examples, and anisotropy is set to 0.3. The effect is most visible around the sun disc. Left: no silver. Middle: intensity 0.5. Right: intensity 1.</p>
</div>

#### Silver Spread
**C# member variable:** `float m_silverSpread` \
Spread of silver lining around clouds close to the sun. Higher values will distribute the silver lining around clouds further away from the sun.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/silver_spread_0.05.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/silver_spread_0.15.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/silver_1.jpg"/></div>
    </div>
    <p>Various silver spread values. Silver intensity is set to 1 for all examples so the effect is visible, and anisotropy is set to 0.3. Left: 0.05. Middle: 0.15. Right: 0.3.</p>
</div>

#### Ambient Height Range
**C# member variable:** `Vector2 m_ambientHeightRange` \
Height range of ambient lighting the clouds receive from the sky. The bottom of clouds generally receives less ambient light than the top. Tweaking this can help reveal self-shadowed details.

#### Ambient Strength Range
**C# member variable:** `Vector2 m_ambientStrengthRange` \
Strength range of ambient lighting the clouds receive from the sky, applied over the specified height range. So, at the lower height, the ambient intensity is the lower strength, and at the higher height, the ambient intensity is the higher strength.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/ambient_strength_0_0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ambient_strength_0.5_1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ambient_strength_1_2.jpg"/></div>
    </div>
    <p>Comparison of different ambient strength ranges. Ambient height range is set to (0, 1) for all examples. Left: (0, 0), so no ambient. Looks pretty rough! Middle: (0.5, 1). This looks pretty decent. Right: (1, 2). This also looks pretty good.</p>
</div>

#### Vertical Probability Height Range
**C# member variable:** `Vector2 m_verticalProbabilityHeightRange` \
Unit height range to apply vertical in-scattering probability to.

#### Vertical Probability Strength
**C# member variable:** `float m_verticalProbabilityStrength` \
Strength of vertical in-scattering probablity.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/vertical_prob_0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/vertical_prob_0.8.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/vertical_prob_1.6.jpg"/></div>
    </div>
    <p>Comparison of different vertical probability strengths. Vertical probability height range is set to (0.05, 0.45) for all examples. Left: 0, so no vertical probability. The bottom of the clouds is clearly too bright. Middle: 0.8. The bottom of the clouds looks correctly darkened now. Right: 1.6. This may be a bit too strong of an effect, but who knows, could be right for a given situation. Perhaps for darker rain clouds.</p>
</div>

#### Depth Probability Height Range
**C# member variable:** `Vector2 m_depthProbabilityHeightRange` \
Unit height range to apply depth in-scattering probability to.

#### Depth Probability Strength Range
**C# member variable:** `Vector2 m_depthProbabilityStrengthRange` \
Strength range of depth in-scattering probability applied over height range.

#### Depth Probability Density Multiplier
**C# member variable:** `float m_depthProbabilityDensityMultiplier` \
Pre-multiplier on density for depth in-scattering probability. Can be useful for bringing the density into a range where the effect is noticeable.

Generally, values between `2` and `4` are good.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/depth_dens_mult_1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/depth_dens_mult_6.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/depth_prob_bias_0.1.jpg"/></div>
    </div>
    <p>Comparison of depth probability density multipliers. The height range is set to (0.08, 0.5), the the strength range is set to (0.1, 1.5), and the bias is set to 0.1. Left: multiplier of 1. This causes too much in-scatter attenuation---it's too dark! Middle: multiplier of 6. This is too bright---no depth probability effect is visible! Right: multiplier of 3. This turns out to be just right for this example.</p>
</div>

#### Depth Probability Bias
**C# member variable:** `float m_depthProbabilityBias` \
Bias applied to depth probability effect. Can help to soften the effect and reign in the extremes.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/depth_prob_bias_1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/depth_prob_bias_0.1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/depth_prob_bias_0.jpg"/></div>
    </div>
    <p>Comparison of different depth probability biases. The height range is set to (0.08, 0.5), the the strength range is set to (0.1, 1.5), and the density multiplier is set to 3. Left: bias of 1, so no depth probability effect is visible. The clouds don't have a lot of visible internal structure. Middle: bias of 0.1. This gives a reasonable powdered sugar effect, where the cloud edges are darkened. Right: bias of 0.</p>
</div>


#### Self Shadowing
**C# member variable:** `bool m_selfShadowing` \
Whether or not the clouds cast shadows on themselves.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/self_shadow.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/no_self_shadow.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/fake_shadow.jpg"/></div>
    </div>
    <p>Self-shadowing vs. no self-shadowing, for a dense layer. In this case, the effect is very noticeable. Left: self-shadowing enabled. Looks good! Middle: no self-shadowing. As you can see, self-shadowing is pretty damned important! Right: you can try to approximate the self-shadowing effect using the vertical proability parameters. However this doesn't really look right, or very good.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/self_shadow_low_density.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/no_self_shadow_low_density.jpg"/></div>
    </div>
    <p>Now, self-shadowing vs. no self-shadowing, for a layer that's not very dense. It's still pretty important, though more subtle than with the dense layer. Left: self-shadowing enabled. Right: no self-shadowing.</p>
</div>

#### Multiple Scattering Amount
**C# member variable:** `float m_multipleScatteringAmount` \
As a way of approximating multiple scattering, Expanse adjusts the self-shadowing effect. This parameter controls the amount of approximated multiple scattering. Because it adjusts the self-shadowing effect, it is only available when self-shadowing is turned on. Otherwise, it is unnecessary.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/no_ms.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_0.25.jpg"/></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_0.5.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_0.75.jpg"/></div>
    </div>
    <p>Different amounts of multiple scattering. Multiple scattering bias is set to 0.15 for all three examples. Top Left: none. This looks pretty wrong! Top Right: 0.25. This is a bit better, maybe for rainclouds. Bottom Left: 0.5. This looks pretty good. Bottom Right: 0.75. This might be a bit too much, depending on the look you're going for.</p>
</div>

#### Multiple Scattering Bias
**C# member variable:** `float m_multipleScatteringBias` \
As a way of approximating multiple scattering, Expanse adjusts the self-shadowing effect. This parameter controls the "bias" of approximated multiple scattering---put another way, it controls the depth into the clouds at which multiple scattering is added. Because it adjusts the self-shadowing effect, it is only available when self-shadowing is turned on. Otherwise, it is unnecessary.

As a rule of thumb, lower values here are good for very dense, puffy clouds, since these exhibit a lot of multiple scattering deep in the cloud. Higher values are good for wispier clouds, since there's not much multiple scattering in that case.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_bias_0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_0.5.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_bias_0.25.jpg"/></div>
    </div>
    <p>Different multiple scattering biases. Multiple scattering amount is set to 0.5 for all three examples. Left: 0. This means self-shadowing is effectively turned off. Makes it clear just how important it is! Middle: 0.15. This looks good to my eyes. Right: 0.25. This is a bit too extreme.</p>
</div>

#### Cast Shadows
**C# member variable:** `bool m_castShadows` \
Whether or not this layer casts shadows on the ground and geometry. Enabling can incur a small performance cost.

#### Cel Shade
**C# member variable:** `bool m_celShade` \
Whether to use cel/"toon" shading on the clouds. This is sort of a fun, experimental feature, so take it with a grain of salt.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/cel_shade_0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/cel_shade_1.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/cel_shade_2.jpg"/></div>
    </div>
    <p>A few examples of cel-shaded volumetric clouds.</p>
</div>

#### Cel Shade Color Bands
**C# member variable:** `bool m_celShadeColorBands` \
Specifies the number of color bands to use when cel shading.

#### Cel Shade Transmittance Band
**C# member variable:** `bool m_celShadeTransmittanceBands` \
Specifies the constant transmittance band to use when cel shading.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MOVEMENT --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Movement
These parameters dictate how the clouds move across the sky.

#### Use Offset
**C# member variable:** `bool m_useOffset` \
Whether to use [offset](/editor/blocks/procedural_cloud_volume_block?id=sampling-offset) or [velocity](/editor/blocks/procedural_cloud_volume_block?id=velocity) to position the clouds.

#### Velocity
**C# member variable:** `Vector2 m_coverageVelocity, m_baseVelocity, m_structureVelocity, m_detailVelocity, m_baseWarpVelocity, m_detailWarpVelocity` \
Velocity of the clouds, specifiable for each noise layer in uv space. Setting this to a non-zero number automatically moves the noise layer across the sky at the specified speed. In particular, it automates the sampling offset parameter.

Having this specifiable for each layer gives you more control over how you want the clouds to change. For instance, if you want the clouds to **appear** to be moving, but really stay in the same place, you could only set the detail velocity.

#### Sampling Offset
**C# member variable:** `Vector2 m_coverageOffset, m_baseOffset, m_structureOffset, m_detailOffset, m_baseWarpOffset, m_detailWarpOffset` \
Sampling offset of the clouds, specifiable for each noise layer in uv space. Can be animated as an alternative to the [velocity](/editor/blocks/procedural_cloud_volume_block?id=velocity) parameter.

Having this specifiable for each layer gives you more control over how you want the clouds to change. For instance, if you want the clouds to **appear** to be moving, but really stay in the same place, you could only animate the detail velocity.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- QUALITY ---------------------------------------->
<!---------------------------------------------------------------------------------------->

### Quality
These parameters tweak the rendering algorithm to trade quality for performance.

Of particular note here are the raymarching parameters---the step ranges, and the zero thresholds. These will give you the biggest performance/quality tradeoff.

Here's an example of what it looks like when you don't use enough samples.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/sample_bad.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/sample_good.jpg"/></div>
    </div>
    <p>Comparison of sampling strategies. Left: using far to few samples. This reveals the blue noise sample offset pattern used to de-band the rendering result. It looks bad! Right: using a sufficient amount of samples. This looks right!</p>
</div>

#### Reprojection Frames
**C# member variable:** `int m_reprojectionFrames` \
Number of history frames to use for reprojection. Increasing can improve performance, but at the cost of quality.

Be aware that Temporal Anti-Aliasing (TAA) can make the clouds appear blurry when using reprojection.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/no_reproj.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/4x_reproj.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/4x_reproj_taa.jpg"/></div>
    </div>
    <p>Comparison of reprojection settings. Left: no reprojection. Middle: maximum reprojection, so rendering at 1/16th of the resolution. Reprojection gives great results if the clouds are slow-moving and far away. In this case the images are nearly indistinguishable. Right: also maximum reprojection, but with TAA enabled. The clouds appear blurrier.</p>
</div>

#### Coarse Step Range
**C# member variable:** `Vector2 m_coarseStepRange` \
Step number range for coarse ray marching. The lower and upper limits are applied at the lower and upper [distance range](/editor/blocks/procedural_cloud_volume_block?id=step-distance-range) values. This lets you use fewer samples for smaller march distances.

#### Detail Step Range
**C# member variable:** `Vector2 m_detailStepRange` \
Step number range for detail ray marching. The lower and upper limits are applied at the lower and upper [distance range](/editor/blocks/procedural_cloud_volume_block?id=step-distance-range) values. This lets you use fewer samples for smaller march distances.

#### Step Distance Range
**C# member variable:** `Vector2 m_stepDistanceRange` \
Distance range over which to apply the step number ranges.

#### Media Zero Threshold
**C# member variable:** `float m_mediaZeroThreshold` \
Threshold below which normalized cloud density is considered to be zero. It is important to set this to something above zero, like `1e-4` or `1e-5`, as it determines an important early out for the algorithm.

#### Transmittance Zero Threshold
**C# member variable:** `float m_transmittanceZeroThreshold` \
Threshold below which cloud transmittance is considered to be zero. It is important to set this to something above zero, like `1e-4` or `1e-5`, as it determines an important early out for the algorithm.

#### Max Consecutive Zero Samples
**C# member variable:** `int m_maxConsecutiveZeroSamples` \
Max number of consecutive zero samples before detail ray marching switches back to coarse ray marching.

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
The Sky and Fog Volume that backs this procedural cloud block. In all likelihood, this should be a single global volume you set up at the start of your project. More complex workflows may have multiple local volumes, in which case you will need to create multiple cloud blocks (one for each volume), or programmatically change this parameter.
