# LVSD-module
LVSD or Linux Virtual Serial Driver is used to create a Virtual Serial Device/Mount point for any kind of data transfer.
It has two parts:(But are linked in one module named lvsd.ko)
1) Control Interface
2) Serial Core

1) Control Interface is a character driver module.
- Which supports IOCTL call to create and destroy the serial mount point dynamically at run-time as needed by user application.

2) Serial Core supports the core function of the driver.
- open/read/write/close/ are the main functions which are required of the serial mount point and are supported.

Additional Note :
Apart from this it also supports events for all file operations which can be sent to user-space application using this driver.

The module can be compiled for a native target for testing like Ubuntu etc by modifying the Makefile.

It supports two memory mapped pages of 12KB for writes on /dev/ctrlX and /dev/lvsd side and vice-versa.

Psuedo Code:
vspparentapp.c
/*This app can be another endpoint for vsp user app (ex. vspuserapp*/

vspcreatorapp.c
/*Open (/dev/ctrlx)*/ (This control interface is available when you insert this module)

/*ioctl (LVSD_CREATE_VSP) on /dev/ctrlX  */

/*mmap for user space to kernel space*/

/*Create thread to receive events and updates from driver core*/

/*Get a serial mount point in /dev (ex. /dev/lvsd "lvsd being the name passed")*/


vspuserapp.c

/* Open (/dev/lvsd)*/

/* Do Write/Read/Close as required */


NOTE: Look at Source code to understand better

LVSD OSS Final Tested release.
- Functionalities
	- Multiple Virtual Serial Device(VSD) creation via /dev/ctrlX interface.
	- Open/Close/Read/Write Operations can be performed successfully on the VSD.
	- VSAL Side writes are done on WBuffer(Memory Mapped Page) supporting a max size of (12KB - 1) in 1 iteration.
	- Application Side writes are done on RBuffer(Memory Mapped Page) supporting a max size of (12KB - 1) in 1 iteration.
Known Issues
	- Sometimes, if a write/read is in progress from application side and the VSP is destroyed/deleted, the TTY layer does not handle this very well and may crash sometime or have a recursive boot fault.
	- Stable fix for this will be provided in the coming time but not immediately.
