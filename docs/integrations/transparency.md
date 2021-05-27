# Expanse and Transparency

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/transparency/splash.jpg"/></div>
    </div>
</div>

I wish that Expanse just worked out of the box with transparent materials. As is often the case when working with Unity's render pipelines, it's very easy to do what they explicitly have decided they want you to do, and very challenging to do anything else.

HDRP's default fog is tightly integrated into the lit render pipeline, and there's no place to hook in a custom fog implementation. For this reason, this integration will be dealing in workarounds and hacks that may even be version-specific. It won't be too much trouble to get things to work, but if either of the following this sound unappealing to you:

* Using a slightly modified copy of the usual Lit shader for any transparent materials.
* Creating a custom shadergraph material for transparents that need to receive fog.

then I suggest you just stick with Unity's default volumetric fog, which looks decent and integrates fully with Expanse.

## Workaround #1: Copying and Modifying the Lit Shader

We may not be able to modify Unity's source code, but we can make a duplicate copy of the lit shader that properly receives Expanse's fog. This strategy is the best one I've been able to come up with so far, because it will produce fully correct results for any configuration of transparent objects, no matter their material parameters.

Things you have to be willing to do to use this workaround:
* Use this tweaked Lit Shader for all transparents you want to receive fog from Expanse.
* Possibly copy-paste a little bit of shader code.

Follow these steps to implement this workaround.

### Step 1: Try the existing tweaked shaders.
For convenience, I've created modified copies of the lit shaders for the following Unity versions:

* 2020.1.17f1
* 2020.3.7f1
* 2021.2.0a16

You ought to try out each of these and see if they work with your version of Unity. The way to do this is pretty straightforward.

Download or clone [this Github repository](https://github.com/bguesman/expanse-transparent-shaders). In the folder "packages", You should see a few packages labeled with the above Unity versions. Import the one you want to try into Unity by dragging over the file into Unity's file browser window. When you import it, there may be errors---this probably indicates that the shader is incompatible with your version of Unity. Still, continue reading to verify that this is the case.

Next, create a new material, and call it "Transparent Test". Then, in the inspector, click where it says "Shader".

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/transparency/shader-select-0.jpg"/></div>
    </div>
</div>

This will bring up the following menu. Select "Expanse". If Expanse doesn't show up here, it means no transparency shaders compiled successfully. In this case, skip to step 2.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/transparency/shader-select-1.jpg"/></div>
    </div>
</div>

This will slide you to another menu that lists one or more Unity versions---these are subfolders for transparency shaders that have successfully compiled.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/transparency/shader-select-2.jpg"/></div>
    </div>
</div>

Click through one more time and you'll see the shader "Expanse Transparent Lit". Select it. This is now a transparent material set up to receive fog from Expanse!

To test this material, drag it onto an object in the scene, and increase the fog density. With luck, you should see your object correctly receive fog! 

If you're unsure if it's receiving fog, you can compare it to a transparent object rendered with the regular lit shader. The regular lit shader should not receive fog properly.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/transparency/correct.jpg"/></div>
        <div class="img-col"><img src="img/transparency/incorrect.jpg"/></div>
    </div>
    <p>Left: the modified transparent lit shader, correctly applying fog to the object. Right: the standard transparent lit shader, failing to apply fog.</p>
</div>

### Step 2: Copy and rename the Unity shader files.

So the preset shaders don't work. Fear not! You can tweak the shaders for your Unity version to work with Expanse.

Create the folder `/Assets/expanse-transparent-shaders/<your unity version>`. So, if you were using 2021.1.7, this would be `/Assets/expanse-transparent-shaders/2021.1.7`.

Now, copy the following two files into this folder (you can copy simply by dragging them into the folder):
* `/Packages/High Definition RP/Runtime/Material/Lit/Lit.shader`. This is the rainbow-colored file.
* `/Packages/High Definition RP/Runtime/RenderPipeline/ShaderPass/ShaderPassForward.hlsl`. This file's icon should just look like a regular text file.

Rename `Lit.shader` to `TransparentLit.shader`, and rename `ShaderPassForward.hlsl` to `ShaderPassTransparentForward.hlsl`.

### Step 3: Modify `TransparentLit.shader`

Open the recently renamed `TransparentLit.shader`, and replace the following:
* On line 1, replace 
```
Shader "HDRP/Lit"
``` 
with 
```
Shader "Expanse/<your unity version>/Expanse Transparent Lit"
```
So, again, for version 2021.1.7, you would use
```
Shader "Expanse/2021.1.7/Expanse Transparent Lit"
```

* Replace all occurrences of 
```
#include "Packages/com.unity.render-pipelines.high-definition/Runtime/RenderPipeline/ShaderPass/ShaderPassForward.hlsl"
```
with
```
#include "ShaderPassTransparentForward.hlsl"
```
There should only be two, which you can easily find with `Ctrl-F` in your favorite text editor. But in later versions who knows, maybe there are more occurrences.

### Step 4: Modify `ShaderPassTransparentForward.hlsl`

Open `ShaderPassTransparentForward.hlsl`, and find the first `#endif`, near the top of the file. This should typically be on line 3. **Right on the next line**, paste the following:

```
#include "Assets/Expanse/transparency/shaders/transparency.hlsl"
```

This gives us access to the code that lets us sample Expanse's fog and clouds in this file.

Now, find the following two lines:

```
outColor = ApplyBlendMode(diffuseLighting, specularLighting, builtinData.opacity);
outColor = EvaluateAtmosphericScattering(posInput, V, outColor);
```

This is where Unity applies its fog ("EvaluateAtmosphericScattering") to transparent objects. Replace these two lines with the following snippet:

```
outColor = ApplyBlendMode(diffuseLighting, specularLighting, builtinData.opacity);
            
// [EXPANSE]: perform atmospheric scattering lookup/composite.
outColor = EvaluateExpanseFogAndClouds(Linear01Depth(posInput.deviceDepth, _ZBufferParams), posInput.positionNDC, outColor, GetCurrentExposureMultiplier());
// [EXPANSE]: End modifications.

outColor = EvaluateAtmosphericScattering(posInput, V, outColor);
```

This samples Expanse's fog and clouds and applies them before applying Unity's fog.

If you don't care about clouds, and only need fog compatibility, you can use this snippet

```
outColor = ApplyBlendMode(diffuseLighting, specularLighting, builtinData.opacity);
            
// [EXPANSE]: perform atmospheric scattering lookup/composite.
outColor = EvaluateExpanseFog(Linear01Depth(posInput.deviceDepth, _ZBufferParams, posInput.positionNDC, outColor, GetCurrentExposureMultiplier());
// [EXPANSE]: End modifications.

outColor = EvaluateAtmosphericScattering(posInput, V, outColor);
```

### Step 5: Go back to step 1!

You should now have a working modified copy of Unity's Lit shader that you can test. Follow along with step 1 (skipping the unpacking portion), making sure to select the shader you just wrote.

That's it! If you have any trouble, come ask for help on [Discord](https://discord.gg/F3VQ2vJy9p).

## Workaround #2: Use a Custom Shadergraph Material

This solution is much more robust to version changes, but may not be quite as accurate as workaround #1, which, I will reiterate, **is currently the best solution**.

That said, this solution is certainly more flexible and is also useful for designing your own custom transparent materials that receive fog from Expanse.

It's very easy, simply create a new material, and select the shader `Shader Graphs/Example Transparent Graph`. If you don't know how to select the shader for a material, take a look at step 1 in Workaround #1 above.

This is a very simple transparent material that's been set up to work with Expanse. If you drag it onto an object in your game, that object should be transparent and correctly receive Expanse's fog.

For details on setting up your own transparent material, open up the shader graph and read the sticky notes---they detail what each part of the graph is doing, and show you how to use the `Sample Expanse Fog` subgraph. The graph is at the path `/Assets/Expanse/transparency/graphs/Example Transparent Graph.shadergraph`.

Again, if you have any trouble, come ask for help on [Discord](https://discord.gg/F3VQ2vJy9p).