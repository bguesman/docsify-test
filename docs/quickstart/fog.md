# Tutorial: Using Volumetric Fog

> Note: this is an advanced tutorial. It'll be a lot of fun, but for most use cases the [Creative Fog Game Object](/editor/creative/creative_fog.md) will be easier to use. If you're new to using Expanse, check out [this video](https://youtu.be/PXs3De2lFqU).

Fog has been a computer graphics staple since nearly the beginning of the artform. What originated as a clever way of hiding the camera's far clipping plane has since evolved into a compelling way to add instant atmosphere and mood to a 3D scene.

In this tutorial, we'll walk through how to create different types of fog with Expanse.

## Problems With Fog

In Expanse, fog is essentially treated like any other layer of the atmosphere. However, there are some things about fog that make it less amenable to Expanse's accelerated atmosphere rendering technique. Namely:
* **Fog is dense.** Because of this, artifacts in Expanse's usual strategy are more likely to be visible.
* **Volumetric shadows are important.** To have sharp volumetric shadows---"light shafts", "god/crepuscular rays"---fog can't be rendered into Expanse's optimized atmosphere lookup tables.

As such, Expanse exposes a special [Creative Fog Game Object](/editor/creative/creative_fog.md) that is rendered differently than general atmosphere layers. It supports dense participating media and volumetric shadowing (either using the light's shadowmap, or via screenspace traces against the depth buffer).

> Technically, fog layers are just wrappers around Atmosphere Layers that use either of two [layer density distributions](/editor/blocks/atmosphere_layer_block.md?id=density-distribution): `Uniform Fog` and `Height Fog`. Selecting one of these density distributions indicates to Expanse that the atmosphere layer in question should be rendered at high resolution, not to optimized lookup tables.

## Setup

To make things easier, we'll start with one of Expanse's **default full skies**. These already have a sun, moon, stars, and some other things set up---this will reduce the irrelevant boilerplate setup we have to do.

Open up your Unity project, right click on the GameObject hierarchy, and select `Expanse => Full Skies => Standard Sky`. This will create a prefab `GameObject` called `Expanse Sky` in our hierarchy. For details on importing Expanse into your project, and rendering your first prefab sky, refer to the [quickstart guide](/quickstart/quickstart).

With that taken care of, we're going to delete the `Fog` GameObject. This is, after all, what we'll be modeling by hand in this tutorial.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/delete-fog.jpg"/></div>
    </div>
    <p>Delete the fog GameObject, since we'll be recreating it ourselves.</p>
</div>

Alright, let's roll in some fog!

## Afternoon Glare

I live in San Francisco, and frequently I'll look down 18th street, towards Twin Peaks, and see this amazing hazy glare around 3-4pm, so-called "Golden Hour". I couldn't find a picture of exactly that, but here's another San Francisco pic that gets the idea across:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://i.imgur.com/3I8pg6N.jpeg"/></div>
    </div>
    <p>Our look goal for our simple height fog.</p>
</div>

We'll start off with a simple goal: to model atmospheric fog that looks something like this.

To get rolling, let's create a fog layer. We can do this by selecting "Expanse => Fog => Fog Layer" in the hierarchy.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/add-fog-layer.jpg"/></div>
    </div>
    <p>Create a fog layer.</p>
</div>

Immediately this presents us with a pretty decent looking height fog.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/default-hf.jpg"/></div>
    </div>
    <p>Create a fog layer.</p>
</div>

But we can definitely match our reference better. You might first reach for the fog color setting, but I'd caution against it. Fog and smog, in the real world, are grey, and receive all of their color from the color of sun and sky light.

Instead, let's try first adjusting the sun angle to better match our reference's color. Ignoring my atrocious kit-bashing skills, this looks closer to our reference already, and we've managed to avoid using any artistic overrides.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/sun-angle-adjustment.png"/></div>
    </div>
    <p>Create a fog layer.</p>
</div>

Now, to match our reference better, we have to adjust the settings on the fog layer Game Object, pictured below.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/fog-settings.png"/></div>
    </div>
    <p>Creative fog layer settings.</p>
</div>

I think the fog in our reference image is denser, lower to the ground, and also more localized than what the default settings reflect. To try to match that, let's
- Lower the `Thickness` of our fog to `200`, so it hugs the ground more.
- Set our fog's `Visibility Distance` very low, to something like `15`.
- Localize our fog by setting its `Radius` to `1500`. This will avoid smogging out the horizon line too much.

With those adjustments, it's already looking better.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/afternoon-1.png"/></div>
    </div>
    <p>After our first round of adjustments.</p>
</div>

I think our reference is still more extreme. In an effort to continue to match it, I'm going to increase the `Glare` to `1.0`. This controls something called the *anisotropy* of the fog---raising it will blow out the halo around the sun disc more.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/afternoon-2.png"/></div>
    </div>
    <p>After adjusting the glare to 1. Subtle, but effective.</p>
</div>

At this point, with a little color grading, we can more closely match the reference---though I prefer the ungraded look.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/afternoon-graded.png"/></div>
    </div>
    <p>Grading with some saturation and a slight orange filter.</p>
</div>

Notice that we haven't touched the `Color` parameter. This means that our fog layer will look good under many lighting conditions. I encourage you, if you're designing a game or digital experience with dynamic time of day, to validate your atmospheric setups at various sun positions.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/afternoon-graded.png"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/afternoon-relight-2.png"/></div>
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/afternoon-relight-3.png"/></div>
    </div>
    <p>The fog we created, under various lighting conditions.</p>
</div>

And, finally, moving the sun around, you should observe sharp crepuscular rays, caused by volumetric shadowing from geometry---so long as your sun's [celestial body Game Object has them enabled](/editor/blocks/celestial_body_block.md?id=volumetric-shadows) under the `Interaction` tab (they are enabled by default).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/afternoon-shadows.png"/></div>
    </div>
    <p>Volumetric shadows cast by the city buildings on our fog.</p>
</div>

## Dense Rain Fog

Let's try something completely different---in London on a rainy day, you might be liable to step outside into something like this:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://cdn.abcotvs.com/dip/images/1283358_040816-kabc-4pm-ie-rain-vid.jpg?w=1600"/></div>
    </div>
    <p>Dense rain fog</p>
</div>

...or, according to this image, apparently Los Angeles.

Rain fog like this completely obscures the sun. It is not localized---it extends very far out in all directions, and is very dense. To model this, let's set our parameters as so:
- `Thickness` to 5000.
- `Visibility` to 75.
- `Radius` to 100000.

And let's also make sure the sun is somewhere decently above the horizon, so it's daytime.

The result is a decent first stab.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/rain-1.png"/></div>
    </div>
    <p>First pass at dense rain fog</p>
</div>

Something that's apparent is that, once again, the color of our fog is off. Our reference image is much bluer than what we have. Again, we'll resist the urge to tweak the color directly, and instead do something sneaky: we'll adjust the multiple scattering amount in the fog's atmosphere layer down from `1.0` to `0.33`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/rain-adjust-ms.png"/></div>
    </div>
    <p>Adjusting the multiple scattering multiplier. Notice how it's much bluer.</p>
</div>

This has the effect of reducing the amount of multi-scattered sunlight that makes it through the dense fog. What remains is blue ambient light from the sky-dome, so the result is overall bluer.

Finally, I want to showcase an interesting feature that can really help breathe life into these sorts of situations. In the real world, in very dense fog banks, light from surrounding geometry is stochastically scattered by the fog. This results in an interesting effect: objects that are further away from the viewer appear smeared out and blurrier.

Expanse attempts to simulate this via a screenspace approximation we refer to as [Fog Diffusion](/editor/blocks/global_settings.md?id=fog-diffusion). To enable it, navigate to the `Global Settings` object and check `Fog Diffusion` under the `Fog` tab.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/rain-adjust-diffusion.png"/></div>
    </div>
    <p>Here's how you enable diffusion.</p>
</div>

You can mess with the parameters here to get the look you want. Or maybe you'll decide you're not a fan---either way, here's what I ended up with as my final result, after a bit more tweaking. 

> As a side note, I've also added a very, very slight red tint to better match the reference, using the atmosphere layer's tint control (not the fog color control, which will also adjust the fog's absorption color). Additionally, I increased the ambient light multiplier a bit to help push more light into the fog (also on the atmosphere layer settings).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/rain-final-2.png"/></div>
    </div>
    <p>My final result.</p>
</div>

## Beijing Smog

Another interesting use case to consider is modeling something like the Beijing skyline---brutally smoky and smoggy.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="https://www.globaltimes.cn/Portals/0/attachment/2021/2021-01-01/08381b3c-d6b9-4070-af0f-ccd4e9706c3c.jpeg"/></div>
    </div>
    <p>Beijing sunset.</p>
</div>

Let's start out by setting our basic parameters like so:
-`Thickness` to 300.
-`Radius` to 100000.
-`Visibility Distance` to 300.

Here's how that looks.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/beijing-start.png"/></div>
    </div>
    <p>Starting point for Beijing smog</p>
</div>
 
This isn't quite right---but what exactly about it looks wrong?

Well, for one, in our reference image, the smog is aggressively red and orange. To me, this indicates that we might be dealing with participating media that is blue in color---because such media will be reddish-orange at sunset (see the atmosphere tutorial for why this is).

To mimick this, we're finally going to touch our `Color` parameter. Setting the color to a saturated blue gets us that sky-on-fire red we're looking for. The HSV color I used for the following image was `(225, 70, 50)`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/beijing-2.png"/></div>
    </div>
    <p>Adjusting the color of the Beijing smog to be blue, so that it appears red due to absorption.</p>
</div>

Still, it's not quite right---our fog is pale and washed out, where the reference is stark and intense. This is because smog is much more absorptive than fog; it scatters much less light than it absorbs, and this darkens very dense areas. Luckily, the fog layer has a `Smog` parameter to easily control this. I'm going to set this fairly high, to `0.87`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/beijing-3.png"/></div>
    </div>
    <p>Upping the smog parameter.</p>
</div>

After tweaking a bit more, here's what I came up with:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/beijing-final.png"/></div>
    </div>
    <p>Upping the smog parameter.</p>
</div>

## Local Lights

Expanse's fog can receive light from point, spot, and area lights. Setting this up is pretty easy---just add the `Light Control` component to any lights you'd like Expanse to use.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/light-control.png"/></div>
    </div>
    <p>Light control component parameters. Add this to your lights to get them to work with Expanse.</p>
</div>

The results are pretty cool. Local lights can cast volumetric shadows on fog, which you can see in this image near the lamps. It's also easy to fake some basic illumination from neon signs and other emissive elements like fire using a few point lights.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/fog-local-lights.png"/></div>
    </div>
    <p>Local lights affecting Expanse's fog.</p>
</div>

For more information on lighting in Expanse, check out the [Lighting](/editor/lighting.md) docs page.

## Fog Particle Systems

Expanse 1 was not designed to support spatially varying fog---i.e. detailed clouds that interact appropriately with opaque and transparent scene geometry. However, at some point it became clear that a limited, coarse version of this was possible within the scope of the fog renderer's design.

Toward this end, Expanse 1.6 and up have supported [Fog Particle Systems](/editor/blocks/fog_particle_system_block.md)---particle systems that drive the density of the a fog layer.

Let's wrap up our tutorial by creating one of these. In the hierarchy, right click and select "Expanse => Fog => Fog Particle System".

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/fog-particle-system-create.png"/></div>
    </div>
    <p>Create a fog particle system.</p>
</div>

By default this will be created at the origin---you'll need to move it to where you want it (we'd like to fix this soon).

Next, on your fog layer, check the `Receive Density Particles` checkbox.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/receive-particles.png"/></div>
    </div>
    <p>Enable receive particles on the fog layer.</p>
</div>

And now you can fool around with the settings on the particle system to get the behavior you want---it's just a standard Unity particle system. The one caveat is that, to adjust the optical density of the particles, you'll need to adjust the `Density` parameter on the actual `Fog Particle System` component.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/adjust-density.png"/></div>
    </div>
    <p>To adjust the optical density of the particles, use this parameter.</p>
</div>

Here's what I was able to create with a basic box emitter. Again, this is extremely coarse, so don't expect any really impressive effects to come from this---just think of it as a way to distribute fog somewhat non-uniformly.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/fog/1-7-3/fog-particles.png"/></div>
    </div>
    <p>Example of a fog particle system.</p>
</div>

## Wrapup

We've seen three examples of fog that you can model with Expanse, and learned how to use local lights and fog particle systems, but this is only scratching the surface of what's possible. You could imagine using the techniques we learned here to model something like Blade Runner's dark impenetrable smog, or the yellow sulfuric gases blanketing the surface of Venus.

Now that you've learned all about Expanse's volumetric fog, [continue on to learn how to model beautiful, evocative cloudscapes.](/quickstart/clouds)