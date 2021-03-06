# Overview

Expanse's code structure can be broken up into 3 layers: the **Control Blocks**, **Settings**, and **Renderer**. In the following diagram, data flows from left to right along the arrows from the user and the user's scripts all the way to the renderer, which spits out Expanse's image.


<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/api/Arch Diagram.png"/></div>
    </div>
</div>

**Control Blocks** are C# scripts that are essentially wrappers around Expanse's internal settings. These are the things that you interact with when you're working with Expanse in the Unity Editor. If you want to write scripts to control Expanse programmatically, in all likelihood you want to interface with control blocks.

**Settings** refers to Expanse's internal state, specified by the class `ExpanseSettings`. While, from a UI perspective, Expanse is modular, internally its state actually has a completely fixed representation. It's set up this way so that it conforms to [HDRP's custom sky interface](https://docs.unity3d.com/Packages/com.unity.render-pipelines.high-definition@7.2/manual/Creating-a-Custom-Sky.html). Chances are you will not want to work with `ExpanseSettings`---it's much more obtuse to set parameters and you're better off using a control block.

**Renderer** refers to the rest of the Expanse codebase, which is responsible for using the internal state to produce a visual result. This is code that you will likely never need to touch, unless you're rolling a custom solution or fixing a bug.

## Simple Example

Say you have a game where you want to dim the intensity of the sun light when players go underwater. You've got Expanse set up and you're using the Creative UI. How would you go about doing this?

It's really pretty simple. You would,
* Create a MonoBehaviour with a [CreativeSun](editor/creative/creative_sun.md) object as a public member variable.
* Drag your sky's `CreativeSun` component into the slot in your MonoBehaviour that the public member variable creates in the editor.
* In your MonoBehaviour's `Update()` function, adjust the `CreativeSun` field `m_lightBrightness` however you like.

Here's a code snippet for that, named after Discord user `OdderOtter` for first proposing this question and making me realize that this section of the docs was completely empty:

```
using Expanse;

public class OdderOttersSunController : MonoBehaviour {

public Expanse.CreativeSun m_sun;  <--- drag over the Creative Sun component into this slot in Unity's UI 

void Update() {
  m_sun.m_lightBrightness = 1000; <--- or whatever value you want it to be.
}

}
```

In fact, the general pattern for automating Expanse's parameters programmatically is:
* Create a MonoBehaviour with a member variable to hold the Control Block you want to automate.
* Adjust whatever parameters you want to automate in the update loop.
* All public parameters are documented in each Control Block documentation page. For instance, here's the documentation for [Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md).

## Clobbering

Sometimes, you may create a script to automate a block and notice that it doesn't change anything. In this case, it's possible that your script's updates are being clobbered by another script editing the same parameters.

As an example, say you're working with the Creative UI. You write a script to adjust the Procedural Cloud Volume Parameter `m_multipleScatteringAmount`. You notice that this doesn't do anything. This is because the Creative Cloud Volume **also tries to adjust that parameter**. It and your script are competing for access to `m_multipleScatteringAmount` and only one of you will win.

The solution in this case is to either switch to adjusting one of the meta-parameters on the Creative Cloud Volume, like `m_shadow`, or ditch the Creative Cloud Volume altogether and work directly with the Procedural Cloud Volume Block.

## Further Examples

For more examples, check out the source for the Creative UI---it's more or less a collection of scripts that interface with the control blocks. You can also take a look at `DateTimeBlock.cs`, which similarly interfaces with control blocks and not the internal `ExpanseSettings` object.

**Do not** reference the Control Blocks directly. They interact with the `ExpanseSettings` object directly and thus are not good references for writing your own scripts.