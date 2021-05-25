# Procedural Nebulae Block

> Implemented as class `Expanse.ProceduralNebulaeBlock` in `blocks/ProceduralNebulaeBlock.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/nebulae_banner.png"/></div>
    </div>
</div>

This block exposes the settings for Expanse's procedural nebulae authoring system.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->
### General Modeling
These parameters describe the general shape and form of the nebulae, independent from the [noise layers](/editor/blocks/procedural_nebulae_block?id=noise-layers).

#### Quality
**C# member variable:** `Expanse.Datatypes.Quality m_quality` \
Quality of procedural nebulae texture.

#### Rotation
**C# member variable:** `Vector3 m_rotation` \
The rotation of the nebulae, specified as euler angles.

#### Coverage Scale
**C# member variable:** `float m_coverageScale` \
Scale of noise used for determining nebula coverage. If this value is high, there will be lots of little nebulae scattered across the sky. If this value is low, there will be a few huge nebulae.

#### Transmittance Scale
**C# member variable:** `float m_transmittanceScale` \
Scale of noise used to randomize nebula transmittance.

#### Transmittance Range
**C# member variable:** `float m_transmittanceRange` \
Range of transmittance values the nebula can have. If the range is set to `(1, 1)`, it will be fully opaque. If it's set to `(0, 0)`, it will be fully transparent---invisible!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/transmittance_1_1.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/transmittance_0_1.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/transmittance_0_0.5.jpg"/></div>
    </div>
    <p>Different transmittance ranges. Left: range of (1, 1), so fully opaque. This clearly lacks depth and also obscures all the stars. Middle: Full range of (0, 1). Some parts are fully transparent, and some parts are fully opaque. This imbues the nebula with a sense of depth and reveals some stars. Right: range of (0, 0.5), so no part of the nebula is fully opaque.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- LIGHTING --------------------------------------->
<!---------------------------------------------------------------------------------------->

### General Lighting
These parameters describe the color and illumination characteristics of the atmosphere layer.

#### Overall Intensity
**C# member variable:** `float m_overallIntensity` \
The overall intensity of the nebulae.

#### Overall Definition
**C# member variable:** `float m_overallDefinition` \
Global definition control for the whole nebula texture. This increases saturation and contrast. It's useful to use in tandem with the global intensity control.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/overall_definition_1.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/overall_definition_4.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/overall_definition_8.jpg"/></div>
    </div>
    <p>Different overall definition values. Overall intensity is adjusted to compensate. Left to right: 1, 4, 8. Setting definition to higher values is pretty important to achieve the bright, saturated look of the nebulae photos we are all familiar with.</p>
</div>

#### Overall Tint
**C# member variable:** `Color m_overallTint` \
Global tint control for the whole nebula texture.

<!---------------------------------------------------------------------------------------->
<!------------------------------------- NOISE LAYERS ------------------------------------->
<!---------------------------------------------------------------------------------------->
### Noise Layers
**C# member variable:** `Expanse.ProceduralNebulaeBlock.LayerSettings[] m_layers` \
Expanse models nebulae as layered noise. The noise layer workflow lets you author and tweak each layer individually, to be composited together in the final nebulae texture. Currently, you can use up to four noise layers.

Each layer provides a number of authoring controls to maximize expressivity.

#### Intensity
**C# member variable:** `float intensity` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Intensity or brightness of the layer. Multiplied through the general intensity control.

#### Color
**C# member variable:** `Color color` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Color of the layer.

#### Noise Type
**C# member variable:** `Expanse.Datatypes.NoiseType noise` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Type of noise to use for this layer. Expanse provides 7 noise distributions, specified by the enum `Expanse.Datatypes.NoiseType`. Refer to the figure below for a comparison.
* `Constant`: exactly what it sounds like---just a flat, solid color.
* `Value`: a sharper noise, helpful for modeling more intense striated gas clouds.
* `Perlin`: a very soft noise, useful for modeling big, featureless swaths of gas.
* `Worley`: Looks kind of like honeycomb. Good for modeling bright nebula strands and highlights.
* `InverseWorley`: the inverse of `Worley`---a billowy cell noise, good for modeling puffy clouds.
* `PerlinWorley`: A combination of `Perlin` and `InverseWorley` noise---so kind of like a softer version of vanilla `Worley` noise. Also good for cloud-like structures.
* `Curl`: if you use this one it will look *weird*.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/constant.jpg"/><div>Constant</div></div>
        <div class="img-col"><img src="img/nebulae/value.jpg"/><div>Value</div></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/perlin.jpg"/><div>Perlin</div></div>
        <div class="img-col"><img src="img/nebulae/worley.jpg"/><div>Worley</div></div>
    </div> 
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/inverse_worley.jpg"/><div>Inverse Worley</div></div>
        <div class="img-col"><img src="img/nebulae/perlin_worley.jpg"/><div>Perlin Worley</div></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/nebulae/curl.jpg"/><div>Curl. Funky, but maybe someone will do something cool with it!</div></div>
    </div>
</div>

#### Scale
**C# member variable:** `float scale` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Scale of base octave of noise. Smaller values give bigger more global features, larger values give smaller more detailed features.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/scale_4.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/scale_8.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/scale_16.jpg"/></div>
    </div>
    <p>One octave of worley noise at differing scales. Left to right: 4, 8, 16.</p>
</div>

#### Octaves
**C# member variable:** `int octaves` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Number of noise octaves. Increasing the number of octaves can dim the overall noise texture, so it is useful to adjust the intensity control in tandem with this parameter.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/scale_8.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/3_octaves.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/6_octaves.jpg"/></div>
    </div>
    <p>Differing octaves of worley noise. Left to right: 1 octave, 3 octaves, 6 octaves. Layering octaves of noise this way, known as "fractal brownian motion" or "fbm" for short, is key to developing complex structures.</p>
</div>

#### Octave Scale
**C# member variable:** `float octaveScale` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Scale multiplier applied to additional octaves of noise. As an example, if this value is 2, each octave will be twice as small as the last octave.

#### Octave Multiplier
**C# member variable:** `float octaveMultiplier` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Intensity multiplier applied to additional octaves of noise. As an example, if this value is 0.5, each octave will be half as intense as the last octave.

#### Coverage
**C# member variable:** `float coverage` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
How much the coverage map effects this layer. A higher value will result in more nebula coverage. A lower value will result in less nebula coverage.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/coverage_0.25.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/coverage_0.5.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/coverage_1.jpg"/></div>
    </div>
    <p>Different coverage values. Left to right: 0.25, 0.5. 1. </p>
</div>

#### Spread
**C# member variable:** `float spread` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
This parameter allows the layer to bleed across the coverage boundary, and is useful for softening edges.

#### Bias
**C# member variable:** `float bias` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Bias of zero value. This can be used as an alternative to coverage, if you want to ignore the coverage map.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/bias_negative_0.45.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/bias_negative_0.25.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/coverage_1.jpg"/></div>
    </div>
    <p>Different biases. Intensity is adjusted to compensate Left to right: -0.45, 0.25, 0. </p>
</div>

#### Definition
**C# member variable:** `float definition` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
This increases saturation and contrast, generally making the layer punchier. Increasing the definition usually requires also increasing the strength parameter to ensure that the strands can still get through the coverage map.

#### Strength
**C# member variable:** `float strength` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
This parameter is meant to be used in tandem with the coverage value. Higher strength values will allow more features to push through the coverage map. The best way to see what this parameter does is to play around with it.

#### Warp Intensity
**C# member variable:** `float warpIntensity` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Intensity of warping effect. Nebulae are big bodies of interstellar gas, and so they obey the laws of fluid mechanics. It's important to capture some of the resulting swirly fluid features. Warping the base noise texture can help with that.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/no_warp.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/warp_0.15.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/warp_0.3.jpg"/></div>
    </div>
    <p>Different warp intensities. Warp scale is 8 for all examples. Left to right: no warp, warp intensity of 0.15, warp intensity 0.3. </p>
</div>

#### Warp Scale
**C# member variable:** `float warpScale` in `Expanse.ProceduralNebulaeBlock.LayerSettings` \
Scale of the noise used to warp this layer. A higher value gives smaller vortices and tendrils. A lower value gives bigger swirls and arcs.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/nebulae/warp_scale_3.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/warp_0.3.jpg"/></div>
        <div class="img-col"><img src="img/nebulae/warp_scale_12.jpg"/></div>
    </div>
    <p>Different warp scales. Left to right: 3, 8, 12. </p>
</div>

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- METADATA --------------------------------------->
<!---------------------------------------------------------------------------------------->
### Metadata
These parameters are metadata or references to components/objects that the block uses.

#### Volume
**C# member variable:** `UnityEngine.Rendering.Volume m_volume` \
The Sky and Fog Volume that backs this procedural nebulae block. In all likelihood, this should be a single global volume you set up at the start of your project. More complex workflows may have multiple local volumes, in which case you will need to create multiple nebulae blocks (one for each volume), or programmatically change this parameter.
