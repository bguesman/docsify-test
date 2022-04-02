# Fog Particle System

> Implemented as class `Expanse.FogParticleSystem` in `blocks/FogParticleSystem.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/fog_particle/banner.jpg"/></div>
    </div>
</div>

This block attaches to a Unity particle system, to allow particles from the system to drive the density of an [Expanse fog layer](/editor/blocks/atmosphere_layer_block?id=fog-distributions).

> Fog particles are currently designated as experimental, because their performance is not quite production-ready.

### General Usage

Using fog particle systems is relatively easy. Simply,

- Create a Unity particle system by right clicking on the hierarchy and selecting `Create -> Effects -> Particle System`.
- Add the Expanse `FogParticleSystem` script to the same `GameObject`.
- Adjust the [density](/editor/blocks/fog_particle_system_block?id=density) to something like `500` to start with.
- Enable [receive density particles](/editor/blocks/atmosphere_layer_block?id=receive-density-particles) on any fog layers you want these fog particles to affect.

Now, when you play the Unity particle system, you should see fog appear near the particles! You'll probably want to disable rendering in the Unity particle system settings as well, so only the Expanse fog shows up, though this is up to you.

> Stay tuned, eventually I'll post a more in-depth YouTube tutorial on how to best make use of fog particle systems.

### Parameters

#### Density

**C# member variable:** `float m_density` \
How much density each particle contributes to the fog buffer. It looks best to keep this relatively low, since particle fog does not have self-shadowing.
