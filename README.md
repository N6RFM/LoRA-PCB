# 30 dBm output LoRa Transceiver for Fossasats using a simple PCB for the GNice 1268F30 Module


The Internet of things (IoT, e.g., wireless sensor networks, "smart home" devices") provides a means to transfer data over a network. Low-Power Wide Area Networks (LPWANs) are wireless. Compared to WiFi and Bluetooth, LPWANs are well suited for applications involving low-power sensors sending small amounts of data over long distances. Currently, a very popular IoT communication technology is LoRa (Long Range).  LoRA is a low-power wide-area network protocol based on spread spectrum modulation techniques. It was developed by Cycleo of Grenoble, France and subsequently acquired by Semtech. Unfortunately, Semtech has not disclosed the physical layer specifications. Since LoRa is not open source, there has been some debate as to whether or not LoRa has a place in the Amateur Radio spectrum utilization.  Nevertheless, LoRa has achieved some impressive communication distance records, albeit at low data rates. Thus, the transmission of relatively small amounts of satellite telemetry make the LoRa protocal an obvious choice.

The FossaSat cubesats (www.fossa.systems) transmit in the 70cm Amateur radio satellite sub-band and are intended to provide free and open source IoT communications using relatively inexpensive LoRa modules.  LoRa modules may be obtained from Semtech, one of its authorized partners or licensees and incorporate the Semtech sx126x and sx127x series chips. The nominal output of the Semtech chips is ~22 dBm.  For use in the amateur 70cm frequency range, examples include the LORA1268-160mW (https://www.nicerf.com/product_193_308.html) and the Dorji DRF1268T (http://dorji.com/products-detail.php?ProId=64) chips.  

These modules have an SPI interface and may be controlled using Arduino or ESP32 microcontrollers, among others.  Examples of operational setups may be found here https://github.com/FOSSASystems and here https://github.com/G4lile0/ESP32-OLED-Fossa-GroundStation/wiki/Arduino-IDE.  The latter software is more sophisticated, incorporates an OLED display, and connects to the Telegram application for data sharing and messaging among users.

Unfortunately, the pin spacing on the LoRa modules described above is typically not the standard breadboard pitch (2.54mm), often differs between suppliers, and incorporates quite small spacing.  While some have been successful performing micro-wiring, custom PCBs are often required to more conveniently build a transceiver.  Some manufacturers are now providing Semtech sx126x based transceivers with built in power amplifiers rasing the output to ~30dBm.   

The purpose of this repo is to describe the design of a simple PCB for the GNiceRF 1268F30 1.5W module (+30 dBm) (https://www.nicerf.com/product_193_312.html) and interfacing it with several SPI based microcontrollers.  The design files (prepared using EasyEDA) and BOM are included.  

![alt text](https://github.com/N6RFM/LoRA-PCB/blob/master/pix/PCBv1.2.png)

By using headers with 2.54mm spacing, this PCB piggybacks onto a standard PCB which in turn may also be populated with an SPI controller such as an Arduino nano or ESP32 based controller.  The GNiceRF 1268F30 requires a 5V supply and sources ~600mA during the TX cycle. Ground traces shown in blue.  Note - the antenna ground remains isolated, per the GNice RF datasheet. 

For those applications where provision of external DC power is desired, the PCB incudes provision for a simple current limiting resistor (R1) or a voltage divider (R1, R2).  As noted above, given that the current consumption during the TX cycle is ~ 600mA R1 alone (or bypassed) may suit. Pads for a standard 7805 regulator are included if an external DC supply of higher voltage is used.  In this case, the 5V supply line from the controller must also be connected otherwise the 1269F30 will return an error code. The PCB also includes pads for filtering capacitors, if desired.  Further information on these may be found in the 7805 datasheet or application notes. 

**Connections to the Arduino nano**

Considering the very intermittent duty cycle for Fossasat applications, the 5V pin on the Nano (which passes 5V from the computer USB port) seems quite adequate in my installation to drive the PA in the 1268F30. In my Nano builds, I have included D1 using a low voltage drop device (1N5819 Schottkey) and jumpered R3.

Pin definition for the Nano interface below.  Be sure to update any Arduino sketches accordingly!
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
