# Overview

The purpose of this section is to give a high level explanation of some of the architectural and user interface features of Expanse. It is *not* a tutorial; in fact, I'd even say this isn't a great thing to read until you've done some of the tutorials. For that, please check out the [quick start section](/quickstart/quickstart) for a crash course.

On a high level, Expanse works just like any other runtime Unity plugin: it breaks out its functionality into multiple `Component`'s that you can organize by adding them to any number of `Game Object`'s. These components are designed to be modular, so you can use only what you need from Expanse's large featureset.

Expanse refers to these components as **blocks**.

## Blocks

Blocks are the primary way that you can interact with Expanse's featureset. For every feature in Expanse, there is a block component that lets you use it. Some examples of blocks are:

- [Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md): if you add this block Component to a Game Object in your scene, it will create a volume filled with 3D clouds that you can manipulate and position with the block's parameters.
- [Celestial Body Block](editor/blocks/celestial_body_block.md): adding this block Component to a Game Object in your scene will create a celestial body in your planet's sky, like the sun or the moon. This block lets you specify a directional light to use as the body's source of illumination, and will automatically control that light to match the body's parameters.
- [Atmosphere Layer Block](editor/blocks/atmosphere_layer_block.md): use this block to add layers of air, gas, and fog to your planet's atmosphere.

There are many other block types that expose different features---you can find descriptions of them all under the [blocks header](editor/blocks/blocks.md) in the menu.

Here's an example of what the block for a sun might look like. You can see that it has familiar parameters like the direction, distance from the planet, and light intensity. It also has physically-based parameters like the temperature and penumbra. Then, finally, there's parameters that describe how it interacts with other parts of the scene, like a checkbox for whether it should cast cloud shadows.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:75%" src="img/quickstart/sun_object.jpg"/></div>
    </div>
    <p>The inspector view of the celestial body block component.</p>
</div>

It might seem daunting at first, having to set up a whole collection of blocks on your own. To make things easier, **I've drawn up a number of pre-authored prefabs that you can drag and drop into your scene.** These have everything already set up for you, so you can just add and remove blocks depending on your use case. For instance, maybe your game takes place on a planet with two moons, so you need to add an extra celestial body block.

Also, for those who are reading this and enjoy scripting, all of the code for implementing the blocks is visible to you, so you can easily write your own custom blocks that are simpler or more complex than Expanse's default blocks. This is particularly useful if you want to create your own meta-parameters. An example of this could be something like a "rainy-ness" parameter for clouds, that adjusts cloud scattering coefficients to make them lighter or darker depending on "how much rain they have".

## Under The Hood: Sky and Fog Volume

Because Expanse is written in HDRP, it uses the Sky and Fog Volume component to actually get itself into the rendering pipeline. This means that every scene that uses Expanse needs to have a Sky and Fog Volume, with a visual environment override set to "Expanse". If you don't know what this means, don't worry about it---all the prefabs have this set up. If you're curious, read on.

All of the settings for Expanse are actually a part of a `SkySettings` object that conforms to the HDRP specification. This means that, in theory, you can interface with Expanse entirely by setting parameters in its Sky and Fog Volume UI. However, this UI is clunky and has a lot of nesting, and is generally unapproachable, so I wrote the blocks UI as a layer on top that allows you to:
- Organize the different settings into a neat `Game Object` hierarchy.
- Easily animate parameters using the animation timeline (Sky and Fog Volume overrides currently cannot be animated in this way).
- Interact with the sky parameters via scripting using the usual Object-Component style access pattern. The Volume pattern is unintuitive, so the blocks abstract it away, making it much simpler to create scriptable systems that model things like weather and time of day.

This means that Expanse's modularity is more or less a UI-level construct. For instance, under the hood, Expanse has fixed limits on how many cloud layers you can use (eight, to be specific). That said, this doesn't make Expanse any less efficient---it still disables any systems you aren't using. It's just that those systems are not strictly modular at the root API level.

Perhaps in the future I'll re-architect Expanse to be fully modular, and use custom render passes so that the Sky and Fog Volume can be ignored. That said, this would mean losing a lot of the nice integration features that come with writing a sky that conforms to HDRP's specification.