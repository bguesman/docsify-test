# Global Settings

> Implemented as class `Expanse.GlobalSettings` in `blocks/GlobalSettings.cs`

This component exposes general global settings for the atmosphere, clouds, and overall rendering algorithm, including quality/performance tradeoff settings. Other non-global quality settings, in particular for volumetric clouds, can be found on component instances.

> NOTE: You must have a `GlobalSettings` component in your scene for Expanse to function.

<!---------------------------------------------------------------------------------------->
<!----------------------------------------- PLANET --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Planet

These are parameters that pertain to the rendering and dimensions of the planet that Expanse's atmosphere/clouds are centered around.

#### Planet Radius
**C# member variable:** `float m_planetRadius` \
The radius of the planet, in world units.

#### Atmosphere Thickness
**C# member variable:** `float m_atmosphereThickness` \
The total thickness of the planet's atmosphere, in world units. This places an upper limit on the thickness of the atmosphere layers.

#### Planet Origin Offset
**C# member variable:** `Vector3 m_planetOriginOffset` \
The planet origin, in world units, but specified as an offset from the position (0, -radius, 0), since that origin is much more convenient. Useful for implementing a floating origin.

#### Aerial Perspective Scale
**C# member variable:** `float m_aerialPerspectiveScale` \
Multiplier on depth used to sample aerial perspective LUT. Increasing this above 1 is non-physical, but it can help imbue small scenes with a better sense of scale.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/planet/ap-scale-1.jpg"/></div>
        <div class="img-col"><img src="img/planet/ap-scale-30.jpg"/></div>
    </div>
    <p>Left: aerial perspective scale of 1 (physically accurate). Right: aerial perspective scale of 30. This is very exaggerated, but nice looking.</p>
</div>

#### Clip Fade
**C# member variable:** `float m_clipFade` \
How quickly to fade from geometry to sky as geometry approaches the far clip plane. Useful to tweak for flight sims, but otherwise, the default value of 0.35 is pretty good.

#### Ground Albedo Texture
**C# member variable:** `Cubemap m_groundAlbedoTexture` \
The ground albedo, specied as a cubemap. The ground is modeled as a Lambertian (completely diffuse) reflector. If no texture is specified, the color of the ground will just be the ground tint.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/planet/earth_day.jpg"/></div>
    </div>
    <p>An example of a ground albedo texture, in this case, Earth's.</p>
</div>

#### Ground Tint
**C# member variable:** `Color m_groundTint` \
A color tint to the ground texture. Perfect grey, `(127, 127, 127)`, specifies no tint. If there is no ground texture specified, this is just the color of the ground.

#### Ground Emission Texture
**C# member variable:** `Cubemap m_groundEmissionTexture` \
The ground emission as a cubemap texture. Useful for modeling things like city lights. Has no effect on the sky. See \"Light Pollution\" for a way of modeling an emissive ground's effect on the atmosphere.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/planet/earth_night.jpg"/></div>
    </div>
    <p>Using a combination of a ground albedo texture and a ground emission texture can create some pretty cool effects!</p>
</div>

#### Ground Emission Multiplier
**C# member variable:** `float m_groundEmissionMultiplier` \
An intensity multiplier on the ground emission texture.

#### Rotation
**C# member variable:** `Vector3 m_planetRotation` \
The rotation of the planet textures as euler angles. This won't do anything to light directions, star rotations, etc. It is purely for rotating the planet's albedo and emissive textures.

#### Planet Reflections

**C# member variable:** `bool m_planetReflections` \
Whether or not to render the planet into the ambient reflection cubemap. Can be useful for faking some global illumination from the ground.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/no-planet-reflection.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/planet-reflection-dark.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/planet-reflection-bright.jpg"/></div>
    </div>
    <p>Left: no planet reflections. Middle: with planet reflections enabled, and a planet color set up to mimick the ground color. Right: with the planet color adjusted to be very bright, to make the effect obvious. Perhaps things look a little strange here because of the lack of ambient directionality to the foliage lighting.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- NIGHT SKY -------------------------------------->
<!---------------------------------------------------------------------------------------->

### Night Sky

These parameters allow you to tweak the look of the night sky, via an artistic scattering override and light pollution.

#### Light Pollution Intensity
**C# member variable:** `float m_lightPollutionIntensity` \
Intensity of light pollution coming from the planet, in Lux.

#### Light Pollution Tint
**C# member variable:** `Color m_lightPollutionTint` \
Color of light pollution coming from the planet.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/night_sky/no_lp.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/lp_orange_30.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/lp_blue_30.jpg"/></div>
        <p></p>
    </div>
    <p>Various light pollution settings. Left: no light pollution. Middle: orange light pollution at an intensity of 30 Lux, similar to what you might find in a dense suburb. Right: blue light pollution at an intensity of 30 Lux. Maybe what it could look like on a planet whose oceans are swimming with bioluminescent algae.</p>
</div>

#### Scatter Intensity
**C# member variable:** `float m_scatterIntensity` \
Expanse computes sky scattering using the average color of the sky texture. There are so many light sources in the night sky (stars) that this is really the only computationally tractable option. However, this can sometimes result in scattering that's too intense, or not intense enough, depending on your use case. This parameter is an artistic override to help mitigate that issue.

To emulate reality, it's probably best to keep this value very low. Stars are very dim and don't contribute much to the sky color at all, compared to the moon.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/night_sky/no_night_scatter.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/scatter_0.3.jpg"/></div>
        <div class="img-col"><img src="img/night_sky/scatter_1.jpg"/></div>
        <p></p>
    </div>
    <p>Various scatter intensities. Left: scatter intensity 0. Middle: scatter intensity 0.3. Right: scatter intensity 1.</p>
</div>

#### Scatter Tint
**C# member variable:** `Color m_scatterTint` \
An additional tint applied on top of the night sky tint, but only to the scattering. This is useful as an artistsic override for if the average color of your sky texture doesn't quite get you the scattering behavior you want. For instance, you may want the scattering to be bluer.

#### Night Sky Reflections

**C# member variable:** `bool m_nightSkyReflections` \
Whether or not to render the night sky (stars and nebulae) into the ambient reflection cubemap.

<!---------------------------------------------------------------------------------------->
<!----------------------------------------- CAMERA --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Camera

This section is concerned with tracking some basic scene info about the camera in use. Chances are you won't have to touch this.

#### Ambient Probe Camera
**C# member variable:** `Camera m_ambientProbeCamera` \
Camera that Expanse's ambient probe is rendered for. Defaults to the scene's main camera.

#### Prefer Editor Camera
**C# member variable:** `bool m_preferEditorCamera` \
Prefer to render the ambient probe relative to the editor camera if both the editor and main camera are rendering. Chances are you want this enabled.

<!---------------------------------------------------------------------------------------->
<!---------------------------------------- GENERAL --------------------------------------->
<!---------------------------------------------------------------------------------------->

### General Quality

These are general quality parameters, relating to post-process effects like anti-aliasing.

#### Dithering

**C# member variable:** `bool m_dither` \
Whether or not to use dithering to reduce color banding. Since expanse computes everything in floating point HDR values, this is more of a de-band operation than a true dither, and you may be better off using a dither post-processing step on your camera.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/no_dither.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/dither.jpg"/></div>
    </div>
    <p>Left: without dithering. Right: with dithering. The effect is challenging to see in these images, but fairly obvious in-engine.</p>
</div>

<!---------------------------------------------------------------------------------------->
<!---------------------------------------- CLOUDS ---------------------------------------->
<!---------------------------------------------------------------------------------------->

### Clouds Quality

These parameters pertain to global cloud quality. Other quality settings can be set locally on each cloud component you use.

#### Cloud Subresolution

**C# member variable:** `float m_cloudSubresolution` \
Specifies resolution to render clouds at, as a function of the camera resolution. `1` means full resolution. `0.5` means half the screen width and height, so, technically, quarter resolution.

Depending on your needs, you may choose to use this or reprojection (specified in each cloud component), to meet your performance requirements. However, be aware that rendering clouds at a lower resolution will result in a visual downgrade.

This parameter can be particularly useful for 4K support, where you may want to render your geometry in 4K, but render the clouds in HD to keep performance reasonable.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/cloud_full_res.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/cloud_sixteenth_res.jpg"/></div>
    </div>
    <p>Left: clouds rendered in full res. Right: clouds rendered at 1/16th res (so a quarter of the screen height and width). It can be hard to tell the difference here, since the images may appear small in the browser, but the right image is blurrier.</p>
</div>

#### Interactive Clouds

**C# member variable:** `bool m_interactiveClouds` \
When enabled, clouds can be rendered between the camera and scene geometry. When disabled, clouds are always rendered behind scene geometry. It is more performant to have this disabled.

#### Cull Clouds Behind Geometry

**C# member variable:** `bool m_cullCloudBehindGeometry` \
When enabled, only renders clouds for un-occluded pixels. This can improve performance a lot, but it is currently an experimental feature, because it can introduce unwanted artifacts.

#### Composite Clouds By Height

**C# member variable:** `bool m_compositeCloudsByHeight` \
Uses an alternative algorithm for cloud compositing that assumes the camera is below the clouds. Does not make much of a difference for three or fewer cloud layers, but for scenes with three layers or more it can improve performance dramatically.

#### Cloud Shadow Map Film Plane Scale

**C# member variable:** `float m_cloudShadowMapFilmPlaneScale` \
How big the cloud shadow map is. This will limit how far away from the origin the cloud shadows are accurate. Beyond this distance, the shadowmap will tile, so results will still be somewhat representative of the general amount of coverage, but won't be specifically correct.

#### Cloud Shadow Map Quality

**C# member variable:** `Expanse.Datatypes.Quality m_cloudShadowMapQuality` \
Texture quality of the shadow map. Make sure to increase the size of the HDRP cookie atlas accordingly. This can be done in the HDRP settings asset. Unity will print errors to indicate that you need to increase the atlas size.

#### Cloud Reflections

**C# member variable:** `bool m_cloudReflections` \
Whether or not to render clouds into the ambient sky cubemap. Disabling this can improve performance, and is particularly useful if you don't require any kind of detailed cloud reflections to be rendered, or you can fake cloud reflections with fog.

#### Amortize Cloud Reflections

**C# member variable:** `bool m_amortizeCloudReflections` \
Given that cloud reflections are enabled, this parameter amortizes their computation over a number of frames. This results in higher fidelity reflections for the same frame-by-frame cost, but the reflections will update slowly as a result of the temporal amortization. In general, you should enable this parameter, and disable it if you find that your lighting conditions are changing too fast for the reflections to update in time.

<!---------------------------------------------------------------------------------------->
<!-------------------------------------- ATMOSPHERE -------------------------------------->
<!---------------------------------------------------------------------------------------->

### Atmosphere Quality

These parameters pertain to global atmosphere quality.

#### Fullscreen Atmosphere

**C# member variable:** `bool m_fullscreenAtmosphere` \
If enabled, renders the atmosphere per-pixel, instead of using an optimized lookup table. This is extremely expensive and should only be used in non-realtime applications.

#### Atmosphere Texture Quality

**C# member variable:** `Expanse.Datatypes.Quality m_atmosphereTextureQuality` \
Quality of atmosphere lookup textures. Lower quality settings will improve performance, at the cost of visual fidelity.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/potato_quality.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/ripping_through_the_metaverse_quality.jpg"/></div>
    </div>
    <p>Left: "Potato" (lowest) quality. Right: "Ripping Through the Metaverse" (highest) quality. Even the lowest quality setting holds up pretty well. Only in special more extreme cases are the higher quality settings necessary.</p>
</div>

#### Use Aerial Perspective

**C# member variable:** `bool m_useAerialPerspective` \
Whether or not to render aerial perspective over objects. Disabling this can improve performance. If you are using Unity or Expanse's fog, you may be able to disable aerial perspective without noticing too much of a visual downgrade. That said, I do think it looks nice and imbues things with an important sense of scale.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/no-ap.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/ap.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/ap-unity-fog.jpg"/></div>
    </div>
    <p>Left: No aerial perspective. Middle: with aerial perspective---note the blue haze in front of the terrain. Right: simulating aerial perspective with Unity's fog, on sky color mode.</p>
</div>

#### Use Multiple Scattering

**C# member variable:** `bool m_useMultipleScattering` \
Expanse uses a state of the art algorithm to model multiple scattering in the atmosphere. It significantly improves the appearance of the sky. However, if you are _really_ performance optimizing, you can disable it, though I would sooner suggest decreasing the [multiple scattering sample count](/editor/blocks/quality_settings_block?id=multiple-scattering-samples).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/no-ms.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/ms.jpg"/></div>
    </div>
    <p>The sky rendered with and without multiple scattering. Left: No multiple scattering. Right: with multiple scattering. The sky is much brighter, and it looks much bluer as well.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/no-ms-evening.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/ms-evening.jpg"/></div>
    </div>
    <p>Multiple scattering in the evening sky. Left: no multiple scattering. Right: with multiple scattering. Note how the penumbra is softened.</p>
</div>

#### Transmittance Samples

**C# member variable:** `int m_transmittanceSamples` \
The number of samples used when computing transmittance lookup tables. With importance sampling turned on, a value of as low as 10 gives near-perfect results on the ground. A value as low as 4 is ok if some visible inaccuracy is tolerable. Without importantance sampling, a value of 32 or higher is recommended.

#### Aerial Perspective Samples

**C# member variable:** `int m_aerialPerspectiveSamples` \
The number of samples used when computing aerial perspective. With importance sampling turned on, a value of as low as 10 gives near-perfect results on the ground. A value as low as 5 is ok if some visible inaccuracy is tolerable. Without importantance sampling, a value of 16 or higher is recommended.

#### Single Scattering Samples

**C# member variable:** `int m_singleScatteringSamples` \
The number of samples used when computing single scattering. With importance sampling turned on, a value of as low as 10 gives near-perfect results on the ground. A value as low as 5 is ok if some visible inaccuracy is tolerable. Without importantance sampling, a value of 32 or higher is recommended.

#### Multiple Scattering Samples

**C# member variable:** `int m_multipleScatteringSamples` \
The number of samples to use when computing the initial isotropic estimate of multiple scattering. Importance sampling does not apply here. To get a near-perfect result, around 8 samples is necessary. But it is a fairly subtle effect, so as low as 4 samples gives a decent result.

#### Multiple Scattering Accumulation Samples

**C# member variable:** `int m_multipleScatteringAccumulationSamples` \
The number of samples to use when computing the actual accumulated estimate of multiple scattering from the isotropic estimate. The number of samples to use when computing the initial isotropic estimate of multiple scattering. With importance sample, 8 samples gives a near-perfect result. However, multiple scattering is a fairly subtle effect, so as low as 3 samples gives a decent result. Without importance sampling, a value of 32 or higher is necessary for near perfect results, but a value of 4 is sufficient for most needs.

#### Importance Sample Atmosphere

**C# member variable:** `bool m_importanceSampleAtmosphere` \
Whether or not to use importance sampling for all atmosphere calculations except aerial perspective. Importance sampling is a sample distribution strategy that increases fidelity given a limited budget of samples. It is recommended to turn it on, as it doesn't decrease fidelity, but does allow for fewer samples to be taken, boosting performance. However, for outer-space perspectives, it can sometimes introduce inaccuracies, so it can be useful to increase sample counts and turn off importance sampling in those cases.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/no_importance.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/importance.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/high_sample_reference.jpg"/></div>
    </div>
    <p>Left: fewer than 4 samples taken per category, with no importance sampling. Middle: fewer than 4 samples taken per category, but this time, with importance sampling. Right: "ground truth" reference with 64 samples taken per category. Importance sampling is able to get startlingly close to the ground truth, with very few samples.</p>
</div>

#### Importance Sample Aerial Perspective

**C# member variable:** `bool m_importanceSampleAerialPerspective` \
Whether or not to use importance sampling for aerial perspective. Importance sampling is a sample distribution strategy that increases fidelity given a limited budget of samples. However, it can sometimes cause artifacts or perform poorly when computing aerial perspective, so the option to turn it off for aerial perspective only is provided.

#### Aerial Perspective Depth Skew

**C# member variable:** `float m_aerialPerspectiveDepthSkew` \
Skews precomputed aerial perspective samples to be further from the camera (if less than 1) or closer to the camera (if greater than 1). Adjusting this can be useful for environments with very heavy fog, where it can be more important to capture scattering close to the camera.

#### Aerial Perspective Render Distance

**C# member variable:** `float m_aerialPerspectiveRenderDistance` \
How far out from the camera to render aerial perspective to. Generally a value of around 30k-100k meters is good. This is decoupled from the camera's far clipping plane because it is necessary to render it very far away to composite on top of clouds.

<!---------------------------------------------------------------------------------------->
<!------------------------------------------ FOG ----------------------------------------->
<!---------------------------------------------------------------------------------------->

### Fog Quality

These parameters pertain to global fog quality---so, screenspace atmosphere layers.

#### Fog Quality

**C# member variable:** `Expanse.Datatypes.Quality m_screenspaceFogQuality` \
Quality of fog lookup texture. Lower quality settings will improve performance, at the cost of visual fidelity. The primary cost here is in the fidelity of the volumetric shadows. If you aren't using volumetric shadows, you may as well set this very low.

#### Fog Sub Samples

**C# member variable:** `int m_fogSubSamples` \
How many samples to use per froxel when lighting fog. Increasing this can help with noise, but **it is important for performance to keep this as low as possible.**

#### Screenspace Shadow Samples

**C# member variable:** `int m_screenspaceShadowSamples` \
The number of samples to use when computing screenspace volumetric shadows for fog layers.

#### Fog Depth Skew

**C# member variable:** `float m_fogDepthSkew` \
Skews precomputed fog samples to be further from the camera (if less than 1) or closer to the camera (if greater than 1). Adjusting this can be useful for environments with very heavy fog, where it can be more important to capture scattering close to the camera.

#### Fog Depth Bias

**C# member variable:** `float m_fogDepthBias` \
Biases the depth value that fog is rendered at for each slice toward the camera, to prevent fog bleeding through objects that should occlude it. The best way to avoid this is to increase the fog quality and bring the camera's far clip plane closer, but this parameter can be useful when these two things are not enough.

At zero, no bias is applied. At one, maximum bias is applied. Default is 0.5.

#### Fog Depth Downscale

**C# member variable:** `int m_fogDepthDownscale` \
Downscale factor for depth buffer used for screenspace shadows in fog layers. If this factor is lower, performance is worse but the volumetric shadows are sharper. If it's higher, performance is better, but the shadows are blurrier. This can be used to an artistic end. Sometimes the sharp shadows you get when using the full resolution depth buffer are too sharp.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/screenspace_downscale_1.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/screenspace_downscale_3.jpg"/></div>
    </div>
    <p>Left: no downscaling applied to depth buffer. Right: downscale factor of 3 applied to depth buffer. Notice how the crepuscular rays aren't quite as streaky.</p>
</div>

#### Fog Use Blur Denoising

**C# member variable:** `bool m_fogUseBlurDenoising` \
When enabled, blurs the fog buffer to reduce noise. You lose a bit of sharpness, but generally it does a good job.

#### Fog Denoising History Frames

**C# member variable:** `int m_fogDenoisingHistoryFrames` \
How many history frames to use when denoising fog. The higher this value is, the better the denoising will be. However, if you set it too high, you'll start to get noticeable ghosting.

For something like an FPS, where the player moves relatively slow compared to volumetric shadow casters, you can probably set this pretty high. For a flight sim, you might have to set this lower to avoid ghosting.

Note: when this is set to zero, temporal denoising is disabled.

#### Fog Diffusion

**C# member variable:** `bool m_fogDiffusion` \
When enabled, turns on a pseudo-blur effect that attempts to model the diffusion of light from objects visible on-screen. You can think about is as a sort of screenspace global illumination approximation.

The effect is fairly subtle, but it's easiest to see in the background of the following example.

Enabling diffusion will result in a slight performance hit (around 0.2ms on a 2080Ti).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quality_settings/diffusion_off.jpg"/></div>
        <div class="img-col"><img src="img/quality_settings/diffusion_on.jpg"/></div>
    </div>
    <p>Left: diffusion disabled. Right: diffusion enabled.</p>
</div>

#### Fog Diffusion Amount

**C# member variable:** `bool m_fogDiffusionAmount` \
Radius of the diffusion effect. Higher values will result in a wider blur.

#### Fog Diffusion Blend

**C# member variable:** `bool m_fogDiffusionBlend` \
Amount to blend diffused result with regular color buffer. 1 = fully diffuse, 0 = no diffusion. 0.5 is a reasonable default.

#### Fog Diffusion Samples

**C# member variable:** `bool m_fogDiffusionSamples` \
How many samples to use when computing the blur effect. This will affect performance if you crank it too high.
