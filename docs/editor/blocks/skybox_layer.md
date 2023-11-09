# Skybox Layer

> Implemented as class `Expanse.SkyboxLayer` in `blocks/SkyboxLayer.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/skybox_layer/nebula.jpg"/></div>
    </div>
</div>

This component renders a background skybox texture behind the sky. The obvious use case is stars/nebula effects in the night sky, but skybox layers are generally useful for any sort of static emissive content you'd like to have show up behind your sky. 

You can also use skybox layers to set up a reference HDRI to use when authoring your skies. This can make it easier to dial in a particular look.

> IMPORTANT: Skybox layers must be tracked in the [Skybox Layer Manager](editor/blocks/skybox_layer_manager.md) in order to show up in the scene.

> Skybox layers replace the procedural/texture star and nebula components, which have since been removed in 1.7.0.

#### Texture
**C# member variable:** `Texture m_texture` \
The cubemap to render behind the sky.

#### Intensity
**C# member variable:** `float m_intensity` \
The emission multiplier on the skybox layer, in Lux. 

Typically, stars have an intensity of `0.1-0.3` Lux. This is usually far too low for reasonable auto-exposure settings though, so a value of around one tenth of the moon's brightness is usually good.

#### Tint
**C# member variable:** `Color m_tint` \
Tint to the skybox layer. Perfect white, `(255, 255, 255)`, specifies no tint.

#### Twinkle Map
**C# member variable:** `Texture m_twinkleMap` \
To mimick the twinkling of stars due to atmospheric diffusion, Expanse can modulate the emission intensity of the cubemap according to another cubemap, the "twinkle map". This map specifies the frequency the skybox layer should be twinkled.

#### Twinkle Frequency Range
**C# member variable:** `Vector2 m_twinkleFrequencyRange` \
Frequency range of the twinkle effect, in cycles/sec.

#### Twinkle Intensity Range
**C# member variable:** `Vector2 m_twinkleIntensityRange` \
Intensity range of the twinkle effect. So, `[0, 1]` specifies a range of completely dark to full brightness. `[0.5, 1.5]` specifies half brightness to 1.5x times the authored brightness.