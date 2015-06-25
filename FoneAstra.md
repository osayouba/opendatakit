> ## Getting started with development for the `FoneAstra` sensor interfacing board ##

### Introduction ###

`FoneAstra` is a battery-powered interfacing board to enable wired (USB-Serial) or wireless (Bluetooth) communications between mobile devices and low-level sensors connected to the board. It is based on the ATMega 328p processor running at 8MHz. It has connection headers for commonly used low-level communication protocols, such as I2C, SPI and 1-wire, as well as general-purpose digital and analog I/O. When connected to a mobile device capable of being a USB Host, the board can be powered by the mobile device itself and does not require a battery. Additionally, `FoneAstra` has an on-board SD memory socket, a real-time clock, a few LEDs, and an audio buzzer for feedback to users.

### Required hardware ###
  * FTDI Basic Breakout board https://www.sparkfun.com/products/9873
    * **NOTE:** Make sure that its a **3.3V** board, **NOT** 5V (Sparkfun also has a 5V version of this breakout board).
  * mini-USB cable

### Setting up the development environment ###
  * Install the [ArduinoIDE](http://arduino.cc/en/Main/Software)
  * Checkout the [FoneAstra](https://code.google.com/p/opendatakit/source/checkout?repo=foneastra) repository
  * Create a folder called "Arduino" in your home directory's documents `(Windows - "/Users/<login name>/My Documents", Mac - "/Users/<login name>/Documents")`
  * Inside the "Arduino" directory, create another directory called 'libraries'
  * If using one wire temperature sensor, then download the [OneWire.Zip](http://www.pjrc.com/teensy/td_libs_OneWire.html) and unzip it in the `Arduino/libraries` folder, which should result in a directory structure `Arduino/libraries/OneWire/OneWire.cpp`
  * To test your installation open the `'FoneAstra30TempSketch'` project by navigating to  `<FoneAstra Repo Root>/software/board/FoneAstra30TempSketch/` and launch `"FoneAstra30TempSketch.ino"`
  * Under Tools->Board select "Arduino Pro or Pro Mini (3.3V, 8MHz) w/ ATMega 328". NOTE: The board type  needs to be set up correctly even if not using a temperature sensor.
  * Connect the FTDI breakout board to a mini-usb cable that is connected to your development machine.
  * Connect the FTDI board to the inner side of the 2x6 connector on the bottom side of the board.
  * Turn `FoneAstra` On, a green LED on the topside of the board flickers when the board is turned on.
  * Click the "Upload" icon in the IDE (this is the second icon from the left in the toolbar and is an arrow pointing to the right).
    * You will be asked to select the appropriate serial port on your development machine.
  * Install ODK Sensors, `FoneAstra30TempSensorDemo` and `FoneAstra30TempDriver` on your Android device.
  * Start the `FoneAstra30TempSensorDemo` application and click on the "Start" button.
  * Follow instructions to discover the `FoneAstra` device and assign the `FoneAstra30TempDriver` to that device.