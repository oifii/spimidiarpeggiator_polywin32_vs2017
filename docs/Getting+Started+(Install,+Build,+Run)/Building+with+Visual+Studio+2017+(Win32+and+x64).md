# Getting Started (Install, Build, Run)

This section walks you through opening, configuring, and building the `spimidiarpeggiator_polywin32` project in Visual Studio 2017 using the v141 toolset. You will learn how to target both Win32 and x64 platforms in Debug and Release modes, and how to resolve common build issues related to include and library paths.

---

## Building with Visual Studio 2017 (Win32 and x64)

### 1. Opening the Project

1. Launch **Visual Studio 2017**.
2. From the **File** menu, choose **Open → Project/Solution...**.
3. Navigate to the repository root and select the file:

```plaintext
   spimidiarpeggiator_polywin32.vcxproj
```

1. Click **Open**. No `.sln` file is required—the `.vcxproj` alone contains all configuration information.

---

### 2. Project Configurations

The project defines four build configurations (Debug/Release × Win32/x64) under the `<ProjectConfigurations>` section of the `.vcxproj`:

- Debug | Win32
- Debug | x64
- Release | Win32
- Release | x64

---

### 3. Selecting Configuration and Platform

In the Standard toolbar of Visual Studio:

1. Locate the **Solution Configurations** dropdown (typically showing “Debug” or “Release”).
2. Select **Debug** or **Release**.
3. Locate the **Solution Platforms** dropdown.
4. Select **Win32** or **x64**.

Build operations will then apply to the chosen combination.

---

### 4. Include Directories

All configurations share the following additional include directories, ensuring the compiler can find headers for FreeImage, PortAudio, PortMidi/PortTime, and the `spiwavsetlib` library:

```plaintext
.\lib-src\portaudio\include;
.\lib-src\freeimage\Source\;
.\spiwavsetlib;
.\lib-src\portmidi\pm_common;
.\lib-src\portmidi\porttime
```

(ClCompile → AdditionalIncludeDirectories)

Ensure these relative paths exist in your workspace. If you relocated any libraries, update **Project Properties → C/C++ → Additional Include Directories** accordingly.

---

### 5. Linker Inputs (Additional Dependencies)

Each configuration specifies the exact libraries to link against under **Linker → Additional Dependencies**. The following table summarizes them:

| Configuration | Additional Dependencies |
| --- | --- |
| Debug | Win32 |


```plaintext
  .\lib-src\freeimage\Dist\FreeImage.lib;
  winmm.lib;
  .\spiwavsetlib_vs2017u\debug\spiwavsetlib_vs2017.lib;
  .\lib-src\portaudio\build\msvc\Win32\Debug\portaudio_x86.lib;
  .\lib-src\portmidi\release\portmidi_s.lib;
  %(AdditionalDependencies)
```

| Debug | x64 |
| --- | --- |


```plaintext
  .\lib-src\freeimage3180\Dist\x64\FreeImage.lib;
  winmm.lib;
  .\spiwavsetlib_vs2017u\x64\debug\spiwavsetlib_vs2017.lib;
  .\lib-src\portaudio(x64)\build\msvc\x64\Release\portaudio_x64.lib;
  .\lib-src\portmidi(x64)\x64\Release\portmidi-dynamic.lib;
  %(AdditionalDependencies)
```

| Release | Win32 |
| --- | --- |


```plaintext
  .\lib-src\freeimage\Dist\FreeImage.lib;
  winmm.lib;
  .\spiwavsetlib_vs2017u\release\spiwavsetlib_vs2017.lib;
  .\lib-src\portaudio\build\msvc\Win32\Release\portaudio_x86.lib;
  .\lib-src\portmidi\Release\portmidi_s.lib;
  %(AdditionalDependencies)
```

| Release | x64 |
| --- | --- |


```plaintext
  .\lib-src\freeimage3180\Dist\x64\FreeImage.lib;
  winmm.lib;
  .\spiwavsetlib_vs2017u\x64\release\spiwavsetlib_vs2017.lib;
  .\lib-src\portaudio(x64)\build\msvc\x64\Release\portaudio_x64.lib;
  .\lib-src\portmidi(x64)\x64\Release\portmidi-dynamic.lib;
  %(AdditionalDependencies)
```

If you have placed libraries under different folders, update **Project Properties → Linker → Additional Library Directories** or adjust these paths directly in the `.vcxproj`.

---

### 6. Building the Project

1. In Visual Studio, ensure your desired **Configuration** and **Platform** are selected.
2. From the **Build** menu, choose **Build Solution** (or press **F7**).
3. The **Output** window will display compilation and linking progress. On success, the executable `spimidiarpeggiator_polywin32.exe` will be placed in:

```plaintext
   $(ProjectDir)$(Configuration)\$(Platform)\
```

For example:

`Debug\Win32\spimidiarpeggiator_polywin32.exe`

---

### 7. Common Build Issues

- **Cannot open include file** `FreeImage.h`, `portmidi.h`, or `spiwavsetlib.h`

→ Verify that the relative paths under **Additional Include Directories** match your folder structure.

- **LNK1104: cannot open file 'FreeImage.lib'** (or other `.lib`)

→ Ensure the `.lib` files exist in the Dist or build directories specified. If you built FreeImage or PortAudio yourself, confirm the output path matches the one in **Additional Dependencies**.

- **Missing **`**spiwavsetlib_vs2017.lib**`

→ Build the `spiwavsetlib` project first (in both Debug and Release for Win32/x64), and ensure its output subfolders (`debug`, `release`, `x64\debug`, `x64\release`) contain the `.lib` files.

- **Incorrect Library Directory**

→ If libraries reside elsewhere, open **Project Properties → Linker → General → Additional Library Directories** and add the correct paths.

- **Platform Toolset Mismatch**

→ Confirm **Project Properties → General → Platform Toolset** is set to **Visual Studio 2017 (v141)** for all configurations.

---

With these steps and references to the exact project settings, you should be able to open, configure, and build the `spimidiarpeggiator_polywin32` application for both Win32 and x64 targets without using a solution file.