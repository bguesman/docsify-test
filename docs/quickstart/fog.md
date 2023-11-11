# Tutorial: Using Volumetric Fog

Fog has been a computer graphics staple since nearly the beginning of the artform. What originated as a clever way of hiding the camera's far clipping plane has since evolved into a compelling way to add instant atmosphere and mood to a 3D scene.

In this tutorial, we'll walk through how to create all sorts of different types of fog with Expanse.

**As a note**: I recommended reading through the [Modeling The Earth's Atmosphere](/quickstart/earth-atmo) page before doing this tutorial. That will give you important foundational knowledge about Expanse and volumetric rendering as a whole, and will help you decipher some of the terminology that follows.

## Problems With Fog

In Expanse, fog is treated like any other layer of the atmosphere---you can add fog to your scene by creating an `Atmosphere Layer` component. However, there are some things about fog that make it less amenable to Expanse's usual technique of rendering atmosphere layers. Namely:
* **Fog is dense.** Because of this, artifacts in Expanse's usual strategy are more likely to be visible.
* **Volumetric shadows are important.** To have sharp volumetric shadows---"light shafts", "god/crepuscular rays"---fog needs to be computed per-pixel.

As such, Expanse exposes two different density distributions that are optimized for fog and rendered per-pixel: `Screenspace Uniform` and `Screenspace Height Fog`. These distributions also support volumetric shadowing---either using the light's shadowmap, or using the depth buffer. The implementation of these layers is inspired by [this classic strategy](https://developer.nvidia.com/gpugems/gpugems3/part-ii-light-and-shadows/chapter-13-volumetric-light-scattering-post-process), used in games like Mirror's Edge: Catalyst and the modern iterations of the Star Wars: Battlefront franchise. It is, however, more physically-based than the implementation described in the linked GPU Gems article.

## Setup

To make things easier, we're gonna start with one of Expanse's **default full skies**. These already have a sun, moon, stars, and some other things set up---this will reduce the irrelevant boilerplate setup we have to do.

Open up your Unity project, right click on the GameObject hierarchy, and select `Expanse => Full Skies => Advanced Sky`. This will create a prefab `GameObject` called `Expanse Volumetric Cloud Sky` in our hierarchy. We'll also unpack this prefab by right-clicking it in the hierarchy and selecting `Prefab->Unpack Completely`. For details on importing Expanse into your project, and rendering your first prefab sky, refer to the [quickstart guide](/quickstart/quickstart).

With that taken care of, we're going to delete the `Fog` GameObject. This is, after all, what we'll be modeling by hand in this tutorial.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/delete-fog.jpg"/></div>
    </div>
    <p>Delete the fog GameObject, since we'll be recreating it ourselves.</p>
</div>

Alright, let's roll in some fog!

## A Simple, Uniform, Isotropic Fog

We're going to start off by modeling the simplest possible fog. It will be **uniformly distributed** within a volume, and it will be **isotropic**, meaning that it scatters light evenly in all directions. This sort of fog works well as a basic uniform haze.

We'll start by adding a new `Atmosphere Layer` to our scene. To do this, we'll right click the hierarchy view and select `Expanse => Advanced => Atmosphere Layer`. We'll also rename it to `Fog`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/fog/1-5-0/add-layer.jpg"/></div>
    </div>
    <p>Add a new Atmosphere Layer to our scene.</p>
</div>

Now, for the task of actually choosing the appropriate parameter values.

We want our fog to appear kind of smoky, or hazy, so we will use scattering coefficients that are slightly lower than the extinction coefficients. This will make the fog absorb slightly more light than it reflects, making it dimmer and smokier. In particular, we'll set the extinction coefficients to `(4.4e-6, 4.4e-6, 4.4e-6)`, and the scattering coefficients to `(4e-6, 4e-6, 4e-6)`. We'll also use the isotropic phase function, so no scattering direction is preferred over any other.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:75%" src="img/quickstart/fog/1-5-0/isotropic.jpg"/></div>
    </div>
    <p>Set the scattering and extinction coefficients to (4e-6, 4e-6, 4e-6) and (4.4e-6, 4.4e-6, 4.4e-6), respectively. Also, use the isotropic phase function.</p>
</div>

Now for the interesting stuff. Select the density distribution `Screenspace Uniform`. You'll notice that you have two parameters to control it: **density** and **radius**. **Density** is exactly what it sounds like---the density of the fog within the uniform volume. **Radius** defines how far the fog volume extends out away from the player. Within the sphere this radius defines, the fog is constant density. Outside this sphere, it is zero. Hence the name "uniform".

If you play around with these parameters, you'll notice that you can get quite a wide variety of different fog conditions. Since visible fog and haze is usually a fairly local phenomenon, let's set the radius to something like 10000 meters. You can now play with the density and see how thicker and thinner fog settings look.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/iso-0.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/iso-5.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/iso-75.jpg"/></div>
    </div>
    <p>Some different density values for the fog we've just authored, with radius set at 10000 for all examples. Left: density zero, so no fog. Middle: density 5. Right: density 75. With this simple setup, we can model everything from light haze to thick smog!</p>
</div>

You may have also noticed two other checkboxes pop up when you changed the density distribution: `Geometry Shadows` and `Cloud Shadows`. These allow, respectively, the scene geometry and the clouds to cast approximate volumetric shadows on the fog.

Let's set the density to something somewhat high, like 20, and turn on `Geometry Shadows`. Adjusting the `Max Geometry Occlusion` parameter that pops up will change the shadowing power that the geometry has on the fog. We'll set it to `0.9`, so most light is blocked.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/quickstart/fog/1-5-0/geo-shadows.jpg"/></div>
    </div>
    <p>Turn on geometry shadows and set the max occlusion parameter to 0.9.</p>
</div>

Now, when we look toward the sun, we see nice light shafts peeking through our scene geometry!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/iso-no-shadow.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/iso-shadow.jpg"/></div>
    </div>
    <p>Left: without volumetric shadows. Right: with volumetric shadows.</p>
</div>

You can play around more with the `Max Occlusion` value to control how intense the light shafts are. Also, the effect becomes even more noticeable if you switch to the `Mie` phase function.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/iso-shadow.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/mie-shadow.jpg"/></div>
    </div>
    <p>Left: shadows with the isotropic phase function. Right: shadows with the Mie phase function, with an anisotropy value of 0.25.</p>
</div>

Well, there you have it: a simple volumetric fog layer!

## Height Fog

Next, let's take a look at tackling a very common fog effect: height fog.

Height fog is fog that is very dense close to the ground, but decays off to nothingness as it reaches toward the sky. It's a popular fog to use when creating cities, as it can accentuate the height of towering skyscrapers.

Let's change the phase function of our simple uniform layer to the `Mie` phase function, but with a lower anisotropy value, like `0.25`. This will look a little bit better than the straight isotropic phase function.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/quickstart/fog/1-5-0/height-fog-mie.jpg"/></div>
    </div>
    <p>Select the mie phase function and adjust the anisotropy to 0.25.</p>
</div>

Now let's select the `Screenspace Height Fog` density distribution. There's a few parameters we have exposed here:
1. **Thickness**: how high up the height fog extends from the ground. For my scene, around 125 meters looks good.
2. **Radius**: how far out the height fog extends around the player. You can play around with this, I'm going to set it to a pretty big number, 25000, so that the height fog extends out more or less as far as we can see.

Let's also adjust the density to something pretty high, that way the fog is really visible. So, something like 200.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/height-fog-geometry.jpg"/></div>
    </div>
    <p>Select the Screenspace Height Fog density distribution, set the height to 125 meters, the radius to 25000 meters, and the density to 200.</p>
</div>

Feel free to play around with the parameters to get the effect you want. Here's a few examples.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/height-fog-taller.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/height-fog-medium.jpg"/></div>
    </div>
    <p>A few different thickness and density tweaks to the height fog we just created.</p>
</div>

Also, make sure to see what your fog looks like under different illumination conditions.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/height-fog-medium.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/height-fog-evening.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/height-fog-night.jpg"/></div>
    </div>
    <p>The same height fog layer, at different times of day.</p>
</div>

## Rain Fog

By "rain fog", what we mean here is basically clouds that are so low to the ground that they appear as visible mist in front of objects. This sort of fog is a little more complex to model convincingly because, often, it has noticeable shape and form---like tendrils and swirls---as opposed to being a sort of uniform haze.

The ideal solution here would be to use an actual volumetric cloud layer, very low to the ground. For far away clouds, this actually works pretty well.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/cloud_fog.jpg"/></div>
    </div>
    <p>Some far away fog, rendered using a volumetric cloud layer. This looks pretty nice.</p>
</div>

However, once inside the cloud layer, the reprojection strategy that Expanse uses starts to break down, and artifacts at the edges of the scene geometry are visible. You can solve this problem by turning off reprojection, but this can cause a performance hit.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/reprojection_artifacts.jpg"/></div>
    </div>
    <p>Artifacts introduced by reprojection, visible at the edges of objects.</p>
</div>

This is an active area of improvement that we are working on. At the moment performance can be improved by rendering clouds at [half or quarter resolution](/editor/blocks/global_settings?id=cloud-subresolution), and also by [disabling cloud self-shadowing](/editor/blocks/procedural_cloud_volume_block?id=self-shadowing). All of this is to say that, while this is possible with Expanse, it can take some effort to get it to work right and be performant. So instead, we'll explore a simpler strategy for modeling rain fog. We'll use this image of London on a really foggy day to base our solution on.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://media.pri.org/s3fs-public/styles/story_main/public/story/images/Reuters%20London%20fog.jpg?itok=0MLALeR2"/></div>
    </div>
    <p>London on a foggy day. Source <a href="https://media.pri.org/s3fs-public/styles/story_main/public/story/images/Reuters%20London%20fog.jpg?itok=0MLALeR2">https://media.pri.org/s3fs-public/styles/story_main/public/story/images/Reuters%20London%20fog.jpg?itok=0MLALeR2</a>.</p>
</div>

To create this effect, we'll make some tweaks to the height fog layer we just set up in the last section. 

For one, because clouds are a more local phenomenon, we'll decrease the radius to something like `10000` meters. We'll also up the thickness to something similar to that of cumulus clouds---we'll go with `3000` meters. The density is largely up to how rainy you want it to look; our reference image is pretty intensely foggy, so we'll pick a value of `1500`. Finally, we'll pull back the anisotropy to something like `0.05`, so that our fog's illumination is pretty even.

Here's the result we get with those values.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/rain-fog-1.jpg"/></div>
    </div>
    <p>First attempt at rain fog.</p>
</div>

Not too bad, but our reference is noticeably bluer. This is because most of the light the fog is receiving is ambient light from the sky, which is not self-shadowed. The physically-correct thing would be to simulate many bounces of "multiple scattered" light, but we can't afford to do this in realtime. In the future, Expanse's fog may use the same multiple scattering approximation model that the clouds do, but at this time that is not the case.

Instead, to "de-blue" the result, we can adjust the scattering coefficients to be ever so slightly orange and brighter. The gives the following result:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/de-blued.jpg"/></div>
    </div>
    <p>Second attempt at rain fog, with scattering coefficients tweaked to make the ambient light less blue.</p>
</div>

That's more like it!

Things are looking pretty good, but, looking back at our reference, we're still missing one really noticeable visible phenomenon: objects in the distance (the clock tower, for instance), appear blurred. This is because light is scattered around diffusely through the fog before it reaches our eyes. The further away the object, the more opportunities the light has to be scattered away.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://media.pri.org/s3fs-public/styles/story_main/public/story/images/Reuters%20London%20fog.jpg?itok=0MLALeR2"/></div>
    </div>
    <p>Notice how blurry the clock tower looks in the background.</p>
</div>

Out of the box, Expanse does not model this, but I've developed an **Expanse Extension** to blur the framebuffer based on the fog transmittance as a post-processing operation. To use it, you can download it from this [github link](https://github.com/bguesman/expanse-fog-blur) and drag and drop it into your project's `Asset` folder.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:80%" src="img/quickstart/fog/1-5-0/add-custom-pass-volume.jpg"/></div>
        <div class="img-col"><img style="width:80%" src="img/quickstart/fog/1-5-0/select-blur-pass.jpg"/></div>
    </div>
    <p>Setting up the fog blur extension.</p>
</div>

You can then add a custom pass volume component to the Fog GameObject we created, and select the `Expanse Fog Blur Custom Pass`. This will then expose 2 parameters:
* Blur Amount: How much to blur the framebuffer based on the fog transmittance.
* Optimization Amount: How much to optimize the blur operation, sacrificing quality for performance. `1` is most performant, `0` theoretically looks the best.

> There are currently some issues with the fog blur extension rendering in the scene view. You may want to disable your custom pass volume when navigating around the scene to avoid visual artifacts.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/fog/1-5-0/blur-options.jpg"/></div>
    </div>
    <p>The options for customizing the fog blur.</p>
</div>

The result is pretty nice! Far away objects are blurred by the fog in front of them. The result looks much more like our reference!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/de-blued.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/blurred.jpg"/></div>
    </div>
    <p>Left: no fog blur. Right: with fog blur.</p>
</div>

## Wrapup

We've seen three examples of fog that you can model with Expanse, but this is only scratching the surface of what's possible. You could imagine using the techniques we learned here to model something like Blade Runner's dark impenetrable smog, or the yellow sulfuric gases blanketing the surface of Venus. Ultimately, the sky's the limit (no pun intended)!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/point-lights.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/venus-2.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-5-0/crepuscular.jpg"/></div>
    </div>
    <p>A few more examples of what's possible with Expanse's volumetric fog.</p>
</div>

Now that you've learned all about Expanse's volumetric fog, [continue on to learn how to model beautiful, evocative cloudscapes.](/quickstart/clouds)