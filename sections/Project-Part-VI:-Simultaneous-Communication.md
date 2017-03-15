## Introduction
In this section of the project we must perform simultaneous communication between the three or more smartblock(s). To start with, we will need the code from [this directory](../code/block-peripheral-controllers), which contains the drivers for the LEDs and NFC. A class called `CPortController` is also included in this directory and is required for controlling the port multiplexer so that the LEDs and NFC ports can be accessed.

## Instructions
1. The port controller is used to select one of the connected faces. Once a connected face is selected, the `CTWController` can be used via the `CLEDController` and the `CNFCController` to configure and control the LEDs and the NFC transciever on that face. The first step will be to modify the `CPortController` such that it can be _locked_ by a task running in the OS.

2. To test if the port controller is working correctly, create a simple task that configures the colors of each LED on each face of the block. Depending on how the system for locking resources such as the `CTWController` works, you may need to make `CPortController` and `CLEDController` lockable as well, and have them in turn lock the `CTWController`. You may find the following code useful for configuring the LEDs.

```c++
#define RGB_RED_OFFSET    0
#define RGB_GREEN_OFFSET  1
#define RGB_BLUE_OFFSET   2
#define RGB_UNUSED_OFFSET 3

#define RGB_LEDS_PER_FACE 4

/***********************************************************/

void SetAllColorsOnFace(uint8_t unRed, uint8_t unGreen, uint8_t unBlue) {

   for(uint8_t unLedIdx = 0; unLedIdx < RGB_LEDS_PER_FACE; unLedIdx++) {
      CLEDController::SetBrightness(unLedIdx * RGB_LEDS_PER_FACE +
                                    RGB_RED_OFFSET, unRed);
      CLEDController::SetBrightness(unLedIdx * RGB_LEDS_PER_FACE +
                                    RGB_GREEN_OFFSET, unGreen);
      CLEDController::SetBrightness(unLedIdx * RGB_LEDS_PER_FACE +
                                    RGB_BLUE_OFFSET, unBlue);
   }
}

/***********************************************************/

void SetAllModesOnFace(CLEDController::EMode e_mode) {

   for(uint8_t unLedIdx = 0; unLedIdx < RGB_LEDS_PER_FACE; unLedIdx++) {
      CLEDController::SetMode(unLedIdx * RGB_LEDS_PER_FACE 
                              + RGB_RED_OFFSET, e_mode);
      CLEDController::SetMode(unLedIdx * RGB_LEDS_PER_FACE +
                              RGB_GREEN_OFFSET, e_mode);
      CLEDController::SetMode(unLedIdx * RGB_LEDS_PER_FACE +
                              RGB_BLUE_OFFSET, e_mode);
      CLEDController::SetMode(unLedIdx * RGB_LEDS_PER_FACE +
                              RGB_UNUSED_OFFSET, CLEDController::EMode::OFF);
   }
}

```

3. Extend the task in the previous step such that the task can run concurrently with other instances of itself. For example, create a task which controls the LEDs on one face of block and have six instances of this task sharing the port controller as well as the  `CLEDController` (which uses the `CTWController internally`)

```c++
CPortController::EPort peAllPorts[NUM_PORTS] {
   CPortController::EPort::NORTH,
   CPortController::EPort::EAST,
   CPortController::EPort::SOUTH,
   CPortController::EPort::WEST,
   CPortController::EPort::TOP,
   CPortController::EPort::BOTTOM,
};

CPortController::EPort peConnectedPorts[NUM_PORTS] {
   CPortController::EPort::NULLPORT,
   CPortController::EPort::NULLPORT,
   CPortController::EPort::NULLPORT,
   CPortController::EPort::NULLPORT,
   CPortController::EPort::NULLPORT,
   CPortController::EPort::NULLPORT,
};

void DetectPorts() {
   for(CPortController::EPort& ePort : peAllPorts) {
      if(m_cPortController.IsPortConnected(ePort)) {
         for(CPortController::EPort& eConnectedPort : peConnectedPorts) {
            if(eConnectedPort == CPortController::EPort::NULLPORT) {
               eConnectedPort = ePort;
               break;
            }
         }         
      }
   }
   m_cPortController.SelectPort(CPortController::EPort::NULLPORT);
}
```
