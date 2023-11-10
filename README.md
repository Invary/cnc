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





