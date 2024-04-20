# Framing  
Often, in prototyping, you want to rapidly set-up wireless Wizard of Oz testing. Several communication protocols can be used for this, but this repo covers the usage of **infrared signals** . 

- Infrared needs line of sight
- Signals get encoded and need to be decoded
- If you use existing remote controls
  - Different transmission protocols are used, usually modulated on a carrier wave of **38 kHz**
  - Most commonly used transmission protocol: **[NEC protocol](https://lookin-home.medium.com/how-does-the-remote-control-work-explained-564bc7cd0291)**
  - Key presses are read as **hexadecimal codes** that are different for each remote
- Receiver diodes receive IR light
  - Come in versions with IC and without breakout board
  - Filters 28 kHz only
  - Converts the modulated signal back to a binary signal
  - Common **pinout**:
    - Without breakout board (looking at the receiver): signal - ground - Vcc
    - With breakout board: ground - Vcc - signal
    - Signals connects to a digital pin
   
  ![pinouts](https://github.com/Arduino-IRremote/Arduino-IRremote/blob/master/pictures/IRReceiverPinout.jpg)
   

## Receiving on Arduino
In this example, I'm using a [ROM-WT138L(N) IR sensor](https://www.kiwi-electronics.com/en/ir-sensor-rom-wt138ln-11180) and an Arduino Nano.
- Connect power (5V), signal (digital pin) and ground
- For the decoding you need to install the [IR remote library](https://www.arduino.cc/reference/en/libraries/irremote/)   

This is a simple script to print the detected hexadecimal signal to the monitor:
```
#include <IRremote.h>     // library to manage IR signals
#define IR_RECEIVE_PIN 2  // signal connected to digital 2

void setup() {
  Serial.begin(9600);
  IrReceiver.begin(IR_RECEIVE_PIN, ENABLE_LED_FEEDBACK);  // initialize the receiver & make the onboard led blink when the receiver receives a hex code from an IR remote
}

void loop() {
  if (IrReceiver.decode()) {                                       // TRUE if the keycode is successfully received and decoded
    Serial.println(IrReceiver.decodedIRData.decodedRawData, HEX);  // the decoded signal is stored in IrReceiver.decodedIRData.decodedRawData
    IrReceiver.resume();                                           // enable receiving of the next value
  }
}
```
