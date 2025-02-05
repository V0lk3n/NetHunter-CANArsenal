# NetHunter-CANArsenal
This repo is used as documentation for my CAN Arsenal addition in NetHunter apk.

## Description

This module allow you to control your CAN Interface and diagnostic your car with can-utils and cannelloni tools. Device should have CAN, SLCAN, VCAN, CAN-RAW, CAN-GW, CAN-BCM enabled as module in the kernel, optionally enable USB_SERIAL_CH341 and CAN USB Interfaces Drivers too.

## Prerequisite - Kernel Modification

In section “Networking support”:

- Select "CAN bus subsystem support"

Under "CAN bus subsystem support --->"

- Select "Raw CAN Protocol (raw access with CAN-ID filtering)"
- Select "Broadcast Manager CAN Protocol (with content filtering)"
- Select "CAN Gateway/Router (with netlink configuration)"

Under "CAN Device Drivers --->"

- Select "Virtual Local CAN Interface (vcan)"
- Select "Serial / USB serial CAN Adaptors (slcan)"
- Select "Plaatform CAN drivers with Netlink support"
- Select "CAN bit-timing calculation"
- Select "Enable LED triggers for Netlink based drivers"
- Select "IFI CAN_FD IP"
- Select "Softing Gmbh CAN generic support"
- Select "CAN devices deubbing messages"

Under "CAN SPI interfaces --->"

- Select "Microchip MCP251x SPI CAN controllers"

Under "CAN USB interfaces --->"

- Select "EMS CPC-USB/ARM7 CAN/USB interface"
- Select "ESD USB/2 CAN/USB interface"
- Select "Geschwister Schneider UG interfaces"
- Select "Kvaser CAN/USB interface"
- Select "PEAK PCAN-USB/USB Pro interfaces for CAN 2.0b/CAN-FD"
- Select "8 devices USB2CAN interface"

![](https://github.com/V0lk3n/NetHunter-CANArsenal/blob/main/img/can_kernel_modification.gif)

In section "Device Drivers ---> USB support ---> USB Serial Converter support --->" :

- Select "USB Winchiphead CH341 Single Port Serial Driver"

![](https://github.com/V0lk3n/NetHunter-CANArsenal/blob/main/img/CH341_Driver.png)

## CAN Arsenal Module Documentation

## Menu

<img src="/img/01-menu.jpg" alt="menu"/>

#### Documentation

This button will open the following documentation in a Browser.

#### Setup

This button will run Setup to install needed CAN tools and packages. Note that it shouldn't be needed as it should be launched as first run.

#### Update

This button will update the installed CAN tools and packages.


## Settings

<img src="/img/02-settings.jpg" alt="menu" width="500"/>

These settings are needed to be used as variable while running commands with the buttons interaction.


## Modules

<img src="/img/03-modules.jpg" alt="menu" width="500"/>

If these USB Modules drivers are enabled in your Kernel. You can use the dropdown to select the desired module, and press the button to load it (if unloaded) or to unload it (if loaded).

Note : Full path of modules should be specified in the Modules tab of the Nethunter App.


## Interface

<img src="/img/04-interface.jpg" alt="menu" width="500"/>

Here you can Start or Stop CAN, VCAN or SLCAN interface.

#### CAN

Command run to Start CAN :

Prerequisite : Set "CAN Interface" and "UART Speed" in Settings

```bash
modprobe -a can can-raw can-gw can-bcm
sudo ip link set <CAN Interface> type can bitrate <UART Speed>
sudo ip link set up <CAN Interface>
```

Command run to Stop CAN interface :

Prerequisite : Set "CAN Interface" in Settings

```bash
modprobe -r can-raw can-gw can-bcm can
sudo ip link set <CAN Interface> down
```

#### VCAN

Command run to Start VCAN :

Prerequisite : Set "CAN Interface" and "MTU" in Settings

```bash
modprobe -a vcan can can-raw can-gw can-bcm
ip link add dev <CAN Interface> type vcan && ip link set <CAN Interface> mtu <MTU>
ip link set up <CAN Interface>
```

Command run to Stop VCAN interface :

Prerequisite : Set "CAN Interface" in Settings

```bash
modprobe -r can-raw can-gw can-bcm vcan can
sudo ip link set <CAN Interface> down 
```

#### SLCAN

Command run to Start SLCAN :

Prerequisite : CAN USB Adapter plugged in and set "CAN Interface", "CAN Speed" and "UART Speed" in Settings
Note : Flow Control is set to Software as default

```bash
modprobe -a can slcan can-raw can-gw can-bcm
sudo slcan_attach -f -s<CAN Speed> -o /dev/ttyUSB0
sudo slcand -o -s<CAN Speed> -t sw -S <UART Speed> /dev/ttyUSB0 <CAN Interface>
sudo ip link set up <CAN Interface>
```

Command run to Stop SLAN interface :

Prerequisite : CAN USB Adapter plugged in and set "CAN Interface" in Settings

```bash
sudo ip link set <CAN Interface> down
sudo slcan_attach -d /dev/ttyUSB0
modprobe -r can-raw can-gw can-bcm can slcan
```


## Tools

<img src="/img/05-tools.jpg" alt="menu" width="500"/>

#### Can-Utils - CanGen

Command to run CanGen to generate CAN traffic :

Prerequisite : Started your desired CAN interface and set "CAN Interface" in Settings

```bash
cangen <CAN Interface> -v
```

#### Can-Utils - CanSniffer

Command to run CanSniffer to sniff CAN traffic :

Prerequisite : Started your desired CAN interface and set "CAN Interface" in Settings

```bash
cansniffer <CAN Interface>
```

#### Can-Utils - CanDump

Command to run CanDump to dump CAN traffic to a file :

Prerequisite : Started your desired CAN interface and set "CAN Interface", "Output" path in Settings

```bash
candump <CAN Inteface> -f <Output Log>
```

#### Can-Utils - CanSend

Command to run CanSend to replay a specific sequence :

Prerequisite : Started your desired CAN interface and set "CAN Interface", "Sequence" in Settings

```bash
cansend <CAN Interface> <Sequence>
```

#### Can-Utils - CanPlayer

Command to run CanPlayer to replay dumped sequences in a log file :

Prerequisite : Started your desired CAN interface and set "CAN Interface", "Input" path in Settings

```bash
canplayer -I <Input Log>
```

#### Custom Script - SequenceFinder

<a href="sequence_finder.sh">You can see the source code here.</a>

Command to run SequenceFinder to find the exact sequence of the desired action from a log file :

Note : This custom script will auto split a log files using head and tail, replay these in loop using CanPlayer until finding the exact sequence of the desired action and replay it with CanSend.

Prerequisite : Started your desired CAN interface and set "Input" path in Settings

```bash
/opt/car_hacking/sequence_finder.sh <Input Log>
```


## Cannelloni

<img src="/img/06-cannelloni.jpg" alt="menu" width="500"/>

Command to run Cannelloni to communicate with two machine on a CAN interface by Ethernet :

Prerequisite : Started your desired CAN interface in Settings and set "RHOST", "RPORT" and "LPORT" in Cannelloni.

```bash
sudo cannelloni -I <CAN Interface> -R <RHOST> -r <RPORT> -l <LPORT>
```


## Logging

<img src="/img/07-logging.jpg" alt="menu" width="500"/>

#### Asc2Log

Command to run Asc2Log to convert ASC format file to classic log file :

Prerequisite : Set "Input" and "Output" path in Settings.

```bash
asc2log -I <Input Log> -O <Output File>
```


#### Log2Asc

Command to run Log2Asc to convert dumped log in the ASC format :

Prerequisite : Set "Input", "Output" path and "CAN Interface" in Settings.

```bash
log2asc -I <Input Log> -O <Output File> <CAN Interface>
```


## Custom Command

<img src="/img/08-custom_command.jpg" alt="menu" width="500"/>

This is in case you need to run a specific command which doesnt match the one provided.




