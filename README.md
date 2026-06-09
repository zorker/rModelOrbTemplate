# rModelOrbTemplate

The inspiration for this addon was to bring Diablo like player unitframe orbs for health and power back into the game. I did something similar years prior.

Addon: https://addons.wago.io/addons/rmodelorbtemplate

The idea was to create an XML template that houses all frame types needed so you can spawn as many of them as you like.

To make the template visually appealing a ModelScene is used which houses a WoW model that fits into the orb environment. The addon comes with over 200 preconfigured models that can be picked from.

The orb art consists of different layers.

## XML structure/layers

- orbFrame (Frame, 256x256)
  - BackgroundTexture (Texture, TOPLEFT+BOTTOMRIGHT)
  - FillingStatusBar (StatusBar, 256x256, BOTTOMLEFT, Vertical Filling)
  - ClipFrame (Frame, 256x256, BOTTOMLEFT)
    - ModelFrame (ModelScene, 256x256, BOTTOMLEFT)
  - OverlayFrame (Frame, TOPLEFT+BOTTOMRIGHT)
    - SparkTexture (Texture, TOP)
    - GlowTexture (Texture, TOPLEFT+BOTTOMRIGHT)
    - LowHealthTexture (Texture, TOPLEFT+BOTTOMRIGHT)
    - GlossTexture  (Texture, TOPLEFT+BOTTOMRIGHT)

#### BackgroundTexture

A 256x256 background texture that lives behind all the other art.

#### FillingStatusBar

A vertically filled statusbar with a circular/orb filling texture.

#### ClipFrame

A helper frame which is anchored to the BOTTOMLEFT. The height of the frame is dependent on the FillingStatusBar:GetStatusBarTexture():GetPoint() and adapts dynamically when the filling statusbar changes value. It clips the Model Scene.

#### ModelFrame

A ModelScene which housed the model. Over 200 preconfigured models are provided. More on that later in the camera section.

#### OverlayFrame

Just a frame on top of the clip frame to house some more textures on another layer.

#### SparkTexture

To give the orb value a bit more dimension a spark texture is used. This is a horizonta divider texture which is bound by a circular mask texture. Same as the ClipFrame its point is reliant on the FillingStatusBar:GetStatusBarTexture():GetPoint() and adapts dynamically when the filling statusbar changes value. The circular mask texture is needed to clip the spark texture to stay believable inside the orb.

#### GlowTexture

A glow texture that can be used for debuff highlighting.

#### LowHealthTexture

A secondary glow texture that can be used for low health highlighting.

#### GlossTexture

This of this as a final gloss layer ontop of everything. Sort of like the crystal top on a pocket watch.

## ZorkCamera

To position a model correctly inside a ModelScene an actor and a camera is needed. The actor will always be at vector [0,0,0].

Blizzard provides the OrbitCameraMixin which I adapted into ZorkCameraMixin. That allows for dynamic model configuration on the fly using mouse controls. That to the orbit camera functions the camera will always move around a target at a given distance. Our target never moves. It is the actor at [0,0,0]. That is why the camera target can stay at [0,0,0] aswell.

The base camera functions are as follows

```lua
--left
self:SetLeftMouseButtonXMode(ZORK_CAMERA_MOUSE_MODE_YAW_ROTATION, true)
self:SetLeftMouseButtonYMode(ZORK_CAMERA_MOUSE_MODE_PITCH_ROTATION, true)
--right
self:SetRightMouseButtonXMode(ZORK_CAMERA_MOUSE_PAN_HORIZONTAL, true)
self:SetRightMouseButtonYMode(ZORK_CAMERA_MOUSE_PAN_VERTICAL, true)
--NEW: m4
self:SetMouse4ButtonXMode(ZORK_CAMERA_MOUSE_MODE_NOTHING, true)
self:SetMouse4ButtonYMode(ZORK_CAMERA_MOUSE_MODE_ROLL_ROTATION, true)
--NEW: m5
self:SetMouse5ButtonXMode(ZORK_CAMERA_MOUSE_MODE_NOTHING, true)
self:SetMouse5ButtonYMode(ZORK_CAMERA_MOUSE_MODE_ROLL_ROTATION, true)
--wheel
self:SetMouseWheelMode(ZORK_CAMERA_MOUSE_MODE_ZOOM, true)
```

With right mouse you can offset the camera in x/y.
With left mouse you can yaw/pitch the camera.
With m4 or m5 you can roll the camera.
With mweel you can zoom the camera in/out of the target.

Shift and Alt modifiers are added to only move the camera on x/y or to increase/decrease zoom speed.

## How to spawn the orb

```lua
local orb = CreateFrame("Frame", nil, UIParent, "rModelOrbTemplate")

--get all model data, a list of all 200+ preconfigured models. The index-key is the modelFileID.
--local allModelData = orb:GetAllModelData()

--LoadModelDataByID(modelFileID, enableMouse)
--modelFileID (has to be part of allModelData), enableMouse true/false
--if enableMouse is true you can dynamically adjust the model configuration using the mouse. Changes are saved on the fly for the selected model.
orb:LoadModelDataByID(2030216, true)

orb:SetPoint("CENTER")
orb.FillingStatusBar:SetValue(0.5) --50%
--orb.FillingStatusBar:SetStatusBarColor(1,0,0)
```
