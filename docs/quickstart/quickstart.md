# Quick Start

It's possible to get Expanse into your project in fewer than 15 minutes! Follow this guide for instructions.

## Licensing

The first thing you'll have to do is acquire a license for Expanse from the asset store.

If you are an **individual creator**, you can purchase an [individual license TODO LINK](). If you are a **studio**, you will need to acquire the more expensive [studio license TODO LINK](). This is standard practice for advanced Unity assets (see [NatureRenderer's Policy](https://assetstore.unity.com/packages/tools/terrain/nature-renderer-personal-license-153552?aid=1011lK8Q&utm_source=&utm_medium=&utm_campaign=&pubref=)).

## Importing

Once you have purchased Expanse, you can download it from the asset store, and import it into your project. It should import to the folder `Assets/Expanse`.

**Your project must be using HDRP version 2019.4 or higher.** Expanse may work with older versions of HDRP, but it hasn't been tested with them. **Expanse does not support the Universal or Builtin Render Pipelines.**

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

## Selecting a Prefab

The easiest way to start using Expanse is to drag and drop in one of the prefabs. These are prebuilt `GameObjects` that lay out all the components necessary for a basic sky, with a sun, moon, and clouds.

As a note: we'll be using the default HDRP project from `2020.1.17.f1` in this tutorial, but any project, **provided it's an HDRP project in version 2019.4 or higher**, should work.

To begin, drag a prefab from the folder `Expanse/prefabs/Full Skies` into your scene. The prefabs are named by the clouds they use, since this is the biggest consideration for performance. In this guide, we'll be using the `Expanse Volumetric Cloud Sky` prefab, but you could easily follow along with any of the other prefabs.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/quickstart/drag_and_drop_edited.jpg"/></div>
    </div>
    <p>Drag the prefab into your scene</p>
</div>

You should now see a sky in your scene, but it will probably look way too bright! We'll fix that in the next section.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/post_process.jpg"/></div>
    </div>
    <p>The sky is there... but it's way too bright!</p>
</div>

If you don't see Expanse's sky load in, you can try the following fixes:
* **Disabling the default visual environment.** If your HDRP asset has a default visual environment, you may not be able to see Expanse's sky. To disable the default visual environment, go to `Edit->Project Settings->HDRP Default Settings` and uncheck the `Visual Environment` checkbox.
* **Adjusting your camera's exposure settings.** If you see a black screen, your camera's exposure might not be set correctly. You can adjust it by adding an exposure override to your global post-processing volume. Typically an auto-exposure with `7` as the minimum and `12` as the maximum is a good starting point.
* **Hitting the play button.** As a last ditch effort, this can sometimes be necessary to build some of Expanse's data structures.

If you try these fixes and still can't see anything, feel free to post in the [Expanse Unity Forum (link coming soon)]().

## Adjusting The Camera
Chances are something looks wrong about your scene---it's too bright! This is because your camera isn't set up to handle the super bright, physical light units that Expanse uses.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/post_process_edit.jpg"/></div>
    </div>
    <p>Select your post-process volume, and add an exposure override if there isn't one there already.</p>
</div>

To fix this, go to your post-processing volume, and add an Exposure override (if you don't see one there already). Adjust it to use Automatic exposure, with a min of `7` and a max of `12`. Now everything should look right!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/post_process_correct_edit.jpg"/></div>
    </div>
    <p>Adjust the exposure settings, and voila, you've got a visible sky!</p>
</div>

Another issue you will encounter is that Expanse computes aerial perspective only up until the camera's far clipping plane. You will want to adjust your camera's far clip plane to something like 30000 so that aerial perspective is computed correctly for clouds.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:40%" src="img/quickstart/camera.jpg"/></div>
    </div>
    <p>Adjust your camera's far clip plane to be 30000 or higher, so that clouds have atmospheric scattering in front of them.</p>
</div>

## Moving the Sun and Moon
Great, so now we've got our prefab loaded in, and we can start to mess around with some stuff! Probably the most satisfying thing to do first is to move the sun and moon around. As game engineers we're used to playing god, but there is an especially cool feeling you get when you command your own sunset.

To move the sun and moon, open up the prefab foldout. You should see a `GameObject` underneath it called `Sun And Moon`. Open it up in the inspector view.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/hierarchy.jpg"/></div>
    </div>
    <p>The sun and moon controls are components on the Sun And Moon GameObject, in the Expanse Volumetric Cloud Sky prefab.</p>
</div>

You'll see two components in the inspector view---two "Celestial Body Blocks"---one for the sun and one for the moon. Expanse organizes its functionality into modular blocks that you can distribute across any number of game objects. This is useful for keeping things organized, cleaning up the UI, and for ensuring that you only access features that you need. For more detailed info on how blocks work, you can check out the [blocks page](/editor/blocks/blocks).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/sun_moon_object_edit.jpg"/></div>
    </div>
    <p>The inspector view of the celestial body Game Object.</p>
</div>

We're going to focus on the direction parameter. This specifies the celestial body's light direction in degrees, and as such also determines its position in the sky. Adjusting the `x` parameter for the sun, as in the following gif, will move it up and down over the horizon. Adjusting the `y` parameter will rotate it about the vertical axis.

If you do this in edit mode, you'll notice that when you adjust the sun's position, the cloud lighting takes a second to catch up. This is because Expanse uses temporal reprojection to render clouds in realtime. In game mode, you won't see this problem.

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/sun-movement.mp4" type="video/mp4">
    </video>
    <p>Sun movement!</p>
</div>

You can also put the sun over the horizon and do the same thing with the moon. Notice that when you move the sun around at night, it changes the moon's illumination!

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/moon-illuminate.mp4" type="video/mp4">
    </video>
    <p>Changing the sun's position illuminates the moon in different ways.</p>
</div>

For more info on celestial bodies, see the [celestial body block documentation page](/editor/blocks/celestial_body_block).

## Adjusting the Atmosphere
So, we can move the sun and moon around, and see how that changes the color of the sky. But what about things like adding pollution, and rolling in fog? Doing both of these things is as simple as adjusting a slider.

Open up the prefab foldout again, and click on the `GameObject` named `Earth Atmosphere` to open it up in the inspector. You should see four `Atmosphere Layer Block` components. Without going into too much detail, each of these models a particular volume of gas in the Earth's atmosphere. You'll notice that each layer has a `Name` field. This is used in debug printouts, but is also handy for keeping track of which layer is which. 

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/atmo_edit.jpg"/></div>
    </div>
    <p>The components on the atmosphere game object. Each component represents a layer of the atmosphere, and has a name field to identify it.</p>
</div>

The layers we'll be messing around with are the ones named `Aerosols` and `Rain Fog`.

**Aerosols** are pieces of particulate matter, like smoke and smog, that float around in the atmosphere. If we adjust the density of the layer named `Aerosols`, we can see that the sky gets hazier. It gets clearer when we bring the density down.

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/aerosols.mp4" type="video/mp4">
    </video>
    <p>Adjusting the density of the aerosol layer.</p>
</div>

The **rain fog** layer is meant to model the look of being inside of a cloud. If we increase its density parameter, we see that gradually more and more fog starts to roll in, until the sky is completely gray. Notice that, when the fog density is very high, no shadows are cast. If you've ever been in Muir Woods on a very foggy day, you'll certainly recognize this phenomenon.

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/rain_fog.mp4" type="video/mp4">
    </video>
    <p>Adjusting the density of the rain fog layer.</p>
</div>

There's plenty of other parameters for you to mess around with. For more info on atmosphere layers, see the [atmosphere layer block documentation page](/editor/blocks/atmosphere_layer_block).

## Selecting a Cloud Preset

Expanse provides a number of different presets to use as starting points for building your volumetric cloudscape. Since, in general, volumetric clouds are pretty hard to model, it can be easiest to start with a preset you like, and then tweak it until it looks how you want it to. Visit the [procedural cloud volume block documentation page](/editor/blocks/procedural_cloud_volume_block) for a deeper dive into what each parameter does.

Open up the prefab foldout and select the `GameObject` called `Volumetric Clouds`. Here you'll find a `Procedural Cloud Volume Block` component.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/cloud_volume_block.jpg"/></div>
    </div>
    <p>The volumetric cloud preset "Across The Prairie".</p>
</div>

Click the preset button---the little sliders in the upper right hand corner of the component---and select any of the presets you are interested in. You'll notice a null reference error that pops up. This is because you have to re-drag the sky and fog volume onto the component when you select a new preset, like so.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/drag_volume.jpg"/></div>
    </div>
    <p>When you select a new preset, make sure you drag the sky and fog volume back into its slot on the component.</p>
</div>

Here's the preset "Across the Prairie".

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/across_prairie.jpg"/></div>
    </div>
    <p>The volumetric cloud preset "Across The Prairie".</p>
</div>

And here's "Jade".

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/jade.jpg"/></div>
    </div>
    <p>The volumetric cloud preset "Jade".</p>
</div>

For fun, let's animate the clouds to be very fast---kind of like you would see in a timelapse. Navigate to the `Movement` tab in the cloud volume component, and adjust the `Base Velocity` parameter to `(0.01, -0.01)`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/base_velocity.jpg"/></div>
    </div>
    <p>The base velocity parameter of the procedural cloud volume.</p>
</div>

**This will look pretty weird in edit mode---you'll see lots of Moire-esque artifacts. These go away when you enter play mode.**

So, what are you waiting for? Click the play button, and watch your clouds smoothly roll across the sky!

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/quickstart/cloud_move.mp4" type="video/mp4">
    </video>
    <p>Moving clouds!</p>
</div>

## Wrapup

Well, that's it for this setup guide! Continue on to the following sections to learn about [building the Earth's atmosphere from scratch](quickstart/earth-atmo.md), [modeling a convincing volumetric cloudscape](quickstart/clouds.md), and [blanketing your scene with moody volumetric fog](quickstart/fog.md).

As always, if you have any questions, please feel free to post them to the [Expanse Unity Forums (link coming soon)]().