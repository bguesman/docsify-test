# Procedural Cloud Volume

> Implemented as class `Expanse.ProceduralCloudVolume` in `blocks/ProceduralCloudVolume.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/1-5-0/banner-3.jpg"/></div>
    </div>
</div>

Adding this block to your scene will create a 3D cloud volume whose density field is controlled by a combination of procedural noises and, optionally, static 3D textures.

<!---------------------------------------------------------------------------------------->
<!---------------------------------------- PRESETS --------------------------------------->
<!---------------------------------------------------------------------------------------->
## Preset Management

As of v1.5, the preferred way to author, save, and load presets for cloud layers is via the Preset Browser, or, programmatically, the `ToUniversal()` and `FromUniversal()` functions.

Expanse stores cloud presets in a "universal" format---universal meaning, "unified across the different types of cloud layers" (2D, 3D, procedural, texture-based, etc.). This universal representation is serialized as a [`ScriptableObject`](https://docs.unity3d.com/ScriptReference/ScriptableObject.html) on save.

What this means for you is that, to save a preset from editor, you can just click the "Save Preset" button, and a file dialogue will pop up for you to specify the path to save the preset file to. A screen capture of the currently rendering main camera will automatically be used as preset image---so line up your shot carefully!

To then load that preset, you can browse for it in the preset browser. You may have to hit `Refresh Library` to get it to show up.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width: 80%" src="img/procedural_cloud_volume/preset-browser.jpg"/></div>
    </div>The cloud preset browser.</p>
</div>

To do this programmatically, you can use the `ToUniversal()` and `FromUniversal()` functions, defined in `blocks/advanced/BaseCloudLayer.cs`, which all cloud layers derive from. Each of these functions accepts a `UniversalCloudLayer` preset to load from. In the editor, you can load these presets via the `AssetDatabase` class, but in your game you will either need to use `Resources.Load()`, or (better yet) store instances of the presets you'd like to switch between on a GameObject (like a weather controller).

You can also use [Unity's preset system](https://docs.unity3d.com/Manual/Presets.html) to manage presets, but this has the disadvantage of not being runtime-compatible (you can only select presets in the editor). In fact, the entire reason this ScriptableObject-based preset system exists is to get around this unfortunate limitation of Unity presets.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->
## Modeling
We can split the parameters related to modeling clouds into two groups:

* **Shaping and Geometry:** parameters related to the overall form of clouds
* **Noise:** parameters related to crafting the noises that describe how the clouds look more locally

### Shaping and Geometry
These parameters describe the general shape, form, and distribution of the clouds.

#### Geometry Extents and Origin
As of v1.5, the origin and size of the cloud volume are controlled via the Unity `GameObject` transform.

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

#### Structure Multiply
**C# member variable:** `float m_structureMultiply` \
How much the structure noise layer multiplies the base noise layer. Good for creating internal details in the cloud that are visible when the sun shines through.

#### Detail Intensity
**C# member variable:** `float m_detailIntensity` \
How much the detail noise layer erodes the base noise layer.

#### Detail Multiply
**C# member variable:** `float m_detailMultiply` \
How much the detail noise layer multiplies the base noise layer. Good for creating internal details in the cloud that are visible when the sun shines through.

#### Base Warp Intensity
**C# member variable:** `float m_baseWarpIntensity` \
How much the base noise layer is warped by its warp texture.

#### Detail Warp Intensity
**C# member variable:** `float m_detailWarpIntensity` \
How much the detail noise layer is warped by its warp texture.

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

#### Attenuation Origin
**C# member variable:** `Transform m_attenuationOrigin` \
Optional origin from which the density attenuation and ramp up is computed. If none is specified, the origin of the cloud volume is used. 

You might, for instance, want to set this to the player's transform. Or, if your map is small, to the center of your map.

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

#### Density Curve
**C# member variable:** `AnimationCurve m_densityCurve` \
Controls how the density of the cloud volume varies across the cloud volume's height. This is a pretty important parameter to tweak to get realistic-looking cloud shapes. For instance, with this parameter you can model,

* Softer cloud bottoms for cumulus clouds
* Wispy anvil formations due to ice crystallization in the upper atmosphere

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/density-curve-bad-editor.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/density-curve-bad.jpg"/></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/density-curve-good-editor.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/density-curve-good.jpg"/></div>
    </div>
    <p>Comparison of different density curves. Top: result with a constant density curve. This looks pretty weird. Bottom: result with a more reasonable density curve, where the cloud density increases over height. This softens the bottoms of the clouds and makes them look much more natural.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/cumulonimbus-density.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/cumulonimbus-coverage.jpg"/></div>
    </div>
    <p>An example of another density curve, used to decrease the density of an anvil structure relative to the base of some massive cumulonimbus clouds.</p>
</div>

#### Coverage Curve
**C# member variable:** `AnimationCurve m_coverageCurve` \
Controls how the coverage of the cloud volume varies across the cloud volume's height. This is crucial for shaping the clouds to achieve a realistic result. This parameter can help you round off the tops of cumulus clouds, create massive anvil formations, etc.

> This parameter replaces the height gradient and rounding parameters that were used to shape clouds prior to v1.5.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage-curve-bad-editor.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage-curve-bad.jpg"/></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage-curve-good-editor.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/coverage-curve-good.jpg"/></div>
    </div>
    <p>Comparison of different coverage curves. Top: result with a constant coverage curve. This looks bad! Bottom: result with a more reasonable density curve, where the cloud density increases over height, and then decreases to round off the cloud tops. This makes the cloud shapes look at lot more familiar.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/cumulonimbus-coverage-editor.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/cumulonimbus-coverage.jpg"/></div>
    </div>
    <p>An example of another coverage curve, used to create an anvil formation in huge cumulonimbus clouds.</p>
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
### Noise
These parameters describe the noises that Expanse uses to construct the density field. This is in contrast to the more global shaping parameters in the Shaping and Geometry section.

#### Noise Layers
**C# member variable:** `Expanse.ProceduralCloudVolumeBlock.LayerSettings[] m_noiseLayers` \
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
* `PerlinWorley2`: An alternate combination of `Worley` and `Perlin` noise. Another great option for cloud base noise.
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

## Lighting
Expanse's cloud lighting model can be broken up into 3 categories:

* **Base Lighting**. This is all the lighting that is fully physical, with no approximations. If Expanse's clouds were path-traced with thousands of bounces, this would be all you need to have the clouds look fully correct.
* **Self Shadowing**. Expanse lumps all direct light attenuation that results from clouds casting volumetric shadows onto themselves into this category. Approximations are made here to make the clouds more art-directable and more performant.
* **Multiple Scattering**. The last two categories would be enough to render convincing smoke or fog, but clouds are very dense, and so light scatters many times within them. Expanse uses a novel approach to approximate the effect of these additional light bounces in a way that's convincing and performant.

We'll split up the parameters into these sections.

### Base Lighting

This is all the lighting that is fully physical, with no approximations. If Expanse's clouds were path-traced with thousands of bounces, this would be all you need to have the clouds look fully correct.

#### Density
**C# member variable:** `float m_density` \
Density of this cloud layer.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/density-5k.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/density-30k.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/density-200k.jpg"/></div>
    </div>
    <p>Comparison of different densities. Left: density 5000. Middle: density 30000. Right: density 200000.</p>
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
        <div class="img-col"><img src="img/procedural_cloud_volume/too-dark.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/bump-scatter.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms-correction.jpg"/></div>
    </div>
    <p>If the clouds you're working on look too dark, your first inclination might be to adjust your scattering coefficients to be brighter than your extinction coefficients. This is non-physical! If you find this is the case, you should first try to use the multiple scattering parameters to fix the issue. Left: clouds that are too dark. Middle: correcting the issue by upping the scattering coefficients to 4x the extinction coefficients. This looks ok but is non-physical. Right: correcting the issue by upping the multiple scattering receptive field. This looks a lot better and is physical! At the end of the day, it's your choice, and different style goals may call for different strategies. That said, it's a principle of mine to always know where your model deviates from physical reality, and make sure it's intentional.</p>
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

#### Physical Ambient
**C# member variable:** `bool m_physicalAmbient` \
When enabled, computes proper physical ambient lighting, as opposed to using the height-based approximation. This can produce some stunning results, especially at twilight, but is prohibitively expensive for most applications. You should in general use this as a reference for tuning the height-based approximation.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/approximate_ambient_000.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/physical_ambient_000.jpg"/></div>
    </div>
    <p>Left: height-based ambient approximation. Right: physical ambient lighting. The clouds here exhibit much more plausible self-shadowing, and their purple hue is pleasantly non-uniform, accordance with the varying appearance of the skydome.</p>
</div>

### Self Shadowing

Expanse lumps all direct light attenuation that results from clouds casting volumetric shadows onto themselves into this category. Approximations are made here to make the clouds more art-directable and more performant.

#### Self Shadowing
**C# member variable:** `bool m_selfShadowing` \
Whether or not the clouds cast shadows on themselves. Enabling this incurs a significant performance cost, but is usually necessary to achieve the desired visual result.

When disabled, a (cost-free) height-based approximation is used as an alternative. It is, on inspection, similar to the one used by Cyberpunk 2077, and generally looks ok for very dense clouds.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/self_shadow.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/no_self_shadow.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/fake_shadow.jpg"/></div>
    </div>
    <p>Self-shadowing vs. no self-shadowing, for a dense layer. In this case, the effect is very noticeable. Left: self-shadowing enabled. Looks good! Middle: no self-shadowing. As you can see, self-shadowing is pretty damned important! Right: you can try to approximate the self-shadowing effect using the vertical proability parameters. This looks a lot worse than using correct self-shadowing, but can function as a tolerable alternative.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/self_shadow_low_density.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/no_self_shadow_low_density.jpg"/></div>
    </div>
    <p>Now, self-shadowing vs. no self-shadowing, for a layer that's not very dense. It's still pretty important, though more subtle than with the dense layer. Left: self-shadowing enabled. Right: no self-shadowing.</p>
</div>

#### Max Self Shadow Distance
**C# member variable:** `float m_maxSelfShadowDistance` \
Maximum distance that clouds can cast shadows onto themselves/each other. If this value is set too high, you may lose some detail in your cloud self-shadowing.

#### Shadow Persistence
**C# member variable:** `float m_shadowPersistence` \
How intense shadowing is. This parameter actually softens shadows by adjusting the multiple scattering approximation, so if the [multiple scattering receptive field](editor/blocks/procedural_cloud_volume_block.md?id=multiple-scattering-receptive-field) is zero, this will have no effect.

> This parameter is similar to the Multiple Scattering Bias parameter from versions prior to v1.5.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/shadow-persistence-0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/shadow-persistence-0.2.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/shadow-persistence-1.jpg"/></div>
    </div>
    <p>Comparison of different shadow persistences. Left: minimum persistence of 0. Middle: persistence of 0.2. Right: maximum persistence of 1.</p>
</div>

#### Height Shadow Range
**C# member variable:** `float m_heightShadowRange` \
Range over which approximate height-based shadowing is applied, when self-shadowing is disabled. 0 means the bottom of the clouds, 1 means the top.

#### Height Shadow Intensity
**C# member variable:** `float m_heightShadowIntensity` \
Range of shadow intensity applied over the specified height gradient. The lower value is the value applied at the lower end of the gradient (the bottom of the clouds), and the higher value is the value applied at the higher end of the gradient (the top of the clouds).

### Multiple Scattering

The last two categories would be enough to render convincing smoke or fog, but clouds are very dense, and so light scatters many times within them. Expanse uses a novel approach to approximate the effect of these additional light bounces in a way that's convincing and performant.

#### Multiple Scattering Receptive Field
**C# member variable:** `float m_multipleScatteringReceptiveField` \
As a way of approximating multiple scattering, Expanse integrates successive orders of multiple scattering using a novel technique. To avoid giving away exactly how it works, I can't explain it fully here. **The end result is that this parameter basically controls how much multiple scattering there is.**

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/receptive-field-0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/receptive-field-1.5.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/receptive-field-4.jpg"/></div>
    </div>
    <p>Different multiple scattering receptive field values. Left: receptive field of 0, so no multiple scattering. Middle: receptive field of 1.5, what looks to be a pretty good value. Right: receptive field of 4, which looks like it might be a little too much.</p>
</div>


#### High Detail Multiple Scattering
**C# member variable:** `bool m_highDetailMultipleScattering` \
Controls how detailed the multiple scattering approximation is. If this is disabled, the multiple scattering will be smoother. If it's enabled, it'll be more detailed. There's no additional expense either way---it's a fully stylistic choice.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/low-detail-ms.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/high-detail-ms.jpg"/></div>
    </div>
    <p>Left: low detail multiple scattering. Right: high detail multiple scattering. The difference is subtle but noticeable.</p>
</div>

#### Phase Persistence
**C# member variable:** `float m_phasePersistence` \
How much the phase function affects the multiple scattering. Decreasing this will brighten the clouds on the opposite side of the sky as the sun.

> The rationale for this parameter comes from the fact that higher orders of multiple scattering lose their directionality. The phase function grows closer to isotropic with each successive order of scattering. This parameter allows you to art-direct this physical phenomenon.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/phase-persistence-0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/phase-persistence-0.5.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/phase-persistence-1.jpg"/></div>
    </div>
    <p>Comparison of the effect of different phase persistences on the clouds at the back of the sky. Left: phase persistence of 0. The phase function is effectively bypassed when computing multiple scattering, so the clouds are bright. Middle: phase persistence of 0.5. The clouds are darker. Right: phase persistence of 1. The anisotropic phase function effects the multiple scattering in full force, and the clouds are very grey.</p>
</div>

#### Allow Non-Physical
**C# member variable:** `bool m_allowNonPhysicalMultipleScattering` \
Whether to show UI controls that allow you to tweak multiple scattering to be "non-physical". In actuality the entire multiple scattering algorithm is one big art-directable approximation, but some parameters are more in the spirit of being PBR than others.

#### Multiple Scattering Multiplier
**C# member variable:** `float m_multipleScatteringMultiplier` \
Non-physically inspired way to increase the multiple scattering. Simply post-multiplies the result. First try adjusting the [receptive field](editor/blocks/procedural_cloud_volume_block.md?id=multiple-scattering-receptive-field), and only resort to this if you can't get that to work.

#### Multiple Scattering Bias
**C# member variable:** `float m_multipleScatteringBias` \
Partially physically-inspired bias to the multiple scattering. It does more than just add a constant amount to the scattering result, but it's not quite in line with the physical parts of the model. Still, it can be very useful for softening the multiple scattering effect.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/ms-bias-0.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms-bias-0.02.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/ms-bias-0.1.jpg"/></div>
    </div>
    <p>Comparison of the effect of different multiple scattering biases. Left: bias of 0. The creases in the low-density parts of the clouds are quite grey. Middle: bias of 0.02. This is a very small bias, but it helps push a little light into those places. Right: bias of 0.1. This is a pretty high bias, and results in relatively featureless multiple scattering.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!------------------------------------- INTERACTION -------------------------------------->
<!---------------------------------------------------------------------------------------->

## Interaction

These parameters control how the clouds interact with different systems in your game.

### Light Pollution Dimmer
**C# member variable:** `float m_lightPollutionDimmer` \
Dimmer to the effect of light pollution on clouds. When it's zero, the clouds receive no light pollution. When it's one, they receive full light pollution. This is a useful parameter for when you're using light pollution as more of an artistic effect to recolor the night sky.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/full-lp.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/dimmed-lp.jpg"/></div>
    </div>
    <p>Left: no light pollution dimming. Notice how the clouds are very blue. Right: full light pollution dimming.</p>
</div>

### Cast Shadows
**C# member variable:** `bool m_castShadows` \
Whether or not this layer casts shadows on the ground and geometry. Enabling can incur a small performance cost.

### Max Shadow Intensity
**C# member variable:** `float m_maxShadowIntensity` \
How dark the shadows that the clouds cast onto the ground are.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MOVEMENT --------------------------------------->
<!---------------------------------------------------------------------------------------->

## Movement
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
<!----------------------------------- POST-PROCESSING ------------------------------------>
<!---------------------------------------------------------------------------------------->

## Post-Processing

Starting in v1.5, Expanse allows you to inject custom post-process operations after cloud layer rendering. 

Custom post-processing passes are implementors of the `ScriptableObject` type `ICloudPostProcessPass`, which is as follows:

```

namespace Expanse {

public struct CloudPostProcessPassInfo {
    public CommandBuffer cmd;
    public RTHandle sourceColorBuffer;
    public RTHandle sourceTransmittanceAndHitBuffer;
    public RTHandle targetColorBuffer;
    public RTHandle targetTransmittanceAndHitBuffer;
    public int bufferIndex;
};

public abstract class ICloudPostProcessPass : ScriptableObject {

    /**
     * @brief: called after a cloud layer is rendered. Allows you to do whatever you
     * will with the cloud's color and transmittance/hit buffer.
     * 
     * In general, you'll probably want to modify:
     *  - color.xyz
     *  - transmittanceAndHit.xyz
     * 
     * You SHOULD NOT touch transmittanceAndHit.w, because that's where the cloud's approximate
     * depth is stored. Well... unless you _really_ want to screw with shit.
     * */
    public abstract void Render(CloudPostProcessPassInfo info);

}

} // namespace Expanse

```

To apply these custom passes to a cloud layer, you can create instances of them in your `Assets` folder in the way that you would normally create `ScriptableObjects`. You can then add these instances to the list in the `Post-Processing` dropdown. Operations will be applied in the order of the list.

Expanse provides a number of example implementations, including a painterly Kuwahara filter, an alpha-overlay, and a simple recolor pass. These are located in the directory `Expanse/custom-post-process-examples`. Feel free to use them in your game or use them as a reference for building your own post-process passes.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/post-process.jpg"/></div>
    </div>
    <p>Pseudo-painterly look achieved with a kuwahara pass and an alpha overlay pass with a brush strokes texture.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!-------------------------------------- SAMPLING ---------------------------------------->
<!---------------------------------------------------------------------------------------->


## Sampling
These parameters tweak the noise sampling strategy to trade quality for performance.

Here's an example of what it looks like when you don't use enough samples.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/sample_bad.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/sample_good.jpg"/></div>
    </div>
    <p>Comparison of sampling strategies. Left: using far to few samples. This reveals the blue noise sample offset pattern used to de-band the rendering result. It looks bad! Right: using a sufficient amount of samples. This looks right!</p>
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

#### Flythrough Step Range
**C# member variable:** `Vector2 m_flythroughStepRange` \
Step distance range used for certain cases of flythrough ray marching. The lower and upper limits are applied at the lower and upper [distance range](/editor/blocks/procedural_cloud_volume_block?id=flythrough-step-distance-range) values. This lets you use fewer samples for smaller march distances.

This parameter is different from the non-flythrough step ranges---it specifies step distance, not in step count. A reasonable value for this might be something like `(16, 512)`, which will take 16 meter steps up close, and 512 meter steps far away.

#### Flythrough Step Distance Range
**C# member variable:** `Vector2 m_flythroughStepDistanceRange` \
Distance range over which to apply the flythrough step range.


#### Sample Jitter
**C# member variable:** `float m_sampleJitter` \
Amount to jitter primary ray-marching samples along the ray spatially (from pixel to pixel) and temporally (across frames). Set this 
* To `0` to reduce noise if you don't plan on using temporal denoising/DLSS/TAA. You may also want to set it to `0` if you're heavily leveraging reprojection or subresolution.
* To `0.5` if you want correct stochastic rendering for use with temporal denoising/DLSS/TAA.
* To `1` if you still see noticeable banding artifacts at 0.5. This will "over-jitter" the samples, which can help further reduce banding, at the cost of more noise.

#### Pixel Jitter
**C# member variable:** `float m_pixelJitter` \
Amount to jitter the ray direction from frame to frame. This is particularly important for anti-aliasing results rendered at lower resolution.

#### Shadow Sample Jitter
**C# member variable:** `float m_shadowSampleJitter` \
How much to jitter secondary raymarching---self-shadow---samples. Increasing this can cause noise, but if you're using DLSS, it can greatly improve the smoothness of the shadows. If you're not using DLSS or are rendering clouds with reprojection enabled, you'll probably want to set this parameter to `0`, indicating not to use any jitter.

#### LOD Distances
**C# member variable:** `Vector2 m_LODDistances` \
Distances at which to transition to medium and low LOD rendering. It's best to set these to where the change is just imperceptible, to improve performance.

#### Media Zero Threshold
**C# member variable:** `float m_mediaZeroThreshold` \
Threshold below which normalized cloud density is considered to be zero. It is important to set this to something above zero, like `1e-4` or `1e-5`, as it determines an important early out for the algorithm.

#### Transmittance Zero Threshold
**C# member variable:** `float m_transmittanceZeroThreshold` \
Threshold below which cloud transmittance is considered to be zero. It is important to set this to something above zero, like `1e-4` or `1e-5`, as it determines an important early out for the algorithm.

#### Max Consecutive Zero Samples
**C# member variable:** `int m_maxConsecutiveZeroSamples` \
Max number of consecutive zero samples before detail ray marching switches back to coarse ray marching.

#### Light Sampling Strategy
**C# member variable:** `int m_lightSamplingStrategy` \
Strategy to use for selecting which directional light(s) to sample when evaluating the cloud lighting. This parameter can have a massive impact on performance when using two or more celestial bodies. One of:

* **All:** sample all directional lights. Most expensive when there are multiple lights, but guarantees there will be no visual artifacts. Cheapest when there is only one light, since no special logic is required.
* **Stochastic:** randomly sample only one directional light. Samples are drawn from a distribution that samples bright lights more frequently, and excludes occluded lights.
* **Brightest:** sample only the brightest non-occluded light. This strategy works beautifully for cases where you have a very bright and a very dim light---i.e., the moon and the sun.


<!---------------------------------------------------------------------------------------->
<!----------------------------------- REPROJ/DENOISE ------------------------------------->
<!---------------------------------------------------------------------------------------->

## Reprojection and Denoising
These parameters allow you to use temporal amortization to reduce the cost of rendering your clouds, either by rendering fewer pixels for frame (reprojection) or using data from previous frames to supplement the samples taken in the current frame, allowing you to use lower sample counts (denoising).

#### Reprojection Frames
**C# member variable:** `int m_reprojectionFrames` \
Number of history frames to use for reprojection. Increasing can improve performance, but at the cost of quality.

Be aware that Temporal Anti-Aliasing (TAA) can make the clouds appear blurry when using reprojection, though less so than in versions prior to 1.5.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/no_reproj.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/4x_reproj.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/4x_reproj_taa.jpg"/></div>
    </div>
    <p>Comparison of reprojection settings. Left: no reprojection. Middle: maximum reprojection, so rendering at 1/16th of the resolution. Reprojection gives great results if the clouds are slow-moving and far away. In this case the images are nearly indistinguishable. Right: also maximum reprojection, but with TAA enabled. The clouds appear blurrier.</p>
</div>

#### Use Temporal Denoising
**C# member variable:** `bool m_useTemporalDenoising` \
When enabled, Expanse will use TAA-based denoising to allow fewer samples to be taken per frame. Be careful when you use this in combination with reprojection, as things can start to get quite blurry.

#### Denoising History Frames
**C# member variable:** `int m_denoisingHistoryFrames` \
Number of history frames to use for temporal denoising. You should probably keep this low if you plan on having rapid cloud movement or cloud flythrough.

#### Denoising Depth Rejection
**C# member variable:** `bool m_denoisingDepthRejection` \
Whether or not to reject denoising history samples based on their approximate depth. Can be useful for reducing blurriness during flythrough or when rendering fast-moving clouds.

#### Depth Rejection Threshold
**C# member variable:** `Vector2 m_depthRejectionThreshold` \
Depth thresholds at which to reject denoising history samples. Specified as a multiplier of the current sample depth. So, for instance, if we set this to `(0.5, 2)`, it would mean we'd reject samples that were closer than half the current depth, and further than twice the current depth.

#### Catmull Rom Filtering
**C# member variable:** `bool m_catmullRomFiltering` \
If enabled, uses Catmull Rom filtering to sample from history buffers (for both denoising and reprojection). This can significantly reduce blurriness due to camera motion.

#### Neighborhood Clamping
**C# member variable:** `bool m_neighborhoodClamping` \
If enabled, uses neighborhood clamping to constrain history samples (for both denoising and reprojection). This can be useful for flythrough and fast-moving clouds, but can introduce artifacts and inhibit the effectiveness of denoising.

#### Neighborhood Clamp Intensity
**C# member variable:** `float m_neighborhoodClampIntensity` \
How strong the neighborhood clamping effect is. 1 is full strength. 0 is minimum strength.
