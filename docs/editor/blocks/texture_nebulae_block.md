# Texture Nebulae

> Implemented as class `Expanse.TextureNebulae` in `blocks/TextureNebulae.cs`

Adding this block to your scene will create a nebulae that uses a pre-authored texture.

#### Nebulae Texture
**C# member variable:** `m_nebulaeTexture` \
The nebulae as a cubemap texture.

#### Intensity
**C# member variable:** `float m_intensity` \
The intensity of the nebulae, multiplied through the overall night sky intensity, in Lux.

#### Tint
**C# member variable:** `Color m_tint` \
The tint to the nebulae.

#### Rotation
**C# member variable:** `Vector3 m_rotation` \
Rotation of the nebulae texture, specified as euler angles.