# Toshiba LED Screen Template

Unity 6 URP template for producing the deliberately stretched HDMI signal used by the specialised Toshiba LED screen.

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

## Requirements

- Unity Hub
- Unity Editor `6000.3.13f1` (Unity 6)
- Windows build support for Unity 6
- Universal Render Pipeline (already configured in this project)

## 1. Download and open the project

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

**Tools > Toshiba LED > Create Templates > Create Both Templates**

This safely creates or refreshes both available tests:

- `Assets/Scenes/LEDTemplateTest_640x1920.unity` - recommended current Toshiba design.
- `Assets/Scenes/LEDTemplateTest.unity` - original 608 x 1080 test.

The command is safe to run again. It updates the existing assets instead of creating duplicate cameras, canvases, or render textures.

To create only the current portrait version, use:

**Tools > Toshiba LED > Create Templates > Create 640x1920 Template**

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

Press **Play** again to stop Game mode before editing objects. Changes made while Unity is playing are normally discarded when Play mode stops.

## 5. View the unstretched design while editing

Select:

**Tools > Toshiba LED > Open LED Preview**

In the preview window:

1. Select **640x1920** as the template source.
2. Select **Design proportions** to inspect the original portrait composition.
3. Select **HDMI 1920x1080** to inspect the deliberately stretched output.
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

For the recommended template, select:

**Tools > Toshiba LED > Validate Templates > Validate 640x1920 Template**

Check the Unity Console for the validation results. The tool checks the Render Texture size, camera capture, UI capture stage, full-screen output, lack of aspect-ratio preservation, Windows x86-64 target, and intended 1920 x 1080 player settings.

The original template can be checked with:

**Tools > Toshiba LED > Validate Templates > Validate 608x1080 Template**

Validation proves the Unity rendering architecture. It does not verify the final mapping on the physical Toshiba LED hardware.

## 8. Create a Windows build

1. Open `LEDTemplateTest_640x1920.unity`.
2. Select **File > Build Profiles**.
3. Select **Windows**.
4. If required, select **Switch Platform**.
5. Confirm the architecture is **Intel 64-bit / x86-64**.
6. Confirm `LEDTemplateTest_640x1920` is enabled and first in the scene list.
7. Select **Build**.
8. Choose a new folder outside the Unity project's `Assets` folder, such as `Builds/ToshibaLEDTest`.
9. Run the generated `.exe` on the computer connected to the Toshiba screen by HDMI.

The project is configured for a 1920 x 1080 fullscreen window. The internal Render Texture remains 640 x 1920.

## 9. Use the setup in the Boxing project

Make a backup or Git commit of the Boxing project first. Then copy these template files into the matching folders of the Boxing project:

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
