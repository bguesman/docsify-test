# Tutorial: Modeling the Earth's Atmosphere

When you were a kid, at some point you probably asked your parents, "Why is the sky blue?". Sadly, the science of atmospheric scattering isn't going to help you resolve any of your outstanding metaphysical struggles. That said, it will be massively helpful in reducing this physical phenomenon down to something we can model with a computer.

In this tutorial, we'll walk through the basics of reconstructing the atmosphere of the Earth with Expanse. This should give you the foundational knowledge you need to simulate all sorts of different atmospheric conditions---on our planet and beyond!

**As a note: you should probably complete the [quickstart tutorial](/quickstart/quickstart) before you dive into anything else, including this tutorial.** Additionally, this tutorial assumes a very small but non-negligible amount of familiarity with Unity---you should know how to create an empty game object, and how to open the inspector.

## The Science Of Atmospheric Scattering

>**Important note:** this section attempts to give an intuition for the physics of light transport. It will not give an account that is fully true-to-life, and will gloss over many details. For a formal treatment, I recommend the [SIGGRAPH 2017 Production Volume Rendering Course](https://graphics.pixar.com/library/ProductionVolumeRendering/paper.pdf).

At the simplest level, the color of the sky is the result of light interacting with the gases in the atmosphere. Just like objects have a color when you shine a light on them, smoke, clouds, and other gases become visible when they are illuminated.

The appearance of an illuminated gas is determined by different properties that describe the way light interacts with it. These properties, fundamentally, are molecular properties of the particles that make up the gas. However, for our purposes, we can simplify things a little bit.

We will make the assumption that gases interact with light in two ways: they **absorb** it, and they **scatter** it.

**Absorption** (or, more technically, **extinction**) is the process of light being attenuated by the gas. Imagine you're standing in front of a fire, with big billowing tufts of smoke in front of it. You'll kind of be able to see through the smoke, but not fully. Some of the photons bouncing off of objects behind the smoke will be absorbed or deflected away by the smoke on their way to your eye. This is absorption at work.

**Scattering** (more specifically, **in-scattering**) is the process of light being "reflected" by the gas into your eye. Say a photon enters into the gas, bounces off a gas particle, and into your eye. We would say that the photon had been "scattered in" to your eye. If we say that **absorption** is responsible for blocking things behind the gas, **scattering** is responsible for giving the gas its illuminated color.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/no_scatter.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/scatter.jpg"/></div>
    </div>
    <p>Left: a volume of gas, set up to only absorb light. So, as you can see, it's pretty much just black (with a little bit of blue fog from the surrounding atmosphere). Right: the same volume, but with scattering added in. Now it both "reflects" and "absorbs" light.</p>
</div>

How might we describe the absorption and scattering characteristics of a gas? Well, for one thing, we know that thicker smoke appears more solid, so much that sometimes you can't even see through it. So we know that **density** must play a role.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/density_10.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/density_50.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/density_250.jpg"/></div>
    </div>
    <p>The same volume of gas, but rendered at different densities. Left: density of 10. Middle: density of 50. Right: density of 250. Notice how, as the density increases, the mountain behind the gas volume gets less visible (so, absorption increases), but the gas also gets brighter (scattering also increases).</p>
</div>

The rest of the physical parameters are, more or less, molecular. So we'll make the observation that absorption and scattering are often **wavelength-dependent**---i.e., they are different for different colors of light---and we'll specify a scattering color and an absorption color. **The scattering color** determines what color of light gets scattered. **The absorption color** determines what color of light gets absorbed.

In the literature, we refer to these colors as the **scattering and extinction coefficients**. We multiply them by the density of the gas so that thick gas absorbs and scatters more light, and thin gas absorbs and scatters less.

The bulk of Earth's atmosphere has blue scattering and absorption coefficients. This means at daytime, when there's not much gas to go through, blue light mostly gets scattered by the gas into our eyes and the sky appears blue. But, at sunset, when the light has to make it all the way through from the horizon line, the absorption effect starts to become visible. Lots of blue light is absorbed. The leftover light is opposite to blue on the color wheel---orange! So this is why Earth has a blue sky during the day, but red/orange sunsets in the evening.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/no_scatter_extinction_yellow.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/scatter_red_extinction_yellow.jpg"/></div>
    </div>
    <p>Some fun examples. Left: extinction yellow, with no scattering. The gas absorbs yellow light, so the light you see is blue! Right: extinction yellow, scattering red. The gas now also scatters red light, so you can see that the gas is a gradient of red, pink, and purple---mixes of red (from the scattering) and blue (from the absorption). In particular, at the edges, it's mostly red, and in the middle, where you're looking through a thicker volume of gas, it's more purple.</p>
</div>


## The Composition of the Earth's Atmosphere

Now that we've got a basic understanding of what makes volumes of gas look like they do, we can start to think about how we might model the atmosphere. From a bird's eye view, we want to answer the following question: **what physical information do we need to know about the gases in Earth's atmosphere to render a convincing reproduction of the color of the sky?**

To make things easier for ourselves, we'll break up the gases in Earth's atmosphere into **four categories.**
1. **Air**. This covers the mixture of molecular gases that make up the bulk of the Earth's atmosphere---Oxygen, Nitrogen, Carbon Dioxide, and some others. This layer is responsible for the blue color of the sky.
2. **Ozone**. This layer of toxic gas concentrated high up in the atmosphere is crucial to model for its absorptive properties.
3. **Aerosols**. This is particulate matter distributed in the air, like smog, smoke, and dust.
4. **Clouds**. Clouds are small liquid or frozen water droplets suspended in the air---in this sense, they are technically also an aerosol! You might not think of clouds as "part of the atmosphere", but they are. However, since they are very different in appearance from the other atmospheric phenomena, we'll have to use a different strategy for modeling them. They won't be discussed here, except for this mention.

Returning to our big guiding question, we can list out what we might need to know about each of these "atmosphere layers" to be able to reproduce it with a volumetric rendering algorithm.

* According to our recent discussion, we'll definitely need to know their **scattering and extinction coefficients**.
* We'll also need to know their densities---however, this is a slightly more complicated matter. The density of gases on Earth varies quite a bit from place to place. So, more specifically, we'll need to know their **density distributions**.

Along the way, we'll also discover that there are a few more things we'll need, but this is a good starting place. So... let's dive in!


### Modeling Air

Fortunately, the scattering and extinction coefficients for air have been well-studied, and there are plenty of sources that list values that are all nearly the same. We can take ours from the 2020 SIGGRAPH Paper [*A Scalable and Production Ready Sky and Atmosphere Rendering Technique*](https://sebh.github.io/publications/egsr2020.pdf), which, by the way, much of Expanse's atmosphere rendering solution is based on.

Let's take the coefficients from that paper and start to build a table to keep track of everything we learn.

|Layer |Extinction Coeff. |Scattering Coeff. |
|----- |----------------- |----------------- |
| Air | `(5.8e-6, 13.6e-6, 33.1e-6)` | `(5.8e-6, 13.6e-6, 33.1e-6)` |

With that handled, we can move on to the density distribution. Luckily for us, the density of air does not vary all that much from place to place. However, it does decrease the higher up you get from the Earth's surface. This means that the atmosphere displays this beautiful gradient from the surface up out into space.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="https://www.techexplorist.com/wp-content/uploads/2020/07/earth-atmosphere.jpg"/></div>
    </div>
    <p>This is real picture of the Earth's atmosphere, not Expanse! Source: <a href="https://www.techexplorist.com/wp-content/uploads/2020/07/earth-atmosphere.jpg">https://www.techexplorist.com/wp-content/uploads/2020/07/earth-atmosphere.jpg</a></p>
</div>

We can model the density distribution as exponentially decreasing with the height above the surface. We'll parameterize the decrease by two numbers: 
* **Thickness**, or the more technical term, **scale height**, that determines how far out into space the atmosphere extends.
* **Density**, which tells us how dense the layer is at the ground.

The appropriate thickness value for Earth's air layer is `8000` meters, and the right density value (using our scattering and extinction coefficients) is just `1`. Let's extend our table.

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness |
|----- |----------------- |----------------- |-------------- |-------- |---------- |
| Air  | `(5.8e-6, 13.6e-6, 33.1e-6)` | `(5.8e-6, 13.6e-6, 33.1e-6)` | Exponential | 1 | 8000 |

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/atmo_3000.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/atmo_8000.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/atmo_15000.jpg"/></div>
    </div>
    <p>Comparison of different scale heights for the simulated atmosphere. Left: scale height 3000. Middle: scale height 8000, a good value for Earth. Right: scale height 15000.</p>
</div>

There's another important piece of the puzzle---**directionality**. Depending on the size of the particles involved in the individual scattering events, light will tend to scatter more in some directions, and less in others. In other words, the scattering behavior for gases is usually **anisotropic**, or **directional**, to some degree. 

This behavior is captured by something called the **phase function**. We won't go into the details of how it works, but it determines what sort of anisotropy a volume of gas exhibits. For air, the appropriate phase function to use is the **Rayleigh Phase Function**. This is the phase function to use when the particles are much smaller than the wavelength of the light they're scattering.

To see the affect this has, here's a comparison between an isotropic (non-directional) phase function, and the correct Rayleigh phase function.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/isotropic.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/rayleigh.jpg"/></div>
    </div>
    <p>Comparison of isotropic (incorrect) and Rayleigh (correct) phase functions. Left: isotropic phase function. Right: the correct Rayleigh phase function. Notice how the sky is brighter around the sun disc---directionality! The effect is subtle, but important.</p>
</div>

Now, we can update our table, with all the info we need about the air to model it as an atmosphere layer!

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness | Phase Func. |
|----- |----------------- |----------------- |-------------- |-------- |---------- |------------ |
| Air  | `(5.8e-6, 13.6e-6, 33.1e-6)` | `(5.802e-6, 13.6e-6, 33.1e-6)` | Exponential | 1 | 8000 | Rayleigh |


### Modeling The Ozone

The ozone layer is a band of toxic gas concentrated at around `25000m` up in the atmosphere. 

It is very good at absorbing harmful ultraviolet light---part of why it's so critical to the Earth's biosphere. It's actually purely absorptive, so it does not scatter light. This means that its scattering coefficients are zero! For the absorption coefficients, we'll reference [the same paper as we did for the air](https://sebh.github.io/publications/egsr2020.pdf).

To model the density, we'll use a **tent** distribution, which distributes the gas in a band with a certain **thickness** at a certain **height**. We'll use a pretty wide thickness, since the density will slope away from the height value down to zero at the edges of the distribution.

The correct phase function to use here is also the Rayleigh phase function---the same as for the air.

With all that info down, let's add it as an entry to our table!

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness | Height | Phase Func. |
|----- |----------------- |----------------- |-------------- |-------- |---------- |------- |------------ |
| Air  | `(5.8e-6, 13.6e-6, 33.1e-6)` | `(5.802e-6, 13.6e-6, 33.1e-6)` | Exponential | 1 | 8000 | - - - | Rayleigh |
| Ozone  | `(0.65e-6, 1.88e-6, 0.085e-6)` | `(0, 0, 0)` | Tent | 0-1 | 30000 | 25000 | Rayleigh |

The effect of the ozone is particularly visible during the sunset, where it shifts the sky color towards purple.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/no_ozone.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/ozone_0.5.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/ozone_1.jpg"/></div>
    </div>
    <p>Comparison of different ozone densities at sunset. Left: no ozone. This looks a bit too green. Middle: ozone density of 0.5. This looks pretty good. Right: ozone density of 1. This might be a bit more purple than the sky actually looks.</p>
</div>


### Modeling Aerosols

Aerosols are more challenging to model, since there are all sorts of things in the sky that could be considered "aerosols". Fog, smog, sandstorms, mist---all of these fall under the general banner of aerosols.

Luckily, Expanse will simultaneously render up to eight different atmosphere layers, so you don't have to choose. To keep this tutorial straightforward though, we'll try to model general smog/pollution/dust.

Smog and dust density varies widely from place to place on Earth. On a particularly bad day in Beijing, you might not even be able to see the sun dip below the horizon. On an empty, remote beach, the sky might be clean and clear.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="http://www.tour-beijing.com/real_time_weather_photo/wp-content/uploads/The-sun-is-rising-in-FengtaiBeijing.jpg"/></div>
        <div class="img-col"><img src="https://images.fineartamerica.com/images/artworkimages/mediumlarge/1/clear-sunset-clara-sue-beym.jpg"/></div>
    </div>
    <p>Left: a smoggy sunset in Beijing. Right: a clear sunset over the ocean. Sources: <a href="http://www.tour-beijing.com/real_time_weather_photo/wp-content/uploads/The-sun-is-rising-in-FengtaiBeijing.jpg">http://www.tour-beijing.com/real_time_weather_photo/wp-content/uploads/The-sun-is-rising-in-FengtaiBeijing.jpg</a> and <a href="https://images.fineartamerica.com/images/artworkimages/mediumlarge/1/clear-sunset-clara-sue-beym.jpg">https://images.fineartamerica.com/images/artworkimages/mediumlarge/1/clear-sunset-clara-sue-beym.jpg</a>.</p>
</div>

We'll try our best to create a "one-size-fits-all" model.

Most of the smog and dust is concentrated around the ground, where it originates from, so the exponential density distribution makes sense here. We'll use a thickness of `1200` meters, but your mileage with that number may vary.

We'll again use [Sebastien Hillaire's paper](https://sebh.github.io/publications/egsr2020.pdf) to guide our coefficient choices. He suggests a value of `(4e-6, 4e-6, 4e-6)` for scattering and `(4.4e-6, 4.4e-6, 4.4e-6)` for extinction. This is consistent with other resources on the topic.

We've discussed the Rayleigh phase function, useful for modeling anisotropic scattering when the particles in question much smaller than the wavelength of light being scattered. Dust particles, however, are roughly the same size as the wavelength of visible light, so they scatter anisotropically according to [Mie Theory](https://en.wikipedia.org/wiki/Mie_scattering).

The correct phase function to use is thus the **Mie Phase Function**. Expanse's realtime approximation to the very complex ground truth Mie phase function exposes one parameter, **anisotropy**, that lets you determine how directional the scattering is. A good value for smog and dust is 0.76.

So, with that, our table is complete! We have all the information we need to construct a model of Earth's atmosphere. Let's take a look at how we can do it in Unity with Expanse.

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness | Height | Phase Func. | Anisotropy |
|----- |----------------- |----------------- |-------------- |-------- |---------- |------- |------------ |----------- |
| Air  | `(5.8e-6, 13.6e-6, 33.1e-6)` | `(5.802e-6, 13.6e-6, 33.1e-6)` | Exponential | 1 | 8000 | - - - | Rayleigh | - - - |
| Ozone  | `(0.65e-6, 1.88e-6, 0.085e-6)` | `(0, 0, 0)` | Tent | 0-1 | 30000 | 25000 | Rayleigh | - - - |
| Aerosols  | `(4.4-6, 4.4e-6, 4.4e-6)` | `(4e-6, 4e-6, 4e-6)` | Exponential | 0-10 | 1200 | - - - | Mie | 0.76 |

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/no_aerosol.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/aerosol_1.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/aerosol_10.jpg"/></div>
    </div>
    <p>Comparison of different aerosol densities. Left: 0, so there's no dust or smog. This is rarely the case on Earth. Middle: 1, so some light aerosols. This might be what you see somewhere in the suburbs. Right: 10, so a denser smog (not quite like that in Beijing though).</p>
</div>

## Setting It Up In Expanse

So, in theory, we've got all the info we need about Earth's atmosphere to render a convincing reproduction, set up here in this table.

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness | Height | Phase Func. | Anisotropy |
|----- |----------------- |----------------- |-------------- |-------- |---------- |------- |------------ |----------- |
| Air  | `(5.8e-6, 13.6e-6, 33.1e-6)` | `(5.802e-6, 13.6e-6, 33.1e-6)` | Exponential | 1 | 8000 | - - - | Rayleigh | - - - |
| Ozone  | `(0.65e-6, 1.88e-6, 0.085e-6)` | `(0, 0, 0)` | Tent | 0-1 | 30000 | 25000 | Rayleigh | - - - |
| Aerosols  | `(4.4-6, 4.4e-6, 4.4e-6)` | `(4e-6, 4e-6, 4e-6)` | Exponential | 0-10 | 1200 | - - - | Mie | 0.76 |

Now, let's set up our atmosphere in Unity. 

### Prefab Setup

To make things easier, we're gonna start with one of Expanse's **full sky prefabs**. These already have a sun, moon, stars, and some other things set up---this will reduce the irrelevant boilerplate setup we have to do.

Open up your Unity project and drag in the prefab `Assets/Expanse/prefabs/Full Skies/Expanse Cloudless Sky.prefab`. For details on importing Expanse into your project, and rendering your first prefab sky, refer to the [quickstart guide](/quickstart/quickstart).

Next, we're going to delete the `Earth Atmosphere` game object underneath this prefab. If you get a pop-up that says deleting it isn't possible, right click the parent `Expanse Cloudless Sky` game object, and select `Prefab->Unpack`. Now you should be able to delete it.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/modeling_atmo/delete_atmo.jpg"/></div>
    </div>
    <p>Delete the atmosphere game object, so we can build it up again ourselves.</p>
</div>

Without the atmosphere, you should just see a black sky!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/modeling_atmo/no_sky.jpg"/></div>
    </div>
    <p>The result of deleting the atmosphere.</p>
</div>

### Air Block

Now, we're going to build up our own atmosphere. Let's create an empty game object underneath the Expanse Cloudless Sky game object, and call it `Custom Atmosphere`---or whatever else you want. It doesn't matter where this game object goes in your scene, but to keep things neat, it can be good to keep all your Expanse game objects near each other.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:50%" src="img/quickstart/modeling_atmo/custom_atmo.jpg"/></div>
    </div>
    <p>Create an empty game object called `Custom Atmosphere`.</p>
</div>

Now, open up the new empty game object in the inspector, and add an `Atmosphere Layer Block` component. 

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/add_atmo_block.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/added_atmo_block.jpg"/></div>
    </div>
    <p>Add an Atmosphere Layer Block to the Custom Atmosphere game object.</p>
</div>

For every category of gas you want in your atmosphere, you'll create an `Atmosphere Layer Block` component. So, in our case, we'll ultimately be creating 3: one for air, one for the ozone, and one for aerosols. This is one of the ways Expanse's UI is *modular*. Instead of having one big UI, Expanse breaks its UI into small components that you can add and remove depending on what you need.

For now, just stick to one layer. Looking at the controls in the component, you'll recognize a lot of familiar faces! Settings for the scattering and extinction coefficients, the density distribution, and the phase function are all right there. There are also a few more parameters that you probably won't recognize---these you can learn more about in the [Atmosphere Layer Block Documentation](/editor/blocks/atmosphere_layer_block).

Before we touch any of those parameters, we have to drag the Sky and Fog Volume that backs Expanse into its slot in the layer block component.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:75%" src="img/quickstart/modeling_atmo/drag.jpg"/></div>
    </div>
    <p>Drag the sky and fog volume into its slot in the layer block.</p>
</div>

The screen might still be black. **To fix this, enter play mode, and then leave play mode.** Sometimes you need to do this in order for the change in the layer to register. **Enabling and disabling the parent `Expanse Cloudless Sky` game object should also fix the issue.**

Now, you should see the sky! If you look closely at the settings, you'll see that this layer is automatically set up to use the **Air** settings in our table. We will make one change though, which is to change the name field from `defaultLayerName` to `Air`. This name gets used in debug printouts, so it's good practice to use an informative name.

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness | Height | Phase Func. | Anisotropy |
|----- |----------------- |----------------- |-------------- |-------- |---------- |------- |------------ |----------- |
| Air  | `(5.8e-6, 13.6e-6, 33.1e-6)` | `(5.802e-6, 13.6e-6, 33.1e-6)` | Exponential | 1 | 8000 | - - - | Rayleigh | - - - |

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/modeling_atmo/air.jpg"/></div>
    </div>
    <p>The sky, using one atmosphere layer block to model the air layer.</p>
</div>

### Ozone Block

Now, let's add another `Atmosphere Layer Block` for the ozone. This time, we'll input the information ourselves. First, like we did for the air layer block, we'll drag the sky and fog volume onto the volume slot, and change the layer's name to `Ozone`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/modeling_atmo/ozone_step_1.jpg"/></div>
    </div>
    <p>Add a new block for the ozone, and set up the sky and fog volume reference, and set the layer name to `Ozone`.</p>
</div>

Now, let's fill out the settings from our table.

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness | Height | Phase Func. | Anisotropy |
|----- |----------------- |----------------- |-------------- |-------- |---------- |------- |------------ |----------- |
| Ozone  | `(0.65e-6, 1.88e-6, 0.085e-6)` | `(0, 0, 0)` | Tent | 0-1 | 30000 | 25000 | Rayleigh | - - - |

First, we'll change the density distribution to `Tent`. We'll then set the density to `1`, the thickness to `30000`, and the height to `25000`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/modeling_atmo/ozone_step_2.jpg"/></div>
    </div>
    <p>Copy the density settings for the ozone to its layer block.</p>
</div>

We'll then copy the scattering and extinction coefficients from our table into the HDR color slots. The easiest way to do this is to open up the color picker, select `RGB 0-1.0` mode, and type in each coefficient. A reminder: the extinction coefficients are `(0.65e-6, 1.88e-6, 0.085e-6)`, in RGB order, and the scattering coefficients are all `0`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/ozone_step_3.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/ozone_color_pick.jpg"/></div>
    </div>
    <p>Copy in the scattering and extinction coefficients, using the color picker's RGB 0-1.0 mode.</p>
</div>

Finally, we'll select the `Rayleigh` phase function (it actually should already be selected by default).

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/modeling_atmo/ozone_step_4.jpg"/></div>
    </div>
    <p>Select the Rayleigh phase function.</p>
</div>

Now, we've got a sky with an ozone layer! You can play around with the density parameter to make the sky more or less purple.
<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/sky_ozone.jpg"/></div>
    </div>
    <p>Sky with air and ozone layers.</p>
</div>

### Aerosol Block

Let's now move on to creating our aerosol layer. Once again, this is mostly just a matter of transferring our table values to another layer block we'll create.

|Layer |Extinction Coeff. |Scattering Coeff. | Density Dist. | Density | Thickness | Height | Phase Func. | Anisotropy |
|----- |----------------- |----------------- |-------------- |-------- |---------- |------- |------------ |----------- |
| Aerosols  | `(4.4-6, 4.4e-6, 4.4e-6)` | `(4e-6, 4e-6, 4e-6)` | Exponential | 0-10 | 1200 | - - - | Mie | 0.76 |

Add a third `Atmosphere Layer Block`. Drag the sky and fog volume onto the volume slot, and change the layer's name to `Aerosols`.

Set the density distribution to `Exponential` (should already be selected), the density to `1`, and the height to `1200`.

Set the extinction coefficients to `(4.4-6, 4.4e-6, 4.4e-6)`, and the scattering coefficients to `(4e-6, 4e-6, 4e-6)`.

Finally, select the `Mie` phase function, and set the anisotropy to `0.76`.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img style="width:60%" src="img/quickstart/modeling_atmo/aerosols_step_4.jpg"/></div>
    </div>
    <p>Select the Mie phase function and set the anisotropy to 0.76.</p>
</div>

And that's it! We've created all three of our atmosphere layer blocks---we have a model of the Earth's atmosphere, being rendered in realtime right before our eyes!

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/sky_full.jpg"/></div>
    </div>
    <p>The sky with all three atmosphere components---air, ozone, and aerosols.</p>
</div>

## Adjustments and Fine Tuning

You still might not be satisfied with the way the atmosphere looks---luckily, there's lots of ways you can tweak the appearance.

For starters, the ozone density control can give you control over how purple your sunsets are.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/no_ozone.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/ozone_0.5.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/ozone_1.jpg"/></div>
    </div>
    <p>Comparison of different ozone densities at sunset. Left: no ozone. This looks a bit too green. Middle: ozone density of 0.5. This looks pretty good. Right: ozone density of 1. This might be a bit more purple than the sky actually looks.</p>
</div>

Also, since the aerosols layer is such a subjective thing, you can tweak the settings quite a bit and still be within the range of reasonable values for the atmosphere.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/aerosol_dense_bright.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/aerosol_thick.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/aerosol_tight_phase.jpg"/></div>
    </div>
    <p>Comparison of different aerosol layer settings. Left: higher density. Middle: higher density, but also thicker. Right: tighter anisotropy.</p>
</div>

If you're dissatisfied with the overall color of the sky, you can either adjust the air layer's scattering coefficients, or use the non-physical [tint](/editor/blocks/atmosphere_layer_block?id=tint) parameter. If you do adjust the scattering coefficents, **make sure that they are never greater than the extinction coefficients, if you want your sky to remain physical.**

Be careful adjusting the air layer's extinction coefficients, because this can change the light color at sunset.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/quickstart/modeling_atmo/air_tint_1.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/air_tint_2.jpg"/></div>
        <div class="img-col"><img src="img/quickstart/modeling_atmo/air_tint_3.jpg"/></div>
    </div>
    <p>Some different adjustments to the air layer's scattering coefficients.</p>
</div>

Of course, this is just the beginning of what's possible with Expanse's atmosphere layer system. I encourage you to play around with different configurations, and to fully explore [all the settings described in the documentation!](/editor/blocks/atmosphere_layer_block)

Now that you know how to set up Earth's atmosphere, [continue on to learn how to blanket your environments in beautiful volumetric fog.](/quickstart/fog)