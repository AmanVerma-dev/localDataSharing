# Local Network Storage — Router-Based NAS

A local file-sharing setup that turns a home router's built-in USB storage feature into a simple network-attached storage (NAS) solution, accessible over SMB/CIFS by any device on the same LAN.

## Problem

Sharing files between devices on a home network — phones, laptops, desktops — typically requires either uploading to cloud storage or manually transferring via USB. Both are slow and inconvenient for frequent use. A dedicated NAS appliance solves this but costs more than most home users need to spend.

## Solution

Most consumer Wi-Fi routers already include a USB port with built-in storage-sharing firmware. Plugging in a USB drive and enabling the router's SMB sharing turns the router into a lightweight NAS at zero additional hardware cost.

## Architecture

```
┌────────────┐     USB      ┌────────────────────┐    SMB/CIFS over LAN    ┌──────────────┐
│  USB Drive │ ────────────▶│   Home Router       │ ──────────────────────▶│  LAN Devices │
│ (16–32 GB) │              │  (built-in USB      │                        │  (PC, phone, │
└────────────┘              │   sharing firmware) │                        │   laptop)    │
                            └────────────────────┘                        └──────────────┘
                                     │
                                     │ Wi-Fi / Ethernet
                                     │
                              ┌──────┴──────┐
                              │ LAN segment │
                              │ 192.168.x.x │
                              └─────────────┘
```

All traffic stays on the local network. The router's firmware handles the SMB/CIFS protocol — no additional server software is installed.

## How It Works

1. A USB pen drive is physically connected to the router's USB port.
2. The router's admin panel exposes a USB/Storage sharing section.
3. When enabled, the router runs a lightweight SMB server internally.
4. Devices on the same LAN can browse, read, and write files on the shared drive using the SMB protocol.
5. Access is gated by a username and password configured in the router.

## Hardware Used

| Component | Details |
|-----------|---------|
| USB storage | 16–32 GB USB pen drive |
| Router | Consumer Wi-Fi router with USB port and storage-sharing feature |
| Clients | Any device on the LAN (Windows, macOS, Linux, Android, iOS) |

No separate server, Raspberry Pi, or dedicated NAS device is involved.

## Setup Steps

1. Plug a USB pen drive into the router's USB port.
2. Log into the router's admin panel (typically `http://192.168.1.1` or `http://192.168.0.1` — varies by manufacturer).
3. Navigate to the **USB / Storage Sharing** section (exact menu name varies by router brand and firmware version).
4. Enable network sharing over **SMB/CIFS**.
5. Set a **username** and **password** to restrict access.
6. Note the **share name** assigned by the router (often the drive label or a default like `USB_Storage`).
7. Connect from client devices using the appropriate method below.

> **Note:** Router menus, terminology, and feature availability vary significantly across manufacturers (TP-Link, Netgear, ASUS, etc.) and firmware versions. The steps above are generic — consult your router's documentation for exact navigation.

## Connecting from Client Devices

### Windows

1. Open **File Explorer**.
2. In the address bar, type:
   ```
   \\<ROUTER_IP>\<SHARE_NAME>
   ```
   Example: `\\192.168.1.1\USB_Storage`
3. Enter the username and password configured on the router.
4. Optionally, right-click the share and select **Map Network Drive** for persistent access.

### macOS

1. Open **Finder**.
2. Press `Cmd + K` (Connect to Server).
3. Enter:
   ```
   smb://<ROUTER_IP>/<SHARE_NAME>
   ```
   Example: `smb://192.168.1.1/USB_Storage`
4. Authenticate with the configured credentials.

### Linux

**File manager (GUI):**
1. Open the file manager and enter in the address bar:
   ```
   smb://<ROUTER_IP>/<SHARE_NAME>
   ```

**Command line (mount):**
```bash
sudo mount -t cifs //<ROUTER_IP>/<SHARE_NAME> /mnt/nas -o username=<USERNAME>,password=<PASSWORD>
```

### Android / iOS

Use a file manager app that supports SMB (e.g., Solid Explorer on Android, Files app on iOS) and connect to `smb://<ROUTER_IP>/<SHARE_NAME>`.

## Security Considerations

- **LAN-only access.** The share is only reachable from devices connected to the same local network. It is not exposed to the internet.
- **Single credential.** Most consumer routers support only one shared username/password for USB storage — there are no per-user accounts or file-level permissions.
- **No encryption in transit.** Consumer router SMB implementations typically use SMBv1 or basic SMBv2 without transport encryption. Traffic is readable to anything else on the same LAN.
- **Router firmware trust.** Security depends entirely on the router's firmware quality and update frequency.
- **Do not expose to the internet.** Port-forwarding SMB (port 445) to the internet is a serious security risk and is not recommended.

## Limitations

- **Local network only.** No access from outside the home Wi-Fi without additional setup (e.g., VPN into the home network).
- **No per-user permissions.** Access control is limited to a single shared username/password.
- **Transfer speed.** Capped by the router's USB controller, typically well below what a dedicated NAS device or direct USB transfer would achieve.
- **Storage capacity.** Limited by the USB drive size and the router's filesystem support (usually FAT32 or NTFS).
- **No redundancy.** Single USB drive with no backup or RAID — data loss is possible.
- **Feature limitations.** No media server, no remote access, no application support — only basic file read/write over SMB.

## Future Improvements

- Replace the pen drive with a 256 GB SSD for better capacity and sustained read/write performance.
- Add a Raspberry Pi running Samba directly for finer-grained access control, per-user permissions, and the option to enable remote access via VPN.
- Explore DLNA/UPnP media serving if the replacement setup supports it.

## Technical Concepts Demonstrated

- **SMB/CIFS protocol** — industry-standard network file-sharing protocol used by Windows, macOS, and Linux.
- **Network-Attached Storage (NAS)** — centralized file storage accessible over a local network.
- **LAN networking** — configuring and using devices within a local area network.
- **Router administration** — configuring router firmware features beyond basic Wi-Fi setup.
- **Access control** — username/password gating of shared resources.
- **Network troubleshooting** — diagnosing connectivity issues between client devices and the share.

## What I Learned

- How SMB/CIFS works at a practical level — mounting shares, credential handling, and cross-platform compatibility.
- The real-world trade-offs of consumer-router USB sharing versus dedicated NAS hardware (speed, features, reliability).
- How network shares appear differently across operating systems and what each OS requires to connect.
- The security implications of running SMB on a home network, including the risks of SMBv1 and unencrypted LAN traffic.

## Resume Summary

- **Router-based NAS:** Configured a home router's USB storage sharing to serve files over SMB/CIFS to all LAN devices, demonstrating practical networking and storage architecture.
- **Cross-platform file sharing:** Set up and documented SMB access from Windows, macOS, Linux, and mobile devices with credential-based access control.
- **Homelab infrastructure:** Designed a low-cost local storage solution, evaluating trade-offs between consumer router sharing and dedicated NAS hardware.

## Classification

`Homelab` · `Networking` · `Storage` · `SMB/CIFS` · `Infrastructure`
