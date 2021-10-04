![Logo](admin/wireless-mbus.png)
# ioBroker.wireless-mbus

This adapter allows to receive wireless M-Bus data from supported receivers. The extent of device implementation varies, but wMBus modes can be configured for all listed devices.

* Embit WMB modules
* Amber Wireless AMB8465 (**Beware:** Command mode (UART_CMD_Out_Enable) is enabled!)
* IMST iM871A
* CUL (experimental!)

The WMBUS stack has been "re-ported" from FHEM project and was extensively fixed and refactored. Testing was done with raw data picked up on the internet, OMS sample data and some test data from the jmbus library. Some edge cases are still untested.

The device creation, updating, etc is mostly based of Apollon77's M-Bus adapter (see below).

If the adapter receives encrypted telegrams the AES key configuration tab should list the device ID automatically.

If the parser fails the raw telegram data will be saved to the info.rawdata state.

*Attention:* The Amber receiver seems to crash after some time (or amount of received messages) in C mode? Hardware flaw?

## Links:
* [WMBus Stack module](https://github.com/mhop/fhem-mirror/blob/master/fhem/FHEM/WMBus.pm)
* [ioBroker.mbus](https://github.com/Apollon77/ioBroker.mbus)
* [Original WMBUS Stack: wm-bus](https://github.com/soef/wm-bus)
* [M-Bus protocol](http://www.m-bus.com/files/MBDOC48.PDF)
* [OMS Specifications](https://oms-group.org/en/download4all/oms-specification/)

## Initial setup

The initial setup requires to configure the basics (hardware connection to the wmbus) and to setup AES keys for all encrypted wmbus nodes to be collected. The most tricky part are the AES keys.

### Basic setup

This requires to select the appropriate USB device and the correct baud rate (**usually** for IMST: 57600 baud; Amber: 9600 baud; Embit: 9600 baud). Most meters will send in "T Mode".

### Other options

* **Update unchanged states**: When a telegram arrives all states will be updated, even if their value did not change. (default: on)
* **Cache for compact frames support**: To support compact telegrams (used by some (Kamstrup?) devices) the structure of all receveived telegrams is cached. This means usually only one cache entry per device. If you do not have any device which sends compact telegrams you can disable it to save a bit of performance and memory. (default: off)
* **Force energy units to kWh**: All energy units (Wh and J) will be converted to kWh. (default: off)
* **Temporarily block device after consecutive failures**: If 10 consecutive telegrams of the same device is not parsed successfully the device will be ignored until adapter restart (default: on)


### AES keys

The device identifier is a combination of the manufacturer code and the device ID (e.g. AAA-12345678). The key can be entered either as a plain-text key with 16 characters or as a hex string with 32 characters (16 bytes).

The easiest way to setup the keys is to start the adapter without any key setup and to wait for an encrypted telegram, after which an entry with "UNKNOWN" key is generated by the adapter. Then you can fill in the corresponding key and save the settings. If you see devices you don't know or just want to get rid of (e.g. devices from neighbours), you can enter them in the blocked devices tab.

## ToDo

* sending telegrams for S mode receivers?
* CUL support needs testing

## Changelog

### 0.7.1 / 0.7.2
* (ChL) Rename to ioBroker.wireless-mbus to be able to publish to npm
* (ChL) Fix block list, admin page logo and repo url in package.json

### 0.7.0
* (ChL) Change main adapter code to class
* (ChL) Include actual (machine) translations besides English and German
* (ChL) Upgrade denpendencies
* (ChL) Add test for wmbus decoder
* (ChL) Add integration tests
* (ChL) Add github workflow

### 0.6.2
* (ChL) Improve admin page to handle custom serialport path
* (ChL) Add option to turn automatic blocking of devices off
* (ChL) Add "Simple Hexstring" receiver for testing purposes
* (ChL) Internal refactoring

### 0.6.0 / 0.6.1
* (ChL) Upgrade of serialport library to 9.2.0
* (ChL) experimental CUL support

### 0.5.2
* (ChL) fix for connection indicator with js-controller 2.x

### 0.5.1
* (ChL) Small fixes
* (ChL) Internal telegram parser now supports wired M-Bus frames (not used - for testing / developing purpose)
* (D Glaser) Added timestamp of last update to device info
* (D Glaser/ChL) Added some setup documentation to README

### 0.5.0
* (ChL) Basic support for Techem devices
* (ChL) Option to force energy units (Wh and J) to kWh - BEWARE this is not really backwards compatible. Old states will keep their "old" unit, but display the adjusted value!

### 0.4.7
* (ChL) Block devices after 10 consecutive failed parse attempts until adapter restart
* (ChL) Assign roles derived from units (as does the mbus adapter)

### 0.4.6
* (ChL) Support for (Kamstrup?) compact frames through data record cache (pre-defined frames have been removed!)

### 0.4.5
* (ChL) Append device ids with key "UNKNOWN" at startup to needskey

### 0.4.2 / 0.4.3 / 0.4.4
* (ChL) Small fixes

### 0.4.1
* (ChL) basic IMST iM871A support

### 0.4.0
* (ChL) better Amber Stick support
* (ChL) Compact mode?
* (ChL) Nicer state names
* (ChL) wMBus mode partially selectable

### 0.3.0
* (ChL) Implemented all VIF types from MBus doc
* (ChL) VIF extensions are handled better (again)
* (ChL) reorganised VIF info
* (ChL) reorganised receiver handling
* (ChL) blocking of devices possible

### 0.2.0 (not tagged)
* (ChL) Dramatically improved parser: support for security mode 7, frame type B, many small fixes
* (ChL) VIF extensions are handled better, but correct handling is still not fully clear
* (ChL) CRCs are checked and removed if still present
* (ChL) raw data is saved if parser fails

### 0.1.0
* (ChL) initial release

## License

Copyright (c) 2019 ISFH - Institute for Solar Energy Research www.isfh.de
Copyright (c) 2021 Christian Landvogt

Licensed under GPLv2. See [LICENSE](LICENSE) and [NOTICE](NOTICE)
