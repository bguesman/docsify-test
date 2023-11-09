# Skybox Layer Manager

> Implemented as class `Expanse.SkyboxLayerManager` in `blocks/SkyboxLayerManager.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/skybox_layer/star_diffusion.jpg"/></div>
    </div>
</div>

This component aggregates together the [Skybox Layers](editor/blocks/skybox_layer.md) to render. 

> Currently all layers are composited additively, but in the future this component may specify various blending modes, i.e. alpha or screen.

#### Skybox Layers
**C# member variable:** `List<Expanse.SkyboxLayer> m_skyboxLayers` \
List of skybox layers to render. Maximum of 4.
