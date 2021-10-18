# Quick Start

It's possible to get Expanse into your project in fewer than 15 minutes! Follow this guide for instructions.

Hate reading? Check out the [video companion](https://youtu.be/3H9KYBnmLo0) for this tutorial!

## Licensing

The first thing you'll have to do is [acquire a license for Expanse from the asset store](https://assetstore.unity.com/packages/slug/192456).

If you are an **individual creator**, you can purchase a single license. If you are a **studio**, you will need to acquire a license for everyone who is going to use Expanse. See the [Unity EULA FAQ's "extension assets" section](https://assetstore.unity.com/browse/eula-faq) for details.

## Importing

Once you have purchased Expanse, you can download it from the asset store, and import it into your project. It should import to the folder `Assets/Expanse`.

**Your project must be using HDRP version 2020.1.17 or higher.** Expanse may work with older versions of HDRP, but it hasn't been tested with them. **Expanse does not support the Universal or Builtin Render Pipelines.**

## Preparing Your Project

There's not much to do in this department---in fact, there's really only two things. 

**Delete any existing Sky and Fog Volumes you have in your scene.** Expanse is backed by a Sky and Fog Volume, so you have to make sure that there are no others in the scene that it can conflict with.

**Delete any sun or moon directional lights that you have set up.** Expanse will add new ones for you.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:40%" src="img/quickstart/delete_sky_fog.jpg"/></div>
    </div>
    <p>Delete the directional light and sky and fog volume!</p>
</div>

## Creating A Sky

The easiest way to start using Expanse is to create a full sky using the Hierarchy's create menu. This will load in a prefab of `GameObjects` that lay out all the components necessary for a basic sky, with a sun, moon, and clouds.

As a note: we'll be using the default HDRP project from `2020.1.17.f1` in this tutorial, but any project, **provided it's an HDRP project in version 2020.1.17 or higher**, should work.

To begin, right click on the Hierarchy tab, and select `Expanse => Full Skies => Creative Sky`. There are other options to select from here---you can try them if you like. The Creative Sky is the easiest to control, and still provides a wide range of authoring capabilities, so it is what we'll use for this tutorial.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/quickstart/1-5-0/create-menu.jpg"/></div>
    </div>
    <p>Create a Creative Sky by right-clicking the Hierarchy tab.</p>
</div>

You should now see a sky in your scene, but it will probably look either too bright, or too dark! We'll fix that in the next section.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/too-dark.jpg"/></div>
    </div>
    <p>The sky is there... but it's a bit too dark.</p>
</div>

If you don't see Expanse's sky load in, you can try the following fixes:
* **Disabling the default visual environment.** If your HDRP asset has a default visual environment, you may not be able to see Expanse's sky. To disable the default visual environment, go to `Edit->Project Settings->HDRP Default Settings` and uncheck the `Visual Environment` checkbox.
* **Adjusting your camera's exposure settings.** If you see a black screen, your camera's exposure might not be set correctly. You can adjust it by adding an exposure override to your global post-processing volume. Typically an auto-exposure with `7` as the minimum and `12.5` as the maximum is a good starting point. This is a bit of a spoiler, since it's what we're going to do in the next section :)
* **Hitting the play button.** As a last ditch effort, this can sometimes be necessary to build some of Expanse's data structures.

If you try these fixes and still can't see anything, feel free to post in the [Expanse Discord](https://discord.gg/F3VQ2vJy9p).


## Adjusting The Camera
Chances are something looks wrong about your scene---it's too dark, or maybe too bright! This is because your camera isn't set up to handle the physical, high-dynamic-range light units that Expanse uses.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/create-exposure.jpg"/></div>
    </div>
    <p>Select your post-process volume, and add an exposure override if there isn't one there already.</p>
</div>

To fix this, go to your post-processing volume, and add an Exposure override (if you don't see one there already). Adjust it to use Automatic exposure, with a min of `7` and a max of `12.5`. Now everything should look right!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/adjust-exposure.jpg"/></div>
    </div>
    <p>Adjust the exposure settings, and voila, you've got a visible sky!</p>
</div>

Another issue you will encounter is that Expanse computes aerial perspective and fog only up until the camera's far clipping plane. You will want to adjust your camera's far clip plane to something like 100000 (100k) so that aerial perspective and fog are computed correctly for far away clouds.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:40%" src="img/quickstart/camera.jpg"/></div>
    </div>
    <p>Adjust your camera's far clip plane to be 100000 or higher, so that clouds have atmospheric scattering in front of them.</p>
</div>

## Selecting a Cloud Preset

We've got a sky, and it looks roughly correct, but we don't have any clouds! This is because we have yet to select a **cloud preset**.

Expanse provides a number of different presets to use as starting points for building your volumetric cloudscape. Since, in general, volumetric clouds are pretty hard to model, it can be easiest to start with a preset you like, and then tweak it until it looks how you want it to. 

Open up the prefab foldout and select the `GameObject` called `Volumetric Clouds`. Here you'll find a [`Creative Cloud Volume`](/editor/creative/creative_cloud_volume_block) component. This is an intuitive wrapper around the more complicated [procedural cloud volume](/editor/blocks/procedural_cloud_volume_block), which exposes all the cloud rendering parameters that you can adjust.

Expanse organizes its functionality into modular blocks that you can distribute across any number of game objects. This is useful for keeping things organized, cleaning up the UI, and for ensuring that you only access features that you need. For more detailed info on how blocks work, you can check out the [blocks page](/editor/blocks/blocks).

Open the preset browser tab. You should see an array of presets that you can select from, all with images demonstrating what they look like.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/select-preset.jpg"/></div>
    </div>
    <p>Select a preset by opening up the preset browser and clicking a preset you like.</p>
</div>

Click on whatever preset you like, and it should load! Here's the preset "Gatos":

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/gatos.jpg"/></div>
    </div>
    <p>The volumetric cloud preset "Gatos".</p>
</div>

And here's the preset, "Brewing":

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/brewing.jpg"/></div>
    </div>
    <p>The volumetric cloud preset "Brewing".</p>
</div>

For fun, let's animate the clouds to move very fast---kind of like you would see in a timelapse. Navigate to the `Modeling` tab in the creative cloud volume component, and adjust the `Wind` parameter to `(10, 10)`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:80%" src="img/quickstart/1-5-0/wind.jpg"/></div>
    </div>
    <p>The wind parameter of the creative cloud volume.</p>
</div>

**This might look pretty weird in edit mode---if you're using one of the "Optimized" presets, you'll see lots of Moire-esque artifacts. These will go away when you enter play mode.**

So, what are you waiting for? Click the play button, and watch your clouds smoothly roll across the sky!

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/1-5-0/moving_000.mp4" type="video/mp4">
    </video>
    <p>Moving clouds!</p>
</div>


## Adjusting the Time of Day
One of the most satisfying things to do is to move the sun and moon around. As game engineers we're used to playing god, but there is an especially cool feeling you get when you command your own sunset.

To move the sun and moon, we can use two strategies: we can directly set their directions, or we can adjust Expanse's time of day. We'll opt for the latter method, since it's the default one set up by Expanse's sky prefabs.

Open up the sky `GameObject` foldout. You should see a `GameObject` underneath it called `Time Of Day Controller`. Open it up in the inspector view.

You'll see a number of different parameters to play around with here. We're going to focus on two parameters:

* The "minute" parameter, which we can drag to adjust the time of day at a reasonable speed---it will also progress the hour as we drag it above 60/below 0.
* The "sky offset" parameter, which we can use to move the sun around the zenith, i.e., "left to right".

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/time-of-day-object.jpg"/></div>
    </div>
    <p>The time of day controls are on the "Time Of Day Controller" GameObject, and the "minute" and "sky offset" parameters.</p>
</div>

If we adjust these parameters, we can see the sun and moon move around, and watch the cloud illumination respond in realtime!

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/1-5-0/tod.mp4" type="video/mp4">
    </video>
    <p>Adjusting the time of day!</p>
</div>

If you do this in edit mode, you'll notice that when you adjust the sun's position, the cloud lighting takes a second to catch up. This is because Expanse uses temporal reprojection to render clouds in realtime. In game mode, you won't see this problem.

If you put the sun over the horizon, and disable "Use Time Of Day" on the "Sun" GameObject, then move the sun around via the "Direction" control, you'll see that the moon's illumination changes depending on the sun direction. This means that Expanse accurately calculates real-world moon phases!

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/1-5-0/moon-illuminate.mp4" type="video/mp4">
    </video>
    <p>Changing the sun's position illuminates the moon in different ways.</p>
</div>

For more info on celestial bodies, see the [celestial body documentation page](/editor/blocks/celestial_body_block).

## Adjusting the Atmosphere
So, we can select a cloud preset, and we can move the sun and moon around. But what about things like adding pollution, and rolling in fog? Doing both of these things is as simple as adjusting a slider.

Open up the prefab foldout again, and click on the `GameObject` named `Atmosphere` to open it up in the inspector. You should see a [`Creative Atmosphere`](/editor/creative/creative_atmosphere_block) component, with controls that allow you to tweak the atmosphere's color and density.

You can play around with these parameters to get different looking skies. For instance, to get a more dramatic and stylized sunset look, we can adjust the "Sunset Color" to be a more saturated orange, and the daytime color to be a deep lavender.

> These controls are _fully physical_, even though their names might make you think otherwise. They adjust the scattering and extinction coefficients of the atmosphere participating medium. For more information, see the [atmosphere layer documentation page](/editor/blocks/atmosphere_layer_block).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/1-5-0/stylized.jpg"/></div>
    </div>
    <p>We can adjust the atmosphere sunset and daytime colors to get a more stylized look, without abandoning Expanse's physically-based atmosphere model.</p>
</div>

We can also easily adjust the amount of fog/smog in the atmosphere. Click on the `Fog` `GameObject`, and adjust the density parameter to see the effect of increasing and reducing the amount of fog.

> Under the hood, the fog is also implemented as an [atmosphere layer](/editor/blocks/atmosphere_layer_block). However, I've found that it's more intuitive to separate it out into its own game object.

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/1-5-0/fog.mp4" type="video/mp4">
    </video>
    <p>Adjusting the density of the fog.</p>
</div>

There's plenty of other parameters for you to mess around with. For more info on how to use atmosphere layers in a more advanced way, see the [atmosphere layer documentation page](/editor/blocks/atmosphere_layer_block).

## Wrapup

Well, that's it for this setup guide! Continue on to the following sections to learn about [building the Earth's atmosphere from scratch](quickstart/earth-atmo.md), [modeling a convincing volumetric cloudscape](quickstart/clouds.md), and [blanketing your scene with moody volumetric fog](quickstart/fog.md).

As always, if you have any questions, please feel free to post them to the [Expanse Discord](https://discord.gg/F3VQ2vJy9p).