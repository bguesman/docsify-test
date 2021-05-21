# Texture Cloud Plane Block

> Implemented as class `Expanse.TextureCloudPlaneBlock` in `blocks/TextureCloudPlaneBlock.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/banner_0.jpg"/></div>
    </div>
</div>

Adding this block to your scene will create a cloud plane whose density field is controlled by a single texture.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->
### Modeling
These parameters describe the general shape, form, and distribution of the clouds.

#### Texture
**C# member variable:** `Texture2D m_texture` \
Texture to use as the cloud density field. Interestingly enough, nothing too special is required here. Images of clouds often work fine, so long as they are,
1. High resolution.
2. Tileable ([easily accomplishable in photoshop](https://creativepro.com/creating-seamless-textures-in-photoshop/)).

#### Tile
**C# member variable:** `int m_tile` \
How many times to tile the texture.

#### Geometry Extents
**C# member variable:** `Vector2 m_geometryXExtent` and `Vector2 m_geometryZExtent` \
Extents of the cloud plane, so boundaries in X and Z directions.

#### Height
**C# member variable:** `float m_geometryHeight` \
Height of this cloud plane. Typically, cirrus clouds form 12000 or so meters above the Earth's surface.

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
        <div class="img-col"><img src="img/texture_cloud_plane/density_100.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/density_500.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/density_1500.jpg"/></div>
    </div>
    <p>Comparison of different densities. Apparent thickness is set to 1500 for all examples. Left: 100. Middle: 500. Right: 1500. No multiple scattering is applied, which is why the right-most image looks far too self-shadowed.</p>
</div>

#### Apparent Thickness
**C# member variable:** `float m_apparentThickness` \
Apparent thickness of this 2D cloud layer. Pushing this value too high can give strange results.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/app_thick_250.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/app_thick_750.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/app_thick_1500.jpg"/></div>
    </div>
    <p>Comparison of different apparent thicknesses. Density is set to 1000 for all examples. Left: 250. Middle: 750. Right: 1500. No multiple scattering is applied, which is why the right-most image looks far too self-shadowed.</p>
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
        <div class="img-col"><img src="img/texture_cloud_plane/scatter_0.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/scatter_1e-6.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/scatter_4e-6.jpg"/></div>
    </div>
    <p>Comparison of different scattering coefficient values. Extinction coefficients are set to (4e-6, 4e-6, 4e-6) in all images. Left: (0, 0, 0). This results in no scattering, so the clouds only blot out light---that is why they appear so dark here. The blue light you see is actually the result of atmospheric scattering rendered in front of the clouds, "aerial perspective". Middle: (1e-6, 1e-6, 1e-6). The scattering coefficients here are only 1/4th the magnitude of the extinction coefficients, so the clouds are noticeably dark. This is a good tactic to use for rain clouds. Right: (4e-6, 4e-6, 4e-6). The scattering coefficient here is the same as the extinction coefficient. In theory, this is the "correct" value, but it looks a little too bright to my eyes.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/neutral.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/purple.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/blood.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/blood_physical.jpg"/></div>
    </div>
    <p>Some fun stylized results from playing around with the scattering coefficient hue and saturation. Left: neutral, grey scattering coefficients---so, physically correct. Middle: Purple scattering coefficients. Middle Right: red scattering coefficients. Note that it's possible to achieve the red look with physically correct coefficients by putting the sun further over the horizon (pictured right).</p>
</div>

#### Anisotropy
**C# member variable:** `float m_anisotropy` \
Anisotropy of cloud scattering. Higher values will give more forward scattering. Lower values will give more backward scattering. A value of zero is neutral---i.e. it will produce \"isotropic\" scattering. Clouds are generally very forward-scattering, so a value of around 0.6 is a good physical approximation. However, sometimes this isn't aesthetically pleasing.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/anisotropy_0.2.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/anisotropy_0.6.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/anisotropy_0.8.jpg"/></div>
    </div>
    <p>Various anisotropy values. Left: 0.2. Middle: 0.6, a reasonable "physical" value. Right: 0.8.</p>
</div>

#### Silver Intensity
**C# member variable:** `float m_silverIntensity` \
Intensity of the characteristic silver lining around clouds close to the sun. This usually results from the phase function, so keeping things strictly physical would mean keeping this value at 0. However, sometimes this artistic override is required to achieve a desired look.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/no_silver.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/silver_0.5.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/silver_1.jpg"/></div>
    </div>
    <p>Various silver intensities. Silver spread is set to 0.2 for all examples. Left: no silver. Middle: intensity 0.5. Right: intensity 1.</p>
</div>

#### Silver Spread
**C# member variable:** `float m_silverSpread` \
Spread of silver lining around clouds close to the sun. Higher values will distribute the silver lining around clouds further away from the sun.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/silver_spread_0.05.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/silver_spread_0.15.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/silver_spread_0.3.jpg"/></div>
    </div>
    <p>Various silver spread values. Silver intensity is set to 1 for all examples so the effect is visible. Left: 0.05. Middle: 0.15. Right: 0.3.</p>
</div>


#### Ambient
**C# member variable:** `float m_ambient` \
Amount of ambient lighting the clouds receive from the sky. Expanse doesn't compute self-shadowing of ambient light, so adjusting this away from 1 can help to lower the ambient light contribution to a level that looks more physically correct.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/ambient_0.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ambient_0.75.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ambient_1.5.jpg"/></div>
    </div>
    <p>Various ambient light values. Left: no ambient light. These clouds are clearly too dark. Middle: 0.75. This seems about right. Right: 1.5. Too much ambient light can hide the details of self-shadowing.</p>
</div>

#### Self Shadowing
**C# member variable:** `bool m_selfShadowing` \
Whether or not the clouds cast shadows on themselves.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/no_ms.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ms_1.jpg"/></div>
    </div>
    <p>Self-shadowing vs. no self-shadowing, for a dense layer. In this case, the effect is very noticeable. Left: self-shadowing. Right: no self-shadowing.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/shadow.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/no_shadow.jpg"/></div>
    </div>
    <p>Now, self-shadowing vs. no self-shadowing, for a layer that's not very dense. In this case, the effect is pretty much unnoticeable. Left: self-shadowing. Right: no self-shadowing.</p>
</div>

#### Multiple Scattering Amount
**C# member variable:** `float m_multipleScatteringAmount` \
As a way of approximating multiple scattering, Expanse adjusts the self-shadowing effect. This parameter controls the amount of approximated multiple scattering. Because it adjusts the self-shadowing effect, it is only available when self-shadowing is turned on. Otherwise, it is unnecessary.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/no_ms.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ms_0.25.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ms_1.jpg"/></div>
    </div>
    <p>Different amounts of multiple scattering. Multiple scattering bias is set to 0.25 for all three examples. Left: none. Middle: 0.25. Right: 1.</p>
</div>

#### Multiple Scattering Bias
**C# member variable:** `float m_multipleScatteringBias` \
As a way of approximating multiple scattering, Expanse adjusts the self-shadowing effect. This parameter controls the "bias" of approximated multiple scattering---put another way, it controls the depth into the clouds at which multiple scattering is added. Because it adjusts the self-shadowing effect, it is only available when self-shadowing is turned on. Otherwise, it is unnecessary.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/ms_bias_0.25.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ms_bias_0.5.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/ms_bias_1.jpg"/></div>
    </div>
    <p>Different multiple scattering biases. Multiple scattering amount is set to 0.25 for all three examples. Left: 0.25. Middle: 0.5. Right: 1.</p>
</div>

#### Light Pollution Dimmer
**C# member variable:** `float m_lightPollutionDimmer` \
Dimmer to the effect of light pollution on clouds. When it's zero, the clouds receive no light pollution. When it's one, they receive full light pollution. This is a useful parameter for when you're using light pollution as more of an artistic effect to recolor the night sky.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/full-lp.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/dimmed-lp.jpg"/></div>
    </div>
    <p>Left: no light pollution dimming. Notice how the clouds are very blue. Right: full light pollution dimming.</p>
</div>

#### Cast Shadows
**C# member variable:** `bool m_castShadows` \
Whether or not this layer casts shadows on the ground and geometry. Enabling can incur a small performance cost.

#### Max Shadow Intensity
**C# member variable:** `float m_maxShadowIntensity` \
How dark the shadows that the clouds cast onto the ground are.

#### Cel Shade
**C# member variable:** `bool m_celShade` \
Whether to use cel/"toon" shading on the clouds. This is sort of a fun, experimental feature, so take it with a grain of salt.

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
Whether to use [offset](/editor/blocks/texture_cloud_plane_block?id=sampling-offset) or [velocity](/editor/blocks/texture_cloud_plane_block?id=velocity) to position the clouds.

#### Velocity
**C# member variable:** `Vector2 m_velocity` \
Velocity of the clouds. Setting this to a non-zero number automatically moves the clouds across the sky at the specified speed. Automates the sampling offset parameter.

#### Sampling Offset
**C# member variable:** `Vector2 m_samplingOffset` \
Sampling offset of the clouds. Can be animated as an alternative to the [velocity](/editor/blocks/texture_cloud_plane_block?id=sampling-offset) parameter.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- QUALITY ---------------------------------------->
<!---------------------------------------------------------------------------------------->

### Quality
These parameters tweak the rendering algorithm to trade quality for performance.

#### Reprojection Frames
**C# member variable:** `int m_reprojectionFrames` \
Number of history frames to use for reprojection. Increasing can improve performance, but at the cost of quality.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/texture_cloud_plane/no_reprojection.jpg"/></div>
        <div class="img-col"><img src="img/texture_cloud_plane/4x_reprojection.jpg"/></div>
    </div>
    <p>Comparison of reprojection settings. Left: no reprojection. Right: maximum reprojection, so rendering at 1/16th of the resolution. Reprojection gives great results if the clouds are slow-moving and far away. In this case the images are nearly indistinguishable.</p>
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
The Sky and Fog Volume that backs this texture cloud block. In all likelihood, this should be a single global volume you set up at the start of your project. More complex workflows may have multiple local volumes, in which case you will need to create multiple cloud blocks (one for each volume), or programmatically change this parameter.
