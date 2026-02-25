# Logging, Status Output, and Diagnostics

## Overview

spimidiarpeggiator_polywin32 reports its internal state, errors, and user-actions via on-screen status text and by logging to output.txt. All messages use the `StatusAddText` (wide) or `StatusAddTextA` (ANSI) APIs to append lines to the UI and to output.txt via the spiwavsetlib integration. This section explains how those messages are generated and how to interpret and troubleshoot common problems such as missing devices, busy ports, silent operation, and channel-filter mismatches.

---

## Logging and Status Output Mechanism

- **Status API**:
- `StatusAddText(pWCHAR)` for wide-string messages
- `StatusAddTextA(pCHAR)` for ANSI-string messages
- **Destination**:
- On-screen static control (via spiwavsetlib)
- `output.txt` file (opened in `StartGlobalProcess`)

Each call to `StatusAddText` or `StatusAddTextA` appends a line of diagnostics to both the UI and the log.

---

## Common Diagnostic Messages and Troubleshooting

### 1. Device Not Found

**Symptoms**

- On startup, you see

• `input midi device not found`

or

• `output midi device not found`

**Code Paths**

- **Input selection failure**

```cpp
  swprintf(pWCHAR, L"input midi device not found\n");  
  StatusAddText(pWCHAR);
  return;
```

- **Output selection failure**

```cpp
  swprintf(pWCHAR, L"output midi device not found\n");  
  StatusAddText(pWCHAR);
```

**Troubleshooting**

1. Run without arguments to list available devices.
2. Verify the exact device **name** (case-sensitive) matches one printed in the list.
3. Pass that name as the first (input) or second (output) command-line argument.

---

### 2. MIDI Input Port Busy

**Symptoms**

- Pressing **C** to connect input prints:

```plaintext
  ***********************
  midi input port is BUSY
  ***********************
```

**Code Path**

```cpp
PmError err = Pm_OpenInput(&global_pPmStreamMIDIIN, global_inputmidideviceid, …);
if (err) {
    swprintf(pWCHAR, L"***********************\n"); StatusAddText(pWCHAR);
    swprintf(pWCHAR, L"midi input port is BUSY\n");       StatusAddText(pWCHAR);
    swprintf(pWCHAR, L"***********************\n"); StatusAddText(pWCHAR);
}
```

**Troubleshooting**

- Ensure no other application is holding the same MIDI input port.
- Press **D** to disconnect (`midi input port DISCONNECTED`), then retry **C**.
- If problem persists, reboot or reconfigure your MIDI routing software.

---

### 3. Playback Paused / Resumed

**Symptoms**

- Pressing **Space** toggles between:
- `pause`
- `play`

**Code Path**

```cpp
if (wParam==0x0020) { // Space bar
    if (global_playflag) {
        swprintf(pWCHAR, L"pause\n"); StatusAddText(pWCHAR); global_playflag=false;
    } else {
        swprintf(pWCHAR, L"play\n");  StatusAddText(pWCHAR); global_playflag=true;
    }
}
```

**Troubleshooting**

- Confirm you are in **play** mode before expecting arpeggiation.
- Press Space until you see `play` in the status.

---

### 4. No Arpeggiation or No Sound

**Possible Causes**

1. **Playback Paused** (`global_playflag==false`)
2. **Empty Note Storage** (no notes captured)
3. **Channel Filter Mismatch**

#### 4.1 Empty Note Storage

- In the WM_TIMER handler, arpeggiation only runs when notes exist:

```cpp
  if (global_playflag) {
      if (global_notenumbermap.size()>0) { … }  
      // else: nothing happens, no status message
  }
```

**Troubleshooting**

- Look for `NoteOn`/`NoteOff` lines in the log to confirm notes are received.
- If none appear, check connection and channel filter.

#### 4.2 Channel Filter Mismatch

- If an input channel filter is set (`global_inputmidichannel ≥ 0`), messages on other channels are silently dropped:

```cpp
  int chan = Pm_MessageStatus(event.message) & MIDI_CHN_MASK;
  if (global_inputmidichannel>-1 && chan!=global_inputmidichannel) {
      break; // ignore this event
  }
```

**Troubleshooting**

- Use `-1` as the input-channel argument for Omni mode.
- Or specify the exact MIDI channel (0–15) matching your device’s output.

---

## Code Reference Summary

| Message | Condition | Code Location |
| --- | --- | --- |
| input midi device not found | Device-name lookup failure | StartGlobalProcess |
| output midi device not found | Device-name lookup failure | StartGlobalProcess |
| midi input port is BUSY | `Pm_OpenInput` returned error when connecting input | WM_KEYDOWN (‘C’ key) |
| midi input port CONNECTED | Successful `Pm_OpenInput` | WM_KEYDOWN (‘C’ key) |
| midi input port DISCONNECTED | Pressing ‘D’ disconnects input | WM_KEYDOWN (‘D’ key) |
| pause / play | Toggled by Space bar | WM_KEYDOWN (Space) |
| NoteOn / NoteOff | Received MIDI messages | `output()` in receive_poll |
|  no arpeggiation | `global_playflag==false` or empty note storage | WM_TIMER handler |
|  dropped by channel | `global_inputmidichannel` mismatch | `receive_poll` |


Use this reference to map observed status messages (or their absence) to the underlying code path, and apply the troubleshooting steps above to resolve common issues.