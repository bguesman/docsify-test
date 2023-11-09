# Advanced Components

Expanse exposes various `Components` that you can add to `Game Objects` to bring different Expanse features into your scene. Like Lego bricks, you can grab them and piece them together, taking only what you need, to build your unique sky.

Want a sun in your scene? Drop a [Celestial Body Component](editor/blocks/celestial_body_block.md) onto an empty `Game Object`, adjust some parameters, and voila!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/angular_radius_2.jpg"/></div>
    </div>
    <p>A sun in the sky, created by adding a Celestial Body Component to the scene.</p>
</div>

What about dynamic 3D clouds? Drag in a [Procedural Cloud Volume Component](editor/blocks/procedural_cloud_volume_block.md), select a preset, and there you have it!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/1-7-0/clouds_1.jpg"/></div>
    </div>
    <p>Procedural volumetric clouds, created by dragging a Procedural Cloud Volume Component onto an empty Game Object.</p>
</div>

Of course, it's not quite this simple---for more details on how to build your first sky in Expanse, check out [quick start guide](quickstart/quickstart.md) and other tutorials under the Getting Started tab.

Under this heading, you'll find detailed descriptions of each component's parameters, with visual examples of what these parameters do. These pages are best used as a reference, when you find yourself asking something like *"What the f\**** *is Media Zero Threshold?"*

As a note: components are designed to be modular, but **there are some components which are required for Expanse to function**. These are:
* The [Planet Component](editor/blocks/planet_block.md)
* The [Quality Settings Component](editor/blocks/quality_settings_block.md)
* The [Aerial Perspective Settings Component](editor/blocks/aerial_perspective_settings_block.md)
* The [Night Sky Component](editor/blocks/night_sky_block.md)
* The [Camera Settings Component](editor/blocks/camera_settings_block.md)

They are included in all sky prefabs, and the editor will print an error telling you to add one if its missing from your scene.