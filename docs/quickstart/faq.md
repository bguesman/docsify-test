# Frequently Asked Questions

Here's a collection of questions folks have asked one or more times---this is a good spot to search for the answer to your question before you ask for help on Discord!

**Q: Fog isn't rendering out far enough/clouds don't receive enough fog/clouds aren't blue enough.**

**A:** Expanse renders fog out to the far clipping plane. Chances are, you need to increase the far clipping plane for your main camera and sceneview camera---something like 100,000 is a good number.

**Q: All my geometry is black.**

**A:** Expanse renders an ambient light probe for one camera, specified in the [Camera Settings](/editor/blocks/camera_settings_block). Make sure your main camera is set here.

Alternatively, you can tag your main camera as `MainCamera` (the default behavior in Unity), and it will be picked up as the ambient probe camera automatically.

**Q: There's a bunch of shader warnings being printed about a punctual/directional shadow filter macro being redefined.**

I.e.

```
Shader warning in 'CloudCompositor(Clone)': 'DIRECTIONAL_FILTER_ALGORITHM': macro redefinition. Previous definition found at C:/Users/bradg/Graphics/unity/Real Landscapes/Assets/Expanse/code/source/atmosphere/Atmosphere.hlsl:11. at kernel FULLSCREEN_0 at C:/Users/bradg/Graphics/unity/Real Landscapes/Library/PackageCache/com.unity.render-pipelines.high-definition@12.1.0/Runtime/Lighting/Shadow/HDShadowAlgorithms.hlsl(27)
```

**A:** This is due to a Unity API change between version 2020.3 and 2021.1. Unfortunately, there's no workaround to bake into the release version of Expanse that avoids these redefinition warnings without breaking Expanse in one Unity version or the other (that I've discovered yet).

To get rid of these warnings, you'll want to edit `Atmosphere.hlsl`. **Near the top of the file remove (or comment out) the following:**

```
// BEGIN HACK [shadow sampling]
// These need to be defined in Unity 2020.3. If you are not using Unity
// 2020.3 and you are getting redefinition warnings, YOU CAN COMMENT
// THESE OUT.
#ifndef PUNCTUAL_FILTER_ALGORITHM
#define PUNCTUAL_FILTER_ALGORITHM(sd, posSS, posTC, sampleBias, tex, samp) SampleShadow_PCF_Tent_3x3(_ShadowAtlasSize.zwxy, posTC, sampleBias, tex, samp)
#endif
#ifndef DIRECTIONAL_FILTER_ALGORITHM
#define DIRECTIONAL_FILTER_ALGORITHM(sd, posSS, posTC, sampleBias, tex, samp) SampleShadow_PCF_Tent_5x5(_CascadeShadowAtlasSize.zwxy, posTC, sampleBias, tex, samp)
#endif
#ifndef AREA_FILTER_ALGORITHM
#define AREA_FILTER_ALGORITHM(sd, posSS, posTC, tex, samp, bias) SampleShadow_EVSM_1tap(posTC, sd.shadowFilterParams0.y, sd.shadowFilterParams0.z, sd.shadowFilterParams0.xx, false, tex, s_linear_clamp_sampler)
#endif
// END HACK [shadow sampling]
```

This should get rid of the warnings.

**Q: The lighting is flickering.**

**A:** This is likely because you have scene view and game view open at the same time. In this case, make sure you disable the [Prefer Editor Camera](/editor/blocks/camera_settings_block?id=prefer-editor-camera) setting in Expanse's camera settings. This will make sure the ambient probe is always rendered from your main camera (or whatever camera you specify as the [Ambient Probe Camera](/editor/blocks/camera_settings_block?id=ambient-probe-camera)).

**Q: My planar reflection probe is flickering.**

**A:** Try increasing the far clipping plane on the reflection probe to something like 10000.

**Q: My clouds look too blurry. What's going on?**

**A:** Chances are you're in one of the following situations:

- You have [reprojection frames](/editor/blocks/procedural_cloud_volume_block?id=reprojection-frames) set to more than 1, and you have Unity's TAA enabled. This will cause slight blurriness---either decrease the number of reprojection frames or disable TAA.
- You have [denoising history frames](/editor/blocks/procedural_cloud_volume_block?id=denoising-history-frames) set very high. First, enable [catmull-rom filtering](/editor/blocks/procedural_cloud_volume_block?id=catmull-rom-filtering), and see if this improves thigs. You can then try lowering the number of denoising frames you use. If the result is too noisy, you can try increasing the sample counts to compensate for it (the [detail step range](/editor/blocks/procedural_cloud_volume_block?id=detail-step-range)).

**Q: The sky/clouds are bleeding through my geometry!**

**A:** Chances are you need to increase the [clip fade](/editor/blocks/planet_block?id=clip-fade) in the planet block.

**Q: I'm getting hitching when I move the sun around, and it says there's some jobs about GI preprocessing/light baking happening.**

**A:** This is because, when baked GI is enabled, Unity rebakes some portion of the GI map whenever the light moves/light color changes. To fix this,

- Go to Window -> Rendering -> Lighting
- Create a new lighting settings object (if you don't have one already)
- Disable baked GI
- Regenerate lighting

**Q: I actually want to use baked lighting, and it's not working.**

**A:** For whatever reason, Unity fails to capture ambient illumination from Expanse when baking (even though Expanse conforms to the necessary API). The solution to this is to export Expanse's sky to a cubemap, and then use this cubemap as the static lighting sky for the baking process.

More specifically, you'll want to

- Disable light baking.
- Export Expanse's sky to an image using `Edit -> Render Pipeline -> Export Sky To Image`. Alternatively, you can use [this awesome \$5 tool](https://assetstore.unity.com/packages/tools/camera/skybox-capture-93266#description) for a higher quality export result.
- Create a new volume profile that has a visual environment with an HDRI sky. Set that sky to use the exported sky image, with a multiplier of 1.
- In the Lighting settings, set the environment to use this new HDRI volume profile with the exported sky.
- Re-enable light baking and generate light maps.
- Voila! Baking will capture the ambient illumination properly.

[Click here for a short video demonstrating how to do this](https://youtu.be/ZU9tPbkyb00).

**Q: How can I export Expanse's sky to a cubemap?**

**A:** There are two ways to do this.

1. Using the built in HDRP function to do this: `Edit -> Render Pipeline -> Export Sky To Image`. This method **looks like garbage** because Unity just saves out the low-resolution, low sample count reflection cubemap. It's good enough for lighting purposes, but won't look good as a skybox.
2. Using [this awesome \$5 tool called "Skybox Capture"](https://assetstore.unity.com/packages/tools/camera/skybox-capture-93266#description). This produces excellent results.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/faq/unity-export.jpg"/></div>
        <div class="img-col"><img src="img/faq/third-party-export.jpg"/></div>
    </div>
    <p>Left: sky exported with Unity's free tool. Looks pretty bad! Right: sky exported with Skybox Capture. Looks a lot better.</p>
</div>

**Q: How do I check if celestial bodies are behind the horizon?**

**A:** This is a useful feature to have. Sometime in the next few updates, Expanse will expose a function in the `CelestialBody` class `BehindHorizon()` that returns whether this is true.

For now, the best way to check this is to look at the `CelestialBody`'s associated `LightControl` script's transmittance value. You access this within `LightControl.cs` via

```
Vector3 transmittance = ((Vector4)AtmosphereRenderer.GetBodyTransmittance(m_atmosphereIndex)).xyz();
```

And check for occlusion with something like

```
bool occluded = (transmittance.x < 1e-6) && (tranmittance.y < 1e-6) && (transmittance.z < 1e-6);
```

This is a pain right now, but it will be easier very soon.

**Q: I get an error that says \_CameraDepthTexture is not set.**

**A:** You likely need to enable Unity's `Depth Prepass Within Deferred` setting. The `_CameraDepthTexture` should be available globally to all shaders by default.

This section will be continually updated with more questions and answers!
