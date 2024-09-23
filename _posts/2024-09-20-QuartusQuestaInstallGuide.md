---
title: Quartus & Questa Installation Guide
date: 2024-09-20 19:03:00 +0700
categories: [Guide, Software]
tags: [quartus,software,guide,faq,server]     # TAG names should always be lowercase
author: haka
toc: true
comments: false
description: This guide serves as a comprehensive manual for install  Quartus & Questa and use SSLC for academic purposes.
---
# Quartus & Questa Installation Guide

This guide provides step-by-step instructions for downloading and installing Intel Quartus and Mentor Questa software on Windows.

## Table of Contents
- [Quartus \& Questa Installation Guide](#quartus--questa-installation-guide)
  - [Table of Contents](#table-of-contents)
  - [Software Downloads](#software-downloads)
  - [Intel Quartus Installation](#intel-quartus-installation)
  - [Questa License Setup](#questa-license-setup)
  - [Additional Resources](#additional-resources)

---

## Software Downloads

To download the required software, follow the link below:

- **Intel Quartus Prime Lite 23.1.1 with Questa**:  
  [Download Here](https://www.intel.com/content/www/us/en/software-kit/825278/intel-quartus-prime-lite-edition-design-software-version-23-1-1-for-windows.html)

This download includes:
- **Quartus Prime Lite 23.1.1**: Full installation of Intel's FPGA development software.
- **Questa**: Simulation tool for FPGA designs.

1. Create an Intel account at [Intel Registration](https://www.intel.com/content/www/us/en/forms/basic-intel-registration.html).
2. Download **Quartus Prime Lite 23.1.1** for Windows with Questa from the link above.

---

## Intel Quartus Installation

1. Extract the downloaded files using **WinRAR** or a similar tool.
2. Run the `setup.bat` file with administrative privileges.
3. Follow the prompts:
   - Agree to the license agreement.
   - Select installation location.
   - Choose the free version and required libraries (e.g., Cyclone V, Questa).
4. Proceed with the installation and wait for it to complete.

---

## Questa License Setup

1. Register for Intel Service Self Licensing at [Intel SSL](https://licensing.intel.com/).
   - The registration process may take a few minutes, and you will receive a confirmation email.
2. Once registered, go to the "Sign up for Evaluation or Free Licenses" tab on the Intel SSL website.
   - Select **Questa – Intel** and set the seat number to 1.
3. Create a license file using the **NIC ID** (MAC address):
   - Find your MAC address by right-clicking on My Computer > Properties > Network Settings.
4. Intel will email you a `license.dat` file.
5. Place the `license.dat` file in the following directory:  
   `C:\flexlm\LR-076522_License.dat`.
6. Set the environment variable for the license:
   - Variable Name: `MGLS_LICENSE_FILE`
   - Variable Value: `C:\flexlm\LR-076522_License.dat`
7. The Questa license setup is now complete.

> **Note**: Quartus does not require a separate license file after installation.

---

## Additional Resources

- [Intel Self Licensing](https://licensing.intel.com)
- [Intel Quartus Prime Lite Download](https://www.intel.com/content/www/us/en/software-kit/825278/intel-quartus-prime-lite-edition-design-software-version-23-1-1-for-windows.html)
