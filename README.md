# Introduction  
In prototyping, you often want to rapidly set-up wireless Wizard of Oz testing. Several communication protocols can be used for this, but this repo covers the usage of **infrared signals** . 

Basic priciples:

- Infrared needs (more or less) line of sight
- Signals get encoded and need to be decoded
- Sending: If you're using **existing remote controls** to send IR signals
  - Different transmission protocols are used for encoding and decoding, which are usually modulated on a carrier wave of **38 kHz**
  - The most commonly used transmission protocol is the **[NEC protocol](https://lookin-home.medium.com/how-does-the-remote-control-work-explained-564bc7cd0291)**
  - Key presses on the remote are send and read as **hexadecimal codes**. These codes are different for each remote control.
- Reiceiving: The receiver diodes receive IR light
  - IR receiver components come in versions with breakout board and without breakout board
  - The receiver Filters the light to only 38 kHz
  - Then, it converts the modulated IR signal back to a binary signal
  - Common **pinout** (always check the data sheet):
    - Without breakout board (looking at the receiver): signal - ground - Vcc
    - With breakout board: ground - Vcc - signal
    - Signals connects to a digital pin
    - For Vcc, 5V is common but most receivers have a broader range (check the data sheet)
   
  ![pinouts](https://github.com/Arduino-IRremote/Arduino-IRremote/blob/master/pictures/IRReceiverPinout.jpg)
   

# Example: Receiving on Arduino
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
- You might encounter older Arduino code (often more complex code). This example uses **version 4.x**.  Always check the [latest documentation](https://github.com/Arduino-IRremote/Arduino-IRremote).
- Note down the codes of the control buttons you want to use. Then apply these codes in [if statements](https://www.arduino.cc/reference/en/language/structure/control-structure/if/) or, better, [switch case statements](https://www.arduino.cc/reference/en/language/structure/control-structure/switchcase/). 
