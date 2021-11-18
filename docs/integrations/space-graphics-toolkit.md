# Expanse and The Space Graphics Toolkit

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/space-graphics-toolkit/beauty-shot.png"/></div>
    </div>
</div>

Integrating Expanse with Carlos Wilkes' wonderful Space Graphics Toolkit is not too challenging. It uses the same transparency workaround that similar integrations (KWS, Crest) use. **Credit goes to Discord user OdderOtter for sorting out how to do this!**

> Better Shaders---a fantastic product---is not required for this integration. But you should still buy it! 

Navigate to and open `/Assets/Space Graphics Toolkit/Features/Terrain/Shaders/TerrainOcean.shader`. After line `320`, which reads:

```
#include "Packages/com.unity.render-pipelines.high-definition/Runtime/ShaderLibrary/ShaderGraphHeader.hlsl" // Need to be here for Gradient struct definition
```

add the following line:

```
#include "Assets/Expanse/transparency/shaders/transparency.hlsl"
```

This will give you access to the necessary fog and cloud compositing utility functions. Next, after line 2263, which reads:

```
outColor = ApplyBlendMode(diffuseLighting, specularLighting, builtinData.opacity);
```

add this line:

```
outColor = EvaluateExpanseFogAndClouds(Linear01Depth(posInput.deviceDepth, _ZBufferParams), posInput.positionNDC, outColor, GetCurrentExposureMultiplier());
```

which will composite Expanse's fog and clouds on top of/behind any transparent materials.

This should be all you need to do to get it to work! If you have any trouble, come ask for help on [Discord](https://discord.gg/F3VQ2vJy9p).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/space-graphics-toolkit/incorrect.png"/></div>
        <div class="img-col"><img src="img/space-graphics-toolkit/correct.png"/></div>
    </div>
    <p>Left: A planet rendered with space graphics tookit, without the workaround. Transparency isn't handled correctly. Right: The same planet, but rendered with the workaround.</p>
</div>