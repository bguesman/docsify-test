# Lens Flare Attenuator

> Implemented as class `Expanse.LensFlareAttenuator` in `blocks/LensFlareAttenuator.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/lens_flare/splash.jpg"/></div>
    </div>
</div>

This component will attenuate an SRP lens flare according to cloud shadowing. This way, when clouds fully cover the sky, the sun lens flare can be effectively disabled.

#### Flare Intensity
**C# member variable:** `float m_flareIntensity` \
Base intensity of the lens flare.

#### Bias
**C# member variable:** `float m_bias` \
Bias to the shadowing effect. When set to 1, the lens flare will never be shadowed. When set to zero, the lens flare will be shadowed in an unaltered way.

#### Lens Flare
**C# member variable:** `UnityEngine.Rendering.LensFlareComponentSRP m_lensFlare` \
SRP lens flare to attenuate.

#### Target
**C# member variable:** `UnityEngine.Rendering.LensFlareComponentSRP target` \
Transform to use when determining cloud shadowing. This should almost always be the transform of the directional light that the lens flare is on.