# Planet

> Implemented as class `Expanse.Planet` in `blocks/Planet.cs`

This block exposes the settings for the ground planet that Expanse's atmosphere and clouds are anchored around.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->
### Modeling
These parameters describe the shape and orientation of the planet.

#### Radius
**C# member variable:** `float m_radius` \
The radius of the planet, in world units.

#### Atmosphere Thickness
**C# member variable:** `float m_atmosphereThickness` \
The total thickness of the planet's atmosphere, in world units. This places an upper limit on the thickness of the atmosphere layers.

#### Origin Offset
**C# member variable:** `Vector3 m_originOffset` \
The planet origin, in world units, but specified as an offset from the position (0, -radius, 0), since that origin is much more convenient. Useful for implementing a floating origin.

#### Clip Fade
**C# member variable:** `float m_clipFade` \
How quickly to fade from geometry to sky as geometry approaches the far clip plane. Useful to tweak for flight sims, but otherwise, the default value of 0.35 is pretty good.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MATERIAL --------------------------------------->
<!---------------------------------------------------------------------------------------->
### Material
These parameters describe the material appearance of the planet.

#### Ground Albedo Texture
**C# member variable:** `Cubemap m_groundAlbedoTexture` \
The ground albedo, specied as a cubemap. The ground is modeled as a Lambertian (completely diffuse) reflector. If no texture is specified, the color of the ground will just be the ground tint.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/planet/earth_day.jpg"/></div>
    </div>
    <p>An example of a ground albedo texture, in this case, Earth's.</p>
</div>

#### Ground Tint
**C# member variable:** `Color m_groundTint` \
A color tint to the ground texture. Perfect grey, `(127, 127, 127)`, specifies no tint. If there is no ground texture specified, this is just the color of the ground.

#### Ground Emission Texture
**C# member variable:** `Cubemap m_groundEmissionTexture` \
The ground emission as a cubemap texture. Useful for modeling things like city lights. Has no effect on the sky. See \"Light Pollution\" for a way of modeling an emissive ground's effect on the atmosphere.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/planet/earth_night.jpg"/></div>
    </div>
    <p>Using a combination of a ground albedo texture and a ground emission texture can create some pretty cool effects!</p>
</div>

#### Ground Emission Multiplier
**C# member variable:** `float m_groundEmissionMultiplier` \
An intensity multiplier on the ground emission texture.

#### Rotation
**C# member variable:** `Vector3 m_rotation` \
The rotation of the planet textures as euler angles. This won't do anything to light directions, star rotations, etc. It is purely for rotating the planet's albedo and emissive textures.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- METADATA --------------------------------------->
<!---------------------------------------------------------------------------------------->
### Metadata
These parameters are metadata or references to components/objects that the block uses.
