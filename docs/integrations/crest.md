# Expanse and Crest

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/crest/correct.jpg"/></div>
    </div>
</div>

Integrating Crest with Expanse is easy, but **you have to complete [Workaround #1 for transparency](integrations/transparency.md?id=workaround-1-copying-and-modifying-the-lit-shader)**.

Once you've done that, simply open up the `.shader` file `/Assets/Crest/Crest/Shaders/Ocean.shader` and replace all occurrences of

```
#include "Packages/com.unity.render-pipelines.high-definition/Runtime/RenderPipeline/ShaderPass/ShaderPassForward.hlsl"
```

with 

```
#include "../../../Expanse/transparency/shaders/<UNITY VERSION OF MODIFIED LIT SHADER>/ShaderPassTransparentForward.hlsl"
```

So, if you're using the modified lit shader `Expanse/2020.1.17f1/Expanse Transparent Lit` for your transparent materials, you would use the line:

```
#include "../../../Expanse/transparency/shaders/2020.1.17f1/ShaderPassTransparentForward.hlsl"
```

There should only be one occurence, near the bottom of the file.

That's all you need to do---Crest should now correctly receive fog from Expanse!

If you have any trouble, come ask for help on [Discord](https://discord.gg/F3VQ2vJy9p).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/crest/incorrect.jpg"/></div>
        <div class="img-col"><img src="img/crest/correct.jpg"/></div>
    </div>
    <p>Left: Crest using the vanilla lit shader---the water doesn't receive fog. Right: Crest using the modified lit shader. Looks a lot better!</p>
</div>