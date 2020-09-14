---
title: Create an ISO image for UEFI platforms
description: Describes how to create an UEFI bootable Windows PE RAM Disk on a CD-ROM for Windows Server 2008.
ms.date: 09/08/2020
author: Deland-Han
ms.author: delhan
manager: dscontentpm
audience: itpro
ms.topic: troubleshooting
ms.prod: windows-server
localization_priority: medium
ms.reviewer: kaushika
ms.prod-support-area-path: Setup
ms.technology: Depolyment
---
# Create an ISO image for UEFI platforms for a Windows PE CD-ROM for Windows Server 2008

This article describes how to create an International Standards Organization (ISO) image for a Windows Preinstallation Environment (Windows PE) CD-ROM for Windows Server 2008. This CD-ROM starts the computer by using Unified Extensible Firmware Interface (UEFI) firmware.

_Original product version:_ &nbsp; Windows Server 2012 R2  
_Original KB number:_ &nbsp; 947024

## Prerequisites

- You have installed one of the following configurations:
  - The Windows OEM Preinstallation Kit (OPK) together with Windows Server 2008
  - The Windows Automated Installation Kit (WAIK) together with Windows Server 2008
- The target platform is an x64-based (amd64) version of Windows Server 2008.
The Windows PE CD-Rom can be started from BIOS firmware or from UEFI firmware. The CD-ROM has two boot catalog entries. One platform ID entry corresponds to the BIOS, and one corresponds to the UEFI.

To create an ISO image for Windows PE on a CD-ROM, follow these steps:

1. Use administrative credentials to log on to a Windows Server 2008-based computer.
2. On the **Programs** Menu, click **Windows OEM Preinstallation Kit (OPK)**, and then click **Windows PE Tools Command Prompt**.

3. Type copype.cmd amd64 winpe_x64, and then press ENTER. This command creates the directory structure and copies the required files.
4. Type copy winpe.wim .\ISO\sources\boot.wim, and then press ENTER. This command will copy the winpe.wim created under winpe_x64 to the ISO\sources folder as boot.wim. 
5. Type the following command, and then press ENTER: oscdimg -m -o -u2 -udfver102 -bootdata:2#p0,e,bc:\winpe_x64\etfsboot.com#pEF,e,bc:\winpe_x64\efisys.bin c:\winpe_x64\ISO c:\winpe_x64\winpeuefi.iso 
    > [!NOTE]
    > See the "Definitions" section for more information about this command.
6. Burn the ISO image (Winpeuefi.iso) to a CD or to a DVD.

## Oscdimg command arguments

- m  
Ignores the maximum size limit of the image.
- o  
Optimizes storage by encoding duplicate files only one time.
- u2  
Produces an ISO image that has only the Universal Disk Format (UDF) file system on it.
- udfver102  
Specifies the UDF version 1.02 format.
- bootdata  
Specifies a multiboot image. This image uses an x86-based boot sector as the default image. This sector starts the Etfsboot.com boot code. A secondary EFI boot image starts an EFI boot application.
- c:\winpe_x64\ISO  
Represents the path of the files for the image.
- c:\winpe_x64\winpeuefi.iso  
Represents the output image file.

## Bootdata command arguments

- 2  
Specifies the number of boot catalog entries.
- `#`  
Functions as the separator between root entries to be put into the boot catalog.
- p0  
Sets the platform ID to 0 for the first, default boot entry for the BIOS.
- e  
Specifies the floppy disk emulation in the El Torito catalog.
- bc:\winpe_x64 \etfsboot.com  
Puts the specified file (Etfsboot.com) in the boot sectors of the disk.
- `#`  
Functions as the separator between the first and second boot entries.
- pEF  
Sets the platform ID to "EF," as defined by the UEFI specification.
- bc:\winpe_x64\efisys.bin  
Puts the specified file (Efisys.bin) in the boot sector of the disk. Efisys.bin is the binary floppy disk layout of the EFI boot code. This disk image contains the files that are used to start from the EFI firmware in the Efi\boot\x64boot.efi folder.