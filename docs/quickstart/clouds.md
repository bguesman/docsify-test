# Tutorial: Creating Expressive Cloudscapes

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/reference_match_2.jpg"/></div>
    </div>
    <p>The clouds we'll create in this tutorial.</p>
</div>

I have to admit, the first things that got me interested in working on realtime clouds weren't real clouds at all---they were clouds in games. This is a little bit silly. What could be so inspiring about digital reconstructions of clouds, compared to their towering, real-life counterparts?

Well, certainly one thing is that we're a lot more likely to be constantly evaluating the beauty of a new digital experience than something we are confronted with every day. When a game does a great job modeling clouds, we notice, because (whether on a subconscious or conscious level) we're continually analyzing how the game world differs from our own. For games taking a swing at photorealism, the question is necessarily, *how well does this measure up to the real world?*

This isn't really what drew me in though. In fact, the two examples of game clouds that captivated me occur in games that deviate from real life quite a bit: Hyrule's warbley cloudscapes in **Zelda: Breath of the Wild**, and Toussaint's papery cumulus clouds in **The Witcher 3's Blood and Wine** DLC.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://assets.vg247.com/current//2019/10/breathofthewild.jpg"/></div>
        <div class="img-col"><img src="https://i.pinimg.com/originals/fb/e2/a0/fbe2a0b7d9b66aa32e7ea8026cf5b4e6.jpg"/></div>
    </div>
    <p>Left: clouds from Zelda: Breath of the Wild. Source: <a href="https://assets.vg247.com/current//2019/10/breathofthewild.jpg">https://assets.vg247.com/current//2019/10/breathofthewild.jpg</a>. Right: Clouds from The Witcher 3's Blood and Wine DLC. Apparently CD Projekt Red implemented an entirely new cloud rendering system for this DLC. Source: <a href="https://i.pinimg.com/originals/fb/e2/a0/fbe2a0b7d9b66aa32e7ea8026cf5b4e6.jpg">https://i.pinimg.com/originals/fb/e2/a0/fbe2a0b7d9b66aa32e7ea8026cf5b4e6.jpg</a>.</p>
</div>

This is where the title of this tutorial comes from: these cloudscapes might not be photoreal, but they are **expressive**. Breath of the Wild's cloudscapes are either tranquil (like in the image), or violently stormy, folding into the game's portrayal of nature as at once beautiful and brutally dangerous. The skies in Blood and Wine are dotted with what you could almost call the "platonic ideal" of clouds, evoking the cultural connotations of romanticism (think of [John Constable's works](https://www.john-constable.org/Landscape-With-Boys-Fishing.html)).

Clouds can emote and express. They can be even be plot devices bordering on characters, like in the Pixar film [*Up*](https://www.youtube.com/watch?v=ozwIZ_sBD24&ab_channel=DisneyClips). It's not so much clouds themselves that are fascinating, but **how we use them to tell stories and convey moods.**

All of this said, Expanse's primary goal is still to get as close to photorealism as possible in realtime, with artistic overrides to help you create more stylized cloudscapes if you want to. Still, we always have to remember that our goal as digital media artists is to make people feel something. This is even more important than realism---a consistent aesthetic should always be our north star.

## Setting Up

Alright, now that we've waxed philosophical, let's open up Unity and get to work.

We'll assume that you've already imported Expanse into your project, and that you know how to use a full sky prefab. For more info on how to do this, and how to render your first sky with Expanse, check out the [quickstart guide](/quickstart/quickstart).

The first thing we'll do is drag in one of the full sky prefabs to minimize the amount of boilerplate setup we need to do. In particular, we'll drag the prefab `Assets/Expanse/prefabs/Full Skies/Expanse Cloudless Sky.prefab` into our scene, since we'll be creating the clouds on our own.

With that out of the way, let's add a `Procedural Cloud Volume Block` to the scene. This component is the authoring and rendering tool for creating volumetric clouds in Expanse.

Specifically, let's create an empty under the main prefab and call it `Volumetric Clouds`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/clouds/create_empty.jpg"/></div>
    </div>
    <p>Create an empty game object under the main prefab, and rename it to "Volumetric Clouds".</p>
</div>

We'll then add the `Procedural Cloud Volume Block` as a component on this empty.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/clouds/add_block.jpg"/></div>
    </div>
    <p>Add the Procedural Cloud Volume Block as a component on the empty Volumetric Clouds game object we just created.</p>
</div>

To fix the warning spew you'll inevitably get, drag the game object named "Expanse Sky And Fog Volume" onto the volume slot in the cloud volume component we just added.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/drag_volume.jpg"/></div>
    </div>
    <p>Drag the game object named "Expanse Sky And Fog Volume" onto the volume slot in the cloud volume component we just added.</p>
</div>

You should now see the sky completely covered by a uniform volumetric cloud cover. **You may need to hit the play button and then go back to edit mode for the cloud volume to activate.**

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/init_state.jpg"/></div>
    </div>
    <p>The default state of the cloud volume block, a uniform haze.</p>
</div>

Ok, we're ready to start making some clouds!

## Strategy Overview

We can break up the cloud creation process into four phases: **modeling**, **lighting**, **movement**, and **performance**. Expanse has foldouts in the `Procedural Cloud Volume Block` that correspond to each.

**Modeling** is the process of designing the cloud density field. We'll be work with procedural noises to create the shape and form of our clouds. You can think about this kind of like authoring a mesh. There are two foldouts we'll work with: the `Modeling` foldout and the `Noise Editor` foldout.

**Lighting** determines the actual rendered appearance of the clouds. If modeling the density field is like authoring a mesh, setting the lighting parameters is analogous to authoring that mesh's material. We'll work with the `Lighting` foldout for this.

**Movement** controls the way the clouds progress across the sky. The relevant parameters are in the `Movement` foldout.

**Performance** is the process of optimizing the cloud rendering algorithm to look good but not tax the GPU too much. Tweaking these settings is critical for being able to maintain high framerates. The relevant foldout is the `Quality` foldout.

We'll present these steps in order, but keep in mind that when you're authoring cloud layers from scratch, you'll probably jump between these steps quite often. Changing the lighting parameters may make you realize you need to fix something in the modeling stage. You might realize that, to get the performance you need, you have to lower the noise texture quality and rebalance the noise layers. These are just a few examples---the process is more of a continual oscillation between the steps that gets you closer and closer to your target look.

Before we get started, let's do what most good artists do, and pick a photo of some clouds to use as our reference. I like the idea of creating some nice, fragmented prairie clouds, so let's use this image.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg"/></div>
    </div>
    <p>Our reference photo. Source: <a href="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg">https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg</a>.</p>
</div>

We might not get this exact result, especially up to things like post-processing and exposure settings. Even so, it's important to use it as a guiding light, so that we are constantly reminded of what actual clouds look like.

## Modeling

The first thing we'll do here is actually completely unrelated to modeling, but it will make our lives a lot easier. Navigate to the `Quality` foldout, and set: 
* `Reprojection Frames` to 1
* `Coarse Step Range` to `(64, 96)`
* `Detail Step Range` to `(128, 256)`

This basically sets the quality to maximum, so that we don't have to worry about un-optimized quality settings affecting our authoring process. We will go back and optimize the quality settings once we are finished authoring the clouds.

You may find that your machine is unable to handle these settings at a consistent framerate---in this case, you can try lowering the step ranges, and, if that fails, increasing the reprojection frames. As a reference, my RTX 2080 Ti handles them just fine.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/max_qual.jpg"/></div>
    </div>
    <p>Set the quality to max, so that we don't have to worry about quality settings interfering with our modeling process.</p>
</div>

### Defining the Volume
Expanse generates and renders clouds within a volume that we specify. So, our first major decision is the size, shape, and location of this volume. Open up the `Modeling` tab, and you'll see parameters for the origin and extents of the volume.

In general, the volume has to be pretty big in order for clouds to properly bend over the horizon. However, this can negatively impact performance. The best thing to do is make your cloud volume as small as possible.

Keeping this in mind, the settings we'll choose here are:
* `Origin`: `(0, 1600, 0)`. We'll center the volume in the x and z directions, and put the clouds 1600 meters into the air, a reasonable height for cumulus clouds.
* `X Extent` and `Z Extent`: both `100000` meters. This won't extend beyond the horizon.
* `Y Extent`: `1200` meters. This bounds the thickness of our clouds.
* `Curved`: `enabled` (so, box checked). This curves the cloud volume with the planet's surface. It's a subtle effect, but it looks nice.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/extents_edit.jpg"/></div>
    </div>
    <p>Set the cloud volume modeling parameters.</p>
</div>

We'll also set up the clouds to fade out nicely toward the horizon. This is controlled by the density attenuation parameters. We'll set these to:
* `Attenuation Distance`: `10000` meters.
* `Attenuation Bias`: `15000` meters.

This isn't necessarily physical, but it looks cool!

We'll ignore the `Ramp Up` parameter, which allows us to specify a start distance from clouds to appear.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/attenuation.jpg"/></div>
    </div>
    <p>Set the density attenuation so that the clouds fade out nicely into the distance.</p>
</div>

### Crafting the Noises

Now that the volume is set up, we're going to dig into authoring the volumetric noises that will form our clouds. Expanse uses six different procedural noises to model the cloud density field:

1. **Base**: 3D noise that forms the general shape of the clouds.
2. **Base Warp**: warp to the base noise texture to help create some fluid-looking features.
3. **Coverage Map**: 2D noise that determines where clouds appear. This can easily be hand-authored for setting up specific scenes.
4. **Structure**: 3D noise that erodes the base noise to add more detail to the clouds.
5. **Detail**: 3D noise that further erodes the base noise, after the structure noise, to add the final level of detail.
6. **Detail Warp**: warp to the detail noise, good for creating wispy-looping tails and tendrils.

I won't sugarcoat it, **authoring these noises is hard.** It takes some practice to feel out exactly what the right settings are to achieve a particular look. This is partly why there's a number of presets for the `Procedural Cloud Volume Block` component; it can be a good idea to use one of these as a starting point for your own clouds.

However, in this tutorial we'll be authoring all the noises by hand, and going over some of the reasons why we choose particular parameter values. So, let's dive in!

The first thing we'll do is open up the `Modeling` tab, and set all the `Intensity` sliders to zero, except the `Coverage Intensity` slider, which will be set to one. This will allow us to view only the base noise.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/sliders_zero.jpg"/></div>
    </div>
    <p>Set all the modeling sliders to zero, except for coverage intensity, which is set to 1.</p>
</div>

We'll also open the lighting tab and set the `Density` parameter to `50000`, so we can easily see the clouds.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/density_50k.jpg"/></div>
    </div>
    <p>Open up the lighting tab, the density to 50000.</p>
</div>

Finally, we'll position the camera above the clouds. This is a nice trick that allows you to more easily see the structure and form of the clouds you're modeling. I've found that it can actually be harder, at least in the beginning phase, to model clouds from the ground.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/above_clouds.jpg"/></div>
    </div>
    <p>Position the camera above the cloud layer.</p>
</div>

The clouds will look pretty flat. This is because the base noise is taking up the entire rectangular volume we've defined. To fix this issue and see some form, open up the `Modeling` tab, and reduce the bottom value of the parameter `Height Gradient Top` to `0.75`. This will erode off the top of the clouds, and expose the internal structure of the base noise.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:40%" src="img/quickstart/clouds/height_gradient_model.jpg"/></div>
    </div>
    <p>Open up the modeling tab and bring down the bottom value of the "height gradient top" parameter to 0.75.</p>
</div>

Now, your clouds should have a bit more visible shape! However, it still won't look too great. As we adjust the base noise values, the noise will start to look more and more like clouds.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/above_clouds_hg.jpg"/></div>
    </div>
    <p>What the clouds look like now that we've lowered the height gradient range.</p>
</div>

#### Base

Now, it's time to actually author the base noise. Open up the `Noise Editor` tab, and you'll be confronted with the following UI:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/noise_editor.jpg"/></div>
    </div>
    <p>The noise editor UI.</p>
</div>

This might look confusing at first, but you'll see as we walk through creating the base noise that it's pretty simple. The `Layer Select` dropdown lets you choose which of the six noise layers you're editing. Click this and select `Base`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/select_base.jpg"/></div>
    </div>
    <p>Select the base layer for editing.</p>
</div>

The `Noise Type` dropdown lets you select the kind of noise this layer uses. For a full documentation of all the different noises, see [the procedural cloud volume block documentation page.](/editor/blocks/procedural_cloud_volume_block?id=noise-type) For the base noise, we'll select the option `Perlin Worley`---this is a soft, kind of bulbous noise that is good for modeling clouds that have the trademark "cauliflower" shape, kind of like our reference photo.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/select_pw.jpg"/></div>
    </div>
    <p>Select the Perlin Worley noise type.</p>
</div>

The `Scale` parameter controls the size of the noise (in the x and z directions), coupled with the `Tile` parameter, which controls how many times to repeat the noise in the volume. A good strategy when using lower quality noise settings is to keep the scale lower, but the tile higher---this way the noise can be lower resolution but still express a lot of detail. We'll opt for something reasonable and set the scale to `(8, 8)`, and the tile to `9`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/base_scale_tile.jpg"/></div>
    </div>
    <p>Set the scale to (8, 8), and the tile to 9.</p>
</div>

And, whoa!! All of a sudden our blah-looking volume is starting to look kind of like clouds!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/base_scaled.jpg"/></div>
    </div>
    <p>Our base noise is starting to look pretty good!</p>
</div>

We'll make one final tweak before moving on: we'll up the `Octaves` parameter to `7`. This will increase the amount of detail in the noise.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/base_octave.jpg"/></div>
    </div>
    <p>Up the octave count to 7.</p>
</div>

With that, our base noise is finished!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/final_base.jpg"/></div>
    </div>
    <p>Our final base noise!</p>
</div>

#### Coverage

Modeling the coverage noise can be bit tricky, since this determines both the "size" and amount of our clouds. If we want smaller clouds that are spread out, we'll need to use pretty big coverage noise, but with a lower coverage intensity. If we want small close together clouds, we'll use a small coverage noise, but with high coverage intensity. If that doesn't make sense to you right now, don't worry---you'll get a feel for it as you play around with the settings.

We'll choose the former option---smaller clouds, spread further apart, like our reference photo. Before we set any values in the `Noise Editor` though, let's go and change the coverage intensity so that the changes we make are visible. Open up the `Modeling` tab and set the `Coverage Intensity` parameter to `0.88`.

Now, open up the `Noise Editor` tab again, and set `Layer Select` to `Coverage`. Set the following parameters:

* `Noise Type`: `Worley`. Worley noise is basically lots of little self-contained splotches. Perfect for making little circular clouds.
* `Scale`: `(7, 13)`. It can often look better to set the coverage scale to be different in both directions. This can give your clouds a sense of directionality.
* `Octaves`: `2`. You can play around with this, I think it looks best at `2`.

Here's an image of the final noise settings for the coverage layer.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/coverage_settings.jpg"/></div>
    </div>
    <p>The final settings for the coverage noise layer.</p>
</div>

And here's what it should look like. If you don't love the look, don't worry---we will be making it look much, much better!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/final_coverage.jpg"/></div>
    </div>
    <p>How our clouds look with the final coverage noise applied.</p>
</div>

#### Structure and Detail

We'll now move on to setting up our structure and detail noises. Our base noise looks alright on its own, but it's lacking some of the small-scale features that really imbue the clouds with a sense of scale.

Open up the `Modeling` tab and set the `Structure Intensity` parameter to `0.17`, so that the changes we make are visible. Then, open up the `Noise Editor` tab, and set `Layer Select` to `Structure`. Set the following parameters:

* `Noise Type`: `Worley`. Worley noise is good for creating the "cauliflower bulb" look that's present in our reference.
* `Scale`: `(8, 8)`.
* `Octaves`: `6`.
* `Tile`: `64`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/structure_settings.jpg"/></div>
    </div>
    <p>The final settings for the structure layer.</p>
</div>

This is how the clouds should look now. Some nice, bulbous shapes!
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/final_structure.jpg"/></div>
    </div>
    <p>How our clouds look with the final structure noise applied.</p>
</div>

Alright, let's add in the detail noise. In the `Modeling` tab, set the `Detail Intensity` parameter to `0.42`, again to ensure our changes are visible. Open up the `Noise Editor` tab and set `Layer Select` to `Detail`. Set the following parameters:

* `Noise Type`: `Inverse Worley`. Inverse Worley noise is nice for creating wispiness---it's perfect for modeling the swirly tendrils in our reference.
* `Scale`: `(8, 8)`.
* `Octaves`: `6`.
* `Octave Multiplier`: `0.65`. We crank this up a little from the usual `0.5` so that we don't lose too much detail in the higher octaves.
* `Tile`: `72`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/detail_settings.jpg"/></div>
    </div>
    <p>The final settings for the detail layer.</p>
</div>

And here's what the clouds should look like. This makes a pretty big difference! You'll notice that the lighting is starting to look pretty off---we'll fix this in the next section.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/final_detail.jpg"/></div>
    </div>
    <p>How our clouds look with the final detail noise applied.</p>
</div>

Finally, we'll warp the detail noise a bit, to create some fluid-like features. In the `Modeling` tab, set the `Detail Warp Intensity` parameter to `0.1`. Open up the `Noise Editor` tab and set `Layer Select` to `Detail Warp`. Set the following parameters:

* `Noise Type`: `Curl`. Curl noise is built to mimick the warping of a density field according to the laws of fluid dynamics.
* `Scale`: `(8, 8)`.
* `Octaves`: `4`.
* `Octave Multiplier`: `0.25`. We pull this down from the usual `0.5` so that the high-frequency warping doesn't dominate the behavior.
* `Tile`: `64`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/detail_warp_settings.jpg"/></div>
    </div>
    <p>The final settings for the detail warp layer.</p>
</div>

And here's what the clouds should look like. This is a subtle difference but will be more visible from the ground.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/final_detail_warp.jpg"/></div>
    </div>
    <p>How our clouds look with the final detail warping applied.</p>
</div>

And there you have it! That's all of our noises. When you design your own noises, the process will probably be much more chaotic---but that's part of the fun, right?

### Balancing and Shaping

The final step in the modeling stage is to adjust the global modeling parameters that shape the clouds independent of the noises. These parameters---things like the height gradient and rounding values---will sculpt the overall shape of the clouds. Adjusting these is crucial for getting your clouds to look pleasing.

For this step, move the camera down below the clouds again. It'll make it easier to see the changes. Here's what our clouds look like now:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/before_shaping.jpg"/></div>
    </div>
    <p>How our clouds look without any additional shaping work.</p>
</div>

They look ok (minus the lighting, of course), but they look a bit boxy. We can fix this by adjusting some of the shaping parameters. In particular, open up the `Modeling` foldout and set,
* `Rounding` to `7.5`. This controls how much the clouds are rounded off at the top.
* `Rounding Shape` to `1.5`. This controls how sharp the round-off effect is.

This helps reign in the boxiness a bit. I encourage you to play around with these parameters, and with the `Height Gradient` parameters, to get the look you desire.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/after_shaping.jpg"/></div>
    </div>
    <p>How our clouds look with the rounding effect applied.</p>
</div>

## Lighting

Now that we've got our clouds shaped how we want, we can move on to tweaking their illumination characteristics.

Before we start, a note: typically, you'd go back and forth tweaking the lighting, and then the modeling parameters, and then back to the lighting, etc. However, we're blasting through it all sequentially so that it's easier to digest. Just know that if you're adjusting the lighting parameters and something doesn't look quite right, you might have to go back to the modeling stage and change some things around!

Ok, down to business.

### The Attenuative Lighting Model

Expanse computes cloud illumination using a modified version of the [Nubis lighting model](http://advances.realtimerendering.com/s2017/Nubis%20-%20Authoring%20Realtime%20Volumetric%20Cloudscapes%20with%20the%20Decima%20Engine%20-%20Final%20.pdf), developed by Andrew Schneider, the engineer/artist behind Horizon Zero Dawn's volumetric cloudscapes. The genius observation underlying the model is that, in a physically-based system, **light can only be attenuated by participating media.** Since this is the case, we can model the color of the clouds by applying different sorts of attenuation strategies to the raw intensity of the sunlight.

Put another way, the strategy for shading a cloud pixel is,
1. Illuminate the pixel using the typical ray-marching scattering/transmittance calculation (described in the [Modeling The Earth's Atmosphere tutorial](/quickstart/earth-atmo)). **This will look far too bright.**
2. Apply successive "dimmer switches" (some physical and some approximate) to this value to **darken the areas of the clouds that are too bright.**

You'll get a sense for how this works as we progress through the different parameters.

### Baseline Lighting Parameters

To give ourselves a "clean slate", so to speak, we'll turn off all the dimmer switches. This will show us the clouds at their brightest possible color. 

Also, since we're making very dense, thick clouds, we'll also set the density parameter very high. For thinner clouds, you might go as low as `250`. We'll use a value of `50000`.

Open up the `Lighting` tab and set the following parameters:
* `Density`: `50000`
* `Anisotropy`: `0`
* `Silver Intensity`: `0`
* `Ambient Strength Range`: `(0, 0)`
* `Vertical Probability Strength`: `0`
* `Depth Probability Bias`: `1`
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/no_atten.jpg"/></div>
    </div>
    <p>Set up the cloud lighting to turn off all attenuation.</p>
</div>

Here's how it should look---pretty terrible!
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/no_atten_vis.jpg"/></div>
    </div>
    <p>How the clouds look with all attenuation off. Pretty horrible if you ask me!</p>
</div>

Let's fix this.

### Shadowing
The first and most important kind of attenuation is known as "self-shadowing". As light scatters into a cloud, it gets attenuated, since some of the photons scatter away before they can bounce out of the cloud and get into your eye. This result in dense clouds casting volumetric shadows onto themselves, darkening certain areas.

To enable self-shadowing, check the `Self Shadowing` box.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/self_shadow_box.jpg"/></div>
    </div>
    <p>Enable self shadowing by checking the "Self Shadow" box.</p>
</div>

Immediately the clouds have much more form! Some areas are dark and in shadow. Others are bright.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/self_shadow.jpg"/></div>
    </div>
    <p>How the clouds look with self shadowing enabled.</p>
</div>

However, some areas might not look dark enough, and some might look too dark. To remedy this, Expanse implements a "multiple scattering" approximation that can help dampen the effect of self-shadowing. It's not important to understand exactly what this is or why it works, but if you're curious you can read more in the [Procedural Cloud Volume Docs](/editor/blocks/procedural_cloud_volume_block?id=multiple-scattering-amount).

We'll set the following parameter values, but feel free to play around with them yourself. Set,
* `Multiple Scattering Amount` to `0.5`.
* `Multiple Scattering Bias` to `0.15`.

You might think that the result is too dark, but you'll see how we'll fix this in the next section.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/multiple_scattering.jpg"/></div>
    </div>
    <p>Set the multiple scattering parameters to adjust the shadows.</p>
</div>

### Ambient Light

Clouds receive most of their light directly from the sun disc, but they also receive some blue light from the skydome. This is crucial to model to get the clouds looking right---otherwise they will look too dark and dusty.

In the `Lighting` tab, set the following parameters, which govern the way ambient light from the sky interacts with the clouds,
* `Ambient Height Range`: `(0, 1)`. This defines the vertical range over which ambient light illuminates the clouds. At the minimum value, the ambient light is weakest, at at the maximum, the ambient light is strongest. This model works because the bottoms of clouds receive less ambient light.
* `Ambient Strength Range`: `(0.5, 1.5)`. These are the actual strength values associated with the height range. So, together, these settings mean "at the bottom of the clouds, ambient strength will be `0.5`, and at the top of the clouds, ambient strength will increase up to `1.5`".

Here's what that looks like. Now the self-shadowing is less pronounced and looks more appropriate.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/ambient.jpg"/></div>
    </div>
    <p>Introducing ambient light from the skydome into the cloud lighting model really helps!</p>
</div>

### Attenuative Approximations

We'll now move onto the rest of the attenuative approximations. The clouds look pretty decent as-is, but there are still some things we can improve upon.

For one, after we introduced the ambient light, the bottoms of the clouds got a bit too bright. We can remedy this by tweaking the `Vertical Probability` parameters. This is an adjustment that darkens the bottoms of the clouds---the idea is that light has a lower probability of scattering into the bottom of the clouds, because they are not facing the sun.

We'll set the following parameters:

* `Vertical Probability Height Range`: `(0.05, 0.25)`. Like the ambient height range, this controls the range over which the attenuation is applied.
* `Vertical Probability Strength`: `0.4`. This controls how aggressive the attenuation is.

Here's what that looks like. The bottoms of the clouds are now slightly darker, and thus they look more natural.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/vertical_prob.jpg"/></div>
    </div>
    <p>Darkening the bottoms of the clouds with the vertical probability parameters can help make them look more natural.</p>
</div>

Things are really starting to look good! However, if we compare to our reference, there's still some things we're missing.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg"/></div>
    </div>
    <p>Our reference photo. Source: <a href="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg">https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg</a>.</p>
</div>

For one, the clouds in this image appear to be brighter around the edges, at least closer to where the sun is in the sky. They also have kind of a "powered-sugar"-looking effect where their internal edges are brighter.

The corresponding things we have to adjust, and the final remaining lighting parameters, are the **phase function** and the **depth probability**.

Let's start with the **phase function**. The phase function tells us how bright clouds should be if they're close to the sun disc, and how dark they should be if they're far away. This is controlled by the `Anisotropy` parameter. The higher the anisotropy is, the brighter the clouds around the sun, and the darker the clouds away from the sun.

Sometimes this isn't enough to meet every artistic goal, so Expanse also adds optional silver lining parameters to tweak the brightness of the clouds very close to the sun.

Let's turn our camera toward the sun and set the following parameters:
* `Anisotropy`: `0.3`. This value is a little less than physical.
* `Silver Intensity`: `0.9`.
* `Silver Spread`: `0.3`.

This is the result:
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/anisotropy.jpg"/></div>
    </div>
    <p>Setting the anisotropy value can help distribute the light more correctly across the cloudscape.</p>
</div>

Now, we'll address the "powdered-sugar" effect. This is the result of light scattering into denser/less dense places in the cloud. Taking this into account and adding more self-shadowing in these spots is kind of like the volumetric version of ambient occlusion.

Let's set the following parameters:
* `Depth Probability Height Range`: `(0.25, 0.8)`.
* `Depth Probability Strength Range`: `(0.5, 2)`.
* `Depth Probability Density Multiplier`: `2`.
* `Depth Probability Bias`: `0.1`.

Repositioning our camera again, we get this result: on the left is without depth probability attenuation, and on the right is with it.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/no_depth_prob.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/depth_prob.jpg"/></div>
    </div>
    <p>Without depth probability (left) vs. with depth probability (right). The effect is subtle, but is most noticeable on the tops of the clouds.</p>
</div>

### Final Tweaks

Now, in theory, we've finished lighting our clouds. However, it's important to check how they look under different illumination conditions. Moving the sun around and comparing to our reference, a few problems appear.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/depth_prob.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/evening.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/behind.jpg"/></div>
    </div>
    <p>Our clouds under different illumination conditions.</p>
</div>

For one, to my eyes, it looks like we overdid it on the vertical scattering probability. The clouds are just too dark on the bottom! To remedy this, let's adjust the following parameters:
* In the `Lighting` tab, set `Vertical Probability Strength` to `0.3`.
* Also in the `Lighting` tab, set `Vertical Probability Height Range` to `(0.05, 0.2)`.

This looks better! On the left is before the tweak, and on the right is after the tweak.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/no_vertical_adjustment.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/vertical_adjustment.jpg"/></div>
    </div>
    <p>Tweaking our vertical probability helps brighten up the bottoms of the clouds. Left: our original values. Right: tweaked.</p>
</div>

I also think that the edges of the clouds aren't quite bright enough at sunset. To help with this, let's adjust the multiple scattering parameters. In particular,
* In the `Lighting` tab, set `Multiple Scattering Bias` to `0.17`.

Now we've got a little more light bleeding through our clouds at sunset.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/no_ms_adjustment.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/ms_adjustment.jpg"/></div>
    </div>
    <p>Reducing the multiple scattering bias pushes the light a little further into our clouds. Left: our original values. Right: tweaked.</p>
</div>

Finally, if we open up the `Noise Editor` tab and up the cloud texture quality (we've been working in medium the whole time), and also apply a little post-processing, we can get pretty close to our reference!
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/reference_match_2.jpg"/></div>
        <div class="img-col"><img style="height:81%" src="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg"/></div>
    </div>
    <p>Our result with a bit of brightness/saturation postprocessing (left) vs. our reference (right).</p>
</div>

Now, we were never going to match the reference exactly, but we did a decent job of using it as a guiding light.

## Movement

Cloud movement in Expanse is pretty simple---you can either specify a sampling offset to each noise value, which you can animate yourself, or you can directly specify a velocity, and Expanse will animate the offset for you.

Let's do this ourselves. Open up the `Movement` tab, and set the following values.
* `Use Offset`: unchecked. This specifies that we want to specify the cloud movement as a velocity.
* `Coverage Velocity`: `(0.001, 0.001)`.
* `Base Velocity`: `(0.00075, 0.00075)`.
* `Structure Velocity`: `(0.005, 0.005)`.
* `Detail Velocity`: `(0.01, 0.01)`.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/velocities.jpg"/></div>
    </div>
    <p>Set the velocity parameters in the movement tab.</p>
</div>

Hit the play button, and voila! Your clouds should be moving.
<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/clouds/moving.mp4" type="video/mp4">
    </video>
    <p>Moving clouds!</p>
</div>

## Performance Optimization

So you've got your clouds looking how you want, but your GPU fan is whirring like a buzzsaw, you're barely hitting 5 FPS, and the corner of your desk is on fire.

Raymarching and lighting a dense volume is computationally expensive, there's no way around that. That said, Expanse allows you to make a number of different quality tradeoffs and optimizations to get yourself to that smooth 60Hz framerate.

What follows are a set of instructions for optimizing your cloudscape.

### Step 1: Texture Quality

Expanse allows you to select the texture quality of the noises your clouds use. Navigate to the `Noise Editor` dropdown and you'll see the `Texture Quality Field`. Chances are, you will not be able to use `Ultra` or `Ripping Through The Metaverse` quality. Experiment around with the different texture qualities and track your framerate to see which is usable.

On my RTX 2080 Ti, for the clouds we just created, I'm going to select `High`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/qual_potato.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/qual_high.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/qual_rtm.jpg"/></div>
    </div>
    <p>Comparison of quality levels. Left: potato. Middle: high. Right: ripping through the metaverse.</p>
</div>

### Step 2: Sample Counts

Expanse also allows you to increase or decrease the number of raymarching samples used to render the clouds. In particular, it lets you specify a sample curve for different raymarch distances. Open up the `Quality` foldout and you'll see the following parameters:
* `Step Distance Range`: the minimum and maximum raymarch distances for the sampling curve.
* `Detail Step Range`: the step counts detail steps corresponding to the two specified raymarch distances.
* `Coarse Step Range`: the step counts for coarse "test" steps corresponding to the two specified raymarch distances. These can be lower than the detail steps.

For my setup, I'll set the step distance range to `(125, 15000)`, the coarse step range to `(32, 96)`, and the detail step range to `(96, 168)`.

If you set the step ranges too low, you'll start to notice noisy artifacts, so be careful here. You want them just low enough that there are no artifacts, but no lower.

### Step 3: Thresholds

Expanse lets you decide when density samples transmittance values should be considered zero---setting this threshold high will allow more lighting calculations to be skipped and will improve performance. If you set these values too high, you'll see artifacts.

In my case, I'll set the `Media Zero Threshold` to `0.001`, and the `Transmittance Zero Threshold` to `0.001`. This is on the upper end of values you can choose before you'll see noticeable issues.

Optimizing these values is particularly crucial for cloudscapes that are very dense.

### Step 4: Reprojection Frames

With the settings we've optimized so far, my RTX 2080 Ti renders the clouds in around 7 ms. This is easily enough to hit 60 FPS if we're just rendering clouds, but if we want our game to include all sorts of other expensive effects, we'll need to optimize performance even more.

This is where reprojection comes in---it allows us to render clouds across multiple frames, meaning we can still render clouds at full resolution, but do less work per individual frame.

The `Reprojection Frames` parameter in the `Quality` dropdown controls this. If I use the maximum reprojection amount (`4`, which really actually means 16 frames), the render time decreases to 1 ms. If I decrease the texture quality to "potato", the render time further decreases to 0.8 ms.

Reprojection isn't perfect---when used with TAA, it can make your clouds look blurry. It's also possible to see artifacts if your clouds are moving very fast. The best thing to do is only use as many reprojection frames as you need. If you can get away with 2 and still hit your performance requirements, do it.

### Step 5 (Optional): Resolution
If you've tried all of these things and still cannot get your framerate to where you want it to be, you can tweak the [global cloud subresolution parameter in your `Quality Settings Block`](/editor/blocks/quality_settings_block?id=cloud-subresolution). However, **this should be your last resort**, as it will really detract from your visual result.

Here's a comparison between our optimized clouds and our non-optimized clouds. The left image takes 50 ms to render. The right image takes only 1 ms!
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/qual_high.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/optimized_result.jpg"/></div>
    </div>
    <p>Comparison of optimized vs. unoptimized result. Left: unoptimized clouds, rendering at 50 ms per frame. Right: optimized clouds, rendering in just 1 ms!</p>
</div>

## Wrapup

Well, I hope you've enjoyed learning how to craft volumetric cloudscapes in Expanse. If you ever feel stuck, please feel free to post to the [Expanse forums TODO LINK!!]()!

This is really just the beginning of what it is possible to create with Expanse. For more info on all the parameters you can play around with, check out the docs for the [`Procedural Cloud Volume Block`](/editor/blocks/procedural_cloud_volume_block).

Below are some of the other results you can get with Expanse's clouds---for inspiration!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/candy.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/dense_6_15.jpg"/></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/pretty_boys.jpg"/></div>
        <div class="img-col"><img src="img/procedural_cloud_volume/scatter_1.jpg"/></div>
    </div>
</div>