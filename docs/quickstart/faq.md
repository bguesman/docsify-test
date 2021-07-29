# Frequently Asked Questions

Here's a collection of questions folks have asked one or more times---this is a good spot to search for the answer to your question before you ask for help on Discord!

**Q: Fog isn't rendering out far enough/clouds don't receive enough fog/clouds aren't blue enough.**

**A:** Expanse renders fog out to the far clipping plane. Chances are, you need to increase the far clipping plane for your main camera and sceneview camera---something like 100,000 is a good number.


**Q: My planar reflection probe is flickering.**

**A:** Try increasing the far clipping plane on the reflection probe to something like 10000. 

**Q: My clouds look too blurry. What's going on?**

**A:** Chances are you're in one of the following situations:

* You have [reprojection frames](/editor/blocks/procedural_cloud_volume_block?id=reprojection-frames) set to more than 1, and you have Unity's TAA enabled. This will cause blurriness---either decrease the number of reprojection frames or disable TAA.
* You have [denoising history frames](/editor/blocks/procedural_cloud_volume_block?id=denoising-history-frames) set very high. Try lowering it and seeing if you can tolerate the amount of noise; you can also increase the sample counts to compensate for it (the [detail step range](/editor/blocks/procedural_cloud_volume_block?id=detail-step-range)).

This section will be continually updated with more questions and answers!