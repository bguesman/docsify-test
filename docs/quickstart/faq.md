# Frequently Asked Questions

Here's a collection of questions folks have asked one or more times---this is a good spot to search for the answer to your question before you ask for help on Discord!

**Q: Fog isn't rendering out far enough/clouds don't receive enough fog/clouds aren't blue enough.**

**A:** Expanse renders fog out to the far clipping plane. Chances are, you need to increase the far clipping plane for your main camera and sceneview camera---something like 100,000 is a good number.

**Q: All my geometry is black.**

**A:** Expanse renders an ambient light probe for one camera, specified in the [Camera Settings](/editor/blocks/camera_settings_block). Make sure your main camera is set here. 

Alternatively, you can tag your main camera as `MainCamera` (the default behavior in Unity), and it will be picked up as the ambient probe camera automatically.

**Q: There's a bunch of shader warnings being printed about a punctual/directional shadow filter macro being redefined.**

**A:** This is due to a Unity API change between version 2020.3 and 2021.1. Unfortunately, there's no workaround that avoids these redefinition warnings (that I've discovered yet).

**Q: The lighting is flickering.**

**A:** This is likely because you have scene view and game view open at the same time. In this case, make sure you disable the [Prefer Editor Camera](/editor/blocks/camera_settings_block?id=prefer-editor-camera) setting in Expanse's camera settings. This will make sure the ambient probe is always rendered from your main camera (or whatever camera you specify as the [Ambient Probe Camera](/editor/blocks/camera_settings_block?id=ambient-probe-camera)). 

**Q: My planar reflection probe is flickering.**

**A:** Try increasing the far clipping plane on the reflection probe to something like 10000. 

**Q: My clouds look too blurry. What's going on?**

**A:** Chances are you're in one of the following situations:

* You have [reprojection frames](/editor/blocks/procedural_cloud_volume_block?id=reprojection-frames) set to more than 1, and you have Unity's TAA enabled. This will cause slight blurriness---either decrease the number of reprojection frames or disable TAA.
* You have [denoising history frames](/editor/blocks/procedural_cloud_volume_block?id=denoising-history-frames) set very high. First, enable [catmull-rom filtering](/editor/blocks/procedural_cloud_volume_block?id=catmull-rom-filtering), and see if this improves thigs. You can then try lowering the number of denoising frames you use. If the result is too noisy, you can try increasing the sample counts to compensate for it (the [detail step range](/editor/blocks/procedural_cloud_volume_block?id=detail-step-range)).

**Q: The sky/clouds are bleeding through my geometry!**

**A:** Chances are you need to increase the [clip fade](/editor/blocks/planet_block?id=clip-fade) in the planet block.

**Q: I'm getting hitching when I move the sun around, and it says there's some jobs about GI preprocessing/light baking happening.**

**A:** This is because, when baked GI is enabled, Unity rebakes some portion of the GI map whenever the light moves/light color changes. To fix this,
* Go to Window -> Rendering -> Lighting
* Create a new lighting settings object (if you don't have one already)
* Disable baked GI
* Regenerate lighting

This section will be continually updated with more questions and answers!