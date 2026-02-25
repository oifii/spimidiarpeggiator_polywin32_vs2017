# Getting Started: Running the App (First Successful Launch)

This section walks you through what you need in your working directory, how to launch the arpeggiator executable for the first time, and what to look for to confirm a successful startup.

## Prerequisites

Before you run the application, ensure your build output directory (typically `Debug` or `Release`) contains the following files:

- **spimidiarpeggiator_polywin32.exe**

The main executable built with Visual Studio 2017 (v141 toolset).

- **background.jpg**

Loaded at startup by `FreeImage_Load` to paint the window background.

- **background_32x32x16.ico** and **background_16x16x16.ico**

Icon files referenced by `LoadImage` when registering the window class and small icon.

- **begin.bat** (or your chosen startup script, e.g. `begin.ahk`)

Invoked via `ShellExecuteA` early in `_tWinMain` to kick off any user-defined initialization .

- **output.txt** (will be created at runtime)

The log file opened by the status‐text library (`fopen("output.txt","w")`) for on-screen and file-based status messages.

## Launching the Executable

> Make sure none of these files are missing or misnamed, as the app will attempt to load them by exact filename at startup.

1. Open a Command Prompt (or File Explorer) and navigate to the folder containing `spimidiarpeggiator_polywin32.exe` and the prerequisite files.
2. Run the app by double-clicking the executable or typing:

```cmd
   spimidiarpeggiator_polywin32.exe
```

You may also pass command-line arguments to select MIDI devices, channels, or an alternate script filename; by default the app launches the script defined in `global_begin`, typically `begin.bat` or `begin.ahk` .

## First-Run Checklist

- [ ] **Required files present**

Confirm that `background.jpg`, both `.ico` files, and your script (`begin.bat`/`begin.ahk`) live alongside the executable.

- [ ] **Window appears**

A borderless or overlapped window should open at the configured position and size, with its layered transparency applied.

- [ ] **Background image drawn**

The JPEG you provided must fill the client area; if it is missing, you will see a blank or default‐colored window.

- [ ] **Status text visible**

White font text should display over the background (handled by the external `spiwavsetlib` library). Look for the initial “MIDI input devices:” list.

- [ ] **Startup script executed**

Your `begin.bat` or `begin.ahk` must run automatically. Verify any side effects (e.g., launching an AutoHotkey helper script).

- [ ] **No error popups**

If any `MessageBox` appears complaining about missing static controls, fonts, or files, address those errors before proceeding.

## What Happens at Startup

1. **Script Launch**

Early in `_tWinMain`, the app calls:

```cpp
   ShellExecuteA(NULL, "open", global_begin.c_str(), "", NULL, nCmdShow);
```

to run your script .

1. **FreeImage Initialization**

`background.jpg` is loaded into a DIB for painting.

1. **Window Class Registration & Creation**

The app registers a Win32 window class, specifying the 32×32 and 16×16 icons from file.

1. **Layered Window Setup**

It creates the main window, sets `WS_EX_LAYERED`, and applies the global alpha transparency.

1. **Static Control & Font**

A transparent static control is created for status text, and the font is set via `CreateFontW`.

1. **Status Text Library Init**

`WavSetLib_Initialize` is called to bind the static control and begin logging to `output.txt`.

1. **PortMidi/PortTime Setup**

The app initializes PortMidi, lists available MIDI inputs on screen, and opens the selected device.

1. **Arpeggiator Timer**

A WinMM timer (`SetTimer`) starts the arpeggiator callback at the tempo-derived interval.

Once these steps complete without errors, you should see your background image and scrolling MIDI event/status text in the window, indicating a successful first launch.