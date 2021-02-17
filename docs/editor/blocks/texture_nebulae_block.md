# Texture Nebulae Block

> Implemented as class `Expanse.TextureNebulaeBlock` in `blocks/TextureNebulaeBlock.cs`

Texture nebulae...

#### Nebulae Texture
**C# member variable:** `m_nebulaeTexture` \
The nebulae as a cubemap texture.

#### Intensity
**C# member variable:** `m_intensity` \
The intensity of the nebulae, multiplied through the overall night sky intensity, in Lux.

#### Rotation
**C# member variable:** `m_rotation` \
Rotation of the nebulae texture, specified as euler angles.

#### Volume
**C# member variable:** `UnityEngine.Rendering.Volume m_volume` \
The Sky and Fog Volume that backs this texture nebulae object. In all likelihood, this should be a single global volume you set up at the start of your project. More complex workflows may have multiple local volumes, in which case you will need to create texture nebulae blocks (one for each volume), or programmatically change this parameter.