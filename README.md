# Toshiba LED Screen Template

Created by **BD and HKD**.

**New here?** Start with the [beginner tutorial](TUTORIAL.md) for a simple explanation of the LED output and step-by-step setup instructions.

Unity 6 URP template for producing either the deliberately stretched HDMI signal used by the specialised Toshiba LED screen or a normal 1920 x 1080 output.

The recommended current setup is:

```text
640 x 1920 internal portrait design
            |
            v
Complete scene and UI captured together
            |
            v
640 x 1920 Render Texture
            |
            v
Stretched without preserving aspect ratio
            |
            v
1920 x 1080 Windows / HDMI output
```

The image will look extremely wide on a normal 1920 x 1080 monitor. That distortion is intentional. The Toshiba hardware maps that signal onto its physical LED display.

A separate normal template is also included:

```text
1920 x 1080 internal design
            |
            v
Complete scene and UI captured together
            |
            v
1920 x 1080 Render Texture
            |
            v
1920 x 1080 Windows / HDMI output (no distortion)
```

## Requirements

- Unity Hub
- Unity Editor `6000.3.13f1` (Unity 6)
- Windows build support for Unity 6
- Universal Render Pipeline (already configured in this project)

## 1. Download and open the project

### Import the package into an existing Unity project

Download [Toshiba_LED_Template_v8.unitypackage](Exports/Toshiba_LED_Template_v8.unitypackage) from this repository. In the destination Unity project:

1. Make a backup or Git commit of the project.
2. Select **Assets > Import Package > Custom Package**.
3. Select `Toshiba_LED_Template_v8.unitypackage`.
4. Leave all included files selected and choose **Import**.
5. Wait for Unity to finish compiling.
6. Select **Tools > Toshiba LED > Create Templates > Create All Templates**.

The package contains the setup tool, test background, and UI instructions. The menu command creates the scenes and Render Textures inside the destination project.

### Git method

Clone this repository:

```powershell
git clone https://github.com/nuaxion-bd/LEDScreenTemplate-Setup.git
```

Then:

1. Open Unity Hub.
2. Select **Add** > **Add project from disk**.
3. Select the cloned `LEDScreenTemplate-Setup` folder.
4. Open it with Unity `6000.3.13f1`.
5. Wait for Unity to finish importing and compiling.

### ZIP method

1. On GitHub, select **Code** > **Download ZIP**.
2. Extract the ZIP to a normal project folder.
3. In Unity Hub, select **Add** > **Add project from disk**.
4. Select the extracted folder and open it with Unity `6000.3.13f1`.

You do not need to close Unity after opening the project. If a menu command has just changed, wait for the small compilation spinner to finish before using it.

## 2. Create or refresh the LED templates

In Unity's top menu, select:

**Tools > Toshiba LED > Create Templates > Create All Templates**

This safely creates or refreshes all available tests:

- `Assets/Scenes/LEDTemplateTest_640x1920.unity` - recommended current Toshiba design.
- `Assets/Scenes/LEDTemplateTest_608x1080.unity` - original 608 x 1080 test.
- `Assets/Scenes/LEDTemplateTest_1920x1080.unity` - normal Full HD design with no distortion.

The command is safe to run again. It updates the existing assets instead of creating duplicate cameras, canvases, or render textures.

Older versions used the name `LEDTemplateTest.unity` for the 608 x 1080 scene. The current setup safely renames that scene to `LEDTemplateTest_608x1080.unity` while preserving its Unity asset identity.

To create only the current portrait version, use:

**Tools > Toshiba LED > Create Templates > Create 640x1920 Template**

To create only the normal Full HD version, use:

**Tools > Toshiba LED > Create Templates > Create 1920x1080 Normal Template**

## 3. Open the recommended test scene

Use either of these methods:

- Select **Tools > Toshiba LED > Open 640x1920 Test Scene**.
- In the Project window, double-click `Assets/Scenes/LEDTemplateTest_640x1920.unity`.

The important hierarchy is:

```text
CaptureCamera
CaptureCanvas
|-- Background
|-- GameUI
`-- TestUI
OutputCanvas
`-- OutputRawImage
```

Everything under `CaptureCanvas` is composed into the portrait Render Texture first. `OutputRawImage` then stretches that completed image across the Windows display.

For the normal template, open `Assets/Scenes/LEDTemplateTest_1920x1080.unity` or select **Tools > Toshiba LED > Open 1920x1080 Normal Test Scene**. It uses the same hierarchy, but its completed 1920 x 1080 Render Texture maps to the output 1:1.

## 4. Test the final HDMI output in Game mode

Yes, use Game mode to test the final output:

1. Open `LEDTemplateTest_640x1920.unity`.
2. Open the **Game** tab.
3. Set the Game view to **16:9** or **1920 x 1080**.
4. Press the **Play** button at the top of Unity.

Expected result on a normal monitor:

- The image fills the entire Game view.
- There is no letterboxing or pillarboxing.
- The portrait composition looks heavily stretched horizontally.
- The UI and background stretch together as one image.

This stretched appearance is correct. Do not enable **Preserve Aspect** on `OutputRawImage` and do not add an Aspect Ratio Fitter to it.

When testing `LEDTemplateTest_1920x1080.unity`, the scene should fill the Game view with normal proportions and no distortion.

Press **Play** again to stop Game mode before editing objects. Changes made while Unity is playing are normally discarded when Play mode stops.

## 5. View the unstretched design while editing

Select:

**Tools > Toshiba LED > Open LED Preview**

In the preview window:

1. Select **608x1080**, **640x1920**, or **1920x1080** as the template source.
2. Select **Design proportions** to inspect the original portrait composition.
3. Select **HDMI 1920x1080** to inspect the final output. Portrait sources are distorted; the normal source is not.
4. Leave **Auto refresh** enabled while adjusting UI, or select **Refresh capture** manually.

The preview window is editor-only. It does not change the production Render Texture or Windows output.

## 6. Add or move UI

Store reusable UI artwork and related assets in:

```text
Assets/UI/
```

Place every UI object that must appear on the LED under:

```text
CaptureCanvas > GameUI
```

Use normal Unity UI anchors so elements stay attached to the intended top, centre, bottom, left, or right location of the 640 x 1920 design.

Do not place normal game UI under `OutputCanvas`. `OutputCanvas` is reserved solely for showing the completed Render Texture across the final 1920 x 1080 output.

Do not use a separate Screen Space Overlay canvas for game UI. Overlay UI would bypass the capture camera and would not be stretched together with the scene.

## 7. Validate the setup

To check every resolution after creating all templates, select:

**Tools > Toshiba LED > Validate Templates > Validate All Templates**

For the recommended template, select:

**Tools > Toshiba LED > Validate Templates > Validate 640x1920 Template**

Check the Unity Console for the validation results. The tool checks the Render Texture size, camera capture, UI capture stage, full-screen output, lack of aspect-ratio preservation, Windows x86-64 target, and intended 1920 x 1080 player settings.

The original template can be checked with:

**Tools > Toshiba LED > Validate Templates > Validate 608x1080 Template**

The normal Full HD template can be checked with:

**Tools > Toshiba LED > Validate Templates > Validate 1920x1080 Normal Template**

Validation proves the Unity rendering architecture. It does not verify the final mapping on the physical Toshiba LED hardware.

## 8. Create a Windows build

1. Open the scene you want to build: `LEDTemplateTest_640x1920.unity` for the Toshiba portrait mapping, or `LEDTemplateTest_1920x1080.unity` for normal Full HD output.
2. Select **File > Build Profiles**.
3. Select **Windows**.
4. If required, select **Switch Platform**.
5. Confirm the architecture is **Intel 64-bit / x86-64**.
6. Confirm your selected test scene is enabled and first in the scene list.
7. Select **Build**.
8. Choose a new folder outside the Unity project's `Assets` folder, such as `Builds/ToshibaLEDTest`.
9. Run the generated `.exe` on the computer connected to the Toshiba screen by HDMI.

The project is configured for a 1920 x 1080 fullscreen window. The internal Render Texture remains 640 x 1920.

If you build `LEDTemplateTest_1920x1080.unity` instead, both the internal Render Texture and final Windows output are 1920 x 1080. Creating a template configures the correct player resolution, but Unity does not build an `.exe` automatically; you still choose **Build** when you are ready.

## 9. Use the setup in the Boxing project

The easiest method is to import `Exports/Toshiba_LED_Template_v8.unitypackage` using **Assets > Import Package > Custom Package**.

Alternatively, make a backup or Git commit of the Boxing project, then copy these template files into its matching folders:

```text
Assets/Editor/LEDTemplateSetup.cs
Assets/UI/TestBackground.png
Assets/UI/README_Toshiba_LED.txt
```

Create the destination `Assets/Editor` or `Assets/UI` folder if it does not already exist. Unity can generate new `.meta` files in the destination project; do not overwrite the `.meta` file of an existing destination folder.

Open the Boxing project and wait for Unity to finish importing and compiling. Then select:

**Tools > Toshiba LED > Integrate Boxing Main Scene (640x1920)**

This creates:

```text
Assets/Scenes/Main_LED_640x1920.unity
```

It does not overwrite the original `Assets/Scenes/Main.unity`. Open the new scene, then use:

**Tools > Toshiba LED > Apply Boxing Portrait UI Layout**

Test the integrated scene in Game mode at 1920 x 1080. Use the LED Preview window in **Design proportions** mode when repositioning portrait UI.

To recreate the complete Boxing game and its connected UI at every supported internal size, select:

**Tools > Toshiba LED > Recreate Boxing Game In All LED Sizes**

This keeps `Main_LED_640x1920.unity` as the master and creates:

- `Main_LED_608x1080.unity`
- `Main_LED_1920x1080.unity`

Validate them with **Tools > Toshiba LED > Validate Boxing Size Variants**.

## Troubleshooting

### The menu is missing

- Wait for Unity to finish compiling.
- Open **Window > General > Console** and fix any red C# errors.
- Confirm `Assets/Editor/LEDTemplateSetup.cs` exists.
- Select **Assets > Refresh**.

### The output looks too wide

That is expected on a normal monitor. The final image must be horizontally distorted for the Toshiba LED mapping.

### The output is portrait with black bars

Check `OutputCanvas > OutputRawImage`:

- Its anchors must stretch to all four edges.
- **Preserve Aspect** must be disabled.
- It must display `LED_Output_640x1920`.
- Remove any Aspect Ratio Fitter from the output image.

### UI does not appear in the Render Texture

Move it under `CaptureCanvas > GameUI`. It must be captured by `CaptureCamera` before the final stretch.

## Maintainer: rebuild the sharing package

After changing the setup tool or included UI files, select:

**Tools > Toshiba LED > Export Sharing Package (v8)**

Unity recreates `Exports/Toshiba_LED_Template_v8.unitypackage` from the current source files.
