# Cloud Layer Interpolator

> Implemented as class `Expanse.CloudLayerInterpolator` in `blocks/CloudLayerInterpolator.cs`

This block allows you to smoothly and efficiently interpolate between different pre-authored cloud presets. For a lighter, more intuitive overview of cloud preset interpolation, see the tutorial [Interpolating Cloudscapes For Weather and Beyond](/quickstart/interpolation).

### Load Function

To load a preset to begin interpolating towards, use the following function:

```
LoadPreset(UniversalCloudLayer preset, string filepath="default preset name")
```

This function is bound to the UI button "Load Preset", which you can use when you're in the editor. However, in a game environment, you'll likely need to have some sort of autonomous weather controller that calls `LoadPreset()` for you, based on weather transitions or scripted events.

The second argument is optional---it will be used to display a name for the preset you are interpolating towards on the editor component.

### Example Usage

Here's a simple example of how you might use a `CloudLayerInterpolator` in a scripted context. The following GameObject allows you to specify a list of presets to interpolate through.

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Expanse;

public class CloudPlaylist : MonoBehaviour
{
    // Our interpolator that will cycle through the presets.
    public CloudLayerInterpolator interpolator;
    // The presets to cycle through---this will be exposed in the editor for us to edit.
    public UniversalCloudLayer[] presets;
    // Next preset to lerp towards (so, an index into the presets array).
    private int index = 0;

    void Start()
    {
        // Make sure our index always starts at zero.
        index = 0;
    }

    void Update()
    {
        // Require that we have at least 2 presets to interpolate between.
        if (presets.Length < 2) 
        {
            return;
        }

        if (interpolator.m_presetsLoaded == 0) 
        {
            // If we haven't loaded any presets yet, load the first two presets simultaneously,
            // so that we start immediately interpolating from preset 1 to preset 2.
            interpolator.LoadPreset(presets[0]);
            interpolator.LoadPreset(presets[1]);
            index = 2;
        } 
        else if (!interpolator.m_interpolating && index < presets.Length) 
        {
            // If the interpolator has stopped interpolating, we know that it's
            // ready to receive another preset. If we have another preset to
            // load, load it!
            interpolator.LoadPreset(presets[index]);
            index++;
        }
    }
}

```

### Interpolation Parameters

#### Transition Time
**C# member variable:** `float m_transitionTime` \
Amount of time it takes to transition from the current to the target preset, in seconds.

#### Bypass Offset
**C# member variable:** `bool m_bypassOffset` \
If enabled, ignores noise offsets in presets when interpolating. You'll probably want this enabled, that way your noises can keep scrolling as the interpolation occurs.

### Backing Components

The only way that this is even possible is through a somewhat complex system of procedural noise generators and interpolators. There was a design decision at some point that had to be made about how best to organize the realtime interpolation and generation of the textures without bloating the ProceduralCloudVolume/Plane classes.

Ultimately, the strategy that Expanse employs ended up leaning heavily on the Unity `GameObject`/`Component` system. The good thing about this is that it makes things very modular and is generally a lot easier to maintain. The downside is that it is a little cumbersome to set up by hand. This problem is somewhat mitigated by the fact that you can create prefabs that are already set up correctly (see the `Interpolable Cloud Layer` prefab), but it still stands that if you want to set up a `CloudLayerInterpolator` yourself, it may be a bit challenging.

Underneath the "Components" header are fields for all the components that are necessary for the cloud layer interpolator to function.

#### Cloud Layer
**C# member variable:** `Expanse.BaseCloudLayer m_cloudLayer` \
This is the cloud layer that the interpolator will use to actually render the clouds. For instance, a `ProceduralCloudVolume`.

#### Interpolated Noise
**C# member variable:** `Expanse.TextureInterpolator m_<noise layer>Interpolated` \
Each layer of noise that the clouds use (Coverage, Base, Detail, etc.) requires a `TextureInterpolator`. This block is responsible for blending between the current and target noise preset.

#### Current and Target Noise
**C# member variable:** `Expanse.ProceduralNoiseGenerator m_<noise layer>Current` and `Expanse.ProceduralNoiseGenerator m_<noise layer>Target` \
Each noise layer also requires two `ProceduralNoiseGenerator`'s---one for the "current" preset and one for the "target" preset. These regenerate noises on the fly as new presets are loaded.