# 30 dBm output LoRa Transceiver for Fossasats using a simple PCB for the GNice 1268F30 Module


The Internet of things (IoT, e.g., wireless sensor networks, "smart home" devices") provides a means to transfer data over a network. Low-Power Wide Area Networks (LPWANs) are wireless. Compared to WiFi and Bluetooth, LPWANs are well suited for applications involving low-power sensors sending small amounts of data over long distances. Currently, a very popular IoT communication technology is LoRa (Long Range).  LoRA is a low-power wide-area network protocol based on spread spectrum modulation techniques. It was developed by Cycleo of Grenoble, France and subsequently acquired by Semtech. Unfortunately, Semtech has not disclosed the physical layer specifications. Since LoRa is not open source, there has been some debate as to whether or not LoRa has a place in the Amateur Radio spectrum utilization.  Nevertheless, LoRa has achieved some impressive communication distance records, albeit at low data rates. Thus, for the transmission of relatively small amounts of satellite telemetry, the LoRa protocol is an obvious choice.

The FossaSat cubesats (www.fossa.systems) transmit in the 70cm Amateur Radio satellite sub-band using relatively inexpensive LoRa modules.  These modules may be obtained from Semtech, one of its authorized partners or licensees and typically incorporate the Semtech sx126x and sx127x series chips. The nominal TX output is ~22 dBm.  For use in the amateur 70cm frequency range, examples include the LORA1268-160mW (https://www.nicerf.com/product_193_308.html) and the Dorji DRF1268T (http://dorji.com/products-detail.php?ProId=64) chips.  

Some modules have an SPI interface to allow easy interfacing with Arduino or ESP32 microcontrollers, among others.  Examples of operational setups may be found here, https://github.com/FOSSASystems and here, https://github.com/G4lile0/ESP32-OLED-Fossa-GroundStation/wiki/Arduino-IDE.  The latter software is more sophisticated, supports an OLED display, and connects to the Telegram application for telemetry data sharing and messaging among registered users.  These packages  rely on RadioLib https://github.com/jgromes/RadioLib, which allows its users to integrate different wireless communication modules, protocols and digital modes into a single consistent system. 

Unfortunately, the pin spacing on the LoRa modules described above is typically not the standard breadboard pitch (2.54mm), often differs between suppliers, and incorporates quite small spacing.  While some have been successful performing micro-wiring, custom PCBs offer a more convenient means to build a transceiver.  Few vendor and third party breakout boards are available.  However, free on-line PCB design tools exist and small scale production of simple PCBs (e.g., 2 layer) can be inexpensive.  

Some manufacturers (e.g., GNice RF, eByte and Dorji) are now providing or developing LoRA transceivers with built in power amplifiers rasing the output to ~30dBm.  The purpose of this repo is to document the design of a simple PCB for the GNiceRF 1268F30 1.5W module (+30 dBm) (https://www.nicerf.com/product_193_312.html) and how to interface it with several SPI based microcontrollers.  The 1268F30 device incorporates an XO, so the TCXO voltage must be set to 0 in the microcontroller software.  The PCB design files (prepared using EasyEDA), Gerber files and a BOM are included in the repo. 

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/PCBv1.2.png)

By using standard headers with 2.54mm spacing, this PCB mounts piggy-back style onto a standard PCB, which in turn may be populated with an SPI controller such as an Arduino Nano or ESP32 based controller.  Ground traces are shown in blue.  Note - the antenna ground (shown in red) remains isolated, per the GNice RF datasheet. 

The GNiceRF 1268F30 requires a 5V DC supply and sources ~600mA during the TX cycle. For those applications where provision of external DC power is desired, the PCB design incudes a simple current limiting resistor (R1) or a voltage divider (R1, R2) upstream of the 5V regulator.  Given that the current consumption during the TX cycle is ~ 600mA, R1 alone (or bypassed) is sufficient and R2 is therefore not required. Users wishing to incorporate a voltage divider approach can populate both R1 and R2.  Pads for a standard 7805 regulator are also included, if an external DC supply of > 5V is required.  When an external power input is used, the 5V supply line from the controller must also be connected.  Otherwise the 1269F30 will return a RadioLib transmit timeout (-5) error code. The PCB design also includes pads for filtering capacitors.  Further information on these capcitor values may be found in the 7805 datasheet or application notes. 

My build approach is to mount the daughter board and the controller on a pre-sized PCB and tack solder several of the unused header pins to hold the boards in place.  The headers serve as standoffs.  Make sure everything is lined up before tacking a few unused pins down.  Then, use point to point wiring for the connections.  I prefer 30AWG wire wrapping for the connections.  Once the microcontroller is flashed and performance verified, a touch of solder on the wirewrap connections makes them permanent.  Builders choice as to do this solder step or not!

Example wire wrapping of connections.
![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4491.PNG)

**Connections to the Arduino nano**

Considering the very intermittent duty cycle for Fossasat applications, the 5V pin on the Nano (which passes 5V from the computer USB port) seems quite adequate in my installation to drive the PA in the 1268F30. In my Nano builds, I have however included D1 using a low voltage drop device (1N5819 Schottkey), and jumpered R3.  The rest of the PCB components are not required.  

Pin definition for the Nano interface below.  Be sure to update any Arduino sketches accordingly!  These should also work for the Uno.

| CHIP | Nano GPIO |
| ---- | ----|
| DIO1 | D2 |
| BUSY | D9 |
| NSS  | D10 |
| MOSI | D11 |
| MISO | D12 |
| SCK  | D13 |
| GND  | GND |
| VCC  | +5V  |

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4483.png)

**Connections to the ESP32 WROOM device with an OLED display**

In the case of the ESP32 WROOM/OLED design, I chose to populate the PCB for the external power input by including R1 (10 ohms), leaving R2 absent, adding the 7805 and filtering capacitors, as well as D1(1N5819) and R3 (33 ohms).  The 5V pin from the ESP32 is also connected to the 5V input on the lower right corner.  A single header pin offers a convenient wire wrap connection point.  This is also the case for the ground conenction between the devices.  I then suply either 9 or 12V DC to the PCB via the barrel jack connector.  It may be that the ESP32 circuit will work without an external DC supply by jumpering D1 and R3 and not including the 5V supply, but I have not tried this with the 1268F30/ESP32 combination.

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

RESET* is not explicity used and handled in the RadioLib software. This hardwried connection may not be needed, but I have included it and it does not seem to cause a problem.

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4444.png)

If an error code is returned during startup, RadioLib offers a list of reasons https://jgromes.github.io/RadioLib/group__status__codes.html. 
From my experience
| Code | Reason |
| ---- | ----|
| -2 | check board wiring |
| -5 | check external input voltage present if using ESP32 |
| -20 | unplug, re-plugin USB cable |
| -707  | verify that TCXO is set to 0 volts |

To insulate the bottom of the boards, they are placed atop 3D printed trays with 7mm high walls.  The blue tape in the picture above is how the PCB is held in place. 

Have fun!  Hope to see you on Fossasat!
