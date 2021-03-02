# Tutorial: Creating Expressive Cloudscapes

I have to admit, the first things that got me interested in working on realtime clouds weren't real clouds at all---they were clouds in games. This is a little bit silly. What could be so inspiring about digital reconstructions of clouds, compared to their towering, real-life counterparts?

Well, certainly one thing is that we're a lot more likely to be constantly evaluating the beauty of a new digital experience than we are of something we are confronted with every day. When a game does a great job modeling clouds, we notice, because (whether on a subconscious or conscious level) we're continually analyzing how the game world differs from our own. For games taking a swing at photorealism, the question is necessarily, *how well does this measure up to the real world?*

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

TODO
* set up structure and introduce
* set up detail and introduce
* set up detail warp and introduce

### Balancing and Shaping

## Lighting

TODO
* "lighting model is attenuative"
* Turn off depth probability, ambient, MS, and vertical probability
* Determine density value.
* Add back multiple scattering.
* Add back ambient.
* Add back vertical.
* Add back depth.
* Tweak phase function and silver lining.
* Check different illumination conditions and adjust!

## Movement

TODO
* Add some base velocity.

## Performance Tweaks

TODO
* Optimize params.