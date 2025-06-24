---
title: Simple Server Guide
date: 2025-01-15 10:00:00 +0700
categories: [Guide, Server]
tags: [server,guide,wireguard,rdp,ssh]     # TAG names should always be lowercase
author: haka
toc: true
comments: false
description: A simple step-by-step guide for accessing and using the DOE lab server infrastructure.
---

# Simple Server Guide

This guide provides step-by-step instructions for accessing and using the DOE lab server infrastructure.

## Step 1: Get Server Access

Receive your server access email containing:
- Username and password
- Wireguard configuration file
- Server domain name (e.g., `anthony.doelab.site`)

![Sample Email](../assets/img/SimpleServerGuide/sample-email.png)

## Step 2: Install and Configure Wireguard

1. Install Wireguard on your system:
   - **Windows**: Download from [wireguard.com](https://www.wireguard.com/install/)
   - **MacOS**: `brew install wireguard-tools`
   - **Linux**: Use your distribution's package manager

![Wireguard Configuration](../assets/img/SimpleServerGuide/wireguard.webp)

2. Add the provided configuration file to Wireguard

3. Activate the VPN connection

> **Note**: Wireguard VPN does not work with HCMUT1/HCMUT2 WiFi networks.

## Step 3: Connect via Remote Desktop

1. Open your Remote Desktop client

![Remote Desktop Connection App](../assets/img/SimpleServerGuide/remote-desktop-connection-app.png)

2. **Important**: Set color depth to 16-bit

![Windows Color Depth Drop Down](../assets/img/SimpleServerGuide/windows-color-depth-drop-down.png)

1. Enter the server domain name (e.g., `anthony.doelab.site`)
2. Use your provided username and password

### Step 3.1: Access Compute Node

In the server terminal, run:
```bash
srun --x11 --pty bash
```

You can see the shell prompt is changed to the compute node name.

![Example of srun on server RDP](../assets/img/SimpleServerGuide/srun-on-server-rdp.png)

This connects you to an available compute node with 1 CPU. Use `-c x` to request x CPUs if needed:
```bash
srun --x11 --pty -c 2 bash  # Request 2 CPUs
```

### Step 3.2: Manage Software Modules

On the compute node, use these commands:
```bash
module avail          # List available software/tools
module load <tool>    # Load a specific tool
module unload <tool>  # Unload a specific tool
```

![Example of module call](../assets/img/SimpleServerGuide/example-module-call.png)

## Step 4: Setup SSH Remote Development

Configure your preferred IDE (VSCode, etc.) for SSH remote development:
- Enable SSH remote extension
- Connect to the server using SSH
- Code and use terminal directly from your IDE

See detail in [this article](https://code.visualstudio.com/docs/remote/ssh).

## Step 5: File Transfer with FileZilla

1. Open FileZilla
2. Connect to: `sftp://{server_name}` (e.g., `sftp://anthony.doelab.site`)
3. Enter username and password
4. Leave port field empty
5. Transfer files between your computer and server

![Example of FileZilla](../assets/img/SimpleServerGuide/example-filezilla.png)

## Step 6: Troubleshoot RDP Issues

If your RDP session hangs (black screen or crash):

1. Connect via SSH to the server
2. Run `htop` to view running processes
3. Find and kill your own xrdp session

![Example of htop](../assets/img/SimpleServerGuide/htop.png)

For more information regarding how to use htop, you can refer to [this article](https://dev.to/karandaid/mastering-the-linux-htop-command-4509).

## Step 7: Contact Administrator

If you encounter issues that cannot be resolved through the above steps, contact the administrator at: **ngthung@hcmut.edu.vn**

---

## Quick Reference Commands

| Command | Description |
|---------|-------------|
| `srun --x11 --pty bash` | Connect to compute node (1 CPU) |
| `srun --x11 --pty -c 2 bash` | Connect to compute node (2 CPUs) |
| `module avail` | List available software |
| `module load <tool>` | Load software module |
| `module unload <tool>` | Unload software module |
| `htop` | Monitor processes and kill sessions | 