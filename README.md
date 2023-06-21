![LibraryBuild](https://github.com/BeelanMX/Beelan-LoRaWAN/workflows/LibraryBuild/badge.svg?branch=master)

Arduino LoRaWAN library by Beelan for ZumIoT
====================
This repository contains the simple LoRaWAN library originally created by Ideetron B.V. This library is slightly
modified and encapsulated to run in the generic platform, allowing using the SX1272, SX1276 transceivers and compatible modules (such as some HopeRF RFM9x modules).

This repository is all based on this [repo]( https://git.antares.id/lorawan-loraid/arduino-loraid), with the principal purpose to port it to the frequencies of indonesia AS923_2 with extra channel 923.2 and 923.4.

To find out how to use the library itself, see the examples, or see the PDF file in the doc subdirectory.

**THIS LIBRARY IS USING AS923 ONLY**
Frequency Used
--------------
- 921.4 MHz
- 921.6 MHz
- 921.2 MHz
- 921.8 MHz
- 922.0 MHz
- 922.8 MHz
- 923.2 MHz (extra channel)
- 923.4 MHz (extra channel)

Data Rate Used for Downlink and Uplink (Default)
--------------
DR2 = SF10BW125

Frame Port Used
---------------
Frame Port Tx = 5

Features
--------
The arduino LoRaWAN library supports LoRaWAN Class A and Class C implementations operating in AS-923-2 bands. Note that this library is fairly simple with the aim of demonstrating the LoRaWAN capabilities.

What certainly works:
 - Sending packets uplink, taking into account duty cycling. --> ABP
 - Custom frequencies and datarate settings.
 - Receiving downlink packets in the RX1 window (AS923-2). --> ABP
 - Over-the-air activation (OTAA / joining) (AS923-2). --> STILL ERROR
 - Class C operation.
 - Receiving downlink packets in the RX2 window.

What has not been tested:
 - Receiving and processing MAC commands.

If you try one of these untested features and it works, be sure to let
us know (creating a github issue is probably the best way for that).

Installing
----------
To install, DOWNLOAD THE LIBRARY, UNZIP IT, AND COPY TO Arduino/libraries

Configuration
-------------
Only for AS923-2 with extra channel 923.2 and 923.4

Supported hardware
------------------
This library is intended to be used with plain LoRa transceivers,
connecting to them using SPI. In particular, the SX1272 and SX1276
families are supported (which should include SX1273, SX1277, SX1278 and
SX1279 which only differ in the available frequencies, bandwidths and
spreading factors). It has been tested with both SX1272 and SX1276
chips, using the Semtech SX1272 evaluation board and the HopeRF RFM92
and RFM95 boards (which supposedly contain an SX1272 and SX1276 chip
respectively).

Some of the supported pre-built board currently available in the market are:

- [Cytron Shield LoRa-RFM](https://www.cytron.io/p-shield-lora-rfm) 
- [Dragino](http://www.dragino.com/products/module/item/102-lora-shield.html) 
- [Electronic Cats CatWAN USB-Stick](https://www.tindie.com/products/electroniccats/catwan-usb-stick/)
- [Electronic Cats CatWAN Relay Board](https://www.tindie.com/products/electroniccats/catwan-relay-board/)
- [Electronic Cats CatWAN Shield](https://www.tindie.com/products/electroniccats/catwan-shield-lora-and-lorawan-for-arduino/)

This library has been tested using:

- Arduino Uno
- WeMos D1 R2 (ESP8266 family board)
- ESP32
- Electronic Cats CatWAN USB-Stick
- Electronic Cats Bast-WAN (Based on SAMR34)

Connections
-----------
To make this library work, your Arduino (or whatever Arduino-compatible
board you are using) should be connected to the transceiver. The exact
connections are a bit dependent on the transceiver board and Arduino
used, so this section tries to explain what each connection is for and
in what cases it is (not) required.


Pin mapping
-----------
As described above, most connections can use arbitrary I/O pins on the
Arduino side. To tell the arduino LoRaWAN library about these, a pin mapping struct is used in the sketch file.

***ADDED**
Support ESP32 HSPI and VSPI with variable .loraHSPI as parameter
- .loraHSPI = false -> Using VSPI
- .loraHSPI = true  -> Using HSPI
* **loraHSPI must defined**

For example, this could look like this:

	sRFM_pins RFM_pins = {
        .loraHSPI = false,
	  	.CS = SS,
	  	.RST = RFM_RST,
	  	.DIO0 = RFM_DIO0,
	  	.DIO1 = RFM_DIO1,
	  	.DIO2 = RFM_DIO2,
	  	.DIO5 = RFM_DIO5
  	}; 
  	
API
--------
This library provides a high-level API for connecting the device to Antares server.

### LoRa module initialization
Initialize LoRa/LoRaWAN module. Must be called once in the Arduino setup block.

#### Syntax
```c
  void init(void);
```

#### Example
```c
void setup() {
  // Setup loraid access
  lora.init();
  ...
}
```

### Setup Authentication Keys for ABP activation
Setup authentication keys for your LoRaWAN device, including device address.

#### Syntax
```c
void setAppSKey(unsigned char *ApskKey_in);
void setNwkSKey(unsigned char *NwkKey_in);
```

#### Example
```c
void setup() {
  // Setup loraid access
  if(!lora.init()){
  Serial.println("RFM95 not detected");
  while(1);
  }
  ...

  // Put Antares Key and DevAddress here
  lora.setNwkSKey("b7300d9f68b649ed30530f9dd69f9afe");
  lora.setAppSKey("9d52eef7fab63eda18794d0e503ddf20");
  ...

}
```

### Setup Device address
Setup device address for activating the device.

#### Syntax
```c
void setDevAddr(String devAddr_in);
```

#### Example
```c
void setup() {
  // Setup loraid access
  if(!lora.init()){
  Serial.println("RFM95 not detected");
  while(1);
  }
  ...

  // Put Antares Key and DevAddress here
  lora.setDevAddr("07000007");
  ...

}
```

### Setup Authentication Keys for OTAA activation
Setup authentication keys for your LoRaWAN device, including device address.

#### Syntax
```c
void setDevEUI(const char *devEUI_in);
void setAppEUI(const char *appEUI_in);
void setAppKey(const char *appKey_in);
```

#### Example
```c
void setup() {
  // Setup loraid access
  if(!lora.init()){
    Serial.println("RFM95 not detected");
    while(1);
  }
  ...

  // Put OTAA credentials here
  lora.setDevEUI("b7300d9f68b649ed");
  lora.setAppEUI("8b649ed30530f9dd");
  lora.setAppKey("9d52eef7fab63eda18794d0e503ddf20");
  ...

}
```
### Join Procedure
Need to join in the network

#### Syntax
```c
bool join();
```

#### Example
```c
void setup() {
  // Setup loraid access
  if(!lora.init()){
    Serial.println("RFM95 not detected");
    while(1);
  }

  // Set LoRaWAN Class change CLASS_A or CLASS_C
  lora.setDeviceClass(CLASS_A);

  // Set Data Rate
  lora.setDataRate(SF9BW125);

  // set channel to random
  lora.setChannel(MULTI);
  
  // Put OTAA Key and DevAddress here
  lora.setDevEUI(devEui);
  lora.setAppEUI(appEui);
  lora.setAppKey(appKey);

  // Join procedure
  bool isJoined;
  do {
    Serial.println("Joining...");
    isJoined = lora.join();
    
    //wait for 10s to try again
    delay(10000);
  }while(!isJoined);
  Serial.println("Joined to network");
}
```


### Set Device Class
Set class of the device (Class A or Class C). Input as `CLASS_A` or `CLASS_C` enum.
#### Syntax
```c
void setDeviceClass(devclass_t dev_class);
```

#### Example
```c
void setup() {
  // Setup loraid access
  if(!lora.init()){
  Serial.println("RFM95 not detected");
  while(1);
  }
  ...

  // Set LoRaWAN Class
  lora.setDeviceClass(CLASS_A);
  ...
}

```
### Set Channel
You can set channel allowed in your region (AS_923, EU_868 or US915).
 - For US_915 the channels can be [0 - 7]
 - Use MULTI if you want random channel

### Syntax
```c
void setChannel(unsigned char channel);
```
#### Example
```c
void setup() {
  // Setup loraid access
  if(!lora.init()){
    Serial.println("RFM95 not detected");
    while(1);
  }
  ...

  // Set random Channel
  lora.setDataRate(MULTI);
}

```

### Set Data Rate
You can set data rate allowed in your region (AS_923, EU_868 or US915).

##	For AS923
| data_rate | Name | Config          |Direction
|-----------|------|-----------------|----------------
| 0         | DR0  | SF12 BW 125 KHz | Uplink/Downlink
| 1         | DR1  | SF11 BW 125 KHz | Uplink/Downlink
| 2         | DR2  | SF10 BW 125 KHz | Uplink/Downlink
| 3         | DR3  | SF9 BW 125 KHz  | Uplink/Downlink
| 4         | DR4  | SF8 BW 125 KHz  | Uplink/Downlink
| 5         | DR5  | SF7 BW 125 KHz  | Uplink/Downlink
| 6         | DR6  | SF7 BW 250 KHz  | Uplink/Downlink


### Syntax
```c
void setDataRate(unsigned char data_rate);
```

#### Example
```c
void setup() {
  // Setup loraid access
  if(!lora.init()){
  Serial.println("RFM95 not detected");
  while(1);
  }
  ...

  // Set Data Rate to SF10 BW 125 KHz
  lora.setDataRate(2);
}

```

### Send data to LoRaWAN
You need to specify the length of data you want to send and also the message type (unconfirmed or confirmed message). Set `confirm = 0` to send unconfirmed message and `confirm = 1`' to send confirmed message.

#### Syntax
```c
void sendUplink(unsigned char *data, unsigned int len, unsigned char confirm);
```        

#### Example
```c
void loop() {
  // put your main code here, to run repeatedly:
  char myStr[] = "Ini data LoRaku";  

  lora.sendUplink(myStr, strlen(myStr), 0);
  ...

} 
```


### Update and run LoRa FSM
Update and run the LoRa Finite State Machine (FSM). This line should be put inside the Arduino `loop` block.

#### Syntax
```c
void update(void);
```

#### Example
```c
void loop() {
  ...

  // Check Lora RX
  lora.update();
}

```

In this version we'll try to use interrupts in order to eliminate this FSM.

### Check and retrieve incoming data
Check for the latest incoming data from server either in binary or string format. You need to provide char buffer to read the data.
#### Syntax
```c
void readData(void);
```

#### Example
```c

char buffer_rx[255];

void setup() {
  ...
}

void loop() {
  int recvStatus;
  ...

  // LoRa FSM
  lora.update();

  // Check data
  recvStatus = lora.readData(buffer_rx);
  if(recvStatus) {
    Serial.println(buffer_rx);
  }
}
```

Tests
-------
 - See [Examples](examples/) 

Maintainer
-------

Beelan invests time and resources providing this open source design, please support Beelan!

Nur Ardli Rachmat S / envyst

License
-------
Most source files in this repository are made available under the
MIT License. The examples which use a more liberal
license. Some of the AES code is available under the LGPL. Refer to each
individual source file for more details.

