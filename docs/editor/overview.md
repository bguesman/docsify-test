# Overview

The purpose of this section is to give a high level explanation of some of the architectural and user interface features of Expanse. It is *not* a tutorial; in fact, I'd even say this isn't a great thing to read until you've done some of the tutorials. For that, please check out the [quick start section](/quickstart/quickstart) for a crash course.

On a high level, Expanse works just like any other runtime Unity plugin: it breaks out its functionality into multiple `Component`'s that you can organize by adding them to any number of `Game Object`'s. These components are designed to be modular, so you can use only what you need from Expanse's large featureset.

## Components

Components are the primary way that you can interact with Expanse's featureset. For every feature in Expanse, there is a component that lets you use it. Some examples of components are:

- [Procedural Cloud Volume](editor/blocks/procedural_cloud_volume_block.md): if you add this component Component to a Game Object in your scene, it will create a volume filled with 3D clouds that you can manipulate and position with the component's parameters.
- [Celestial Body](editor/blocks/celestial_body_block.md): adding this component Component to a Game Object in your scene will create a celestial body in your planet's sky, like the sun or the moon. This component lets you specify a directional light to use as the body's source of illumination, and will automatically control that light to match the body's parameters.
- [Atmosphere Layer](editor/blocks/atmosphere_layer_block.md): use this component to add layers of air, gas, and fog to your planet's atmosphere.

There are many other components that expose different features---you can find descriptions of them all under the Creative/Advanced Components subheadings in the menu.

Here's an example of what the component for a sun might look like. You can see that it has familiar parameters like the direction, distance from the planet, and light intensity. It also has physically-based parameters like the temperature and penumbra. Then, finally, there's parameters that describe how it interacts with other parts of the scene, like a checkbox for whether it should cast cloud shadows.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/overview/celestial-body.jpg"/></div>
    </div>
    <p>The inspector view of the celestial body component.</p>
</div>

It might seem daunting at first, having to set up a whole collection of components on your own. To make things easier, **Expanse has hierarchy create menu shortcuts for creating full skies.** These have everything already set up for you, so you can just add and remove components depending on your use case. For instance, maybe your game takes place on a planet with two moons, so you need to add an extra celestial body component.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/overview/full-sky.jpg"/></div>
    </div>
    <p>Creating a full sky from the hierarchy's create menu.</p>
</div>

Also, for those who are reading this and enjoy scripting, all of the code for implementing the components is visible to you, so you can easily write your own custom components that are simpler or more complex than Expanse's default components. This is particularly useful if you want to create your own meta-parameters. An example of this could be something like a "rainy-ness" parameter for clouds, that adjusts cloud scattering coefficients to make them lighter or darker depending on "how much rain they have".

## Under The Hood: Sky and Fog Volume

Because Expanse is written in HDRP, it uses the Sky and Fog Volume component to actually get itself into the rendering pipeline. This means that every scene that uses Expanse needs to have a Sky and Fog Volume, with a visual environment override set to "Expanse".