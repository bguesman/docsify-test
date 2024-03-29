
# Date Time Controller

> Implemented as class `Expanse.DateTimeController` in `blocks/DateTimeController.cs`

This component implements a physical model of the sun/moon position, based on a specified time UTC. It'll also rotate the star texture if you want, using a non-physical strategy. It's best to think of this component more like a high-level controller for other components---you give it a time, and it sets the direction on specified sun/moon Celestial Bodies.

**Important to note:** this component exposes two functions `SetDateTimeUTC()`, and `SetDateTimeLocal()`, that both accept a C# `DateTime` object. The first one will set the time in UTC, disregarding the component's UTC offset. The second will apply the offset before setting the time. If you are maintaining your game's time as a DateTime object, these are useful.

#### Sun
**C# member variable:** `GameObject m_sun` \
Celestial body that will have the position of the sun. It's fine for this to be `None`, if you don't want this component to control the sun direction.

#### Moon
**C# member variable:** `GameObject m_moon` \
Celestial body that will have the position of the soon. It's fine for this to be `None`, if you don't want this component to control the moon direction.

#### Night Sky
**C# member variable:** `GameObject m_nightSky` \
Night sky game object that will rotate as time passes---typically whatever the parent game object is for all of your [Skybox Layers](editor/blocks/skybox_layer.md). It's fine for this to be `None`, if you don't want the night sky to rotate.

#### Night Sky Rotation Speed
**C# member variable:** `Vector3 m_nightSkyRotationSpeed` \
Rotation speed of night sky along each axis.

#### Latitude, Longitude
**C# member variable:** `float m_latitude` and `float m_longitude` \
Latitude and longitude coordinate, in degrees. The latitude/longitude coordinate you'd supply for San Francisco is around 37, 122.

#### Sky Offset
**C# member variable:** `float m_skyOffset` \
Azimuthal offset to the sky rotation, in degrees. This is useful if Expanse's notion of East and West doesn't map to your game's---in this case, you would set this to `180`.

#### Time UTC Offset
**C# member variable:** `float m_timeUTCOffset` \
Offset to the UTC time, in hours. This parameter is useful for localizing your time to a particular time zone. This is specified as the number of hours ahead your location is. In Munich, 12:00 local time is 10:00 UTC, so you would set this parameter to 2.

It's crucial that this corresponds approximately to your latitude/longitude coordinate, if you want reasonable results.

#### Time Local
**C# member variable:** `SunTime m_timeLocal` \
Local time, according to the UTC offset. The `SunTime` struct is a custom serializable class that's a standin for C#'s (non-serializable) `DateTime`. If you want to control this component via a `DateTime`, you can use this component's public `SetDateTimeLocal()` or `SetDateTimeUTC()` function, which both accept a `DateTime` object.

If you choose to use the `SunTime` class directly, here is the specification.
```
class SunTime {
    int year;
    int month;
    int day;
    int hour;
    int minute;
    int second;
    int millisecond;
}
```