# How to setup/use CNC3040 with GRBL

## About

Smaller CNCs such as the CNC3018 use GRBL as the controller. <br />
However, for CNC3040 and above, Mache 3/4, which is older in design and less user-friendly, is often used. Here we show how to build a CNC3040 system with GRBL/wifi. <br />

![CNC3040](https://raw.githubusercontent.com/Invary/cnc/main/img/all_480x270.png)



<br />

## Preparation / CNC3040Z

Required <br/>
* CNC DIY frame. 3Axis [CNC3040Z](https://s.click.aliexpress.com/e/_DC8N8X1) without motor <br />
* 2 motor. Dual 8mm shaft NEMA23 motor, [23HS5628-SZ](https://s.click.aliexpress.com/e/_Dlx0W5N) for X/Z axis <br />
* 1 motor. Mono 8mm shaft NEMA23 motor, [23HS5628-8mm](https://s.click.aliexpress.com/e/_DmWwv0B) for Y axis <br />
* 3 coupling, [8mm x 8mm](https://s.click.aliexpress.com/e/_DkbfAH5) <br />
* 3 driver. [DM542](https://s.click.aliexpress.com/e/_DnLqMan) <br />
* Spindle. [BLDC 300W spindle](https://s.click.aliexpress.com/e/_DeVVoub) with holder, speed meter and ER11 <br />
* GRBL board. [32bit 6axis CNC card](https://s.click.aliexpress.com/e/_DBUy0Rt) <br />
* Power unit. [24V 360W-Quiet](https://s.click.aliexpress.com/e/_DdIBWQr) for motors <br />
* Power unit. [36V 400W-Quiet](https://s.click.aliexpress.com/e/_DdIBWQr) for spindle <br />
* Shielded cable. [4core 18AWG 10m](https://s.click.aliexpress.com/e/_DBOf5Yn) for motors <br />
* Shielded cable. [3core 18AWG 5m](https://s.click.aliexpress.com/e/_DBOf5Yn) for spindle <br />
* microSD card. 1GB <br />

Optional <br />
* Ribon cable. [28AWG 5m](https://s.click.aliexpress.com/e/_DCX7pV1) <br />
* XH2.54 connector. [230Pcs XH2.54](https://s.click.aliexpress.com/e/_Dmz7UOf) for motors <br />
* Ferrule connector. [Many types of 340pcs](https://s.click.aliexpress.com/e/_DCKdpaT) for GRBL board and drivers <br />
* Terminal connector. [OT-3A](https://s.click.aliexpress.com/e/_DBOf5Yn) for power units <br />
* Drag chain. [10x20mm 2m](https://s.click.aliexpress.com/e/_DDre0dV) <br />
* 3D printer <br />

** Shielded cable is required. Unshielded cables will cause motor errors such as stalling. <br />

<br />

## Test wifi connection

GRBL board, [32bit 6axis CNC card](https://s.click.aliexpress.com/e/_DBUy0Rt), is equipped with an EPS32 and has a standard [FluidNC](https://github.com/bdring/FluidNC)(GRBL for ESP32) firmware burned in. <br />
Wireless connectivity is supported. Before wiring, first check this operation. <br />
This board is compatible with 12-24V power supply, 36V is not support. <br />

Connect to 24V power supply. Then the on-board LED glows momentarily and ESP32 operates as an access point mode. <br />
Enable the wireless on your smartphone or PC and you can find the SSID **`GRBL_ESP`**. The password is **`12345678`**. <br />

Once connected to the GRBL board, access **`http://192.168.100.101/`** in your browser. <br />
If connection failed, try another URL **`http://192.168.100.104/`** , **`http://192.168.101.100/`** or so on. Or, open a command prompt on your PC and run **`arp -a`** to identify the IP address = URL. <br />

<br />

## Motors Wiring

GRBL board, [32bit 6axis CNC card](https://s.click.aliexpress.com/e/_DBUy0Rt), is equipped with an EPS32 and has a standard [FluidNC](https://github.com/bdring/FluidNC)(GRBL for ESP32) firmware burned in. <br />

* PUL/DIR/EN pins are 5V high/low output. DO NOT connect to high voltage power supply. <br />
* High voltage of PWM-OUT is adjustable. <br />
* Both 24V pins are directly connected. Therefore, they can be used for both IN and OUT. <br />

![GRBL board](https://raw.githubusercontent.com/Invary/cnc/main/img/grbl_board_960x1396.png)

<br />

[DM542](https://s.click.aliexpress.com/e/_DnLqMan) is stepper motor driver. <br />
This is compatible with 20-50V power supply. <br />

* PUL-/DIR-/ENA- pins are connected to GND. <br />
* If you want to keep motor always on, connect both ENA+/ENA- pins to GND. <br />
* When wiring more than 2m, use shileded cable, or motor trouble will occur. <br />

<br />


Dual 8mm shaft NEMA23 motor, [23HS5628-SZ](https://s.click.aliexpress.com/e/_Dlx0W5N) for X/Z axis <br />
Mono 8mm shaft NEMA23 motor, [23HS5628-8mm](https://s.click.aliexpress.com/e/_DmWwv0B) for Y axis <br />
23HS5628 is 56mm height, 2.8A, 126N.cm. <br />

* Pin assignments vary by product, so check with a tester. <br />

<br />

The direction of motor rotation is not important because it can be changed by software.

DIP switch setting of DM542 is **`1.0A`**, **`Full Current`**, **`25600`** steps. <br />
The motor's rated current is 2.8 A, but it is better to keep the value small unless it is necessary because it generates more heat. <br />

CNC3040Z's ball screw are SFU1605 with 5mm pitch. <br />
In other words, the 25600 steps in DM542 are 5mm, so 5120 steps/mm. <br />

![Connections for motors](https://raw.githubusercontent.com/Invary/cnc/main/img/connection_motor_960x896.png)

![Setting of DM542](https://raw.githubusercontent.com/Invary/cnc/main/img/dm542_side_960x451.jpg)



<br />

## Spindle wiring

[300W spindle](https://s.click.aliexpress.com/e/_DeVVoub) is brushless spindle motor. <br />
RPM can be manually set by volume or automatically adjusted by G-CODE. <br />

For automatic control, turn the PWM-ADJ volume to 0-10V PWM output before wiring. <br />
To adjust, access GRBL board with a browser. Set speed **`1000rpm`** in spindle tab and press the **`On Fwd`** button. Then voltage is output to PWM-OUT pin. The voltage should be 10V. <br />

The direction of rotation of the motor is important. Be sure to set correctly by change order U/V/W to U/W/V, etc., if the rotation is in reverse. <br />


Manual control
![Manual control connections for spindle](https://raw.githubusercontent.com/Invary/cnc/main/img/connection_spindle_manual_960x641.png)

Automatic control
![Auto control connections for spindle](https://raw.githubusercontent.com/Invary/cnc/main/img/connection_spindle_auto_960x892.png)

<br />

## GRBL setting

To change the GRBL configuration, follow the steps below. <br />

1. Download the configuration file from GRBL board to your PC <br />
2. Edit the configuration file on your PC <br />
3. Upload the file to GRBL board <br />
4. Restart GRBL board <br />
<br />

Here is my [CNC3040Z configuration file](https://github.com/Invary/cnc/cnc3040z.yaml) <br />

The various port settings on the GRBL board are not problm as default. <br />
The following items are important <br />

**`steps_per_mm:`** of X/Y/Z set to **`5120.000`**. <br />
**`max_rate_mm_per_min:`** of X/Y/Z set to **`500.000`**. <br />

**`homing:cycle:`** of X/Y set to **`1`**. <br />
**`homing:cycle:`** of Z/A/B/C set to **`0`**. <br />

**`max_travel_mm:`** of X set to **`300mm`**. <br />
**`max_travel_mm:`** of Y set to **`400mm`**. <br />
**`max_travel_mm:`** of Z set to **`100mm`**. <br />

**`start:must_home;`** set to **`false`**. <br />

When the direction of the stepping motor is reverse rotation, edit **`direction_pin:`**. <br />
Just add **`:low`** to reverse rotation, ex. **`direction_pin: I2SO.4`** to **`direction_pin: I2SO.4:low`** <br />

<br />

## Offline controller

GRBL board, [32bit 6axis CNC card](https://s.click.aliexpress.com/e/_DBUy0Rt), has no port for offline controller. <br />
But you can control via wifi. Although it is not an offline controller, it can be used like an offline controller. <br />

1. With smart phone, access GBRL board with browser. <br />
2. Open **`Tablet`** tab. <br />
3. You can control CNC via wifi. <br />

<br />

## Stand alone CNC machining

GRBL board, [32bit 6axis CNC card](https://s.click.aliexpress.com/e/_DBUy0Rt), has a microSD slot. <br />
Stand-alone processing is available by uploading a g-code file in microSD. Upload can do via wifi. <br />

1. Access GBRL board with browser. <br />
2. Upload g-code file to microSD. File extension is like **`.nc`**. <br />
3. Set **`GRBL Reports:`** to **`Auto`** <br />
4. Set endmil to the spindle. <br />
5. Move spindle head to start position. <br />
6. Press **`��XYZABC`** button, work coordinates set to zero. <br />
7. Start CNC machining. <br />

If you want to abort, press pause and refresh button. <br />

<br />

## Serial connected CNC machining

GRBL board, [32bit 6axis CNC card](https://s.click.aliexpress.com/e/_DBUy0Rt), has a microUSB connector. <br />
It supports HID/serial protocol. So you can control CNC via serial port. <br />

<br />

## Serial connected CNC machining via wifi

GRBL board, [32bit 6axis CNC card](https://s.click.aliexpress.com/e/_DBUy0Rt), supports multiple wireless connection methods. <br />
So you can control CNC via wifi, and by using a wireless-serial port bridge with virtual serial port, it can be controlled like as wired connected CNC. <br />

* Bluetooth/serial <br />
* WebSocket via wifi <br />
* Telnet via wifi <br />

Bluetooth is for exclusive use with wifi. Therefore, enabling bluetooth is not recommended since wifi is not available. <br />

WebSocket is also used for control by browsers. This is not recommended as the connection is a bit unstable and can be dropped. <br />

So telnet/wifi is recommended. <br />

The following is an example of a setup is with [Candle](https://github.com/Denvi/Candle) for windows. <br />

1. Install virtual serial port [com0com](https://sourceforge.net/projects/com0com/files/com0com/3.0.0.0/). <br />
2. In setup of com0com, check **`enumulate baudrate`** and add one pair of virtual serial port, COM19 & COM20. <br />
3. [Download](https://github.com/Invary/IvyTcp2Serial/releases) latest version of [IvyTcp2Serial](https://github.com/Invary/IvyTcp2Serial/) and extract. <br />
4. Create shortcut file of **`IvyTcp2Serial.exe`** .<br />
5. Right click shortcut file and open property. <br />
6. Edit shortcut link **`IvyTcp2Serial.exe`** to **`IvyTcp2Serial.exe /server=192.168.100.101 /port=23 /comport=COM19 /baudrate=115200 /parity=none /stopbits=1`**. IP address and comport should be changed according to your environment. <br />
7. Execute IvyTcp2Serial with serial, and connected to CNC via wifi/telnet. <br />
8. Execute Candle with serial port like **`COM20`**. <br />
9. At first, send command to CNC, **`$Report/Interval=50`**. This is because the default frequency of coordinate report is low. <br />
10. Now you can control CNC via wifi with Candle. <br />

<br />






<br />

<br />

<br />


<br />

##  Suggestions and feedback
If you have any idea or suggestion, please add a github issue.

<br />



<br />
<br />
<br />

#### Copyright (c) Invary
