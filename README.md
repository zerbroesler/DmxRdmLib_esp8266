# DmxRdmLib for ESP8266
[![Build Status](https://travis-ci.com/JonasArnold/DmxRdmLib_esp8266.svg?branch=master)](https://travis-ci.com/JonasArnold/DmxRdmLib_esp8266)
Initial version by [Matthew Tong](https://github.com/mtongnz/espDMX), June 2016.  This library is derived from the HardwareSerial library.

Merged with the [version with RDM](https://github.com/mtongnz/ESP8266_ArtNetNode_v2/tree/master/libs/espDMX_RDM) (does not have its own git repo) which was used in the [ESP8266_ArtNetNode_v2](https://github.com/mtongnz/ESP8266_ArtNetNode_v2) project.

This library will transmit up to 2 DMX universes from an ESP8266 module. It utilizes the hardware UARTs and is entirely interrupt driven meaning the DMX output has very precise timing.

The DMX will refresh at a minimum rate of 44Hz.  The library will detect how many channels have been set, outputting less than 512 if possible to increase the refresh rate. This increases the responsiveness of fixtures. It will still output a full 512 channels at least once per second.

## USAGE:

dmxA uses the same uart as *Serial*, dmxB uses the same uart as *Serial1*.  If you wish to use a serial port, dont call the .begin() function of the relevant dmx port.

espDMX is driven entirely by the TX FIFO empty interupts.  To ensure constant output and accurate timing, ensure that global interupts aren't stopped for too long.

**Note:** I will be using dmxN in place of dmxA or dmxB as the commands for each are identical.

Include the following code in setup.  ledPin is the pin to put status LED on, ledIntensity is the intensity (0 - 255) allowing the LEDs to be dimmed.  Neither parameter is required.
```
  // No status LED
  dmxN.begin();
  
  // Status LED at full intensity
  dmxN.begin(ledPin);
  
  // Status LED at specified intensity
  dmxN.begin(ledPin, ledIntensity);
```
To change the LED intensity later, use the following:
```
  dmxN.ledIntensity(newIntensity);
```
Now set some channels.  data is a byte array up to 512 length, numChans is the number of channels you're setting, startChan is the channel number (1 - 512) of the first item in data.
```
  dmxN.setChans(data, numChans, startChan);
  
  // assume startChan = 1
  dmxN.setChans(data, numChans);
  
  // assume 512 channels starting at channel 1
  dmxN.setChans(data);
```
**Note:** No data will be sent until the first setChans() function is called.  This is to ensure no "zero" data is sent on power up or reboot.

To stop DMX transmission, use the following function.  It will leave the DMX line in an idle (HIGH) state.  You can also pause or unPause the output which disables the interupts
```
  dmxN.end();
  dmxN.pause();
  dmxN.unPause();
```

To clear the data buffer and reset all channels to zero:
```
  dmxN.clearChans();
```

To get a pointer to the data buffer:
```
  dmxN.getChans();
```
