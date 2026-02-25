# Reference: Controls, Menus, and Resources – Resource Files and IDs

This section describes the contents of the resource script (`spimidiarpeggiator_polywin32.rc`) and the accompanying header (`Resource.h`), including application icons, menu definitions, accelerators, the About dialog, and string‐table entries. It also notes where these assets are included and referenced in the project.

## Resource Script (`spimidiarpeggiator_polywin32.rc`)

### Icon Resources

The application defines two icon resources to ensure a consistent look at different scales:

- **IDI_SPIWAVWIN32 (107)**: Main 32×32 icon

File: `spimidiarpeggiator_polywin32.ico`

- **IDI_SMALL (108)**: Small 16×16 icon used in window captions and dialogs

File: `small.ico`

### Menu Definition

A simple two‐menu bar is defined under the identifier `IDC_SPIWAVWIN32`:

```rc
IDC_SPIWAVWIN32 MENUBEGIN
    POPUP "&File"
        MENUITEM "E&xit",    IDM_EXIT
    END
    POPUP "&Help"
        MENUITEM "&About .", IDM_ABOUT
    END
END
```

- **IDM_EXIT (105)**: Closes the application
- **IDM_ABOUT (104)**: Opens the About dialog

### Accelerator Table

Keyboard shortcuts mapped to menu commands:

```rc
IDC_SPIWAVWIN32 ACCELERATORSBEGIN
    "?", IDM_ABOUT, ASCII, ALT
    "/", IDM_ABOUT, ASCII, ALT
END
```

Both Alt+? and Alt+/ invoke the About dialog .

### About Dialog Template

Defines the “About” modal dialog under **IDD_ABOUTBOX (103)**:

```rc
IDD_ABOUTBOX DIALOGEX 0, 0, 170, 62
STYLE DS_SETFONT | DS_MODALFRAME | DS_FIXEDSYS | WS_POPUP | WS_CAPTION | WS_SYSMENUCAPTION
CAPTION "About spiwavwin32"
FONT 8, "MS Shell Dlg", 0, 0, 0x1
BEGIN
    ICON      128,IDC_STATIC,14,14,21,20
    LTEXT     "spiwavwin32, Version 1.0",IDC_STATIC,42,14,114,8,SS_NOPREFIX
    LTEXT     "Copyright (C) 2012",     IDC_STATIC,42,26,114,8
    DEFPUSHBUTTON "OK",IDOK,113,41,50,14,WS_GROUP
END
```

- **ICON control** (IDC_STATIC) displays the small icon.
- Two static text lines show version and copyright.
- **IDOK** is the dialog’s OK button .

## Header File (`Resource.h`)

All resource IDs are defined here for use in C++ code:

```c
//{{NO_DEPENDENCIES}}
// Microsoft Visual C++ generated include file.

#define IDC_MYICON               2
#define IDD_SPIWAVWIN32_DIALOG  102
#define IDS_APP_TITLE            103
#define IDD_ABOUTBOX             103
#define IDM_ABOUT                104
#define IDM_EXIT                 105
#define IDI_SPIWAVWIN32          107
#define IDI_SMALL                108
#define IDC_SPIWAVWIN32          109
#define IDR_MAINFRAME            128
#define IDC_STATIC               -1
```

- **IDS_APP_TITLE (103)**: String ID for the window title bar
- **IDC_SPIWAVWIN32 (109)**: Class name used when registering the main window class

## String Table

The string table in the resource script makes these identifiers loadable at runtime:

```rc
STRINGTABLE
BEGIN
    IDS_APP_TITLE      "spimidiarpeggiator_polywin32"
    IDC_SPIWAVWIN32    "SPIMIDIARPEGGIATOR_POLYWIN32"
END
```

- **"spimidiarpeggiator_polywin32"**: Displayed as the application’s title in the window frame
- **"SPIMIDIARPEGGIATOR_POLYWIN32"**: Registered window-class name

## Inclusion and References

- **Project inclusion**
- Both `.ico` files are listed under `<None Include="small.ico" />` and `<None Include="spimidiarpeggiator_polywin32.ico" />` in the project file (`.vcxproj`) .
- `Resource.h` is included in `spimidiarpeggiator_polywin32.cpp` and `stdafx.h` via `#include "resource.h"`.

- **Runtime loading**
- The menu is loaded in `InitInstance` through `LoadMenu(hInst, MAKEINTRESOURCE(IDC_SPIWAVWIN32))`, and accelerators via `LoadAccelerators`.
- The About dialog is invoked in the window procedure’s `WM_COMMAND` handling:

```cpp
    case IDM_ABOUT:
        DialogBox(hInst, MAKEINTRESOURCE(IDD_ABOUTBOX), hWnd, About);
        break;
```

.

This completes the reference for the resource definitions and their identifiers as used by the application.