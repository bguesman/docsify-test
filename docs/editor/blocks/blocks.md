# Blocks

Blocks are `Components` that you can add to `Game Objects` to bring different Expanse features into your scene. Like Lego bricks, you can grab them and piece them together, taking only what you need, to build your unique sky---hence the name "blocks".

Want a sun in your scene? Drop a [Celestial Body Block](editor/blocks/celestial_body_block.md) onto an empty `Game Object`, adjust some parameters, and voila!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/celestial_bodies/angular_radius_2.jpg"/></div>
    </div>
    <p>A sun in the sky, created by adding a Celestial Body Block to the scene.</p>
</div>

What about dynamic 3D clouds? Drag in a [Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md), select a preset, and there you have it!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/1-4-0/mallow-2.jpg"/></div>
    </div>
    <p>Procedural volumetric clouds, created by dragging a Procedural Cloud Volume Block onto an empty Game Object.</p>
</div>

Of course, it's not quite this simple---for more details on how to build your first sky in Expanse, check out [quick start guide](quickstart/quickstart.md) and other tutorials under the Getting Started tab.

Under this heading, you'll find detailed descriptions of each block's parameters, with visual examples of what these parameters do. These pages are best used as a reference, when you find yourself asking something like *"What the f\**** *is Media Zero Threshold?"*

As a note: blocks are designed to be modular, but **there are some blocks which are required for Expanse to function**. These are:
* The [Planet Block](editor/blocks/planet_block.md)
* The [Quality Settings Block](editor/blocks/quality_settings_block.md)
* The [Aerial Perspective Settings Block](editor/blocks/aerial_perspective_settings_block.md)
* The [Night Sky Block](editor/blocks/night_sky_block.md)
* The [Camera Settings Block](editor/blocks/camera_settings_block.md)

They are included in all sky prefabs, and the editor will print an error telling you to add one if its missing from your scene.