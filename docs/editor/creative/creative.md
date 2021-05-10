# Creative UI

As of v1.1.0, Expanse has some additional components that can make it easier to control and art-direct your sky. These are the *Creative UI Blocks*---so-named because their focus is more on artistic manipulation. At least, moreso than the regular blocks, which expose every parameter of Expanse's enormous physical model. **These blocks will get you the exact same physically-based visual fidelity as the regular blocks UI**. They're just a different way of controlling Expanse's model.

The easiest way to get started with the Creative UI layout is to drag the `Prefabs/Creative Skies/Expanse Creative Sky` prefab into your scene. This has all the creative ui blocks set up for you to use.

To ensure that you can always access the more complicated parameter set if you want, each of the creative ui blocks is backed by regular blocks. For instance, the `Creative Sun` block requires an instance of a `Celestial Body Block` to function. It will then manipulate that celestial body's parameters. If you want more control, you can delete the `Creative Sun` and just directly edit its `Celestial Body Block`.

As a final note, all of the parameter ranges are set up to be physically plausible, to make sure that the sky looks pretty good no matter what you do. If you want to create a very unrealistic sky, you'll probably have to work with the more complex representation.