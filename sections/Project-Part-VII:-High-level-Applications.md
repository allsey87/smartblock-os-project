## Introduction
This part of the project is somewhat flexible, although a more sophisticated demonstration will attract a higher grade. 

To complete this section, control or monitoring of a remote structure must be demonstrated. This could be achieved through a combination of:

1. controlling the LEDs on any block,
2. reading the value of accelerometers on any block,
3. displaying the connectivity of the structure on a remote PC.

The remote link is provided using an Xbee module, a small radio based on the Zigbee standard, for which there is a slot on the smartblock board.

## Instructions

The code to initialize use the Xbee module is provided as part of the [original firmware](https://github.com/allsey87/smartblock-bsp). It is recommended to study and test this code before integrating it into the OS. The Xbee communicates with the microcontroller using the `CTUARTController`. This is a virtual UART port based on the microcontroller's 16-bit timer. Similar to the `CHUARTController`, you will need to import this driver into your source code and probably modify it in order for it to be shareable amongst the tasks, which are running concurrently.

The following sections propose three configurations, increasing in complexity. Starting by implementing the first configuration and extending the code to support the later configurations is recommended.

### Configuration A

Configuration A is the simplest setup. Two Xbees form a link between Block 0 and a remote PC. The remote PC must be able to control the LEDs or read the accelerometers on any block, via Block 0 which forwards the request to Block 1, 2, and 3 over NFC as required.

![Configuration A](./Project-Part-VII:-High-level-Applications-A.png "Configuration A")

### Configuration B

Configuration B is somewhat more complex. Similar to Configuration A, two Xbees form a link between Block 0 and a remote PC. This time, however, Block 3 is only accessible via Block 2. This will require a primitive routing algorithm in order to forward the messages to the correct block. A successful demonstration allows a user on the remote PC to control the color or read the accelerometer of any block in the structure. Bonus points if Block 0 can query the connectivity of the structure and send it back to the remote PC for display.

![Configuration B](./Project-Part-VII:-High-level-Applications-B.png "Configuration B")

### Configuration C

Configuration C has the same requirements (and bonus points) as Configuration B. The main difference is the proposed structure now contains a loop, which will require a more sophisticated routing algorithm.

![Configuration C](./Project-Part-VII:-High-level-Applications-C.png "Configuration C")
