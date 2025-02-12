# WiTcontroller

A WiTcontroller is a simple DIY, handheld controller that talks to a wThrottle Server (JMRI, DCC++EX and many others) using the wiThrottle protocol to control DCC model trains. 

[See a video of it in use here.](https://youtu.be/RKnhfBCP_SQ)


[Discussion on Discord.](https://discord.com/channels/713189617066836079/735019837746577438)

[3d Printed Case](https://www.thingiverse.com/thing:5440351)

---

## Prerequisites

1. Some basic soldering skills.  

    The components will work if just plugged together using jumpers, but they take a lot of space that way, so soldering them together is advised to make it hand held.

2. Loading the code (sketch) requires downloading of one of the IDEs, this sketch, the libraries, etc. so some experience with Arduinos is helpful, but not critical.

3. A wiThrottle Server to connect to. WiTcontroller will work with any wiThrottle Server. e.g.

    * JMRI
    * DCC++EX
    * MRC WiFi
    * Dijitrax LnWi

---

## Building

Required Components

* WeMos Lite LOLIN32  (ESP32 Arduino with LiPo charger) ([Example](https://www.ebay.com.au/itm/284800618644?hash=item424f709094:g:-soAAOSwHslfC9ce&frcectupt=true))
* 3x4 Keypad  ([Example](https://www.jaycar.com.au/12-key-numeric-keypad/p/SP0770?pos=2&queryId=20aedf107668ad42c6fe1f8b7f7a9ca7))
* Polymer Lithium Ion Battery LiPo 400mAh 3.7V 502535 JST Connector (or larger capacity) ([500mAh Example](https://www.ebay.com.au/itm/133708965793?hash=item1f21ace7a1:g:tlwAAOSwfORgYqYK))
* KY-040 Rotary Encoder Module ([Example](https://www.ebay.com.au/itm/394147297055?hash=item5bc502771f:g:gr0AAOSwcixiwo2~&frcectupt=true))
* OLED Display 0.96" 128x64 Blue I2C IIC SSD1306 ([Example](https://www.ebay.com.au/itm/273746192621?ssPageName=STRK%3AMEBIDX%3AIT&_trksid=p2060353.m2749.l2649))
* Case - my one was 3d printed (see below)
* Knob ([Example](https://www.jaycar.com.au/35mm-knob-matching-equipment-style/p/HK7766?pos=7&queryId=cbd19e2486968bca41273cc2dbce54a4&sort=relevance))

![Assembly diagram](WiTcontroller%20pinouts%20v0.1.png)

Note:

* My case was 3D Printed for me by peteGSX (See the [Thingiverse.](https://www.thingiverse.com/thing:5440351) )
* The 3x4 keypad he designed the case for came from Jaycar and is slightly narrower than the one you see in the 'deconstructed' view in the video above.
* The case requires about a dozen M2x4mm screws

## Loading the code

1. Download the Arduino IDE. (I prefer to use VSC, but you still need to have the Arduino IDE installed.)
2. download the esp32 boards using the Boards Manager
3. Download or clone this repository. (Note: if you 'clone', it is easier to receive updates to the code.)
4. Load the needed libraries to your PC (these can loaded from the library manager)
    * WiFi.h https://github.com/espressif/arduino-esp32/tree/master/libraries/WiFi
    * ESPmDNS.h https://github.com/espressif/arduino-esp32/blob/master/libraries/ESPmDNS  (You should be able to download it from here https://github.com/espressif/arduino-esp32 Then unzip it and copy 'just' the ESPmDNS folder to your own libraries folder )
    * AiEsp32RotaryEncoder.h https://github.com/igorantolic/ai-esp32-rotary-encoder
    * Keypad.h https://www.arduinolibraries.info/libraries/keypad
    * U8g2lib.h   (Just get "U8g2" via the Arduino IDE Library Manager)
5. Manually download or clone my version of WiThrottleProtocol library to you PC (this **must** be placed in the 'libraries' folder) (Note: if you 'clone', it is easier to receive updates to the code.)
    * WiThrottleProtocol.h https://github.com/flash62au/WiThrottleProtocol
6. Copy **config_network_example.h** to **config_network.h**  
Then edit it to include the network ssids you want to use
7. Copy **config_buttons_example.h** to **config_buttons.h**  
Optionally, edit this to change the mapping of the keypad buttons to specific functions 
8. Upload the sketch  (Select the board type as 'WEMOS LOLIN32 Lite')

Note: WiFi.h and ESPmDNS.h were automatically installed for me at some point when I installed VSC and Platform.io, but if you are using the Arduino IDE, you may need to install them manually.

---

## Using WiTController

**Currently functioning:**
- provides a list of discovered SSIDs with the ability to choose one. When you select one:
  - if it is one in your specified list (in the sketch), it will use that specified password 
  - if it is a DCC++EX wiFi Command Station in access Point mode, it will guess the password
  - otherwise it will ask to enter the password (Use the rotary encoder to choose each character and the encoder button to select it.  * = backspace.  # = enter the password.) 
- Optionally provides a list of SSIDs with the specified passwords (in the sketch) to choose from
- Auto-connects to the first found wiThrottle Protocol Server if only one found, otherwise 
  - asks which to connect to
  - if none found will ask to enter the IP Address and Port
  - Guesses the wiThrottle IP address and Port for DCC++EX WiFi Access Point mode Command Stations
- Rudimentary on-the-fly consists
- Assign commands directly to the 1-9 buttons (in the sketch) (see list below)
  - this is done in config_button.h
  - latching / non-latching for the function is provided by the roster entry of wiThrottle server
- Command menu (see below for full list) including:
  - Able to select and deselect locos 
    - by their DCC address, via the keypad
      - On NCE systems, a leading zero (0) will force a long address
    - from the first 50 locos in the roster
  - Able to throw/close turnouts/points
    - from the address
    - from the first 50 turnouts/points in the server list
  - Able to activate routes
    - from their address
    - from the first 50 routes in the server list
  - set/unset a multiplier for the rotary encoder
  - Power Track On/Off
  - Disconnect / Reconnect
  - limited dealing with unexpected disconnects.  It will throw you back to the WiThtottle Server selection screen.
  - Put ESP32 in deep sleep and restart it
  - Boundary between short and long DCC addresses can be configured in config_buttons.h
  - The default speed step (per encoder click) can be configured in config_buttons.h

**ToDo:**
- speed button repeat (i.e. hold the button down)
- deal with unexpected disconnects better
  - automatic attempt to reconnect
- functions are currently only sent to the earliest selected loco
- change facing of a loco in consist
- keep a list of ip addresses and ports if mDNS doesn't provide any
- remember (for the current session only) recently selected locos
- restructure the '9' menu item.

### Command menu:
- 0-9 keys = pressing these directly will do whatever you has been preset in the sketch for them to do  (see \# below)
- \* = Menu:  The button press following the \* is the actual command:
  - 1 = Add loco.  
     - Followed by the loco number, followed by \# to complete.  e.g. to select loco 99 you would press '\*199\#'
     - or \# alone to show the roster   \# again will show the next page
  - 2 = release loco:
     - Followed by the loco number, followed by \# to release an individual loco.  e.g. to deselect the loco 99 you would press '\*299\#'
     - Otherwise followed directly by \#  to release all e.g. '\*2\#'
  - 3 = Change direction.  Followed by \# to complete.  e.g. forward '\*21\#'  reverse '\*20\#'
  - 4 = Set / Unset a 2 times multiplier for the rotary encoder dial.
  - 5 = Throw turnout/point.  
     - Followed by the turnout/point number, followed by the \# to complete.  e.g. Throw turnout XX12 '\*512\#'  (where XX is a prefix defined in the sketch) 
     - or \# alone to show the list from the server   \# again will show the next page
  - 6 = Close turnout.    
     - Followed by the turnout/point number, followed by \# to complete.  e.g. Close turnout XX12 '\*612\#'  (where XX is a prefix defined in the sketch)
     - or \# alone to show the list from the server
  - 7 = Set Route.    
      - Followed by the Route number, followed by \# to complete.  e.g. to Set route XX:XX:0012 '\*60012\#'  (where \'XX:XX:\' is a prefix defined in the sketch)
      - or \# alone to show the list from the server   \# again will show the next page
  - 0 = Function button. Followed by the loco number, Followed by \# to complete.  e.g. to set function 17 you would press '\*017\#'
  - 8 = Track Power On/Off. Followed by \# to complete.
  - 9 = Extra. 
      - Followed by \# to Disconnect/Reconnect.  
      - or followed by 8 to toggle the heartbeat check.
      - or followed by 9 then \# to put into deep sleep
Pressing '\*' again before the '\#' will terminate the current command (but not start a new command)
 - \# = Pressing # alone will show the function the the numbered keys (0-9) perform, outside the menu.

Pressing the Encoder button while the ESP32 is in Deep Sleep will revive it.


### Default number key assignments (0-9)  (outside the menu)

* 0 = FUNCTION_0 (DCC Lights)
* 1 = FUNCTION_1 (DCC Bell)
* 2 = FUNCTION_3 (DCC Horn/Whistle)
* 3 = FUNCTION_3
* 4 = FUNCTION_4
* 5 = FUNCTION_5
* 6 = SPEED_MULTIPLIER
* 7 = DIRECTION_REVERSE
* 8 = SPEED_STOP
* 9 = DIRECTION_FORWARD

### Allowed assignments for the 0-9 keys:

Note: you need to edit config_buttons.h to alter these assignments   (copy config_buttons_example.h)
- FUNCTION_NULL   - don't do anything
- FUNCTION_0 - FUNCTION_28
- SPEED_STOP
- SPEED_UP
- SPEED_DOWN
- SPEED_UP_FAST
- SPEED_DOWN_FAST
- SPEED_MULTIPLIER
- E_STOP
- POWER_TOGGLE
- POWER_ON
- POWER_OFF
- DIRECTION_TOGGLE
- DIRECTION_FORWARD
- DIRECTION_REVERSE