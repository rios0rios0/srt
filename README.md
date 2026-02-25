<h1 align="center">SRT - SecurityRobot Tool</h1>
<p align="center">
    <a href="https://github.com/rios0rios0/srt/releases/latest">
        <img src="https://img.shields.io/github/release/rios0rios0/srt.svg?style=for-the-badge&logo=github" alt="Latest Release"/></a>
    <a href="https://github.com/rios0rios0/srt/blob/main/LICENSE">
        <img src="https://img.shields.io/github/license/rios0rios0/srt.svg?style=for-the-badge&logo=github" alt="License"/></a>
</p>

A Windows desktop utility that automatically discovers a specific router on the local network via ARP table inspection and configures UPnP port forwarding rules through the Windows NAT/UPnP COM interface. Built with Delphi 7 as a home-network automation tool to simplify port-forwarding setup for a security camera or home server.

**Language:** Object Pascal (Borland Delphi 7) | **Status:** Discontinued (2014-08-09)

## Features

- **Automatic router discovery** -- executes `arp -a` as a hidden subprocess, captures its output into a memo log, and scans each line for a hardcoded MAC address (`88-f7-c7-6e-bc-94`) to extract the router's IP address
- **Hidden process execution** -- uses the Windows `CreateProcess` API with `STARTF_USESTDHANDLES` and `SW_HIDE` to run console commands invisibly, piping stdout through anonymous pipes back to the Delphi application
- **UPnP port mapping** -- creates a COM `HNetCfg.NATUPnP` object, accesses the `StaticPortMappingCollection`, and adds a TCP port forwarding rule mapping a user-selected external port (1--1024) to internal port 8080 on the discovered router IP
- **Configurable port selection** -- provides a `TSpinEdit` control with a range of 1 to 1024 (default 900) for choosing the external port number
- **Real-time logging** -- all ARP output and status messages are displayed in a read-only `TMemo` with `Courier New` font, showing the discovery process and results

## Technologies

| Component | Technology |
|-----------|------------|
| Language | Object Pascal |
| IDE | Borland Delphi 7 |
| UI framework | VCL (Visual Component Library) |
| COM automation | `ComObj` / `ActiveX` units for UPnP NAT traversal |
| Process management | Windows API (`CreateProcess`, `CreatePipe`, `ReadFile`) |
| Network discovery | ARP table parsing (`arp -a` command) |

## Project Structure

```
SRT.dpr          # Delphi project file (program entry point, creates TFrmPrincipal)
USRT.pas         # Main unit with all application logic:
                 #   - RunDosInMemo: hidden subprocess execution with pipe capture
                 #   - BtnProcurarClick: ARP-based router discovery
                 #   - BtnAtivarClick: UPnP port mapping via COM
USRT.dfm         # Form definition (330x350, fixed border, centered on desktop):
                 #   - BtnProcurar (Search button)
                 #   - BtnAtivar (Activate button, disabled until router found)
                 #   - MmoLog (read-only memo, Courier New, vertical scrollbar)
                 #   - SePorta (spin edit, range 1-1024, default 900)
SRT.res          # Compiled resource file (icons)
```

## How It Works

1. **Search phase** -- clicking "Procurar" (Search) runs `arp -a` in a hidden console process, pipes the output into the memo log, and scans for the target MAC address. If found, the router's IP address is extracted by trimming the line content before the MAC string.

2. **Activation phase** -- clicking "Ativar" (Activate) instantiates the Windows `HNetCfg.NATUPnP` COM object, retrieves the `StaticPortMappingCollection`, and calls `.Add()` to create a TCP port mapping from the selected external port to `8080` on the discovered IP. If the port is already in use, an error message is displayed.

## Installation

1. Open `SRT.dpr` in Borland Delphi 7 (or a compatible IDE such as Embarcadero RAD Studio)
2. Compile the project (Ctrl+F9)
3. Run the generated `SRT.exe` on a Windows machine connected to the target network

**Note:** The application has a hardcoded MAC address for router discovery. To use it with a different router, update the MAC address string in `USRT.pas`.

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
