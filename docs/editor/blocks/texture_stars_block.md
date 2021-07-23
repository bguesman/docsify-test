# Texture Stars Block

> Implemented as class `Expanse.TextureStarsBlock` in `blocks/TextureStarsBlock.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/stars/texture_stars.jpg"/></div>
    </div>
</div>

Adding this block to your scene will create a star field that uses a pre-authored texture.

#### Star Texture
**C# member variable:** `Cubemap m_starTexture` \
The stars as a cubemap texture.

#### Intensity
**C# member variable:** `float m_intensity` \
The intensity of the stars, multiplied through the overall night sky intensity, in Lux. Typically stars have an intensity of `0.1-0.3` Lux. This is usually far too low for reasonable auto-exposure settings though, so a value of around one tenth of the moon's brightness is usually good.

#### Tint
**C# member variable:** `Color m_tint` \
Tint to the star texture. Perfect white, `(255, 255, 255)`, specifies no tint.

#### Rotation
**C# member variable:** `Vector3 m_rotation` \
Rotation of the star texture, specified as euler angles.