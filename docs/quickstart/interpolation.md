# Tutorial: Interpolating Cloudscapes For Weather and Beyond

So, you've crafted a beautiful atmosphere. Picturesque clouds scurry across a deep blue sky, like dandelion seeds drifting through a meadow. The day moon peeks out from behind a misty mountaintop.

And then your character exclaims, "hmmm, looks like rain", and suddenly you realize---you have no way of transforming this sea of tranquility into a violent, blustery tempest.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/1-7-0/clouds_1.jpg"/></div>
        <div class="img-col"><img src="img/1-7-0/highlands.jpg"/></div>
    </div>
    <p>How can we build a system that will allow us to transform the left image into the right image over the course of our game?</p>
</div>

Corny poetic waxing aside, the observation stands: clouds in the real world are dynamic, ever-changing entities. When you author clouds in Expanse, you get them to look a certain way and they will roughly stay that way forever (until you close the game). How can we reconcile this jarring dissimilarity? Put plainly: **how can we make Expanse dynamic?**

## Dynamism

I thought about the best way to do this for a while. At the outset I had imagined that a full-blown dynamic weather implementation would be the best way to go about it---a system based on some sort of loose model of atmospheric dynamics. However, I quickly came to realize that something like this would be far too rigid for most applications. In games and other digital experiences, *control* is often more important than realism.

Expanse's system for dynamism is informed by this need. The workflow is split up into two separate cases:

* **Clouds**
    * Author and save [universal cloud layer presets](api/universal_clouds.md).
    * Smoothly blend between them using a [cloud layer interpolator](editor/blocks/cloud_layer_interpolator.md).
* **Everything else**
    * Author reasonable defaults for fog, the atmosphere, etc.
    * Script or animate various parameters, since everything will update in realtime with no special requirements.

This system allows you to arbitrarily automate various atmospheric state changes in whatever way you see fit. The primary reason that clouds are handled separately is because they require special computations to ensure that the blending process is smooth and performant.

This tutorial will show you **how to author and transition between different cloud presets**, and **how to combine this with parameter automation to build a simple weather controller**.

## Interpolating Between Cloud Presets

Setting up Expanse to handle cloud interpolation is as easy as dragging in a prefab! To start off,
* Create a new scene
* Delete the default directional light
* Drag the prefab `Assets/Expanse/prefabs/Full Skies/Expanse Interpolable Volumetric Cloud Sky.prefab` into the scene.

With luck, you should see this, a clear blue sky. If you've never set up Expanse in a scene before and are having trouble, I'd suggest reading [this getting started tutorial](quickstart/quickstart.md), which goes into much greater detail!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/interpolation/drag-in.jpg"/></div>
    </div>
    <p>What you should see after dragging the prefab into the new scene.</p>
</div>

To make things a little nicer to look at, we'll
* Add a global volume with an exposure override, and set it to auto with a minimum of 5 and a max of 12.
* Turn off dynamic clipping on the editor camera, and adjust the far clipping plane to something high like 50000.
* Adjust our main camera's far clipping plane some that it's also 50000.

And here's what that looks like. Again, if you've set up Expanse before, this should all be familiar to you.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/interpolation/adjust-postprocessing.jpg"/></div>
    </div>
    <p>How things should look after adjusting the exposure and clipping planes.</p>
</div>

Alright, with that set up, let's actually try interpolating between some cloud presets! In the hierarchy pane, expand the Expanse prefab dropdown,and select the `Interpolable Cloud Volume` GameObject.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/interpolation/select-interpolable.jpg"/></div>
    </div>
    <p>Select the interpolable cloud volume GameObject.</p>
</div>

You should now see the following UI in the inspector.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/interpolation/interp-UI.jpg"/></div>
    </div>
    <p>The interpolable cloud volume settings.</p>
</div>

This exposes all of the functionality available for interpolating between presets. Going through each of these:
* **Transition Time** is the amount of time it will take to blend from one preset to another, in seconds. It defaults to 5 seconds, but this is actually quite fast!
* **Bypass Offset** specifies whether or not to ignore the offsets specified in the cloud presets when interpolating. This will avoid the situation where clouds look like they move really fast across the sky when interpolating between far away offsets. You'll probably want this enabled.
* **Progress/Current Preset/Target Preset** are all just progress indicators. You'll see these update as you cycle between different cloud presets.
* **Load Preset** is the button you click to load a new cloud preset and start interpolating towards it!

So, let's give it a go! More specifically:
* Click the **Load Preset** button. A file selection menu should appear.
* Select the preset `Assets/Expanse/blocks/presets/procedural cloud volume/Stratus/Minerva Optimized.json`

The preset should load immediately! You should see the clouds rendering.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/interpolation/minerva-loaded.jpg"/></div>
    </div>
    <p>The "Minerva Optimized" preset.</p>
</div>

Now, let's try loading a new preset. This time, since we have a preset already specified, we should see the presets smoothly blend from one to another.
* Click the **Load Preset** button again.
* Select the preset `Assets/Expanse/blocks/presets/procedural cloud volume/Stratus/Jade Optimized.json`

And... **oh no!** The presets are sort of blending, and it doesn't look too horrible, but there's definitely some weird snapping behavior going on at the start and end of the interpolation process. What's wrong?

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/interpolation/not-good.mp4" type="video/mp4">
    </video>
    <p>Interpolation, but with some unappealing snapping/hitching at the start and end of interpolation.</p>
</div>

## Optimizing Presets For Smooth Interpolation

The issue lies in an incompatibility between the two presets. If you've ever authored clouds with Expanse before, you'll know that it relies heavily on the tiling of noises to imbue the clouds with small-scale detail. When two presets are set up with different tile factors, Expanse does its best to try to reconcile them, but it often does not work perfectly, resulting in unpleasant "snapping" and "skipping" artifacts.

The solution is to author all of your cloud presets using the same tile factors. However, this can be a total pain, especially if you're trying to convert presets you've already authored. Luckily, Expanse provides a utility component for making 2 presets interpolation-compatible.

Add the component `Cloud Preset Mapper` to the interpolable cloud volume.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/interpolation/add-preset-mapper.jpg"/></div>
    </div>
    <p>Add a preset mapper component to the interpolable cloud volume.</p>
</div>

You should see 3 buttons:
* **Load Source Preset**: selects the preset you'll be modifying.
* **Load Target Preset**: selects the preset that `Source Preset` will be made compatible with.
* **Map Source Onto Target**: takes the source preset, makes it compatible with the target preset, and saves out the resulting preset.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/interpolation/mapper-ui.jpg"/></div>
    </div>
    <p>The preset mapper component.</p>
</div>

For us, we'd like to make the `Jade Optimized` preset compatible with the `Minerva Optimized` preset. So we'll:
* Click **Load Source Preset** and select `Assets/Expanse/blocks/presets/procedural cloud volume/Stratus/Jade Optimized.json`
* Click **Load Target Preset** and select `Assets/Expanse/blocks/presets/procedural cloud volume/Stratus/Minerva Optimized.json`
* Click **Map Source Onto Target** and save it to wherever you choose. I'm going to use the path `Assets/Expanse Interpolation Presets/Jade Mapped Onto Minerva`.

Now, we can repeat the steps from above, but instead select `Jade Mapped Onto Minerva` as our second preset, and voila! Our interpolation is smooth as butter!

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/interpolation/good.mp4" type="video/mp4">
    </video>
    <p>Interpolation, this time without the snapping!</p>
</div>

> If you still see some unappealing pixel-ey looking artifacts, try watching the interpolation happen in play mode. Expanse relies on a relatively high framerate (i.e. 15+ FPS) to make the interpolation smooth. In the editor, the framerate is generally quite low unless you are moving the camera around (2-3 FPS).

## Building a Simple Weather Controller

> **As of the release of Expanse 1.5, this section of the tutorial is out of date.** For best practices on programmatically using interpolation, see the [Cloud Layer Interpolator](editor/blocks/cloud_layer_interpolator.md) documentation. This tutorial is still useful as a guide to building a weather controller, but you will need to make a few adjustments based on the new API. In particular, you will need to maintain a list of `UniversalCloudLayer` `ScriptableObjects`, instead of a list of filepaths to these objects.

So this is great! We've now gotten to see one cloud preset slowly morph into another one, and we've fixed any hitching errors by making sure that the presets' tile factors align. Naturally though, nobody is gonna be around to click a button to transition between presets when a user is interacting with your shipped digital experience. In order for this dynamism to be useful, we have to make it autonomous.

From here on out, we'll refer to an entity that makes autonomous decisions about when to switch presets as a **weather controller**. In this section, we'll build a very simple weather controller---one that lets us specify a list of presets, and every so often decides randomly to switch to one.

**As a note:** if you're unfamiliar with Unity C#, this section of the tutorial may not be all that useful for you---there will be a lot of code! However, you should be able to copy the end result into your own C# script and use it as you see fit.

### Defining The Class

The first thing we're going to do is create a C# script called `SimpleWeatherController`. I'm going to put this in my `Assets/Expanse Interpolation Presets` folder I previously created just for convenience. We'll then add an instance of this script to an empty GameObject on the top level of the hierarchy that I'm going to call `Weather Controller`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/interpolation/drag-controller.jpg"/></div>
    </div>
    <p>Drag our new weather controller script onto the weather controller game object we created.</p>
</div>

Now, open up the script in your favorite code editor (which is certainly Visual Studio Code). You should be confronted with the usual Unity MonoBehaviour template.

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SimpleWeatherController : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
```

The first thing we'll do is declare a type representing a weather state. There are many possible parameters we could choose to control in Expanse to simulate changing weather conditions, but since we're trying to keep things simple for the scope of our tutorial, let's pick a few important ones.

```
// Class representing the state of the weather.
public class WeatherState {
    public string cloudPreset;     // File path to cloud preset
    public float fogDensity;       // How much fog there is
    public float fogHeight;        // How high the fog extends
}
```

We'll add this as a definition inside our weather controller class, making sure to add the `Serializable` attribute (from the `System` namespace, which we've also included), so that it later shows up in the UI. We've also added the `ExecuteInEditMode` attribute to our class so that it runs while we are in the editor. Now our class looks like:

```
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[ExecuteInEditMode]
public class SimpleWeatherController : MonoBehaviour
{
    // Class representing the state of the weather.
    [Serializable]
    public class WeatherState {
        public string cloudPreset;     // File path to cloud preset
        public float fogDensity;       // How much fog there is
        public float fogHeight;        // How high the fog extends
    }

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
```

We want to be able to specify multiple of these weather states, and transition between them in a random order. To do this, we'll need to maintain some user-editable array of states, and two integer indexes into that array that represent the state we're currently at and the state we're transitioning toward. We'll add the following member variables to our class:

```
// User-editable array of states.
public WeatherState[] m_states;
// State we are at, and state we are transitioning toward.
private int m_currentState;
private int m_targetState;
```

We'll also stub out the following private helper functions to make our main `Update()` function much cleaner.
```
bool readyForNextState() {
    // TODO: check if we are ready to pick a new state to transition to
    return false;
}

void transitionToNextState() {
    // TODO: choose and transition to the next weather state
}
```

We'll edit our `Update()` function to use these two new helpers, and add an early out in the case that we don't have at least 2 presets to transition between.

```
public void Update() {
    // We need to have at least 2 weather states to work.
    if (m_states.Length < 2) {
        return;
    }

    // Check if we are ready to move to the next weather state, and if we are,
    // begin transitioning toward it.
    if (readyForNextState()) {
        transitionToNextState();
    }
}
```

So now, our class looks like this:

```
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[ExecuteInEditMode]
public class SimpleWeatherController : MonoBehaviour
{
    // Class representing the state of the weather.
    [Serializable]
    public class WeatherState {
        public string cloudPreset;     // File path to cloud preset
        public float fogDensity;       // How much fog there is
        public float fogHeight;        // How high the fog extends
    }

    // User-editable array of states.
    public WeatherState[] m_states;
    // State we are at, and state we are transitioning toward.
    private int m_currentState;
    private int m_targetState;


    // Start is called before the first frame update
    void Start()
    {

    }

    // Update is called once per frame
    void Update()
    {
        // We need to have at least 2 weather states to work.
        if (m_states.Length < 2) {
            return;
        }

        // Check if we are ready to move to the next weather state, and if we are,
        // begin transitioning toward it.
        if (readyForNextState()) {
            transitionToNextState();
        }
    }

    bool readyForNextState() {
        // TODO: check if we are ready to pick a new state to transition to
        return false;
    }

    void transitionToNextState() {
        // TODO: choose and transition to the next weather state
    }
}
```

### Wiring It Up To Expanse

Ok, this is all well and good, but this very symbolic representation of a weather controller can't really do anything yet. In order to actually work, it'll need access to the relevant Expanse components.

To do this, we'll add public member variables for the fog atmosphere layer and the cloud layer interpolator. We'll also make sure we include the Expanse namespace.

```
using Expanse;

...

// Expanse components
public Expanse.CloudLayerInterpolator m_cloudInterpolator;
public Expanse.AtmosphereLayerBlock m_fog;
```

We'll then drag the relevant components into the slots, as in the following image:

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/interpolation/drag-components.jpg"/></div>
    </div>
    <p>Drag the fog and cloud layer interpolator objects into the slots in our weather controller component.</p>
</div>

Now, let's fill out the start function. It will be very simple, we'll just set both of our initial state indices to `-1` to signify that no states have been chosen yet.

```
void Start()
{
    m_currentState = -1;
    m_targetState = -1;
}
```

We can now fill out our `readyForNextState()` function. We're going to piggy-back here on the interpolation capabilities and the `IsInterpolating()` function exposed by the `CloudLayerInterpolator`.

```
bool readyForNextState() {
    // Check if we are ready to pick a new state to transition to. This happens when:
    //  - The cloud interpolator has finished (i.e., is no longer) interpolating
    //  - We just initialized the weather controller and have no state
    return (!m_cloudInterpolator.IsInterpolating() || m_currentState == -1 || m_targetState == -1);
}
```

Finally, we can fill out the `transitionToNextState()` function, the more complicated one. The first thing we'll need to do is check if we need to first load an initial state (in the event that the weather controller was uninitialized). We'll also add a random number generator to our class to use when choosing the next state.

```
// Random generator for selecting new states
private System.Random m_rnd = new System.Random();

...

void transitionToNextState() {
    // Check if we were just initialized and need to load an initial state.
    if (m_currentState == -1) {
        m_currentState = m_rnd.Next(0, m_states.Length);
        m_cloudInterpolator.LoadPreset(m_states[m_currentState].cloudPreset);
    }
}
```

No matter what, we'll also need to pick a new target state. Use the same procedure, but make sure we exclude the current state from our available choices.

```
void transitionToNextState() {
    // Check if we were just initialized and need to load an initial state.
    if (m_targetState == -1) {
        m_targetState = m_rnd.Next(0, m_states.Length);
        m_cloudInterpolator.LoadPreset(m_states[m_targetState].cloudPreset);
    }

    // Pick a new state to transition to that's not the same as the current state.
    // NOTE: it's possible but extremely unlikely that this will time out. You
    // may want to implement a more robust method of avoiding duplicates for your
    // own purposes.
    m_currentState = m_targetState;
    m_targetState = m_rnd.Next(0, m_states.Length);
    while (m_targetState == m_currentState) {
        m_targetState = m_rnd.Next(0, m_states.Length);
    }
    m_cloudInterpolator.LoadPreset(m_states[m_targetState].cloudPreset);
}
```

Finally, we have to make sure that we update our fog layer to blend the fog parameters specified in states. This is as easy as tacking a simple interpolation calls onto the end of the `Update()` function.

```
// Update is called once per frame
void Update()
{
    
    ...

    // Interpolate and set the fog parameters based on the cloud interpolator's interpolation amount.
    m_fog.m_density = Mathf.Lerp(m_states[m_currentState].fogDensity, m_states[m_targetState].fogDensity, m_cloudInterpolator.m_interpolationAmount);
    m_fog.m_thickness = Mathf.Lerp(m_states[m_currentState].fogHeight, m_states[m_targetState].fogHeight, m_cloudInterpolator.m_interpolationAmount);
}
```

And there you have it! Here's our final class:
```
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Expanse;

[ExecuteInEditMode]
public class SimpleWeatherController : MonoBehaviour
{
    // Expanse components
    public Expanse.CloudLayerInterpolator m_cloudInterpolator;
    public Expanse.AtmosphereLayerBlock m_fog;

    // Class representing the state of the weather.
    [Serializable]
    public class WeatherState {
        public string cloudPreset;     // File path to cloud preset
        public float fogDensity;       // How much fog there is
        public float fogHeight;        // How high the fog extends
    }

    // User-editable array of states.
    public WeatherState[] m_states;
    // State we are at, and state we are transitioning toward.
    private int m_currentState;
    private int m_targetState;

    // Random generator for selecting new states
    private System.Random m_rnd = new System.Random();


    // Start is called before the first frame update
    void Start()
    {
        m_currentState = -1;
        m_targetState = -1;
    }

    // Update is called once per frame
    void Update()
    {
        // We need to have at least 2 weather states to work.
        if (m_states.Length < 2) {
            return;
        }

        // Check if we are ready to move to the next weather state, and if we are,
        // begin transitioning toward it.
        if (readyForNextState()) {
            transitionToNextState();
        }

        // Interpolate and set the fog parameters based on the cloud interpolator's interpolation amount.
        m_fog.m_density = Mathf.Lerp(m_states[m_currentState].fogDensity, m_states[m_targetState].fogDensity, m_cloudInterpolator.m_interpolationAmount);
        m_fog.m_thickness = Mathf.Lerp(m_states[m_currentState].fogHeight, m_states[m_targetState].fogHeight, m_cloudInterpolator.m_interpolationAmount);
    }

    bool readyForNextState() {
        // Check if we are ready to pick a new state to transition to. This happens when:
        //  - The cloud interpolator has finished (i.e., is no longer) interpolating
        //  - We just initialized the weather controller and have no state
        return (!m_cloudInterpolator.IsInterpolating() || m_currentState == -1 || m_targetState == -1);
    }

    void transitionToNextState() {
        // Check if we were just initialized and need to load an initial state.
        if (m_targetState == -1) {
            m_targetState = m_rnd.Next(0, m_states.Length);
            m_cloudInterpolator.LoadPreset(m_states[m_targetState].cloudPreset);
        }

        // Pick a new state to transition to that's not the same as the current state.
        // NOTE: it's possible but extremely unlikely that this will time out. You
        // may want to implement a more robust method of avoiding duplicates for your
        // own purposes.
        m_currentState = m_targetState;
        m_targetState = m_rnd.Next(0, m_states.Length);
        while (m_targetState == m_currentState) {
            m_targetState = m_rnd.Next(0, m_states.Length);
        }
        m_cloudInterpolator.LoadPreset(m_states[m_targetState].cloudPreset);
    }
}
```

### Using the Weather Controller

Ok, so we've written this thing---now it's time to test it out. The first thing to do is populate the array with some valid weather states. This is as easy as adding new entries to the UI in the component menu.

> You'll probably see some "empty path name is not legal" warnings print out while you're doing this, since we didn't check if paths were valid in our update loop.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:70%" src="img/interpolation/new-entries.jpg"/></div>
    </div>
    <p>We can easily add new weather presets by increasing the size field and editing the entries in the component editor.</p>
</div>

And now, if we enter play mode, we get beautiful (very, very fast, since the interpolator's still set at 5 seconds) weather transitions!

<div class="img-block">
    <video width="100%" height="100%" class="inline-video" controls>
    <source src="img/interpolation/weather.mp4" type="video/mp4">
    </video>
    <p>Random weather presets!</p>
</div>

## Final Thoughts

Well, hopefully you now have a good sense for how to create a dynamic sky with Expanse! If you're looking for any more fun projects, here are a few ideas for how to augment your weather controller, all possible to build easily on top of the current dynamic system:

* Restrict the other states that a particular weather state can transition to. This would allow you to make weather transitions less random and more semantically meaningful (i.e., a "rainy" state should not be able to go a "sunny" state directly---it should have to go through a "cloudy" state).
* Add scripted weather events---aka, allow outside sources to override the weather state.
* Make volume collision notifications trigger changes in weather states. Something like "if the player enters this forest, make it really foggy".
* Use custom coverage maps to further improve the realism of the weather presets---for instance, making clouds gather around a mountaintop.
* Allow presets to trigger VFX like snow and rain.

Also, as a final note, this work has been coming down the pipe for some time now, and I want to thank Discord user Morpheus101 (Erik) for pioneering a lot of the ideas that led to this strategy being realized in his weather controller for Expanse.