# Gameplay Query Interface

Rendering clouds is great and all, but what if you need to **_know things about them?_**

Put more formally: what if actors in your game need to be able to query the state of the clouds? Your game might require clouds to be more than just a pretty backdrop to the action---you might need the clouds to be a **real, stateful object** that can interact intelligently with the other elements of your game.

Let's ditch the abstract pontificating and make this more concrete. Say you're making a combat-oriented flight sim. You'll need your AI to know if they can see the player through the clouds, so that they perform worse when visibility is low. To do this, you'd need a way to calculate visibility through the clouds between two points from your C# game AI scripts.

Enter Expanse's gameplay query interface.

## Overview

Expanse lets you perform two kinds of queries on the cloud state: **density queries** and **visibility queries**.

**Density queries** take as input a point, and output the density of the clouds at that point. Pretty simple.

**Visibility queries** take as input two points---a start point and an end point---and tell you how visible the end point is from the start point, on a scale from 0 (not at all visible) to 1 (fully visible).

To use the gameplay query interface to query density and visibility, all you have to do is call the following function, implemented in the static class `GameplayQueries` in the `Expanse` namespace (the file is `code/source/main/GameplayQueries.cs`, if you want to take a look at it).

```
public static void Dispatch(QueryInfo query, Action<QueryInfo> callback);
```

It takes as arguments a `QueryInfo` struct, that specifies the nature of the query and has result members that will be filled upon successful completion

```
    public struct QueryInfo {
        // Inputs.
        public Vector3 startWS;         // Start point for visibility query, and also test point for density query.
        public Vector3 endWS;           // End point for visibility query.
        // Outputs.
        public float density;           // Result of density query.
        public float visibility;        // Result of visibility query.
    };
```

and a callback function that will be called when the query is executed, which supplies the fulfilled `QueryInfo` result as a function argument.

Something that might strike you as odd: **there is only one kind of query, and it performs both a density and visibility test.** This is because there's virtually no added performance cost to doing both at the same time, and under the hood it's much more concise to implement it this way. 

You can feel free to ignore any struct members you don't care about. For instance, if you only want to query the density, you can just ignore the `visibility` result and pass in whatever garbage value you like for the `endWS` parameter---though simply using the same value as `startWS` is a good best practice, to avoid unnecessary ray marching. Frankly it doesn't really matter though, because the actual query fulfillment on the GPU is so blazing fast that you'd be hard pressed to notice a difference.

## Example

To get you started, here's a simple MonoBehaviour that acts as a density probe, and also as a visibility probe from the game object it's on to a different specified game object.

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using Expanse;

[ExecuteInEditMode]
public class DensityProbe : MonoBehaviour
{
    // Target of visibility test.
    public Transform target;
    // Private variables for storing query results.
    private float m_density = 0;
    private float m_visibility = 0;

    void Update()
    {
        // Create the query.
        Expanse.GameplayQueries.QueryInfo q;

        // Initialize it with the params we want.
        q.startWS = gameObject.transform.position;  // Visibility start point is the probe's position.
        q.endWS = target.position;                  // Visibility query end point is the target's position.
        q.density = 0;                              // Initialize to null result, to be safe.
        q.visibility = 1;                           // Initialize to null result, to be safe.
        
        // Dispatch it using the static GameplayQueries class.
        Expanse.GameplayQueries.Dispatch(q, OnQueryCompletion);
    }

    // This function is called asynchronously when the query is completed. All we have to do here is update our member variables to reflect the query result.
    void OnQueryCompletion(Expanse.GameplayQueries.QueryInfo q)
    {
        m_visibility = q.visibility;
        m_density = q.density;
    }
}
```

## Usage Ideas

I thought it would be fun to collect a short list of ideas folks have had regarding how this system might be used. There are a lot of cool ideas that go beyond the basic "make my plane AI less dumb" use case, and I don't want them to go unnoticed.


* Obligatory "make my plane AI less dumb".
* Perform visibility tests upwards from the player position to automate rain and snow VFX.
* Perform a density test at various locations to add condensation to materials.
* Perform a density test to add post-processing effects when the player is inside dense cloud cover. This could be used to cover up any noticeable visual artifacts from rendering clouds at lower quality settings.
* Perform visibility tests toward lights to see if the player is in the shadow of a cloud. Could be used to make Geralt of Rivia's "hm, looks like rain" dialogue trigger in a way that's fully accurate to the world state.

I'll keep updating the list as more interesting use cases crop up.

## Design Justification

So you can see that the basic design is pretty simple---it took maybe two paragraphs to describe---but I think it could be useful to go through a little of the motivation behind why it has to be the way it is. Some of you may find it interesting. Some of you might find it dull. Some of you might say "this is super obvious, why write all of this?". Whatever, we're doing it.

Expanse's volumetric cloud layers are stateful objects that are parameterized entirely by the settings you specify in a [Procedural Cloud Volume Block](editor/blocks/procedural_cloud_volume_block.md). However, because the primary reason Expanse exists is to render the sky efficiently, **all of the actual volumetric data for the clouds lives on the GPU**.

This is all well and good when we're rendering them. After all, we render things on the GPU. The trouble comes when we want to make that data accessible to things that run on the CPU, like C# scripts. We can't just copy it all over from the GPU to the CPU, because that would be extremely slow, and we'd have to write an entire library for mirroring all of the sampling operations we have on the GPU on the CPU. So what can we do?

Well, if we construct a **concise, predetermined** interface for querying the state of the clouds, we can...
* **Submit queries** through that interface from C# scripts **on the CPU**.
* **Fulfill those queries on the GPU**, where the cloud state lives and is easily accessible.
* **Copy the query results** from the GPU **back to the CPU**, which comprise much less data than the actual cloud textures themselves (we're talking kilobytes as opposed to tens or hundreds of megabytes).

Right off the bat, there are huge advantages to doing things this way. For one, all queries are automatically executed in parallel, by nature of being executed on the GPU. As a fun test, I tried running 20000 queries on my 2080Ti. It took about **30 microseconds**. Of course the subsequent CPU processing of those queries in C# was outrageously slow, but the point stands: executing queries on the GPU buys us huge performance wins.

Another victory implicit in this design, but maybe so obvious that you may think it's not worth stating, is that **our graphical and logical representations are the same**. In the development of realtime simulation applications, I've found that a lot friction arises from trying to marry disparate representations of the same in-game entity. Often, we simply can't, and we have to resort to simple but largely ineffectual heuristics to tie together a game's visuals, gameplay, physics, et cetera. By querying the exact same GPU-based representation of the clouds for all of our behavioral logic, **we automatically guarantee that the visual and behavioral components of our game are perfectly in sync.** I think this is pretty cool.

So, things being how we've described them, our implementation would probably look something like this, in very vague but hopefully intelligible pseudocode:

```
/**
 * Simple query specification (simpler than the real one, just for illustrative purposes).
 * */
QueryResult {
    Vector3 point; // Input: point to sample density at
    float density; // Output: density
}

/**
 * Fulfills a query of the global cloud data structures.
 * */
void QueryClouds(QueryResult q) {
    // Copy the query info to GPU memory.
    copyToGPU(q);

    // Execute the query on the GPU.
    executeGPUQuery();

    // Read back from the GPU.
    q = readbackQueryFromGPU();
}

...

/**
 * Example usage.
 * */
void MyFunCoolDensityProbe() {
    // Initialize the query result struct with our desired input parameters.
    QueryResult q;
    q.position = new Vector3(0, 0, 0);

    // Actually query the clouds using the interface.
    QueryClouds(q);

    // Print out the result, the cloud density at the point we requested.
    print("Cloud density: " + q.density);
}
```

This is all well and good, but it has one fatal flaw: reading data back from the GPU is **SUPER SLOW**. For reference: no matter how much data you're reading back, it will take a least 1ms, for reasons I won't go into here. We simply can't afford that kind of slowdown if we're targeting to 60FPS for our realtime experience.

Lucky for us, there's a light at the end of the tunnel. If we perform the readback to the CPU **on another thread**, we can do it without stalling our game's main thread, and read everything back with virtually no performance impact. In other words, we will have to make our query interface **asynchronous**.

Taking that into account, we arrive at something that looks a lot more like what we saw in the overview section.

```
QueryResult {
    Vector3 point; // Input: point to sample density at
    float density; // Output: density
}

/**
 * Fulfills a query of the global cloud data structures.
 * */
void QueryClouds(QueryResult q, void (*onAsyncReadback) (QueryResult)) {
    // Copy the query info to GPU memory.
    copyToGPU(q);

    // Execute the query on the GPU.
    executeGPUQuery();

    // Read back from the GPU asynchronously, and execute the callback when the readback is successful.
    readbackQueryFromGPUAsynchronously(onAsyncReadback);
}

...

/**
 * Example usage.
 * */
void MyFunCoolDensityProbe() {
    // Initialize the query result struct with our desired input parameters.
    QueryResult q;
    q.position = new Vector3(0, 0, 0);

    // Actually query the clouds using the interface, but this time supply a callback function to be executed when the query succeeds.
    QueryClouds(q, OnReadback);
}

void OnReadback(QueryResult q) {
    // Print out the result. When this callback is executed, the query has
    // been fulfilled, and the result is the argument to the function.
    print("Cloud density: " + q.density);
}
```

And there you have it! That's why the interface looks how it does. I hope this was interesting to you in some way.