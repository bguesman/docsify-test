# Texture Interpolation Block

> Implemented as class `Expanse.TextureInterpolationBlock` in `blocks/TextureInterpolationBlock.cs`

Simple block that will interpolate between 2 generated textures (`BaseTextureGeneratorBlock`'s). Exposes a function `GetTexture()` that returns an `RTHandle` to the interpolated texture.

#### Texture A
**C# member variable:** `BaseTextureGeneratorBlock m_textureA` \
First texture to interpolate.

#### Texture B
**C# member variable:** `BaseTextureGeneratorBlock m_textureB` \
Second texture to interpolate.

#### Blend
**C# member variable:** `float m_blend` \
Amount to interpolate between textures. A value of `0` will give you texture A. A value of `1` will give you texture B.

#### Tile A
**C# member variable:** `float m_tileA` \
Amount to tile texture A.

#### Tile B
**C# member variable:** `float m_tileB` \
Amount to tile texture B.

#### Amortize
**C# member variable:** `bool m_amortize` \
Amortizes the interpolation process over multiple (4) frames. Improves performance.