# Aerial Perspective Settings

> Implemented as class `Expanse.AerialPerspectiveSettings` in `blocks/AerialPerspectiveSettings.cs`

This block exposes settings related to aerial perspective, which refers to the phenomenon of atmospheric scattering being composited on top of objects. Games often simulate this with simple blue fog. Expanse takes a more physically-based approach, and uses its atmospheric model to compute aerial perspective.

#### Directional Occlusion Bias
**C# member variable:** `float m_directionalOcclusionBias` \
Provides a way of offsetting the attenuation of aerial perspective as a consequence of approximate volumetric shadowing (for Mie ("directional") layers). To see the effect, put the sun behind a big piece of geometry (like a mountain) and play around with this parameter. Expanse does not accurately model atmospheric volumetric shadows due to the performance cost, and instead uses this approximation to avoid visual artifacts.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_ground_truth.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_bias_1.jpg"/></div>
        <p></p>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_bias_0.25.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_bias_0.jpg"/></div>
        <p>Comparison of different directional occlusion biases. In all cases, occlusion spread is set to 1. Top left: "ground truth" result using a screenspace height fog layer with pseudo-volumetric shadows. Top Right: occlusion bias of 1, so aerial perspective is not attenuated at all around the sun disc. Bottom Left: occlusion bias of 0.25, so aerial perspective is attenuated by some amount around the sun disc. This approximates the ground truth reasonably, though not perfectly. Bottom Right: occlusion bias of 0, so aerial perspective is fully attenuated around the sun disc.</p>
    </div>
</div>

#### Directional Occlusion Spread
**C# member variable:** `float m_directionalOcclusionSpread` \
How aggressively aerial perspective due to Mie ("directional") layers is attenuated as a consequence of approximate volumetric shadowing. To see the effect, put the sun behind a big piece of geometry (like a mountain) and play around with this parameter. Expanse does not accurately model atmospheric volumetric shadows due to the performance cost, and instead uses this approximation to avoid visual artifacts.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_ground_truth.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_spread_0.jpg"/></div>
        <p></p>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_spread_0.25.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/directional_occlusion_spread_1.jpg"/></div>
        <p>Comparison of different directional occlusion spreads. In all cases, occlusion bias is set to 0.5 to make the effect as noticeable as possible. Top left: "ground truth" result using a screenspace height fog layer with pseudo-volumetric shadows. Top Right: occlusion spread of 0, so maximum spread. Bottom Left: occlusion spread of 0.25, so some the spread is tighter. Bottom Right: occlusion spread of 1, so aerial perspective is minimally spread.</p>
    </div>
</div>

#### Uniform Occlusion Bias
**C# member variable:** `float m_uniformOcclusionBias` \
Provides a way of offsetting the attenuation of aerial perspective as a consequence of approximate volumetric shadowing (for Rayleigh and Isotropic ("uniform") layers). To see the effect, put the sun behind a big piece of geometry (like a mountain) and play around with this parameter. Expanse does not accurately model atmospheric volumetric shadows due to the performance cost, and instead uses this approximation to avoid visual artifacts.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/uniform_occlusion_bias_1.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/uniform_occlusion_bias_0.jpg"/></div>
    </div>
    <p>Comparison of different uniform occlusion biases. In both cases, occlusion spread is set to 0.5. Left: uniform occlusion bias 1. right: uniform occlusion bias 0. The effect is almost unnoticable.</p>
</div>

#### Uniform Occlusion Spread
**C# member variable:** `float m_uniformOcclusionSpread` \
How aggressively aerial perspective due to Rayleigh and Isotropic ("uniform") layers is attenuated as a consequence of approximate volumetric shadowing. To see the effect, put the sun behind a big piece of geometry (like a mountain) and play around with this parameter. Expanse does not accurately model atmospheric volumetric shadows for non-screenspace layers, and instead uses this approximation to avoid visual artifacts.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/uniform_occlusion_spread_1.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/uniform_occlusion_spread_0.jpg"/></div>
    </div>
    <p>Comparison of different uniform occlusion spreads. In both cases, occlusion bias is set to 0.5. Left: uniform occlusion spread 1. right: uniform occlusion spread 0. The effect is almost unnoticable.</p>
</div>

#### Night Scattering Multiplier
**C# member variable:** `float m_nightScatteringMultiplier` \
The night scattering effect can sometimes be either too intense or not intense enough for aerial perspective. This artistic override allows for attenuation of night scattering for aerial perspective only.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/aerial_perspective/night_scatter_0.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/night_scatter_1.jpg"/></div>
        <div class="img-col"><img src="img/aerial_perspective/night_scatter_100.jpg"/></div>
    </div>
    <p>Comparison of different night scattering multipliers. Left: zero. Middle: 1, so physical. Right: 100, so very exaggerated.</p>
</div>