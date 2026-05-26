# Hexy Hustle Kit Bot
This repository contains the CAD, code, and assembly instructions necessary to build a Hexy Hustle Kit Bot.

## Printing
Printable STLs have been provided for all kit bot parts in the STL folder. Check the BOM for quantity to print.

All kits are printed on Bambu Labs 0.20mm Standard settings. 

## Standard Hardware
Kitbots utilize the following standard hardware
- 4mm Round Shafts
- 5mm Hex Shafts
- M3 Socket Head Screws
- M3 Heatset Inserts

When designing 3D printable parts to interface with this hardware, the following dimensions tend to work well.
- 0.13", M3 screw thru hole diameter for hole printed flat
- 0.14", M3 screw thru hole diameter for hold printed standing up
- 0.15", Counterbore depth for M3 socket screws 
- 0.18", M3 Heatset insert hole diameter
- 0.115", M3 hole diameter for threading into the plastic
- 0.25", 5mm Hex shaft thru hole diameter
- 0.2", Blind depth of a heatset insert hole
- 0.18", 4mm Shaft thru hole diameter

## Assembly
If you did not buy a kit that contains all prints, start by printing the STLs contained in CAD/Kitbot. Reference the BOM.md to see quantities of each part to print. If a part is mirrored, a pre-mirrored STL is provided for convenience.

Assembly video coming soon!

## Lipo charging
Do not leave batteries unattended while charging! 
It is reccommended that you charge batteries in a lipo safe bag/box.

B6 Neo manual:
https://www.skyrc.com/download/100198%20B6neo%20Instruction%20Manual%20V1.1_240318.pdf

After using the batteries, it is reccommended to discharge them to storage voltage for longevity.

## Code

### Setup

1. Install the Arduino IDE https://www.arduino.cc/en/software/
2. Add the ESP32 and Bluepad32 board packages to Board Manager
- [Video Tutorial](https://www.youtube.com/watch?v=0jnY-XXiD8Q) (You do not need to open the example project)
- Paste the following text into the "additional boards manager URLs" section per the instructions linked above.
 ```
 https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
 https://raw.githubusercontent.com/ricardoquesada/esp32-arduino-lib-builder/master/bluepad32_files/package_esp32_bluepad32_index.json
 ```
3. Open the Code.ino file
4. Select the board "esp32_bluepad32 > ESP32 Dev Module"
5. Plug in the brain, and select the COM port that appears.
6. Verify that uploading code to the brain works

### Configuration

Define all motors and servos at the top of Code.ino
Here are the example definitions from the kitbot itself
```
// motors
Motor leftDrive;
Motor rightDrive;
Motor intake;
Motor hang;

// servos
ServoMotor flipper;
const int flipperMin = 500;
const int flipperMax = 2050;
```
Defining limits for the servo is helpful for writing driver controls. These should be values between 500 and 2500.

In the `setup()` function, assign motor and servo ports. DC motors can be reversed by adding `,1` after the port. Servos should be given a starting position that it moves to when the robot is turned on. This is usually the min or max value, but doesn't have to be.
```
// motors
leftDrive.init(M3); 
rightDrive.init(M4, 1); // reversed
intake.init(M6);
hang.init(M2);

// servos
flipper.init(S1, flipperMin);
```

### Driver Controls

In the `driverControls()` function, map controller buttons to subsystems
```
// intake
if (ctl->r1()) {
    intake.move(100);
} else if (ctl->r2()) {
    intake.move(-100);
} else {
    intake.move(0);
}
```
In this example, the intake runs forward when R1 is pressed, backwards when R2 is pressed, and stops when neither button is pressed.

For the chassis controls, they are configured for split arcade by default.
```
leftDrive.move(y-x);
rightDrive.move(y+x);
```

#### Split Arcard
Get the joystick values from the same stick, instead of two different sticks.
```
// left stick
int x = ctl->axisX();
int y = ctl->axisY();
```
```
// right stick
int x = ctl->axisRX();
int y = ctl->axisRY();
```

#### Tank Drive
Switch from the RX axis to the RY axis.
```
// get joysticks
int x = ctl->axisRY();
int y = ctl->axisY();
```
Then update the left and right drive assignment to use the joystick values directly instead of adding/subtracting them.
```
leftDrive.move(y);
rightDrive.move(x);
```

## Attribution
Credit to Ricardo Quesada for creating the bluepad32 library that the Kitbot runs on
https://github.com/ricardoquesada/bluepad32