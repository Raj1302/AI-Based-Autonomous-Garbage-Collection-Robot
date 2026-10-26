# AI-Based Autonomous Garbage Collection Robot with Robotic Arm

An educational robotics project that combines AI-based waste detection,
a Raspberry Pi-controlled mobile robot, a robotic arm, ultrasonic
distance sensing, and a Flutter Android application for manual control.

> **Important:** Verify all wiring against your actual robot before
> powering it on. GPIO assignments in this document are based on the
> project's supplied connection reference and may need to be adjusted if
> your physical wiring or Python code differs.

## Table of Contents

-   [Project Overview](#project-overview)
-   [Features](#features)
-   [Repository Files](#repository-files)
-   [Hardware Components](#hardware-components)
-   [Raspberry Pi GPIO and Hardware
    Connections](#raspberry-pi-gpio-and-hardware-connections)
-   [Power and Safety](#power-and-safety)
-   [Autonomous AI Setup](#autonomous-ai-setup)
-   [Manual Control Setup](#manual-control-setup)
-   [Android Application](#android-application)
-   [Troubleshooting](#troubleshooting)

## Project Overview

The robot is designed to identify waste using a camera and an AI model,
approach a target, and use a robotic arm to assist with collection.
Ultrasonic sensors provide distance measurements for approach and
obstacle-handling logic. The Android application provides a user
interface for controlling the robot.

The robot's real-world behaviour depends on the installed software,
calibration, wiring, battery condition, and environment. Autonomous
navigation and collection should be tested in a clear, supervised area.

## Features

-   AI-based waste detection using an ONNX model
-   Raspberry Pi-based robot control
-   Autonomous control program
-   Manual control program
-   Four-wheel mecanum drive
-   Robotic arm controlled through a PCA9685 servo driver
-   Front and rear ultrasonic distance sensors
-   Flutter Android application and APK
-   Separate application screenshots for reference

## Repository Files

  -----------------------------------------------------------------------
  File                                Description
  ----------------------------------- -----------------------------------
  `App source code.zip`               Flutter application source code

  `GC_Bot_App.apk`                    Android application installer

  `GC_Bot_App_Screenshots.zip`        Screenshots of the Android
                                      application

  `Raspberry_Pi_Manual_Control.zip`   Raspberry Pi manual-control project

  `gc_yolo.zip`                       AI/autonomous-control project,
                                      including the ONNX model and
                                      `requirements.txt`
  -----------------------------------------------------------------------

The AI/autonomous ZIP contains the `gc_yolo/` project directory,
including `main_robot.py`, `main_ai_test.py`, `requirements.txt`, and
`models/waste_model.onnx`.

## Hardware Components

The following list is based on the supplied project component reference.

  Component                                       Quantity
  ------------------------------------------ -------------
  Raspberry Pi 4 Model B                                 1
  Raspberry Pi USB 2.0 camera                            1
  SD card                                                1
  BO motors                                              4
  Mecanum wheels                                         4
  L298N motor drivers                                    2
  3S 12 V lithium-ion batteries                          3
  Lithium-ion charger                                    1
  Wooden-board chassis                                   1
  3D-printed robotic arm                                 1
  MG995 servo motors                                     3
  MG90S servo motors                                     3
  Buck converter                                         1
  PCA9685 servo driver                                   1
  HC-SR04 ultrasonic sensors                             2
  1 kΩ resistors for ECHO voltage dividers               4
  Jumper wires                                 As required
  Switches                                               4
  Cooling fans                                           2

*Adjust the quantities above if your final physical build differs from
this reference list.*

## Raspberry Pi GPIO and Hardware Connections

GPIO numbers below use **BCM numbering**, not physical header-pin
numbering.

### 1. L298N Motor Driver #1 --- Left Side

  Driver connection     Raspberry Pi BCM GPIO   Physical header pin
  ------------------- ----------------------- ---------------------
  IN1                                  GPIO17                Pin 11
  IN2                                  GPIO27                Pin 13
  IN3                                  GPIO10                Pin 19
  IN4                                  GPIO24                Pin 18
  ENA                                  GPIO18                Pin 12
  ENB                                  GPIO19                Pin 35

Motor outputs:

-   OUT1 / OUT2 → Front-left motor
-   OUT3 / OUT4 → Rear-left motor

### 2. L298N Motor Driver #2 --- Right Side

  Driver connection     Raspberry Pi BCM GPIO   Physical header pin
  ------------------- ----------------------- ---------------------
  IN1                                   GPIO5                Pin 29
  IN2                                   GPIO6                Pin 31
  IN3                                  GPIO13                Pin 33
  IN4                                  GPIO26                Pin 37
  ENA                                  GPIO20                Pin 38
  ENB                                  GPIO21                Pin 40

Motor outputs:

-   OUT1 / OUT2 → Front-right motor
-   OUT3 / OUT4 → Rear-right motor

Motor direction can vary with the polarity of each motor's wires. Test
the wheels with the robot lifted safely off the ground and correct the
wiring or software direction mapping if needed.

### 3. HC-SR04 Ultrasonic Sensors

  Sensor         TRIG                       ECHO
  -------------- -------------------------- --------------------------
  Front sensor   GPIO9 (physical pin 21)    GPIO25 (physical pin 22)
  Rear sensor    GPIO16 (physical pin 36)   GPIO12 (physical pin 32)

**Voltage-level warning:** The HC-SR04 ECHO output is typically 5 V.
Raspberry Pi GPIO pins are **not 5 V tolerant**. Use a suitable voltage
divider or level shifter on each ECHO line before connecting it to the
Pi. Connect sensor GND to the common ground. Check the sensor supply
requirements before powering it.

### 4. PCA9685 Servo Driver

  PCA9685 pin   Connection
  ------------- ----------------------------------
  VCC           Raspberry Pi 3.3 V
  GND           Raspberry Pi GND
  SDA           GPIO2 / SDA1 (physical pin 3)
  SCL           GPIO3 / SCL1 (physical pin 5)
  I²C address   `0x40` (default, unless changed)

Enable I²C in Raspberry Pi configuration if it is not already enabled.

### 5. Robotic Arm Servo Channels

  PCA9685 channel   Joint            Servo
  ----------------- ---------------- -------
  CH0               Base rotation    MG995
  CH1               Shoulder         MG995
  CH2               Elbow            MG995
  CH3               Wrist            MG90S
  CH4               Wrist rotation   MG90S
  CH5               Gripper          MG90S

Power the servos through the PCA9685 servo power input (`V+`) using a
suitable regulated supply rated for the servos' voltage and current
requirements. Do not power the servos from the Raspberry Pi's 3.3 V pin.
Confirm the polarity and voltage before connecting the servo rail.

### 6. Cooling Fans --- Parallel Connection

According to the supplied connection reference:

-   Connect Fan 1 positive to Fan 2 positive.
-   Connect Fan 1 negative to Fan 2 negative.
-   Connect the shared negative line to the battery negative.
-   Connect the shared positive line to the battery positive through a
    suitable switch.

Use this arrangement only if the fans are rated for the battery voltage.
If not, use a correctly rated regulated supply. Do not connect a fan
directly to a Raspberry Pi GPIO pin.

## Power and Safety

-   Power the Raspberry Pi through a suitable USB-C supply or a properly
    regulated power converter rated for the Pi.
-   The motor battery supply feeds the L298N motor drivers, within the
    drivers' and motors' voltage/current limits.
-   Use a separate, suitable regulated supply for the servo power rail.
-   Ensure the Raspberry Pi, motor-driver control ground, sensor ground,
    and servo-supply ground share a common reference where required by
    the circuit.
-   **Never connect a 12 V battery directly to a Raspberry Pi 5 V or 3.3
    V pin.**
-   Install a physical emergency-stop switch in the motor power path and
    make sure it can cut motor power.
-   Secure the robot before testing motors and keep hands clear of
    wheels and the arm.
-   Check battery polarity, wiring, motor-driver enable pins, and GPIO
    assignments before powering on.
-   Avoid stalling servos or motors; they can draw high current and
    overheat.

## Autonomous AI Setup

### 1. Prepare Raspberry Pi OS

Use a compatible Raspberry Pi OS installation with Python 3. The
Raspberry Pi needs internet access for the initial package installation.

``` bash
sudo apt update
sudo apt install -y python3 python3-venv python3-pip
```

### 2. Download and extract the autonomous ZIP

Download `gc_yolo.zip` from this repository and extract it on the
Raspberry Pi. Open a terminal in the extracted project directory.

For example, if the ZIP is in the Downloads directory:

``` bash
cd ~/Downloads
unzip gc_yolo.zip
cd gc_yolo
```

If you extracted it elsewhere, change to that location instead.

### 3. Create and activate a virtual environment

The virtual environment is intentionally not included in the ZIP. Create
a fresh one on the Raspberry Pi:

``` bash
python3 -m venv .venv
source .venv/bin/activate
```

When the environment is active, the terminal prompt normally shows
`(.venv)`.

### 4. Install dependencies

The autonomous project includes a `requirements.txt` file:

``` bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

The requirements file lists the Python packages used by this project.
Package compatibility can vary by Raspberry Pi OS and CPU architecture.
If installation fails, record the full error and check that your
OS/Python version is supported by the package.

### 5. Test the AI model

``` bash
python main_ai_test.py
```

### 6. Run the autonomous robot

``` bash
python main_robot.py
```

Run the robot only after verifying the motor, camera, ultrasonic sensor,
and servo wiring. Keep the wheels off the ground during the first motor
test and supervise all autonomous trials.

### 7. Exit the virtual environment

``` bash
deactivate
```

## Manual Control Setup

1.  Download `Raspberry_Pi_Manual_Control.zip` from this repository.
2.  Extract it and open a terminal in the extracted project folder.
3.  Create and activate a virtual environment:

``` bash
python3 -m venv .venv
source .venv/bin/activate
```

4.  Install the Python packages required by the manual-control code.
5.  Run the manual-control program:

``` bash
python app2.py
```

**Dependency note:** The uploaded manual-control ZIP does not currently
include a `requirements.txt`. Its dependencies must be identified from
the Python imports and installed before running `app2.py`. Do not assume
the autonomous project's dependency list is sufficient for the
manual-control program.

When finished, exit the environment with:

``` bash
deactivate
```

## Android Application

The repository includes `GC_Bot_App.apk` and `App source code.zip`.

### Install the APK

1.  Open this repository on your Android phone.
2.  Select `GC_Bot_App.apk` and download the file.
3.  Open the downloaded APK and follow Android's installation prompts.
4.  If Android asks, allow installation from that source only if you
    trust the file.
5.  Open the app and confirm the robot and phone are configured to
    communicate over the intended network.

### Flutter source code

The Flutter project source is provided separately in
`App source code.zip`. Extract it on a development computer and open the
project using Flutter-compatible tooling such as Visual Studio Code with
the Flutter SDK installed.

## Troubleshooting

-   **`python3 -m venv .venv` fails:** Install the `python3-venv`
    package and try again.
-   **`requirements.txt` not found:** Make sure the terminal is inside
    the extracted `gc_yolo` directory.
-   **AI model or camera fails to load:** Check that
    `models/waste_model.onnx` exists, the camera is connected, and the
    program's model/camera paths match the project files.
-   **GPIO or I²C errors:** Verify BCM pin numbering, enable I²C if
    needed, and check the wiring.
-   **Motors move in the wrong direction:** Lift the robot off the
    ground before testing; adjust motor-wire polarity or direction
    mapping as appropriate.
-   **Servo resets or jitters:** Check the external servo supply,
    current capacity, ground reference, and mechanical load.
-   **Manual control fails to start:** Identify and install the
    dependencies used by the manual-control source code; that ZIP does
    not yet include a `requirements.txt`.

## Project Title

**AI-Based Autonomous Garbage Collection Robot with Robotic Arm**

Educational robotics project. Contributions, bug reports, and
suggestions are welcome.
