# Contributing

> **This project was discontinued in August 2014 and is no longer actively maintained.**
> The repository is preserved as a historical reference. No new features or bug fixes are planned.

## Historical Build Information

This project was built using the following tools and technologies:

- **Language:** Object Pascal (Delphi 7)
- **IDE:** Borland Delphi 7
- **UI Framework:** VCL (Visual Component Library)
- **COM Automation:** `ComObj` / `ActiveX` units for UPnP NAT traversal (`HNetCfg.NATUPnP`)
- **Process Management:** Windows API (`CreateProcess`, `CreatePipe`, `ReadFile`)
- **Network Discovery:** ARP table parsing (`arp -a` command)

### Build Steps (Historical)

1. Open `SRT.dpr` in Borland Delphi 7 (or compatible IDE)
2. Compile the project (`Ctrl+F9`)
3. Run `SRT.exe` on a Windows machine connected to the target network

> **Note:** Contains a hardcoded MAC address (`88-f7-c7-6e-bc-94`) for router discovery that must be changed for other routers.
