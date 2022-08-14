# Expanse and KWS Water System

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/kws/kws-workaround.png"/></div>
    </div>
</div>

> KWS will soon natively support an Expanse integration---as you're reading this, it may already be true. This page will still lie around until then though, for legacy documentation purposes.

Integrating KWS with Expanse is not particularly challenging, but it is a little bit different than the usual transparency workaround. Since KWS uses its own custom lighting model, and does not rely on Unity's HDRP forward lit pass implementation, you'll need to copy and paste a bit of shader code.

In the KWS folder, up `WaterResources/Shaders/Resources/WaterVertFrag.cginc`. 

First, at the top of the file, add the line

```
#include "Assets/Expanse/transparency/shaders/transparency.hlsl"
```

This will give you access to the necessary fog and cloud compositing utility functions.

Next, in the same file, find the following lines (you can do this by searching for `EvaluateAtmosphericScattering`):

```
#if defined(USE_FOG)
    float3 atmosColor;
    float3 atmosOpacity;
    PositionInputs posInput = GetPositionInput(i.pos, _ScreenSize.zw, i.pos.z, UNITY_MATRIX_I_VP, UNITY_MATRIX_V);
    EvaluateAtmosphericScattering(posInput, i.viewDir, atmosColor, atmosOpacity);
#endif
```

And swap them out for 

```
#if defined(USE_FOG)
    float3 atmosColor = 0;
    float3 atmosOpacity = 1;

    PositionInputs posInput = GetPositionInput(i.pos, _ScreenSize.zw, i.pos.z, UNITY_MATRIX_I_VP, UNITY_MATRIX_V);
    EvaluateAtmosphericScattering(posInput, i.viewDir, atmosColor, atmosOpacity);

    float4 expanseFogAndClouds = float4(0, 0, 0, 1);
    float opacity = 0;
    expanseFogAndClouds = EvaluateExpanseFogAndClouds(Linear01Depth(posInput.deviceDepth, _ZBufferParams), posInput.positionNDC, expanseFogAndClouds, GetCurrentExposureMultiplier(), opacity);
    atmosColor += expanseFogAndClouds.xyz;
    atmosOpacity *= opacity;
#endif
```

This will composite Expanse's fog and clouds over the water (and also over Unity's fog, so you can still use it as well).

That's all you need to do---KWS should now correctly receive fog from Expanse!

If you have any trouble, come ask for help on [Discord](https://discord.gg/F3VQ2vJy9p).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/kws/kws-no-workaround.png"/></div>
        <div class="img-col"><img src="img/kws/kws-workaround.png"/></div>
    </div>
    <p>Left: KWS without the workaround---the water doesn't receive fog. Right: KWS with the workaround implemented. Looks a lot better!</p>
</div>