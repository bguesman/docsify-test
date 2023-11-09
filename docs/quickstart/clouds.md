# Tutorial: Creating Expressive Cloudscapes

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/result_001.jpg"/></div>
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

We'll assume that you've already imported Expanse into your project, and that you know how to create a new advanced sky. For more info on how to do this, and how to render your first sky with Expanse, check out the [quickstart guide](/quickstart/quickstart).

The first thing we'll do is create a default full sky to minimize the amount of boilerplate setup we need to do. In particular, we'll right click on the hierarchy view and select `Expanse => Full Skies => Advanced Sky`. This will create a GameObject `Expanse Volumetric Cloud Sky` that has all the required sky components laid out for you.

In particular, this includes a GameObject called `Volumetric Clouds`, which possesses a [`Procedural Cloud Volume`](editor/blocks/procedural_cloud_volume_block.md) component. This component is the authoring and rendering tool for creating volumetric clouds in Expanse.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/cloud-object.jpg"/></div>
    </div>
    <p>The Procedural Cloud Volume component, the basis for the myriad of cloud types you can create with Expanse.</p>
</div>

The default state for the Procedural Cloud Volume is to render no clouds---a clear, empty sky. It will be our endeavor to change that.

**Let's make some clouds!**

## Strategy Overview

We can break up the cloud creation process into five phases: **modeling**, **lighting**, **movement**, **interaction**, and **performance**. The Procedural Cloud Volume has a number of foldouts that can be grouped into each category.

**Modeling** is the process of designing the cloud density field. We'll be work with procedural noises to create the shape and form of our clouds. You can think about this kind of like authoring a mesh. There are two foldouts we'll work with: the `Modeling` foldout and the `Noise Editor` foldout.

**Lighting** determines the actual rendered appearance of the clouds. If modeling the density field is like authoring a mesh, setting the lighting parameters is analogous to authoring that mesh's material. We'll work with the `Base Lighting`, `Self Shadowing`, and `Multiple Scattering` foldouts for this.

**Movement** controls the way the clouds progress across the sky. The relevant parameters are in the `Movement` foldout.

**Interaction** controls the way the clouds integrate with the other systems in your digital world---things like shadow casting and light pollution. These parameters are in the `Interaction` foldout.

**Performance** is the process of optimizing the cloud rendering algorithm to look good but not tax the GPU too much. Tweaking these settings is critical for being able to maintain high framerates. The relevant foldout is the `Quality` foldout.

We'll present these steps in order, but keep in mind that when you're authoring cloud layers from scratch, you'll probably jump between these steps quite often. Changing the lighting parameters may make you realize you need to fix something in the modeling stage. You might realize that, to get the performance you need, you have to lower the noise texture quality and rebalance the noise layers. These are just a few examples---the process is more of a continual oscillation between steps that gets you closer and closer to your target look.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:80%" src="img/quickstart/clouds/1-5-0/categories.jpg"/></div>
    </div>
    <p>A logical way to break up the different sections of the Procedural Cloud Volume component. Ungrouped are the "Preset Browser" foldout, which is just a way of cycling through saved cloud appearances, and the "Post Processing" foldout, which is outside the scope of this tutorial.</p>
</div>

Before we get started, let's do what most good 3D artists do, and pick a photo of some clouds to use as our reference. I like the idea of creating some nice, fragmented prairie clouds, so let's use this image.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg"/></div>
    </div>
    <p>Our reference photo. Source: <a href="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg">https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg</a>.</p>
</div>

We might not get this exact result, especially up to things like post-processing and exposure settings---though we will get extremely close. Still, it's always important to use a reference, so that we are constantly reminded of what actual clouds look like.

## Modeling

The first thing we'll do here is actually completely unrelated to modeling, but it will make our lives a lot easier. Navigate to the `Quality` foldout, and set: 
* `Reprojection Frames` to 1
* `Coarse Step Range` to `(48, 96)`
* `Detail Step Range` to `(96, 168)`
* `Media Zero Threshold` to `1e-5`
* `Use Temporal Denoising` to unchecked

This basically sets the quality to maximum, so that we don't have to worry about un-optimized quality settings affecting our authoring process. We will go back and optimize the quality settings once we are finished authoring the clouds.

You may find that your machine is unable to handle these settings at a consistent framerate---in this case, you can try lowering the step ranges, and, if that fails, increasing the reprojection frames. As a reference, my RTX 2080 Ti handles them just fine.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:80%" src="img/quickstart/clouds/1-5-0/max-qual.jpg"/></div>
    </div>
    <p>Set the quality to max, so that we don't have to worry about quality settings interfering with our modeling process.</p>
</div>

### Defining the Volume
Expanse generates and renders clouds within a volume that we specify. So, our first major decision is the size, shape, and location of this volume. These are controlled by the transform of the GameObject that the Procedural Cloud Volume is on.

In general, the volume has to be pretty big in order for clouds to properly bend over the horizon. However, this can negatively impact performance. The best thing to do is make your cloud volume as small as possible.

Keeping this in mind, the settings we'll choose here are:
* `Origin`: `(0, 1250, 0)`. We'll center the volume in the x and z directions, and put the clouds 1250 meters into the air, a reasonable height for cumulus clouds.
* `X Scale` and `Z Scale`: both `200000` meters. This won't extend beyond the horizon but will be more than enough to give us a good distance.
* `Y Scale`: `1200` meters. This bounds the thickness of our clouds.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:80%" src="img/quickstart/clouds/1-5-0/transform.jpg"/></div>
    </div>
    <p>Set the transform of the Game Object to define the local and extents of the cloud volume.</p>
</div>

Next, we'll open up the `Modeling` tab on the component. We'll be greeted with a whole host of what are probably foreign and possibly confusing parameters. Don't worry, we'll learn what they do as we go! For now, we'll just do a few things to make the authoring process easier. 

Click the animation curve labeled `Density Curve`. This curve controls the density of the clouds over height. For now we'll set it to be 1 everywhere---the clouds will have full density across the entire height of the cloud volume.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/density-curve.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/density-curve-editor.jpg"/></div>
    </div>
    <p>Set the density curve to be 1 over the entire height of the cloud volume.</p>
</div>

Do the exact same thing with the `Coverage Curve`---this curve controls how much the clouds cover the sky, again over the height of the volume. Once you do this, you should see clouds! *Well... ugly clouds, but clouds!*

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/first-cloud-visual.jpg"/></div>
    </div>
    <p>Now that we've adjusted the density and coverage curves away from zero, clouds are now visible!</p>
</div>

We'll also set up the clouds to fade out nicely toward the horizon. This is controlled by the density attenuation parameters. We'll set these to:
* `Attenuation Distance`: `16000` meters.
* `Attenuation Bias`: `30000` meters.

This isn't necessarily physical, but it looks good!

Finally, we'll set the various `Intensity` and `Multiply `parameters (`Structure Intensity`, `Detail Multiply`, etc.) to `0`. These control the various ways the cloud noises interact to compose the cloudscape. We'll investigate these more later.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/intensities-0.jpg"/></div>
    </div>
    <p>Set the various intensity and multiply parameters to 0, as a sort of "blank slate" to start from.</p>
</div>

We'll make one exception to this---we'll set the `Coverage Intensity` to `0.25`. This is the global (i.e., non-height-related) version of the curve parameter we set earlier. It controls how much the clouds cover the sky. Play around with it, it's fun!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/coverage-0.2.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/coverage-0.25.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/coverage-0.5.jpg"/></div>
    </div>
    <p>Different coverage intensities. Left: 0.2. Middle: 0.25. Right: 0.5.</p>
</div>

The rest of the defaults are fine for now. We will come back to mess with them later.

There's no denying it: **our clouds look like shit**. But we're only just getting started.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/coverage-0.25.jpg"/></div>
    </div>
    <p>Our clouds look terrible, but not for long!</p>
</div>

### Crafting the Noises

Now that the volume is set up and we have some reasonable defaults, we're going to dig into authoring the volumetric noises that will form our clouds. Expanse uses six different procedural noises to model the cloud density field:

1. **Base**: 3D noise that forms the general shape of the clouds.
2. **Base Warp**: warp to the base noise texture to help create some fluid-looking features.
3. **Coverage Map**: 2D noise that determines where clouds appear. This can easily be hand-authored for setting up specific scenes.
4. **Structure**: 3D noise that erodes the base noise to add more detail to the clouds.
5. **Detail**: 3D noise that further erodes the base noise, after the structure noise, to add the final level of detail.
6. **Detail Warp**: warp to the detail noise, good for creating wispy-looping tails and tendrils.

I won't sugarcoat it, **authoring these noises is hard.** It takes some practice to feel out exactly what the right settings are to achieve a particular look. This is partly why there's a number of presets for the `Procedural Cloud Volume` component; it can be a good idea to use one of these as a starting point for your own clouds.

However, in this tutorial we'll be authoring all the noises by hand, and going over some of the reasons why we choose particular parameter values. So, let's dive in!

First, we'll position the camera above the clouds. This is a nice trick that allows you to more easily see the structure and form of the clouds you're modeling. I've found that it can actually be harder, at least in the beginning phase, to model clouds from the ground.

> Recall that the coverage intensity slider in the modeling foldout should be set to 0.25.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/move-cam-above.jpg"/></div>
    </div>
    <p>Position the camera above the cloud layer.</p>
</div>

The clouds will look pretty flat on the top. This is because the base noise is taking up the entire rectangular volume we've defined. To fix this issue and see some form, open up the `Modeling` tab, and open the curve editor for the `Coverage Curve`. Adding one control point somewhere in the middle and moving the rightmost control point to 0, make it look something like this:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/coverage-curve-take-1.jpg"/></div>
    </div>
    <p>Pull down the coverage toward the top of the cloud volume using the coverage curve editor.</p>
</div>

This will round off the top of the clouds, and expose the internal structure of the base noise. Here's the result:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/rounded-off.jpg"/></div>
    </div>
    <p>The result of adjusting the coverage curve.</p>
</div>

Now, your clouds should have a bit more visible shape---funnily enough, this already looks pretty good! That said, as we continue to adjust the base noise values, the noise will start to look even more and more like clouds.

#### Base

Now, it's time to actually author the base noise. Open up the `Noise Editor` tab, and you'll be confronted with the following UI:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/noise-editor-ui.jpg"/></div>
    </div>
    <p>The noise editor UI.</p>
</div>

This might look confusing at first, but you'll see as we walk through creating the base noise that it's pretty simple. The `Layer Select` dropdown lets you choose which of the six noise layers you're editing. Click this and select `Base`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/select-base.jpg"/></div>
    </div>
    <p>Select the base layer for editing.</p>
</div>

The `Noise Type` dropdown lets you select the kind of noise this layer uses. For a full documentation of all the different noises, see [the procedural cloud volume documentation page.](/editor/blocks/procedural_cloud_volume_block?id=noise-type) For the base noise, we'll select the option `Perlin Worley 2`---this is a soft, kind of bulbous noise that is good for modeling clouds that have the trademark "cauliflower" shape, kind of like our reference photo. You can also experiment with the other noises to see which ones you like!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/select-pw-2.jpg"/></div>
    </div>
    <p>Select the Perlin Worley 2 noise type.</p>
</div>

The `Scale` parameter controls the size of the noise (in the x and z directions), coupled with the `Tile` parameter, which controls how many times to repeat the noise in the volume. A good strategy when using lower quality noise settings is to keep the scale lower, but the tile higher---this way the noise can be lower resolution but still express a lot of detail. We'll opt for something reasonable and set the scale to `(8, 8)`, and the tile to `46`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/scale-tile.jpg"/></div>
    </div>
    <p>Set the scale to (8, 8), and the tile to 46.</p>
</div>

And, whoa!! All of a sudden our blah-looking volume is starting to look kind of like clouds!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/base-ok.jpg"/></div>
    </div>
    <p>Our base noise is starting to look pretty good!</p>
</div>

We'll make two final tweaks before moving on. 
* We'll up the `Octaves` parameter to `7`. This will increase the amount of detail in the noise.
* We'll set the `Octave Multiplier` parameter to `0.5`. This parameter controls how much each successive noise octave is multiplied by. `0.5` means that each higher detail octave will be half as intense as the previous level of detail---this creates a good balance between the low frequency and high frequency features of the noise.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/base-octaves.jpg"/></div>
    </div>
    <p>Up the octave count to 7 and set the octave multiplier to 0.5.</p>
</div>

With that, our base noise is finished!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/base-final.jpg"/></div>
    </div>
    <p>Our final base noise!</p>
</div>

#### Coverage

Modeling the coverage noise can be bit tricky, since this determines both the "size" and amount of our clouds. If we want smaller clouds that are spread out, we'll need to use pretty big coverage noise, but with a lower coverage intensity. If we want small close together clouds, we'll use a small coverage noise, but with high coverage intensity. 

If that doesn't make sense to you right now, don't worry. You'll get a feel for it as you play around with the settings. For now, we'll choose the latter option---smaller clouds, relatively close together, like our reference photo.

Open up the `Noise Editor` tab again, and set `Layer Select` to `Coverage`. Set the following parameters:

* `Noise Type`: `Worley`. Using Worley noise as a coverage map will give us longer, more irregular cloud shapes. I think it's a good match for our reference. You can play around with the different options though and see what you like best.
* `Scale`: `(48, 56)`. It can often look better to set the coverage scale to be different in both directions. This can give your clouds a sense of directionality.
* `Octaves`: `5`. You can play around with this, I think `5` looks pretty good.
* `Octave Multiplier`: `0.25`. A lower value here will keep the cloud edges from looking too jagged.
* `Tile`: `2`. We'll leverage tiling to get a little more detail out of our coverage texture.

Here's an image of the final noise settings for the coverage layer.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/coverage-settings.jpg"/></div>
    </div>
    <p>The final settings for the coverage noise layer.</p>
</div>

And here's what it should look like.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/final-coverage.jpg"/></div>
    </div>
    <p>How our clouds look with the final coverage noise applied.</p>
</div>

#### Structure and Detail

We'll now move on to setting up our structure and detail noises. Our base noise looks alright on its own, but it's lacking some of the small-scale features that really imbue the clouds with a sense of scale.

Open up the `Modeling` tab and set the `Structure Intensity` parameter to `0.35`, so that the changes we make are visible. Then, open up the `Noise Editor` tab, and set `Layer Select` to `Structure`. Set the following parameters:

* `Noise Type`: `Perlin Worley 2`. Worley noise is particularly good for creating the "cauliflower bulb" look that's present in our reference. We'll use Perlin Worley noise though, because it'll give us a little more variation.
* `Scale`: `(24, 24)`.
* `Octaves`: `3`.
* `Tile`: `48`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/structure-settings.jpg"/></div>
    </div>
    <p>The final settings for the structure layer.</p>
</div>

This is how the clouds should look now. More detail!
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/final-structure.jpg"/></div>
    </div>
    <p>How our clouds look with the final structure noise applied.</p>
</div>

Alright, let's add in the detail noise. In the `Modeling` tab, set the `Detail Intensity` parameter to `0.2`, again to ensure our changes are visible. Open up the `Noise Editor` tab and set `Layer Select` to `Detail`. Set the following parameters:

* `Noise Type`: `Inverse Worley`. Inverse Worley noise is nice for creating wispiness---it's perfect for modeling the swirly tendrils in our reference.
* `Scale`: `(12, 12)`.
* `Octaves`: `5`.
* `Octave Multiplier`: `0.75`. We crank this up a little from the usual `0.5` so that we don't lose too much detail in the higher octaves.
* `Tile`: `196`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/detail-settings.jpg"/></div>
    </div>
    <p>The final settings for the detail layer.</p>
</div>

And here's what the clouds should look like. You'll notice that the lighting is starting to look pretty off---we'll fix this in the next section.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/final-detail.jpg"/></div>
    </div>
    <p>How our clouds look with the final detail noise applied.</p>
</div>

Finally, we'll warp the detail noise a bit, to create some fluid-like features. In the `Modeling` tab, set the `Detail Warp Intensity` parameter to `0.13`. Open up the `Noise Editor` tab and set `Layer Select` to `Detail Warp`. Set the following parameters:

* `Noise Type`: `Curl`. Curl noise is built to mimick the warping of a density field according to the laws of fluid dynamics.
* `Scale`: `(8, 8)`.
* `Octaves`: `3`.
* `Octave Multiplier`: `0.3`. We pull this down from the usual `0.5` so that the high-frequency warping doesn't dominate the behavior.
* `Tile`: `196`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/curl-settings.jpg"/></div>
    </div>
    <p>The final settings for the detail warp layer.</p>
</div>

And here's what the clouds should look like. This is a subtle difference but will be more visible from the ground.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/detail-warp-final.jpg"/></div>
    </div>
    <p>How our clouds look with the final detail warping applied.</p>
</div>

And there you have it! That's all of our noises. When you design your own noises, the process will probably be much more chaotic---but that's part of the fun, right?

### Balancing and Shaping

The final step in the modeling stage is to adjust the global modeling parameters that shape the clouds independent of the noises. These parameters---things like the density/coverage curves and the relative noise intensities---will sculpt the overall shape of the clouds. Adjusting these is crucial for getting your clouds to look pleasing.

For this step, move the camera down below the clouds again. It'll make it easier to see the changes. Here's what our clouds look like now:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/no-shaping.jpg"/></div>
    </div>
    <p>How our clouds look without any additional shaping work.</p>
</div>

They still look pretty bad---even without considering that we haven't adjusted the lighting. In particular, they look very boxy and very solid. We can fix this by adjusting some of the shaping parameters.

First, let's up the coverage a bit, so the clouds cover a bit more of the sky---this will give us more room to sculpt with. We'll open the modeling tab and increase the `Coverage Intensity` to `0.28`. The clouds will look something like this:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/coverage-up.jpg"/></div>
    </div>
    <p>How our clouds look with the coverage intensity at 0.28.</p>
</div>

Looking back at our reference photo, a few things are clear. For one, the clouds are nice and round on the top. Also, the clouds seem to start out as thin and wispy at a lower altitude, and then get denser and denser until they reach a maximum density somewhere in middle of their altitude range.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg"/></div>
    </div>
    <p>Our reference photo. Source: <a href="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg">https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg</a>.</p>
</div>

Luckily, we can model these phenomena with our coverage and density curves! Open up the curve editors for the `Density Curve` and `Coverage Curve` parameters, and draw curves that correspond to the things we've observed:

* Density: cloud density initially increases over height, but eventually plateaus.
* Coverage: cloud coverage decreases near the top of the height range, so that the cloud tops round off.

In other words, make them look something like this (density curve left, coverage curve right):

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/density-curve-final.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/coverage-curve-final.jpg"/></div>
    </div>
    <p>Left: the density curve. Cloud density increases over height and plateaus. Right: the coverage curve. Clouds coverage increases sharply, forming the bottoms of the clouds, and then slowly decreases so the tops of the clouds round off.</p>
</div>

This makes a world of difference! Here's what the clouds should look like now:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/adjust-curves.jpg"/></div>
    </div>
    <p>The clouds with the coverage and density curves adjusted.</p>
</div>

Finally, we're going to adjust the `Multiply` sliders. These allow us to use the structure and detail noise to modulate the density of the clouds and create interesting internal structure. This is in contrast to the **intensity** sliders, which will only erode away the clouds at their edges. Both strategies are necessary to create convincing volumetric details.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/multiply-sliders.jpg"/></div>
    </div>
    <p>The multiply modeling sliders.</p>
</div>

In particular, we'll set
* `Structure Multiply` to `0.08`
* `Detail Multiply` also to `0.08`

And with that, we have our final modeling result!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/modeling-result.jpg"/></div>
    </div>
    <p>The final result of modeling and shaping our clouds!</p>
</div>

These look pretty great, but they still don't quite look like our reference. We'll fix this by adjusting the lighting parameters.

## Lighting

Now that we've got our clouds shaped how we want, we can move on to adjusting their illumination characteristics.

Before we start, a note: typically, you'd go back and forth tweaking the lighting, and then the modeling parameters, and then back to the lighting, etc. However, we're blasting through it all sequentially so that it's easier to digest. Just know that if you're adjusting the lighting parameters and something doesn't look quite right, you might have to go back to the modeling stage and change some things around!

Ok, down to business.

### Expanse's Lighting Model

Expanse computes cloud illumination using a custom state-of-the-art lighting model. It can be described by the three different types of lighting it computes:

* **Base Lighting**. This is all the lighting that is fully physical, with no approximations. If Expanse's clouds were path-traced with thousands of bounces, this would be all you need to have the clouds look fully correct.
* **Self Shadowing**. Expanse lumps all direct light attenuation that results from clouds casting volumetric shadows onto themselves into this category. Approximations are made here to make the clouds more art-directable and more performant.
* **Multiple Scattering**. The last two categories would be enough to render convincing smoke or fog, but clouds are very dense, and so light scatters many times within them. Expanse uses a novel approach to approximate the effect of these additional light bounces in a way that's convincing and performant.

You'll get a sense for how these pieces fit together as we progress through the different parameters.

### Setup

To give ourselves a clean slate, so to speak, we'll turn off all the approximations and render the clouds with only "single scattering". This is pretty easy. Simply,

* Open up the `Self Shadowing` foldout and set the `Shadow Persistence` parameter to `1`.
* Open up the `Multiple Scattering` foldout and set the `Receptive Field` parameter to `0`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/lighting-clean-slate.jpg"/></div>
    </div>
    <p>Set these two parameters to turn off all approximations. This will make it easy to see their effect as we progress through the lighting setup.</p>
</div>

### Base Lighting

Now we're ready to start setting up our base lighting parameters. Open up the `Base Lighting` foldout. You'll be greeted with a number of parameters, some of which you'll recognize and some of which you might not. We'll be messing with the following ones:

* `Density`: overall thickness/solid-ness of the clouds.
* `Anisotropy`: How much light scatters around the sun vs. away from the sun.
* `Ambient Strength Range`: How much ambient light the clouds receive over the height range of the volume.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/base-lighting.jpg"/></div>
    </div>
    <p>The base lighting foldout, and the parameter's we'll be using.</p>
</div>

Since our reference has very dense, solid clouds, we'll also set the density parameter very high. For thinner clouds, you might go as low as `250`. We'll use a value of `200000`. Immediately, this will make the clouds look very different.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/density-10k.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/density-200k.jpg"/></div>
    </div>
    <p>Left: density of 10000. Right: density of 200000, which is what we will be using.</p>
</div>

Next, we'll adjust the anisotropy of the clouds, which controls the directionality of the light scattering. Typically, clouds are highly anisotropic. This is what creates the sharp silver lining you see in clouds right next to the sun in the sky, and why clouds further across the sky from the sun are darker.

We'll take a cue from nature and set the `Anisotropy` parameter to a relatively high value of `0.6`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/anisotropy-0.1.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/anisotropy-0.6.jpg"/></div>
    </div>
    <p>Left: anistropy of 0.1. Right: anisotropy of 0.6, which is what we will be using.</p>
</div>

Finally, we'll adjust the amount of ambient light the clouds receive from the sky. Generally, the bottoms of clouds receive slightly less ambient light because they face the ground. Because of this, Expanse lets you specify the ambient light strength at the bottom and the top of the clouds, and smoothly blends between them over the height of the cloud volume.

We'll set the `Ambient Strength Range` parameter to `1.0` on the lower end, and a slightly higher `1.1` on the higher end.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ambient-0.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ambient-1-1.1.jpg"/></div>
    </div>
    <p>Left: no ambient light at all. Looks pretty bad! Right: ambient range of (1, 1.1), the values we'll be using.</p>
</div>


### Multiple Scattering

These clouds are starting to look really good! However, there's still a problem. Comparing our clouds to our reference photo, **they look a bit too grey**, and their **shadows are too dark**. This becomes even more apparent when you look at the clouds at the back of the sky, opposite the sun.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/too-grey.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/intense-shadows.jpg"/></div>
    </div>
    <p>Left: the clouds at the back of the sky are too grey. Right: the self shadowing is way too intense.</p>
</div>

In short, this is because our clouds exhibit none of the characteristics of multiple scattering. When light interacts with a cloud, it bounces around many times, making it appear whiter and more diffuse than it would otherwise. In practice, this is too computationally expensive to model. Expanse uses a physically-based approximation that's convincing while remaining performant.

Open up the multiple scattering tab and you'll see a few parameters. The one we'll focus on is `Multiple Scattering Receptive Field`. This controls how strong the multiple scattering effect is. If you drag it around, you'll notice that it brightens/darkens the clouds. For our purposes, we'll set this to `1.0`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/ms-foldout.jpg"/></div>
    </div>
    <p>The multiple scattering foldout, with the receptive field parameter and the bias parameter.</p>
</div>

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ms-rf-0.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ms-rf-1.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ms-rf-5.jpg"/></div>
    </div>
    <p>Left: receptive field of 0, so no multiple scattering. Middle: receptive field of 1, what we'll use. Right: receptive field of 5, arguably too strong.</p>
</div>

Expanse also provides a few artistic overrides to the approximation. Let's up the parameter `Multiple Scattering Bias` to `0.015`. This will push a little more light into the creases of the clouds.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ms-rf-1.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ms-bias-1.5.jpg"/></div>
    </div>
    <p>Left: No bias to multiple scattering. Right: bias of 0.015 to multiple scattering. It's subtle, but it makes a big difference for far away clouds.</p>
</div>

### Self Shadowing

The last thing to notice here is that the shadows are still a bit too intense. We can fix this easily by tweaking the self-shadowing settings.

Open up the `Self Shadowing` foldout, and mess around with the `Shadow Persistence` parameter. This will make the shadows more or less intense. It does so via the multiple scattering approximation, so it remains relatively physical, but it will have no effect if you have no multiple scattering set up.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/shadow-persistence.jpg"/></div>
    </div>
    <p>The shadow persistence parameter.</p>
</div>

We'll roll with a value of `0.33`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ss-0.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ss-0.33.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/ss-1.jpg"/></div>
    </div>
    <p>Left: shadow persistence of 0. This leaves the clouds a little too formless. Middle: shadow persistence of 0.33, what we'll use. Right: shadow persistence of 1. This results in shadows that are too intense.</p>
</div>

### Validation

Now, in theory, we've finished lighting our clouds. However, it's important to check how they look under different illumination conditions. Moving the sun around and comparing to our reference, I think things look pretty good!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/daylight.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/twilight.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/moonlight.jpg"/></div>
    </div>
    <p>Our clouds under different illumination conditions.</p>
</div>

However, you usually don't have the benefit of copying down previously worked out parameters from a tutorial. Chances are if you embark on this process on your own, you'll need to go through a few cycles of tweaking, checking under illumination conditions, and tweaking again.

**We've finished lighting our clouds, and they look great!** We're pretty much done with the visual part of this tutorial---the remaining sections will be focused on interaction and optimization. Congratulations for making it this far!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://scx2.b-cdn.net/gfx/news/2019/observingclo.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/result_001.jpg"/></div>
    </div>
    <p>Left: the reference. Right: our result. Not perfect, but pretty good!</p>
</div>

## Movement

Cloud movement in Expanse is pretty simple---you can either specify a sampling offset to each noise value, which you can animate yourself, or you can directly specify a velocity, and Expanse will animate the offset for you.

Let's do this ourselves. Open up the `Movement` tab, and set the following values.
* `Use Offset`: unchecked. This specifies that we want to specify the cloud movement as a velocity.
* `Coverage Velocity`: `(0.0002, -0.0002)`.
* `Base Velocity`: `(0.00075, -0.00075)`.
* `Structure Velocity`: `(0.005, -0.005)`.
* `Detail Velocity`: `(0.01, -0.01)`.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/movement-settings.jpg"/></div>
    </div>
    <p>Set the velocity parameters in the movement tab.</p>
</div>

Hit the play button, and voila! Your clouds should be moving.
<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/clouds/1-5-0/moving_000.mp4" type="video/mp4">
    </video>
    <p>Moving clouds!</p>
</div>

## Interaction

It's crucial that our clouds interact with the rest of the rendering pipeline in a way that makes sense. For the most part, Expanse handles this automatically, but there a few things we can adjust.

Open up the `Interaction` foldout and you'll see a few settings you can play around with. 

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/clouds/1-5-0/interaction-foldout.jpg"/></div>
    </div>
    <p>The interaction dropdown.</p>
</div>

If we place a piece of geometry under the clouds and enable the `Cast Shadows` checkbox, we can see shadows show up on the geometry! We can also tweak the `Max Shadow Intensity` parameter to make the shadows more or less intense.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/shadows-0.3.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/shadows.jpg"/></div>
    </div>
    <p>Left: cloud shadows with max shadow intensity at 0.3. Right: cloud shadows with max shadow intensity at 1.</p>
</div>

You might also notice that if you crank the light pollution on the [Night Sky](editor/blocks/night_sky_block.md), the clouds can end up receiving too much light. You can fix this by adjusting the `Light Pollution Dimmer` parameter.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/lp-dim-1.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/lp-dim-0.2.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/lp-dim-0.jpg"/></div>
    </div>
    <p>Left: light pollution dimmer at 1, so clouds are receiving full light pollution. They look a bit too washed out. Middle: light pollution dimmer at 0.2. This looks pretty good. Right: light pollution dimmer at 0, so clouds receive no light pollution. This looks a bit strange.</p>
</div>

## Performance Optimization

So you've got your clouds looking how you want, but your GPU fan is whirring like a buzzsaw, you're barely hitting 5 FPS, and the corner of your desk is on fire.

Raymarching and lighting a dense volume is computationally expensive, there's no way around that. That said, Expanse allows you to make a number of different quality tradeoffs and optimizations to get yourself to that smooth 60Hz framerate.

What follows are a set of instructions for optimizing your cloudscape.

### Step 1: Texture Quality

Expanse allows you to select the texture quality of the noises your clouds use. Navigate to the `Noise Editor` dropdown and you'll see the `Texture Quality Field`. Chances are, you will not be able to use `Ultra` or `Ripping Through The Metaverse` quality. Experiment around with the different texture qualities and track your framerate to see which is usable. They can all look quite good, even "Potato".

On my RTX 2080 Ti, for the clouds we just created, I'm going to select `High`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/botato.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/high.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/rtm.jpg"/></div>
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

To mitigate these noisy artifacts, you can click the `Use Temporal Denoising` checkbox, or you can alternatively enable Unity's TAA (or use both!). This will only get you so far though.

### Step 3: LOD Distances

Expanse renders clouds at 3 different levels of detail. You can decide where these LOD distances are with the `LOD Distances` parameter in the `Quality` foldout. Set this as aggressively as possible for maximum performance.

I'll set them to `15000` and `30000`---just far enough that I can't notice the LOD transitions.

### Step 4: Thresholds

Expanse lets you decide when density samples transmittance values should be considered zero---setting this threshold high will allow more lighting calculations to be skipped and will improve performance. If you set these values too high, you'll see artifacts.

In my case, I'll set the `Media Zero Threshold` to `1e-5`, and the `Transmittance Zero Threshold` to `0.001`. This is on the upper end of values you can choose before you'll see noticeable issues.

Optimizing these values is particularly crucial for cloudscapes that are very dense.

### Step 5: Reprojection Frames

With the settings we've optimized so far, my RTX 2080 Ti renders the clouds in around 3.5 ms. This is easily enough to hit 60 FPS if we're just rendering clouds, but if we want our game to include all sorts of other expensive effects, we'll need to optimize performance even more.

This is where reprojection comes in---it allows us to render clouds across multiple frames, meaning we can still render clouds at full resolution, but do less work per individual frame.

The `Reprojection Frames` parameter in the `Quality` dropdown controls this. If I use the maximum reprojection amount (`4`, which really actually means 16 frames), the render time decreases to 0.75 ms. If I decrease the texture quality to "potato", the render time further decreases to 0.55 ms.

One thing you may notice is some nasty flickering pixel-looking artifacts. You can do three things to fix these:
* Try increasing your sample counts
* In the `Self Shadowing` foldout, set `Shadow Sample Jitter` to 0
* Enable `Use Temporal Denoising` in the `Quality` dropdown

Reprojection isn't perfect---when used with TAA, it can make your clouds look a bit blurry. It's also possible to see artifacts if your clouds are moving very fast. The best thing to do is only use as many reprojection frames as you need. If you can get away with 2 and still hit your performance requirements, do it.

### Step 6 (Optional): Resolution
If you've tried all of these things and still cannot get your framerate to where you want it to be, you can tweak the [global cloud subresolution parameter in your `Quality Settings`](/editor/blocks/quality_settings_block?id=cloud-subresolution). However, **this should be your last resort**, as it will really detract from your visual result. The exception to this is if you decide to render in 4K, at which point rendering clouds at 1080p (half resolution) is recommended.

Here's a comparison between our optimized clouds and our non-optimized clouds. The left image takes 3.5 ms to render. The right image takes only 0.75 ms!
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/unoptimized.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/clouds/1-5-0/optimized.jpg"/></div>
    </div>
    <p>Comparison of optimized vs. unoptimized result. Left: unoptimized clouds, rendering at 3.5 ms per frame. Right: optimized clouds, rendering in just 0.75 ms!</p>
</div>

## Wrapup

Well, I hope you've enjoyed learning how to craft volumetric cloudscapes in Expanse. If you ever feel stuck, please feel free to post to the [Expanse Discord](https://discord.gg/F3VQ2vJy9p)!

This is really just the beginning of what it is possible to create with Expanse. For more info on all the parameters you can play around with, check out the docs for the [`Procedural Cloud Volume`](/editor/blocks/procedural_cloud_volume_block).

Below are some of the other results you can get with Expanse's clouds---for inspiration!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/1-7-0/clouds_1.jpg"/></div>
        <div class="img-col"><img src="img/1-7-0/overhead.jpg"/></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/1-7-0/big_cloud.jpg"/></div>
        <div class="img-col"><img src="img/1-7-0/moon.jpg"/></div>
    </div>
</div>