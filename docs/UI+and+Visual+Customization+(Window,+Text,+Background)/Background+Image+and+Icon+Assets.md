# UI and Visual Customization: Background Image and Icon Assets

This section describes how the application loads and manages its background image and window icons at runtime. All asset files listed here must reside in the same directory as the executable (`spimidiarpeggiator_polywin32.exe`) so that the runtime `LoadImage` and `FreeImage_Load` calls can find them.

## Background Image

### File Requirement

– **background.jpg**: A JPEG image file used as the window’s background.

### Loading

In the `InitInstance` function, the application calls FreeImage to load the JPEG into a `FIBITMAP*`:

```cpp
global_dib = FreeImage_Load(FIF_JPEG, "background.jpg", JPEG_DEFAULT);
```

This must succeed before the window is shown so that the image can be painted in the `WM_PAINT` handler.

### Unloading

When the application shuts down (in response to `WM_DESTROY`), the background bitmap is released:

```cpp
FreeImage_Unload(global_dib);
```

Failing to ship or load `background.jpg` will typically result in a null `global_dib` pointer and no background image displayed.

## Icon Assets

Two external ICO files are used to define the window’s large and small icons. Both files must be placed alongside the executable.

| Asset File | Purpose | Size |
| --- | --- | --- |
| background_32x32x16.ico | Class (large) icon | 32×32 |
| background_16x16x16.ico | Small window icon | 16×16 |


### Class Registration (`MyRegisterClass`)

When registering the window class, the application loads these icons via `LoadImage` with the `LR_LOADFROMFILE` flag:

```cpp
wcex.hIcon = (HICON)LoadImage(
    NULL,
    L"background_32x32x16.ico",
    IMAGE_ICON,
    0, 0,
    LR_LOADFROMFILE
);
```

```cpp
wcex.hIconSm = (HICON)LoadImage(
    NULL,
    L"background_16x16x16.ico",
    IMAGE_ICON,
    0, 0,
    LR_LOADFROMFILE
);
```

These calls override any icons compiled into resources and allow the icons to be swapped simply by replacing the `.ico` files.

### Alternate Registration Path

In the initial `RegisterClassEx` call (before window creation), the same pattern appears:

```cpp
wcex.hIcon   = (HICON)LoadImage(NULL, L"background_32x32x16.ico", IMAGE_ICON, 0, 0, LR_LOADFROMFILE);
...
wcex.hIconSm = (HICON)LoadImage(NULL, L"background_16x16x16.ico", IMAGE_ICON, 0, 0, LR_LOADFROMFILE);
```

This ensures consistency whether the app uses the menu-enabled or popup‐only window style.

---

**Note:** If any of these files are missing or unreadable, `LoadImage` returns `NULL`, and Windows will fall back to the default icon defined in the resource script (`spimidiarpeggiator_polywin32.rc`). Ensure that `background.jpg`, `background_32x32x16.ico`, and `background_16x16x16.ico` are deployed alongside the executable.