# Tutorial: Using Volumetric Fog

Fog has been a computer graphics staple since nearly the beginning of the artform. What originated as a clever way of hiding the camera's far clipping plane has since evolved into a compelling way to add instant atmosphere and mood to a 3D scene.

In this tutorial, we'll walk through how to create all sorts of different types of fog with Expanse.

**As a note**: I recommended reading through the [Modeling The Earth's Atmosphere](/quickstart/earth-atmo) page before doing this tutorial. That will give you important foundational knowledge about Expanse and volumetric rendering as a whole, and will help you decipher some of the terminology that follows.

## Problems With Fog

In Expanse, fog is treated like any other layer of the atmosphere---you can add fog to your scene by creating an `Atmosphere Layer Block` component. However, there are some things about fog that make it less amenable to Expanse's usual technique of rendering atmosphere layers. Namely:
* **Fog is dense.** Because of this, artifacts in Expanse's usual strategy are more likely to be visible.
* **Volumetric shadows are important.** To have sharp volumetric shadows---"light shafts", "god/crepuscular rays"---fog needs to be computed per-pixel.

As such, Expanse exposes two different density distributions that are optimized for fog and rendered per-pixel: `Screenspace Uniform` and `Screenspace Height Fog`. These distributions also support a limited form of volumetric shadowing using the depth buffer. The implementation of these layers is inspired by [this classic strategy](https://developer.nvidia.com/gpugems/gpugems3/part-ii-light-and-shadows/chapter-13-volumetric-light-scattering-post-process), used in games like Mirror's Edge: Catalyst and the modern iterations of the Star Wars: Battlefront franchise. It is, however, more physically-based than the implementation described in the linked GPU Gems article.

## Setup

We'll assume that you've already imported Expanse into your project, and that you know how to use a fully sky prefab. For more info on how to do this, and how to render your first sky with Expanse, check out the [quickstart guide](/quickstart/quickstart).

The first thing we'll do is drag in one of the full sky prefabs to minimize the amount of boilerplate setup we need to do. In particular, we'll drag the prefab `Assets/Expanse/prefabs/Full Skies/Expanse Cloudless Sky.prefab` into our scene. We'll also fully unpack the prefab by right-clicking it in the hierarchy and selecting `Prefab->Unpack Completely`.

With that taken care of, we're going to select the `Earth Atmosphere` object, and delete the `Rain Fog` atmosphere layer block. This is, after all, one of the things we'll be modeling by hand in this tutorial.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/delete_rain.jpg"/></div>
    </div>
    <p>Delete the rain fog layer, since we'll be modeling that ourselves.</p>
</div>

Alright, let's roll in some fog!

## A Simple, Uniform, Isotropic Fog

We're going to start off by modeling the simplest possible fog. It will be **uniformly distributed** within a volume, and it will be **isotropic**, meaning that it scatters light evenly in all directions. This sort of fog works well as a basic uniform haze.

We'll start by adding a new `Atmosphere Layer Block` component to our `Earth Atmosphere` game object. 

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/add_atmo_block.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/added_atmo_block.jpg"/></div>
    </div>
    <p>Add an Atmosphere Layer Block to the Custom Atmosphere game object.</p>
</div>

Recall that, in order for it to work, you have to drag the Sky and Fog Volume onto the `volume` slot.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:75%" src="img/quickstart/modeling_atmo/drag.jpg"/></div>
    </div>
    <p>Drag the sky and fog volume onto the appropriate slot.</p>
</div>

We'll also rename it `Fog`, to make it easy to keep track of.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:75%" src="img/quickstart/fog/rename.jpg"/></div>
    </div>
    <p>Rename the layer to "Fog" for ease of identification.</p>
</div>


Now, for the task of actually choosing the appropriate parameter values.

We want our fog to appear kind of smoky, or hazy, so we will use scattering coefficients that are slightly lower than the extinction coefficients. This will make the fog absorb slightly more light than it reflects, making it dimmer and smokier. In particular, we'll set the extinction coefficients to `(4.4e-6, 4.4e-6, 4.4e-6)`, and the scattering coefficients to `(4e-6, 4e-6, 4e-6)`. We'll also use the isotropic phase function, so no scattering direction is preferred over any other.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:75%" src="img/quickstart/fog/uniform_scatter_extinct.jpg"/></div>
    </div>
    <p>Set the scattering and extinction coefficients to (4e-6, 4e-6, 4e-6) and (4.4e-6, 4.4e-6, 4.4e-6), respectively. Also, use the isotropic phase function.</p>
</div>

Now for the interesting stuff. Select the density distribution `Screenspace Uniform`. You'll notice that you have two parameters to control it: **density** and **radius**. **Density** is exactly what it sounds like---the density of the fog within the uniform volume. **Radius** defines how far the fog volume extends out away from the player. Within the sphere this radius defines, the fog is constant density. Outside this sphere, it is zero. Hence the name "uniform".

If you play around with these parameters, you'll notice that you can get quite a wide variety of different fog conditions. Since visible fog and haze is usually a fairly local phenomenon, let's set the radius to something like 10000 meters. You can now play with the density and see how thicker and thinner fog settings look.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/uniform_density_0.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/uniform_density_2.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/uniform_density_12.jpg"/></div>
    </div>
    <p>Some different density values for the fog we've just authored, with radius set at 5000 for all examples. Left: density zero, so no fog. Middle: density 2. Right: density 12. With this simple setup, we can model everything from light haze to thick smog!</p>
</div>

You may have also noticed two other checkboxes pop up when you changed the density distribution: `Geometry Shadows` and `Cloud Shadows`. These allow, respectively, the scene geometry and the clouds to cast approximate volumetric shadows on the fog.

Let's set the density to something high, like 12, and turn on `Geometry Shadows`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/quickstart/fog/shadow_checkbox.jpg"/></div>
    </div>
    <p>Left: without volumetric shadows. Right: with volumetric shadows.</p>
</div>

Now, when we look toward the sun, we see nice light shafts peeking through our scene geometry!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/uniform_no_shadows.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/uniform_shadows.jpg"/></div>
    </div>
    <p>Left: without volumetric shadows. Right: with volumetric shadows.</p>
</div>

You can play around with the `Max Occlusion` value to control how intense the light shafts are. Also, the effect becomes even more noticeable if you switch to the `Mie` phase function.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/uniform_shadows.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/uniform_shadows_mie.jpg"/></div>
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
        <div class="img-col"><img style="width:70%" src="img/quickstart/fog/change_phase.jpg"/></div>
    </div>
    <p>Change the phase function to Mie, with an anisotropy value of 0.25.</p>
</div>

Now let's select the `Screenspace Height Fog` density distribution. There's a few parameters we have exposed here:
1. **Thickness**: how high up the height fog extends from the ground. For my scene, around 125 meters looks good.
2. **Radius**: how far out the height fog extends around the player. You can play around with this, I'm going to set it to a pretty big number, 25000, so that the height fog extends out more or less as far as we can see.

Let's also adjust the density to something pretty high, that way the fog is really visible. So, something like 200.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/quickstart/fog/height_fog_edit.jpg"/></div>
    </div>
    <p>Select the Screenspace Height Fog density distribution, set the height to 125 meters, the radius to 25000 meters, and the density to 200.</p>
</div>

Finally, let's tone down the volumetric shadow effect, to fake some multiple scattering.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/quickstart/fog/tone_down_occlusion.jpg"/></div>
    </div>
    <p>Select the Screenspace Height Fog density distribution, set the height to 125 meters, the radius to 25000 meters, and the density to 200.</p>
</div>

Moving the camera up to a better vantage point, we've got our final result!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/height_fog_final.jpg"/></div>
    </div>
    <p>Height fog!</p>
</div>

Feel free to play around with the parameters to get the effect you want. Here's a few examples.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/height_fog_tall.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/height_fog_nice.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/height_fog_low_height_high_density.jpg"/></div>
    </div>
    <p>A few different thickness and density tweaks to the height fog we just created. Left: upping the thickness and lowering the density. Middle: lowering the density and thickness a little. Right: lowering the thickness but upping the density.</p>
</div>

Also, make sure to see what your fog looks like under different illumination conditions.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/height_fog_daytime.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/height_fog_evening.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/height_fog_night.jpg"/></div>
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

However, once inside the cloud layer, the reprojection strategy that Expanse uses starts to break down, and artifacts at the edges of the scene geometry are visible. You can solve this problem by turning off reprojection, but this will cause a significant performance hit.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/reprojection_artifacts.jpg"/></div>
    </div>
    <p>Artifacts introduced by reprojection, visible at the edges of objects.</p>
</div>

This is an active area of improvement that we are working on. For now, though, we'll have to use a different strategy for modeling rain fog. We'll use this image of London on a really foggy day to base our solution on.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://media.pri.org/s3fs-public/styles/story_main/public/story/images/Reuters%20London%20fog.jpg?itok=0MLALeR2"/></div>
    </div>
    <p>London on a foggy day. Source <a href="https://media.pri.org/s3fs-public/styles/story_main/public/story/images/Reuters%20London%20fog.jpg?itok=0MLALeR2">https://media.pri.org/s3fs-public/styles/story_main/public/story/images/Reuters%20London%20fog.jpg?itok=0MLALeR2</a>.</p>
</div>

To create this effect, we'll make some tweaks to the height fog layer we just set up in the last section. 

For one, because clouds are a more local phenomenon, we'll decrease the radius to something like `10000` meters. We'll also up the thickness to something similar to that of cumulus clouds---we'll go with `3000` meters. The density is largely up to how rainy you want it to look; our reference image is pretty intensely foggy, so we'll pick a value of `150`. Finally, we'll pull back the anisotropy to something like `0.05`, so that our fog's illumination is pretty even.

Here's the result we get with those values.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/rain_fog.jpg"/></div>
    </div>
    <p>First attempt at rain fog.</p>
</div>

Not too bad, but our reference is noticeably darker. This is because of self-shadowing. We can't mimick that directly with the height fog layer, but we can approximate it by reducing the scattering coefficients to `(2e-6, 2e-6, 2e-6)`. If we also tweak the scattering coefficients to be a little bit blue, this is the result.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/rain_fog_tweak.jpg"/></div>
    </div>
    <p>Second attempt at rain fog, with scattering coefficients tweaked to model self-shadowing.</p>
</div>

That's more like it! Tweaking the density values, we can get some different levels of fogginess.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/rain_fog_tweak.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/rain_400.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/fog/rain_1500.jpg"/></div>
    </div>
    <p>Tweaking the density of the rain fog. Left: 150. Middle: 400. Right: 1500, nearly impenetrable.</p>
</div>

## Wrapup

We've seen three examples of fog that you can model with Expanse, but this is only scratching the surface of what's possible. You could imagine using the techniques we learned here to model something like Blade Runner's dark impenetrable smog, or the yellow sulfuric gases blanketing the surface of Venus. Ultimately, the sky's the limit (no pun intended)!

Now that you've learned all about Expanse's volumetric fog, [continue on to learn how to model beautiful, evocative cloudscapes.](/quickstart/clouds)