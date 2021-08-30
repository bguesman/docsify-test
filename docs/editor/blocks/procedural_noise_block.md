# Procedural Noise Block

> Implemented as class `Expanse.ProceduralNoiseBlock` in `blocks/ProceduralNoiseBlock.cs`

Block that generates a procedural noise texture. Exposes this texture as an `RTHandle` via the `GetTexture()` function.

### Texture Parameters

#### Name
**C# member variable:** `string m_name` \
Name for the generated texture. Useful for inspecting in tools like RenderDoc or the frame debugger.

#### Dimension
**C# member variable:** `Expanse.Datatypes.NoiseDimension m_dimension` \
Desired texture dimension (i.e., 2D or 3D).

#### Res2D/Res3D
**C# member variable:** `Vector2Int m_res2D` and `Vector2Int m_res3D` \
Desired texture resolution---2D and 3D, respectively.

### Noise Parameters

#### Noise Type
**C# member variable:** `Expanse.Datatypes.NoiseType m_noiseType` \
Type of noise to generate---i.e. Perlin, Worley, etc.

#### Scale
**C# member variable:** `Vector2Int m_scale` \
Scale of noise in x/y and z dimensions.

#### Octaves
**C# member variable:** `int m_octaves` \
How many octaves of noise to compute. This is sort of akin to how "detailed" the noise is. Computing more octaves is more computationally expensive though.

#### Octave Scale
**C# member variable:** `float m_octaveScale` \
How much to scale each octave by.

#### Octave Multiplier
**C# member variable:** `float m_octaveMultiplier` \
How much influence each successive octave has on the final composited texture.