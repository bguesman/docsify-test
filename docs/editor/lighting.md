# Lighting In Expanse

What's easy to forget---but becomes immediately obvious when you start setting up a scene---is that, because Expanse controls the skybox, sun, and moon, it has a huge influence on your game's lighting! What follows in this section is an overview of how Expanse handles lights of all kinds, how they affect things like fog and clouds, and how ambient light is calculated.

## Overview

Expanse can interact with the following kinds of lights:

* **Directional lights**, which can light the atmosphere, fog, and clouds.
* **Point lights**, which can light fog and clouds.
* **Spot lights**, which can only light fog.
* **Expanse's fog does not support area lights**.

Expanse interacts with lights in two ways: it **pulls data from lights** to use in lighting the atmosphere/fog/clouds, and it also **pushes data to lights** to modify their properties to better reflect how the light should look under Expanse's volumetric conditions.

Some examples of the prior: 
* Expanse grabs the light direction, color, and brightness from a directional light, and uses it to light the clouds.
* Expanse uses the position/color/intensity of a point light and uses it to light the clouds.
* Expanse grabs the shape, direction, position, etc. of a spotlight and uses it to light the fog.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/procedural_cloud_volume/ms_0.75.jpg"/></div>
        <div class="img-col"><img src="img/lighting/fog-lights.jpg"/></div>
        <div class="img-col"><img src="img/lighting/cloud-point-lights.jpg"/></div>
    </div>
    <p>Lights interacting with Expanse's different volumetric components.</p>
</div>

An example (actually, probably the canonical example) of the latter: Expanse sets the light color on a directional light at sunset, so that it reflects how the light travels through the atmosphere---in other words, it makes the sunlight dimmer and redder at sunset.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/lighting/noon.jpg"/></div>
        <div class="img-col"><img src="img/lighting/sunset.jpg"/></div>
    </div>
    <p>Left: the sunlight color at noon. Right: the sunlight color at golden hour---much more orange, and much dimmer!</p>
</div>


## Light Control

The way you indicate to Expanse that you want a light to interact with its volumetrics is simple: you add the `Light Control` script to the light game object.

You'll then be presented with the following set of parameters to be adjusted to control various aspects of the light. These will be different depending on what the light type is (spot, point, etc.).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/lighting/ui.jpg"/></div>
    </div>
    <p>The Expanse light control UI.</p>
</div>

A few notes:
* If you can avoid it, don't enable the "Raymarch" parameter for point/spot lights. It's way more expensive than the alternative analytical strategy and isn't any more physically correct.
* You'll have to specify the light color in the light control UI, as opposed to using the Unity light's light color. This is because Expanse modulates the light's _actual_ color via the transmittance through the fog/atmosphere.

## Ambient Lighting and Reflections

Expanse renders the sky, clouds, and fog to Unity's reflection cubemap. Because of this, ambient light and reflections should just work, no setup required. However, if you want these to update in realtime, it's _crucial_ that you set the Visual Environment override's `Ambient Mode` setting on the sky and fog volume to `Dynamic`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/lighting/dynamic.jpg"/></div>
    </div>
    <p>Make sure your sky and fog volume's ambient mode setting is set to "Dynamic"!</p>
</div>