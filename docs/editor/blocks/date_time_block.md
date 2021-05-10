
# Date Time Block

> Implemented as class `Expanse.DateTimeBlock` in `blocks/DateTimeBlock.cs`

This block implements a physical model of the sun/moon position, based on a specified time UTC. It'll also rotate the star texture if you want, using a non-physical strategy. It's best to think of this block more like a high-level controller for other blocks---you give it a time, and it sets the direction on specified sun/moon Celestial Body Blocks.

**Important to note:** this block exposes a function `SetDateTime()` that accepts a C# `DateTime` object. This will convert the time to UTC and set the block's internal time representation. If you are maintaining your game's time as a DateTime object, this is useful.

#### Sun
**C# member variable:** `Expanse.CelestialBodyBlock m_sun` \
Celestial body block that will have the position of the sun. It's fine for this to be `None`, if you don't want this block to control the sun direction.

#### Moon
**C# member variable:** `Expanse.CelestialBodyBlock m_moon` \
Celestial body block that will have the position of the soon. It's fine for this to be `None`, if you don't want this block to control the moon direction.

#### Night Sky
**C# member variable:** `Expanse.NightSkyBlock m_nightSky` \
Night sky block that will rotate as time passes. It's fine for this to be `None`, if you don't want the night sky to rotate.

#### Night Sky
**C# member variable:** `Expanse.NightSkyBlock m_nightSky` \
Night sky block that will rotate as time passes. It's fine for this to be `None`, if you don't want the night sky to rotate.

#### Night Sky Rotation Speed
**C# member variable:** `Vector3 m_nightSkyRotationSpeed` \
Rotation speed of night sky along each axis.

#### Latitude, Longitude
**C# member variable:** `float m_latitude` and `float m_longitude` \
Latitude and longitude coordinate, in degrees. The latitude/longitude coordinate you'd supply for San Francisco is around 37, 122.

#### Time UTC
**C# member variable:** `SunTime m_timeUTC` \
Time in UTC (Universal Time). The `SunTime` struct is a custom serializable class that's a standin for C#'s (non-serializable) `DateTime`. If you want to control this block via a `DateTime`, you can use this block's public `SetDateTime()` function, which accepts a `DateTime` object.

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