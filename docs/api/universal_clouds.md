# Universal Cloud Layers

In 1.3.0, the **Universal Cloud Layer** specification was introduced. This is a unified representation of an Expanse cloud layer that is 

* Designed to be easily serialized and saved (good for presets)
* GPU-mirrorable with minimal effort (all `.hlsl` classes are auto-generated)
* Natively interpolable (hopefully coming soon)

All Expanse cloud layers---at the time of writing this, the [procedural cloud volume block](/editor/blocks/procedural_cloud_volume_block), [procedural cloud plane block](/editor/blocks/procedural_cloud_plane_block), and [texture cloud plane block](/editor/blocks/texture_cloud_plane_block)---conform to this specification by nature of extending the `BaseCloudLayerBlock.cs` class. This class has the following two functions:

```
/**
 * @return: Universal representation of this cloud layer.
 * */
public abstract UniversalCloudLayer ToUniversal();

/**
 * @brief: Set this cloud layer's values from a universal representation.
 * Is allowed to not handle certain attributes.
 * */
public abstract void FromUniversal(UniversalCloudLayer from);
```

In plain English: every `BaseCloudLayerBlock` must be able to be converted to and from a `UniversalCloudLayer`. In this sense, you can sort of think about each of the cloud blocks as just convenient, scriptable wrappers around Expanse's *actual* representation of cloud state---the universal layer. 

It's really always been this way, since v1.0.0; Expanse used to compress all the cloud settings down into a GPU-mirrorable struct for rendering, it just happened behind the scenes. The 1.3.0 update just makes this explicit and leverages it to implement things like universal presets and (again, hopefully) preset interpolation in a convenient, unified way.

For now, it's not too important for anyone to know how this works, if they choose to interact with clouds via script. However, in the near future, it may become relevant for controlling preset interpolation, since universal layers are the things that will be interpolable.