# Procedural Stars Block

> Implemented as class `Expanse.ProceduralStarsBlock` in `blocks/ProceduralStarsBlock.cs`

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/stars/star_splash.png"/></div>
    </div>
</div>

This block exposes the settings for Expanse's procedural star field authoring system.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- LIGHTING --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Lighting

Parameters that affect the color and brightness of the stars.

#### Intensity
**C# member variable:** `float m_intensity` \
The intensity of the stars, multiplied through the overall night sky intensity, in Lux. Typically stars have an intensity of `0.1-0.3` Lux. This is usually far too low for reasonable auto-exposure settings though, so a value of around one tenth of the moon's brightness is usually good.

#### Tint
**C# member variable:** `Color m_tint` \
Tint to the star texture. Perfect white, `(255, 255, 255)`, specifies no tint.

#### Temperature Range
**C# member variable:** `Vector2 m_temperatureRange` \
Range of possible random star temperatures, in Kelvin. The accuracy of the blackbody model diminishes for temperatures above `20000K`, use at your own discretion.

#### Temperature Bias
**C# member variable:** `Vector2 m_temperatureBias` \
Biases star temperature toward one end of the range. 0 is biased toward the minimum temperature. 1 is biased toward the maximum temperature. 0.5 is neutral.

<!---------------------------------------------------------------------------------------->
<!--------------------------------------- MODELING --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Modeling

Parameters that affect the orientation, distribution, and shape of the stars.

#### Rotation
**C# member variable:** `Vector3 m_rotation` \
Rotation of the star field, specified as euler angles.

#### Quality
**C# member variable:** `Expanse.Datatypes.Quality m_quality` \
Quality of star texture.

#### High Density Mode
**C# member variable:** `bool m_highDensityMode` \
Activates high density mode, which layers a second detail star texture on top of the primary one. Dense star fields are important for imparting a sense of realism in scenes with minimal light pollution, but can be too much for more stylized skies.

#### Density
**C# member variable:** `float m_density` \
Density of star field.

<div class="img-block">
    <div class="img-row">
        <div class="img-col"><img src="img/stars/density_0.02.jpg"/></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/stars/density_0.2.jpg"/></div>
    </div>
    <div class="img-row">
        <div class="img-col"><img src="img/stars/density_1.jpg"/></div>
    </div>
    <p>Comparison of different star densities. Top: 0.02. Middle 0.2. Bottom: 1.</p>
</div>

#### Size Range
**C# member variable:** `Vector2 m_sizeRange` \
Range of random star sizes.

#### Size Bias
**C# member variable:** `float m_sizeBias` \
Biases star sizes toward one end of the range. 0 is biased toward the minimum size. 1 is biased toward the maximum size. 0.5 is neutral.

#### Intensity Range
**C# member variable:** `float m_intensityRange` \
Range of random star brightnesses.

#### Intensity Bias
**C# member variable:** `float m_intensityBias` \
Biases star intensity toward one end of the range. 0 is biased toward the minimum intensity. 1 is biased toward the maximum intensity. 0.5 is neutral.

<!---------------------------------------------------------------------------------------->
<!------------------------------=--------- TWINKLE --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Twinkle Effect

#### Twinkle
**C# member variable:** `bool m_twinkle` \
Whether or not to use star twinkle effect.

#### Twinkle Threshold
**C# member variable:** `float m_twinkleThreshold` \
Brightness threshold for twinkle effect to be applied.

#### Twinkle Frequency Range
**C# member variable:** `Vector2 m_twinkleFrequencyRange` \
Range of randomly generated twinkle frequencies. Higher values will make the stars twinkle faster. Lower values will make them twinkle slower. A value of zero will result in no twinkling at all.

#### Twinkle Bias
**C# member variable:** `float m_twinkleBias` \
Bias to twinkle effect. Negative values increase the time when the star is not visible.

#### Twinkle Smooth Amplitude
**C# member variable:** `float m_twinkleSmoothAmplitude` \
Intensity of smoother twinkle effect.

#### Twinkle Chaotic Amplitude
**C# member variable:** `float m_twinkleChaoticAmplitude` \
Intensity of more chaotic twinkle effect.


<!---------------------------------------------------------------------------------------->
<!--------------------------------------- METADATA --------------------------------------->
<!---------------------------------------------------------------------------------------->

### Metadata

#### Volume
**C# member variable:** `UnityEngine.Rendering.Volume m_volume` \
The Sky and Fog Volume that backs this texture stars object. In all likelihood, this should be a single global volume you set up at the start of your project. More complex workflows may have multiple local volumes, in which case you will need to create texture stars blocks (one for each volume), or programmatically change this parameter.