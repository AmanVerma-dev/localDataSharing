# Router Configuration Guide

This document outlines the generic configuration steps needed to reproduce this project. Because router admin interfaces vary significantly by manufacturer (TP-Link, Netgear, ASUS, D-Link, etc.) and firmware version, exact menu names and layouts will differ.

## Prerequisites

- A Wi-Fi router with a USB port and built-in USB storage sharing support.
- A USB pen drive (FAT32 or NTFS formatted — check your router's supported filesystems).
- A device connected to the router's LAN (Wi-Fi or Ethernet).

## Step 1: Connect the USB Drive

Plug the USB pen drive into the router's USB port. Some routers have multiple USB ports — either will work unless the router's documentation specifies otherwise.

## Step 2: Access the Router Admin Panel

Open a web browser and navigate to the router's admin page:

```
http://<ROUTER_IP>
```

Common default addresses:
- `192.168.1.1`
- `192.168.0.1`
- `10.0.0.1`

Log in with the router's admin credentials (not the Wi-Fi password — the admin panel credentials, usually printed on the router label or set during initial setup).

## Step 3: Navigate to USB Storage Settings

Look for a section named one of the following (varies by manufacturer):

- **USB Storage** / **USB Settings**
- **Storage Sharing** / **Network Sharing**
- **Media / File Sharing**
- **USB Application** → **File Sharing**

## Step 4: Enable SMB/CIFS Sharing

Enable the **SMB** or **Samba** or **Network Neighborhood** sharing option. Some routers label this as "Windows file sharing" or simply "File Sharing."

## Step 5: Configure Access Credentials

Set a username and password for the share:

```
Username: <USERNAME>
Password: <PASSWORD>
```

> **Do not use the router admin password for the share.** Use a separate credential.

## Step 6: Note the Share Name

The router will assign a share name — this is often the USB drive's volume label or a default like `USB_Storage` or `sda1`. Note this value.

## Step 7: Connect from Devices

### Windows
```
\\<ROUTER_IP>\<SHARE_NAME>
```

### macOS / Linux
```
smb://<ROUTER_IP>/<SHARE_NAME>
```

### Linux (command line)
```bash
sudo mount -t cifs //<ROUTER_IP>/<SHARE_NAME> /mnt/nas -o username=<USERNAME>,password=<PASSWORD>
```

## Placeholder Reference

| Placeholder | Description |
|-------------|-------------|
| `<ROUTER_IP>` | The router's LAN IP address (e.g., `192.168.1.1`) |
| `<SHARE_NAME>` | The name of the shared folder as configured on the router |
| `<USERNAME>` | The username set for SMB access |
| `<PASSWORD>` | The password set for SMB access |

## Important Notes

- **Router menus vary.** The exact steps depend on your router's brand, model, and firmware version. Consult your router's manual if you cannot locate the USB sharing settings.
- **Filesystem support.** Most routers support FAT32 and NTFS. Some may also support ext4 or HFS+. Check your router's specifications.
- **Performance.** Transfer speeds are limited by the router's USB controller and internal processing, not by the USB drive itself or the network.
- **Security.** The share is accessible to any device on the LAN that has the credentials. There is no per-file or per-user access control on most consumer routers.
