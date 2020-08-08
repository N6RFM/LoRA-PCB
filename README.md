# High power LoRa Transceiver for Fossasats.  A simple PCB for the GNice 1268F30 Module.
The FossaSat cubesats (www.fossa.systems) are intended to provide free and open source IoT communications using relatively inexpensive LoRa modules.  These include the Semtech sx126x and sx127x series devices.  Such devices are offered by a variety of suppliers including GNice RF https://www.nicerf.com/ and Dorji http://dorji.com/.  

For use in the amateur 70cm frequency range, examples include the LORA1268-160mW (https://www.nicerf.com/product_193_308.html) and the Dorji DRF1268T (http://dorji.com/products-detail.php?ProId=64) chips.  

These modules have an SPI interface and may be controlled using Arduino or ESP32 microcontrollers, among others.  Examples of operational setups may be found here https://github.com/FOSSASystems and here https://github.com/G4lile0/ESP32-OLED-Fossa-GroundStation/wiki/Arduino-IDE.

Unfortunately, the pin spacing on these devices is not the standard breadboard pitch (2.54mm)  different between suppliers,and quite small.  So far only Dorji offers breakout boards, and custom PCBs are often required to build a transceiver.

The nominal output of the modules mentioned above is ~22 dBm.  However, some manufacturers are now providing Semtech sx126x based transceivers with built in power amplifiers rasing the output to ~30dBm.  The purpose of this repo is to describe the design of a simple PCB for the GNiceRF 1268F30 1.5W module (https://www.nicerf.com/product_193_312.html) and interfacing with several SPI based microcontrollers.


![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/PCBv1.2.png)

By using heades with 2.54mm spacingm, this PCB piggybacks onto a standard PCB which in turn may also be populated with an Arduino nano, or ESP32 based controller.
The GNiceRF 1268F30 requires a 5V supply and sources ~600mA during the TX cycle.  

For those applications where provision of external DC power is desired, then the PCB incudes provision for a simple current limiting resistor (R1) or a voltage divider (R1, R2).  As noted above, given that the current consumption during the TX cycle is ~ 600mA R1 alone (or bypassed) may suit. Pads for a standard 7805 regulator are included if an external DC supply is used.  In this case, the 5V supply line from the controller must also be enabled otherwise the 1269F30 will return an error code. The PCB also includes pads for filtering capaitors, if desired.  Further information on these may be found in the 7805 datasheet or application notes. 

Connections to the Arduino nano

Considering the very intermittent duty cycle for Fossasat applications, the 5V pin on the Nano (which passes 5V from the computer USB port) seems quite adequate to drive the PA in the 1268F30. When using a Nano, D1 and R3 may be bypassed.  In some Nano builds, I have included D1 using a low voltage drop device (1N5819 Schottkey) and jumpered R3.

Pin definition for the Nano interface below.  Be sure to update any Arduino sketches accordingly
| CHIP | Nano GPIO |
| ---- | ----|
| DIO1 | DO2 |
| BUSY | D09 |
| NSS  | D10 |
| MISO | D12 |
| MOSI | D11 |
| SCK  | D13 |
| GND  | GND |
| VCC  | 5V  |


![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4483.png)

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/IMG_4444.png)
