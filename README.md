# PCB for GNice 1268F30
The FossaSat cubesats (www.fossa.systems) are intended to provide free and open source IoT communications using relatively inexpensive LoRa modules.  These include the Semtech sx126x and sx127x series devices.  Such devices are offered by a variety of suppliers including GNice RF https://www.nicerf.com/ and Dorji http://dorji.com/.  

For use in the amateur 70cm frequency range, examples include the LORA1268-160mW (https://www.nicerf.com/product_193_308.html) and the Dorji DRF1268T (http://dorji.com/products-detail.php?ProId=64) chips.  

These modules may be controlled using Arduino or ESP32 microcontrollers, among others.  Examples of operational setups may be found here https://github.com/FOSSASystems and here https://github.com/G4lile0/ESP32-OLED-Fossa-GroundStation/wiki/Arduino-IDE.

Unfortunately, the I/O pin spacing on these devices is different, and often quite small.  While Dorji offers breakout boards, GNice does not and custom PCBs are often required to build a transceiver.

The nominal output of the devices mentioned above is +22 dBm.  However, some manufacturers are now providing Semtech sx126x based transceivers with built in power amplifiers rasing the output to ~ 30dBm.  The purpose of this repo is to describe the design of a simple PCB for the GNiceRF 1268F30 1.5W module (https://www.nicerf.com/product_193_312.html).  This chip has an SPI interface tom allow for interfacing with SPI based microcontrollers.  
