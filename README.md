# 30 dBm output LoRa Transceiver for Fossasats using a simple PCB for the GNice 1268F30 Module


The Internet of things (IoT, e.g., wireless sensor networks, "smart home" devices") provides a means to transfer data over a network. Low-Power Wide Area Networks (LPWANs) are wireless. Compared to WiFi and Bluetooth, LPWANs are well suited for applications involving low-power sensors sending small amounts of data over long distances. Currently, a very popular IoT communication technology is LoRa (Long Range).  LoRA is a low-power wide-area network protocol based on spread spectrum modulation techniques. It was developed by Cycleo of Grenoble, France and subsequently acquired by Semtech. Unfortunately, Semtech has not disclosed the physical layer specifications. Since LoRa is not open source, there has been some debate as to whether or not LoRa has a place in the Amateur Radio spectrum utilization.  Nevertheless, LoRa has achieved some impressive communication distance records, albeit at low data rates. Thus, the transmission of relatively small amounts of satellite telemetry make the LoRa protocal an obvious choice.

The FossaSat cubesats (www.fossa.systems) transmit in the 70cm Amateur radio satellite sub-band using relatively inexpensive LoRa modules.  LoRa modules may be obtained from Semtech, one of its authorized partners or licensees and typically incorporate the Semtech sx126x and sx127x series chips. The nominal output of these Semtech chips is ~22 dBm.  For use in the amateur 70cm frequency range, examples include the LORA1268-160mW (https://www.nicerf.com/product_193_308.html) and the Dorji DRF1268T (http://dorji.com/products-detail.php?ProId=64) chips.  

These modules have an SPI interface and are suitable for Arduino or ESP32 microcontrollers, among others.  Examples of operational setups may be found here, https://github.com/FOSSASystems and here, https://github.com/G4lile0/ESP32-OLED-Fossa-GroundStation/wiki/Arduino-IDE.  The latter software is more sophisticated, incorporates an OLED display, and connects to the Telegram application for data sharing and messaging among users.  These sketches rely on RadioLib https://github.com/jgromes/RadioLib, which allows its users to integrate different wireless communication modules, protocols and digital modes into a single consistent system. 

Unfortunately, the pin spacing on the LoRa modules described above is typically not the standard breadboard pitch (2.54mm), often differs between suppliers, and incorporates quite small spacing.  While some have been successful performing micro-wiring, custom PCBs are more convenient to build a transceiver.  Some third party breakout boards are available.  However, free on-line PCB design tools exist and small scale production of simple PCBs (e.g., 2 layer) can be pretty inexpensive.  

Manufacturers are now providing Semtech sx126x based transceivers with built in power amplifiers rasing the output to ~30dBm.  The purpose of this repo is to document the design of a simple PCB for the GNiceRF 1268F30 1.5W module (+30 dBm) (https://www.nicerf.com/product_193_312.html) and how to interface it with several SPI based microcontrollers.  Thgis device incudes a simple XO only, so TCXO voltage must be set to 0 in the microcontroller sketch.  The design files (prepared using EasyEDA), Gerber files and BOM are included in the repo. 

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/PCBv1.2.png)

By using standard headers with 2.54mm spacing, this PCB piggybacks onto a standard PCB which in turn may also be populated with an SPI controller such as an Arduino Nano or ESP32 based controller.  Ground traces shown in blue.  Note - the antenna ground remains isolated, per the GNice RF datasheet. 

The GNiceRF 1268F30 requires a 5V DC supply and sources ~600mA during the TX cycle. For those applications where provision of external DC power is desired, the PCB design incudes a simple current limiting resistor (R1) or a voltage divider (R1, R2) upstream of a 5V regulator.  As noted above, given that the current consumption during the TX cycle is ~ 600mA, R1 alone (or bypassed) may suit and R2 is not required. Pads for a standard 7805 regulator are included if an external DC supply of > 5V is used.  Even if an external power input is used, the 5V supply line from the controller must be connected otherwise the 1269F30 will return an error code. The PCB design also includes pads for filtering capacitors.  Further information on these may be found in the 7805 datasheet or application notes. 

The build approach is to mount the daughter board and the controller on a pre-sized PCB and solder tack several of the unused header pins to hold things in place.  The headers serve as standoffs.  Make sure everything is lined up before tacking the unused pins down.  Then, use point to point wiring for the connections.  I prefer 30AWG wire wrapping for the connections.  Once the microcintroller is flashed and performance verified, a touch of solder on the wirewrap connections makes them permanent.  Builders choice as to do this solder step or not!

Example wire wrapping of connections.
![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4491.PNG)

**Connections to the Arduino nano**

Considering the very intermittent duty cycle for Fossasat applications, the 5V pin on the Nano (which passes 5V from the computer USB port) seems quite adequate in my installation to drive the PA in the 1268F30. In my Nano builds, I have included D1 using a low voltage drop device (1N5819 Schottkey), and jumpered R3.  The rest of the PCB components are not required.  

Pin definition for the Nano interface below.  Be sure to update any Arduino sketches accordingly!  These should also work for the Uno.

| CHIP | Nano GPIO |
| ---- | ----|
| DIO1 | DO2 |
| BUSY | D09 |
| NSS  | D10 |
| MOSI | D111 |
| MISO | D12 |
| SCK  | D13 |
| GND  | GND |
| VCC  | 5V  |

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4483.png)

**Connections to the ESP32 WROOM device with an OLED display**

In the case of the ESP32 WROOM/OLED design, I chose to use the external power input - R1 (10 ohms), R2 absent, the 7805, filtering capacitors, D1(1N5819) and RX (33 ohms).  The 5V pin from the ESP32 is connected to the 5V input on the lower right corner.  A single header pin offers a convenient wire wrap connection point.  This is also the case for the ground conenction between the devices.  It may be that the ESP32 circuit works without an external DC supply by jumpering D1 and R3, but I have not tried this.

Pin definition for the ESP32 interface below.  Be sure to update any Arduino or Platformio sketches accordingly!

| CHIP | ESP32 GPIO |
| ---- | ----|
| OLED VCC | 3.3V |
| BUSY | P26 |
| DI01  | P27 |
| RESET* | P14 |
| VCC | 5V |
| GND  | OLED GND, 1268F30 GND |
| MOSI | P23 |
| OLED SCL | P22 |
| OLED SCA | P21 |
| MISO | P19 |
| SCK | P18 |
| NSS | P5 |
| GND  | GND |
| 1268F30 VCC  | 5V  |

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4444.png)

If an error code is returned during startup, RadioLib offers a list of reasons https://jgromes.github.io/RadioLib/group__status__codes.html. 
From my experience
| Code | Reason |
| ---- | ----|
| -2 | check board wiring |
| -5 | check external input voltage present if using ESP32 |
| -20 | unplug, re-plugin USB cable |
| -707  | verify that TCXO is set to 0 volts |

Have fun!  Hope to see you on Fossasat!
