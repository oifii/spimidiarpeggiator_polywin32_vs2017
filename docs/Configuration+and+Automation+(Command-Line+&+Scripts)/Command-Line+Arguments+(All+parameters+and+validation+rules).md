# Configuration and Automation (Command-Line & Scripts)

## Command-Line Arguments

The application accepts a sequence of positional arguments to configure MIDI device selection, arpeggiation behavior, window appearance, text styling, and automation scripts. This enables fully automated startup and shutdown—ideal for live performance rigs or batch processing without manual intervention.

### Positional Argument List

Arguments are parsed in `_tWinMain()` (in **spimidiarpeggiator_polywin32.cpp**) and mapped to global variables based on their order:

| Position | Parameter | Type | Global Variable | Description | Default |
| --- | --- | --- | --- | --- | --- |
| 1 | Input MIDI Device Name | string | global_inputmididevicename | Name of the MIDI input device to open | `"Q49"` |
| 2 | Output MIDI Device Name | string | global_outputmididevicename | Name of the MIDI output device to open | `"Out To MIDI Yoke:  1"` |
| 3 | Output MIDI Channel | int | global_outputmidichannel | MIDI channel for output (0–15) | `1` |
| 4 | Tempo (BPM) | float | global_tempo_bpm | Arpeggio tempo in beats per minute | `60.0f` |
| 5 | Program String | string | global_programstring | Arpeggiation algorithm; one of `GOZILLA`, `ULTRAMAN`, `AVATAR`, or `THOR` | `"AVATAR"` |
| 6 | Window X Position | int | global_x | X-coordinate of the application window | `100` |
| 7 | Window Y Position | int | global_y | Y-coordinate of the application window | `200` |
| 8 | Window Width | int | global_xwidth | Width of the application window | `400` |
| 9 | Window Height | int | global_yheight | Height of the application window | `400` |
| 10 | Window Alpha | int (0–255) | global_alpha | Transparency level of the window | `200` |
| 11 | Title Bar Display | int (0/1) | global_titlebardisplay | Show title bar? `0` = off, `1` = on | `1` |
| 12 | Menu Bar Display | int (0/1) | global_menubardisplay | Show menu bar? `0` = off, `1` = on | `0` |
| 13 | Accelerator Active | int (0/1) | global_acceleratoractive | Enable keyboard accelerators? `0` = no, `1` = yes | `1` |
| 14 | Font Height | int | global_fontheight | Height of the font used for on-screen status text | `24` |
| 15 | Font Color – Red | int (0–255) | global_fontcolor_r | Red component of font color | `255` |
| 16 | Font Color – Green | int (0–255) | global_fontcolor_g | Green component of font color | `255` |
| 17 | Font Color – Blue | int (0–255) | global_fontcolor_b | Blue component of font color | `255` |
| 18 | Static Text Alignment | int (0–2) | global_staticalignment | Alignment of status text: `0` = left, `1` = center, `2` = right | `0` |
| 19 | Window Class Name | wide string | szWindowClass | Window class name passed to `RegisterClass` | `"spimidiarpeggiator_polywin32class"` |
| 20 | Window Title | wide string | szTitle | Text shown in the window’s title bar | `"spimidiarpeggiator_polywin32title"` |
| 21 | Begin Script Path | string | global_begin | Path to the script or batch file executed immediately on startup | `"begin.ahk"` |
| 22 | End Script Path | string | global_end | Path to the script or batch file executed on shutdown | `"end.ahk"` |
| 23 | Input MIDI Channel Filter | int | global_inputmidichannel | MIDI channel to listen for input: `-1` = Omni mode (all channels), `0`–`15` = specific channel | `-1` |


### Validation Rules

Once all arguments are assigned, the application performs the following checks in `_tWinMain()` and exits with an error if any fail:

- **Input MIDI Channel (**`**global_inputmidichannel**`**)**

Must be `-1` (for Omni mode) or within `0` to `15`.

- **Output MIDI Channel (**`**global_outputmidichannel**`**)**

Must be within `0` to `15`.

- **Program String (**`**global_programstring**`**)**

Must exactly match one of:

`GOZILLA`, `ULTRAMAN`, `AVATAR`, `THOR`.

If the program string is valid, the corresponding ID macro is assigned to `global_programid` (`PROGRAM_GOZILLA`, `PROGRAM_ULTRAMAN`, `PROGRAM_AVATAR`, or `PROGRAM_THOR`).

### Automation Script Invocation

Immediately after parsing (and before validation), the application launches the “begin” script via:

```cpp
ShellExecuteA(NULL, "open", global_begin.c_str(), "", NULL, nCmdShow);
```

This allows any preparation steps (e.g., routing MIDI cables, initializing external hardware) to occur before the main window appears. On shutdown, the “end” script (`global_end`) may be invoked elsewhere in the application to clean up.

### Example Invocation

```plaintext
spimidiarpeggiator_polywin32.exe \
  "Q49" \
  "Out To MIDI Yoke:  1" \
  0 \
  120.0 \
  GOZILLA \
  50 50 800 600 128 \
  1 1 0 \
  32 \
  0 255 0 \
  1 \
  "MyWinClass" \
  "My Title" \
  "C:\Scripts\start.bat" \
  "C:\Scripts\stop.bat" \
  3
```

This configures:

- Input=`Q49`, Output=`Out To MIDI Yoke:  1`, Out Chan=`0`, Tempo=`120 BPM`, Program=`GOZILLA`
- Window at (50, 50), size 800×600, 50% opacity
- Title bar & menu shown, accelerators off
- Font 32 px high, green text, center-aligned
- Custom window class/title
- Startup script `start.bat`, shutdown script `stop.bat`
- Only MIDI channel 3 is listened to

If any parameter fails its validation, an error message is printed via `StatusAddText` and the process terminates.