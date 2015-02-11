<H1>Extended Android Debug Bridge</H1>
A script designed to make using adb with multiple devices easier.

<H2>Features</H2>
<ul>
  <li>Provides a menu if you forget to specify a device when multiple devices are attached.</li>
  <li>Provides the option to run the adb command on all attached devices</li>
  <li>Allows pushing multiple files at once.</li>
  <li>Easy assigning specific terminal sessions to a device</li>
  <li>Naming devices</li>
</ul>

<H2>Usage</H2>
<pre>
Extended Android Debug Bridge
 -a                            - directs eadb to listen on all interfaces for a connection
 -d                            - directs command to the only connected USB device
                                 returns an error if more than one USB device is present.
 -e                            - directs command to the only running emulator.
                                 returns an error if more than one emulator is running.
 -s <specific device>          - directs command to the device or emulator with the given
                                 serial number or qualifier. Overrides ANDROID_SERIAL
                                 environment variable.
 -p <product name or path>     - simple product name like 'sooner', or
                                 a relative/absolute path to a product
                                 out directory like 'out/target/product/sooner'.
                                 If -p is not specified, the ANDROID_PRODUCT_OUT
                                 environment variable is used, which must
                                 be an absolute path.
 -H                            - Name of eadb server host (default: localhost)
 -P                            - Port of eadb server (default: 5037)
 devices [-l]                  - list all connected devices
                                 ('-l' will also list device qualifiers)
 connect <host>[:<port>]       - connect to a device via TCP/IP
                                 Port 5555 is used by default if no port number is specified.
 disconnect [<host>[:<port>]]  - disconnect from a TCP/IP device.
                                 Port 5555 is used by default if no port number is specified.
                                 Using this command with no additional arguments
                                 will disconnect from all connected TCP/IP devices.

device commands:
  eadb push [-p] <local> <remote>
                               - copy file/dir to device
                                 ('-p' to display the transfer progress)
  eadb pull [-p] [-a] <remote> [<local>]
                               - copy file/dir from device
                                 ('-p' to display the transfer progress)
                                 ('-a' means copy timestamp and mode)
  eadb sync [ <directory> ]     - copy host->device only if changed
                                 (-l means list but don't copy)
                                 (see 'adb help all')
  eadb shell                    - run remote shell interactively
  eadb shell <command>          - run remote shell command
  eadb emu <command>            - run emulator console command
  eadb logcat [ <filter-spec> ] - View device log
  eadb forward --list           - list all forward socket connections.
                                 the format is a list of lines with the following format:
                                    <serial> " " <local> " " <remote> "\n"
  eadb forward <local> <remote> - forward socket connections
                                 forward specs are one of: 
                                   tcp:<port>
                                   localabstract:<unix domain socket name>
                                   localreserved:<unix domain socket name>
                                   localfilesystem:<unix domain socket name>
                                   dev:<character device name>
                                   jdwp:<process pid> (remote only)
  eadb forward --no-rebind <local> <remote>
                               - same as 'adb forward <local> <remote>' but fails
                                 if <local> is already forwarded
  eadb forward --remove <local> - remove a specific forward socket connection
  eadb forward --remove-all     - remove all forward socket connections
  eadb jdwp                     - list PIDs of processes hosting a JDWP transport
  eadb install [-l] [-r] [-d] [-s] [--algo <algorithm name> --key <hex-encoded key> --iv <hex-encoded iv>] <file>
                               - push this package file to the device and install it
                                 ('-l' means forward-lock the app)
                                 ('-r' means reinstall the app, keeping its data)
                                 ('-d' means allow version code downgrade)
                                 ('-s' means install on SD card instead of internal storage)
                                 ('--algo', '--key', and '--iv' mean the file is encrypted already)
  eadb uninstall [-k] <package> - remove this app package from the device
                                 ('-k' means keep the data and cache directories)
  eadb bugreport                - return all information from the device
                                 that should be included in a bug report.

  eadb backup [-f <file>] [-apk|-noapk] [-obb|-noobb] [-shared|-noshared] [-all] [-system|-nosystem] [<packages...>]
                               - write an archive of the device's data to <file>.
                                 If no -f option is supplied then the data is written
                                 to "backup.ab" in the current directory.
                                 (-apk|-noapk enable/disable backup of the .apks themselves
                                    in the archive; the default is noapk.)
                                 (-obb|-noobb enable/disable backup of any installed apk expansion
                                    (aka .obb) files associated with each application; the default
                                    is noobb.)
                                 (-shared|-noshared enable/disable backup of the device's
                                    shared storage / SD card contents; the default is noshared.)
                                 (-all means to back up all installed applications)
                                 (-system|-nosystem toggles whether -all automatically includes
                                    system applications; the default is to include system apps)
                                 (<packages...> is the list of applications to be backed up.  If
                                    the -all or -shared flags are passed, then the package
                                    list is optional.  Applications explicitly given on the
                                    command line will be included even if -nosystem would
                                    ordinarily cause them to be omitted.)

  eadb restore <file>           - restore device contents from the <file> backup archive

  eadb help                     - show this help message
  eadb version                  - show version num

scripting:
  eadb wait-for-device          - block until device is online
  eadb start-server             - ensure that there is a server running
  eadb kill-server              - kill the server if it is running
  eadb get-state                - prints: offline | bootloader | device
  eadb get-serialno             - prints: <serial-number>
  eadb get-devpath              - prints: <device-path>
  eadb status-window            - continuously print device status for a specified device
  eadb remount                  - remounts the /system partition on the device read-write
  eadb reboot [bootloader|recovery] - reboots the device, optionally into the bootloader or recovery program
  eadb reboot-bootloader        - reboots the device into the bootloader
  eadb root                     - restarts the eadbd daemon with root permissions
  eadb usb                      - restarts the eadbd daemon listening on USB
  eadb tcpip <port>             - restarts the eadbd daemon listening on TCP on the specified port
networking:
  eadb ppp <tty> [parameters]   - Run PPP over USB.
 Note: you should not automatically start a PPP connection.
 <tty> refers to the tty for PPP stream. Eg. dev:/dev/omap_csmi_tty1
 [parameters] - Eg. defaultroute debug dump local notty usepeerdns

adb sync notes: eadb sync [ <directory> ]
  <localdir> can be interpreted in several ways:

  - If <directory> is not specified, both /system and /data partitions will be updated.

  - If it is "system" or "data", only the corresponding partition
    is updated.

environmental variables:
  eadb_TRACE                    - Print debug information. A comma separated list of the following values
                                 1 or all, eadb, sockets, packets, rwx, usb, sync, sysdeps, transport, jdwp
  ANDROID_SERIAL               - The serial number to connect to. -s takes priority over this if given.
  ANDROID_LOG_TAGS             - When used with the logcat option, only these debug tags are printed.

eadb specific commands:
  setdefault                    - Makes a device from the list the default device when running eadb.
  unsetdefault                  - Allows the prompt to show up if there are more than one devices.
  toggle-silent                 - Shows/hides the device sn/name when using a default device.
  show-default                  - Shows the current default device
  name                          - Stores a name for a device
  wait (mode)                   - Waits until a specific mode is detected
  plogcat [package name]        - Runs logcat on a specific package name
  slogcat (-p package_name) search terms
  screenshot                    - Grabs a screenshot
</pre>

<H2>Install</H2>
echo /path/to/eadb.functions >> ~/.bashrc<br/>
source /path/to/eadb.functions<br/>
<b>or</b><br/>
source eadb.functions
