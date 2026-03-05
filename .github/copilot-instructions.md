# Copilot Instructions for SRT

## Project Overview

SRT (SecurityRobot Tool) is a historical Windows desktop utility built with **Object Pascal (Delphi 7)** and discontinued in **August 2014**. The application automatically discovers a specific router on the local network by inspecting the ARP table and then configures a UPnP port-forwarding rule through the Windows NAT/UPnP COM interface. The repository is preserved as an educational and archival reference. No new features or bug fixes are planned.

## Repository Structure

```
srt/
├── .github/
│   └── copilot-instructions.md  # This file
├── SRT.dpr                       # Delphi project file – application entry point, creates TFrmPrincipal
├── SRT.res                       # Compiled application resource file (icons)
├── USRT.pas                      # Main unit – ARP-based router discovery, UPnP port mapping, hidden subprocess execution
├── USRT.dfm                      # Main form layout – Buttons, TMemo log, TSpinEdit for port selection
├── CONTRIBUTING.md               # Historical build information
├── LICENSE                       # MIT License
└── README.md
```

## Technology Stack

| Component            | Details                                                                          |
|----------------------|----------------------------------------------------------------------------------|
| **Language**         | Object Pascal (Delphi 7)                                                         |
| **IDE**              | Borland Delphi 7 (or compatible: Embarcadero RAD Studio)                         |
| **UI Framework**     | VCL (Visual Component Library)                                                   |
| **COM Automation**   | `ComObj` / `ActiveX` units for UPnP NAT traversal (`HNetCfg.NATUPnP`)           |
| **Process Management** | Windows API (`CreateProcess`, `CreatePipe`, `ReadFile`)                        |
| **Network Discovery**| ARP table parsing via `arp -a` command                                           |
| **Platform**         | Windows only (Win32)                                                             |

## Architecture

The application is a single-form VCL application with one unit (`USRT.pas`) containing all logic.

### Form: `TFrmPrincipal` (`USRT.pas` / `USRT.dfm`)

A 330×350 px fixed-border form centered on the desktop, with:

- **`BtnProcurar`** (Search button) – triggers ARP-based router discovery
- **`BtnAtivar`** (Activate button, disabled until router is found) – triggers UPnP port mapping
- **`MmoLog`** (`TMemo`, read-only, Courier New font, vertical scrollbar) – displays ARP output and status messages
- **`SePorta`** (`TSpinEdit`, range 1–1024, default 900) – selects the external port number for the forwarding rule

### Key Procedures

#### `RunDosInMemo(DosApp: string; AMemo: TMemo)`

A standalone procedure that executes a console command invisibly and pipes its output into a `TMemo`:

1. Creates `TSecurityAttributes` with `bInheritHandle = True`
2. Opens an anonymous pipe with `CreatePipe`
3. Fills `TStartupInfo` with `STARTF_USESTDHANDLES + STARTF_USESHOWWINDOW` and `SW_HIDE`
4. Spawns the process with `CreateProcess`
5. Polls with `WaitForSingleObject` (100 ms timeout) while calling `Application.ProcessMessages` for UI responsiveness
6. Reads pipe output into a `PChar` buffer (2400 bytes), converts OEM to ANSI with `OemToAnsi`, and appends to the memo
7. Closes all handles (`hProcess`, `hThread`, `ReadPipe`, `WritePipe`)

#### `TFrmPrincipal.BtnProcurarClick`

1. Clears the memo and runs `arp -a` via `RunDosInMemo`
2. Scans each line for the hardcoded MAC address `88-f7-c7-6e-bc-94`
3. If found, extracts the router IP by trimming the line up to the MAC string
4. Enables `BtnAtivar` on success; shows an error dialog if not found

#### `TFrmPrincipal.BtnAtivarClick`

1. Disables `BtnAtivar` to prevent double submission
2. Creates the `HNetCfg.NATUPnP` COM object with `CreateOLEObject`
3. Retrieves `StaticPortMappingCollection`
4. Calls `.Add(SePorta.Value, 'TCP', 8080, IP, True, 'teste')` to create the TCP forwarding rule
5. Shows success or error dialogs

## Build Instructions (Historical)

There is no automated build system. Compilation requires a Windows machine with Borland Delphi 7 or a compatible IDE.

1. Open `SRT.dpr` in **Borland Delphi 7** (or Embarcadero RAD Studio)
2. Compile: **Ctrl+F9** (compile only) or **F9** (build and run)
3. Run the generated `SRT.exe` on a Windows machine connected to the target network

> **Note:** The application contains a hardcoded MAC address (`88-f7-c7-6e-bc-94`) for router discovery. To use with a different router, update this string in `USRT.pas`.

## Tests and Linting

This project has no automated tests or linters. There is no CI/CD pipeline or GitHub Actions configuration. The project is a preserved historical archive and no testing infrastructure exists.

## Development Workflow

Because this project is archived and no longer maintained, there is no active development workflow. If making historical corrections or documentation updates:

1. Edit the relevant `.pas`, `.dfm`, or documentation files
2. Open the project in Delphi 7 / RAD Studio to verify compilation if source changes are made
3. Submit a pull request with a clear description of the archival correction

## Coding Conventions

- **Language:** Object Pascal with Delphi 7 naming conventions
  - Form component names use mixed-case prefixes (e.g., `Btn` for `TButton`, `Mmo` for `TMemo`, `Se` for `TSpinEdit`)
  - Event handlers follow Delphi's auto-generated pattern: `<ComponentName><EventName>` (e.g., `BtnProcurarClick`)
  - Type names use the `T` prefix (e.g., `TFrmPrincipal`)
  - Instance variable names use the same name as the type without the `T` prefix (e.g., `FrmPrincipal`)
- **UI strings:** The application UI is in Brazilian Portuguese (Português do Brasil)
- **Error handling:** Uses `try/except` blocks with `ShowMessage` dialogs for user-facing errors

## Common Tasks

| Task                       | How                                                                                   |
|----------------------------|---------------------------------------------------------------------------------------|
| Build the project          | Open `SRT.dpr` in Delphi 7, press **Ctrl+F9**                                        |
| Run the application        | Press **F9** in Delphi 7, or run the compiled `SRT.exe` directly on Windows          |
| Search for router          | Click **Procurar** in the running application                                         |
| Activate port forwarding   | After finding the router, click **Ativar** (port is configured via the spin control)  |
| Change the target router   | Update the hardcoded MAC address string in `USRT.pas`                                 |
| Change the internal port   | Update the hardcoded `8080` value in `BtnAtivarClick` in `USRT.pas`                  |

## Notes for AI Assistants

- This is a **Windows-only Win32 application**; all file paths, Windows API calls, COM objects, and UI controls are Windows-specific
- The project is **discontinued** – avoid suggesting feature additions or library upgrades
- The source code contains **Brazilian Portuguese** UI strings (e.g., `'Listando....'`, `'ERRO! Roteador não encontrado'`); do not translate them
- The `SRT.res` file is a binary compiled resource – it cannot be edited as plain text
- The MAC address `88-f7-c7-6e-bc-94` is hardcoded and specific to the original author's hardware; it is intentional, not a bug
