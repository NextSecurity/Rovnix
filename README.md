# Rovnix
Rovnix Bootkit Modified

Boot loader for drivers
------------------------

It allows you to download a specially compiled drivers when the operating system starts.
The driver is loaded to initialize the NT kernel, which means before the start of PatchGuard, and could patch any
 core code. The driver gains control before any other device drivers to load (including early
 All boot-load drivers) and can monitor and influence their load.
Digital signature No drivers are required.

It supports all Windows operating systems, starting with XP, and Windows 8, inclusive.
Supports two architectures: x86 and AMD64 (EM64T).
Boot-loader works with all types of NTFS-partitions.

Meeting of the Project consists of three main parts:
- Boot loader (IPL);
- Specially built, taking into account the possibility to start the NT kernel, driver;
- The installer program (or (DLL) library installer);

IPL metamorphic code, consists of a count of blocks that are shuffled in random order at
 Each project is built. IPL code is encrypted and decrypted dynamically at runtime.
Thus, each svezhesobrany IPL binary is different from the previous one.
The driver is also encrypted when written to disk, and stands at the start of the bootloader.

There is a limit on the size of the driver: by virtue of the technical features of the IPL operation, driver size
 can not exceed 100KB.

The project is built with the help of MS Visual Studio 2005 and MS Windows 7 WDK.


Additional components
-------------------------

The driver can contain the following additional components:

- Menedzher virtual filesystem. It creates a virtual, an encrypted (RC6) file system (VFS) in untagged
disk sectors. Provides User-mode interface for working with files on this file system.
- Filter disk access. Blocks access from "outside" to the sectors containing the IPL and the virtual file system. Hides
 a virtual file system.
- DLL injector. It allows you to upload (inject) DLL, located on VFS or attached directly to the
 Driver file in the specified processes. It has an interface for managing Inject from user-mode.
- Driver loader. It provides an interface to be able to download third-party unsigned drivers.

- A stack of TCP / IP protocols (including: ARP, ICMP, DNS). It provides drivers and user-mode application interfaces
to work with the network that is compatible with BSD-sockets.


The composition of the project
--------------
  1. Generator IPL (\ BkGen).
  2. loader Library (\ BkLib).
  3. The installer (\ BkSetup).
  4. The installer Library (\ SetupDll).
  5. Driver Injector (\ KLoader).
  6. The virtual file system library (\ FsLib).
  7. to load third-party drivers Library (\ DrvLdr).
  8. The library filter to protect the loader sector and the virtual file system (\ BkFilter).
  9. Utility to attach files (\ FJ).
  10. Utility to manage the virtual file system (\ VFS)
  11. The batch file to build an exemplary installer DLL and examples (\ BkBuild).


Generator IPL
-------------
  It is going only for the x86 executable file BkGen.exe
  When you start creating a file containing VBR.COM metamorphic code bootloader.
  Each time you generate a unique loader.


loader Library
------------------
  Going under X86 and for AMD64 to a static library (.lib).
  It contains the functions necessary for the installation and initialization of the loader.
  Imports installer and driver. See. Bklib.h file.


The installer
--------------------
  When assembling sought VBR.COM loader file and integrated into resources.
  Only by going to the x86 executable file BkSetup.exe.


Installer Library
---------------------
  It is going under x86 and x64 in the dynamic link library SetupDll.dll
  The library exports a single function: ULONG BkInstall (BOOL bReboot),
  a call which you are installing the boot loader on your system.
  If an error occurs, the function returns a Win32 error code.


Driver Injector
----------------
  It is going under the x86 and AMD64 as an NT driver (kloader.sys).
  Inject DLL in prikrёplennye ukazannae processes. List DLL and processes
  given the configuration file for the FJ utility.


Virtual FS Library
-------------------------
  Going under the x86 and AMD64, the links in the driver-injector (kloader.sys).
  It creates a virtual file system to the unallocated space of the system hard disk.
  If otstutsvie unallocated space of sufficient size decreases
  the size of the last partition on the disk.
  FS is a modified FAT16. Cluster size is the size of the physical
  disk sector. The maximum supported capacity of the virtual disk ~ 32MB.
  The file names in the 8.3 format, long filenames are not supported. Catalogs are not supported.
  FS is fully encrypted by RC6.


to load third-party drivers Library
-------------------------------------------
  Going under the x86 and AMD64, the links in the driver-injector (kloader.sys).
  It allows you to download and run a third-party NT kernel drivers images.


Filter library to protect the loader sector and the virtual file system
-------------------------------------------------- ---------------
  Going under the x86 and AMD64, the links in the driver-injector (kloader.sys).
  It filters out the low-level requests to read / write disk sectors.
  Prevents change (post) sector containing bootloader.
  Blocks changing third-party applications and drivers that contain sectors
  a virtual file system. When reading sectors containing virtual FS nullifies
  their contents, masking thus, the presence of PS.
  

Utility to attach files
-------------------------------
  Only by going in the x86 executable file FJ.EXE.
  Used to connect the injected DLL to the driver file for
  joining the driver file to the installer. See. \ FJ \ ReadMe.txt.


Batch file to build an exemplary Installer
-----------------------------------------
  BkBuild.bat - gathers installer with attachments thereto drivers kloader.sys
                x86 and amd64, respectively. Each driver attached
                DLL for injection produce.
  BkSetup.cfg - configuration file for Programmes at the assembly, installation and attachment to his driver.
  setupdll.cfg- configuration file for the assembly, installation of the library and attach it to the driver.
  demo32.dll - 32-bit demo library.
  demo64.dll - the 64-bit demo library.


assembly Procedure
--------------
  1. In Visual Studio 2005 help collect the entire project. First, gather under the i386,
     then, under amd64.
  2. Open the console (CMD.EXE), go to the folder \ BkBuild and run from the console
     BkBuild.exe
  3. Pick up ready-installer from the folder \ BkBuild \ Release.
