## Instructions
1. Confirm that you can upload and run the Blink example from the Arduino IDE. This verifies that the hardware is working correctly.
2. Work through [this tutorial](http://www.ladyada.net/learn/arduino/lesson4.html) and confirm that you can send strings of characters from the board to the serial monitor in the Arduino IDE
3. Using the example from the [documentation](https://github.com/chrismoos/avr-os/blob/master/README.md) in the original repository, create a new sketch with two tasks. Since you don't have an LCD panel (as used in that example), replace it with the Serial object used in step #2. What we should be able to see is the two tasks sharing the serial port by use of the spinlock.
