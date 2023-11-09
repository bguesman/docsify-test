# Camera Settings

> Implemented as class `Expanse.CameraSettings` in `blocks/CameraSettings.cs`

Component for specifying camera-specific settings that can't be inferred from Unity cameras themselves. Required for Expanse to function.

#### Ambient Probe Camera
**C# member variable:** `Camera m_ambientProbeCamera` \
Camera that Expanse's ambient probe is rendered for. Defaults to the scene's main camera.

#### Prefer Editor Camera
**C# member variable:** `bool m_preferEditorCamera` \
Prefer to render the ambient probe relative to the editor camera if both the editor and main camera are rendering. Chances are you want this enabled.