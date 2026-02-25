# Getting Started (Install, Build, Run)

This section describes everything you need to install, the libraries and tooling required, and where the project expects those dependencies to live in order to build and run the spimidiarpeggiator_polywin32 application.

## Prerequisites

- **Operating System**

Windows desktop (Windows 10 recommended).

Target Windows SDK version is **10.0.19041.0** .

- **IDE and Toolset**

Visual Studio 2017 with **Platform Toolset v141** selected for all configurations .

“Desktop development with C++” workload must be installed, including the Windows 10 SDK.

## Dependencies

The project uses several native libraries for MIDI I/O, scheduling, image loading, and on-screen text. The headers and libraries for these must be present in the directories listed below.

### Bundled Libraries and Folders

Ensure the following folders exist at the root of the repository:

- **.\lib-src\portmidi**
- pm_common (PortMidi headers)
- porttime (PortTime headers)

- **.\lib-src\portaudio**
- include (PortAudio headers)
- build\msvc\Win32\Debug / Release (x86 libs)
- build\msvc\x64\Release (x64 libs)

- **.\lib-src\freeimage**
- Source (FreeImage headers)
- Dist or Dist\x64 (FreeImage.lib)

- **.\spiwavsetlib**
- C++ headers for on-screen text / logging

- **.\spiwavsetlib_vs2017u**
- \debug\spiwavsetlib_vs2017.lib
- \release\spiwavsetlib_vs2017.lib

- **System library**
- winmm.lib (part of Windows SDK)

### Include Directories

All configurations add these include paths to the compiler:

```plaintext
.\lib-src\portaudio\include;
.\lib-src\freeimage\Source\;
.\spiwavsetlib;
.\lib-src\portmidi\pm_common;
.\lib-src\portmidi\porttime;
%(AdditionalIncludeDirectories)
```

### Library Dependencies

The linker is configured per-configuration and per-platform as follows:

| Configuration | Platform | AdditionalDependencies | Citation |
| --- | --- | --- | --- |
| Debug | Win32 | .\lib-src\freeimage\Dist\FreeImage.lib; winmm.lib; .\spiwavsetlib_vs2017u\debug\spiwavsetlib_vs2017.lib; .\lib-src\portaudio\build\msvc\Win32\Debug\portaudio_x86.lib; .\lib-src\portmidi\release\portmidi_s.lib; %(AdditionalDependencies) |  |
| Release | Win32 | .\lib-src\freeimage\Dist\FreeImage.lib; winmm.lib; .\spiwavsetlib_vs2017u\release\spiwavsetlib_vs2017.lib; .\lib-src\portaudio\build\msvc\Win32\Release\portaudio_x86.lib; .\lib-src\portmidi\Release\portmidi_s.lib; %(AdditionalDependencies) |  |
| Debug | x64 | .\lib-src\freeimage3180\Dist\x64\FreeImage.lib; winmm.lib; .\spiwavsetlib_vs2017u\x64\debug\spiwavsetlib_vs2017.lib; .\lib-src\portaudio(x64)\build\msvc\x64\Release\portaudio_x64.lib; .\lib-src\portmidi(x64)\x64\Release\portmidi-dynamic.lib; %(AdditionalDependencies) |  |
| Release | x64 | .\lib-src\freeimage3180\Dist\x64\FreeImage.lib; winmm.lib; .\spiwavsetlib_vs2017u\x64\release\spiwavsetlib_vs2017.lib; .\lib-src\portaudio(x64)\build\msvc\x64\Release\portaudio_x64.lib; .\lib-src\portmidi(x64)\x64\Release\portmidi-dynamic.lib; %(AdditionalDependencies) |  |


### Summary of External Dependencies

- PortMidi / PortTime (MIDI I/O and polling)
- PortAudio (audio backend)
- FreeImage (background image loading)
- winmm (Windows multimedia timers for scheduling)
- spi­wav­set­lib (on-screen status text, logging to output.txt)

## Building the Project

1. Open **spimidiarpeggiator_polywin32.sln** in Visual Studio 2017.
2. Select your desired **Configuration** (Debug/Release) and **Platform** (Win32/x64).
3. Build the solution (`Build → Build Solution`).

All include paths and linker inputs are already configured in **spimidiarpeggiator_polywin32.vcxproj** for each configuration    .

## Running the Application

After a successful build:

- The executable will be located in `.\$(Configuration)\$(Platform)\`.
- Ensure that the folders containing the required DLLs (if any) are on your `%PATH%`, or copy the necessary `.dll` files into the same directory as the `.exe`.
- Run `spimidiarpeggiator_polywin32.exe` to launch the arpeggiator.
- Logs and status text will be written to **output.txt** in the working directory.