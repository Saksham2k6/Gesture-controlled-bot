# Gesture Controlled Bot

A gesture-controlled robotic vehicle that uses **computer vision and hand gesture recognition** to control its movement wirelessly.

The system uses **Python, OpenCV, and MediaPipe** to detect hand landmarks through a webcam and determine the number of raised fingers. The detected gesture is converted into a movement command and transmitted over Wi-Fi to an ESP-based controller. The controller drives two DC motors through an **L293D motor driver**.

The project demonstrates the integration of **computer vision, embedded systems, wireless communication, and motor control** into a single robotic system.

## How It Works

The system is divided into two parts:

1. **Gesture Recognition — Python**

   * Captures live video from a webcam using OpenCV.
   * Detects hand landmarks using MediaPipe Hands.
   * Determines the number of raised fingers from the detected landmarks.
   * Maps each gesture to a movement command.
   * Sends the command to the controller through a TCP socket over Wi-Fi.

2. **Robot Control — Embedded C/C++**

   * Connects the controller to the same Wi-Fi network.
   * Runs a TCP server to receive movement commands.
   * Interprets the received commands.
   * Controls the L293D motor driver through digital direction and PWM signals.
   * Drives the two motors to move the robot forward, backward, left, right, or stop.

### Control Mapping

| Gesture   | Command | Robot Action  |
| --------- | ------- | ------------- |
| 0 fingers | `zero`  | Stop          |
| 1 finger  | `one`   | Move Forward  |
| 2 fingers | `two`   | Turn Right    |
| 3 fingers | `three` | Turn Left     |
| 4 fingers | `four`  | Move Backward |

A thumbs-up gesture is also detected separately by the Python program, although it is currently used as a visual recognition output rather than a motor command.

## System Architecture

```text
        Webcam
           │
           ▼
     OpenCV Video Capture
           │
           ▼
      MediaPipe Hands
           │
           ▼
    Hand Landmark Detection
           │
           ▼
      Finger Counting
           │
           ▼
     Gesture → Command
           │
           │ TCP / Wi-Fi
           ▼
     ESP-based Controller
           │
           ▼
       L293D Driver
        ┌────┴────┐
        ▼         ▼
     Motor A   Motor B
        │         │
        └────┬────┘
             ▼
          Robot
```

## Gesture Recognition

The Python program uses the **MediaPipe Hands** framework to obtain 21 hand landmarks for the detected hand.

The landmark positions are analyzed to determine whether the fingers are raised. The program checks the relative positions of the fingertip and base landmarks for the index, middle, ring, and little fingers. The thumb is handled separately based on its orientation.

The recognized finger count is then mapped to a command:

```text
1 → Forward
2 → Right
3 → Left
4 → Backward
0 → Stop
```

To avoid repeatedly transmitting the same command, the Python program stores the previously transmitted command and sends a new command only when the detected gesture changes.

## Wireless Communication

Communication between the computer and the robot is implemented using a **TCP socket connection**.

The Python program acts as the TCP client and connects to the controller using its IP address and port:

```text
Computer
   │
   │ TCP over Wi-Fi
   ▼
ESP Controller
```

Commands are transmitted as simple text messages such as:

```text
one
two
three
four
zero
```

The embedded program runs a TCP server on port `80` and processes the received command to control the motors.

## Motor Control

The robot uses an **L293D motor driver** to control two DC motors.

The controller provides separate direction signals for each motor along with PWM-based speed control.

| Motor Control | Function          |
| ------------- | ----------------- |
| IN1 / IN2     | Motor A direction |
| IN3 / IN4     | Motor B direction |
| ENA           | Motor A speed     |
| ENB           | Motor B speed     |

The basic motor operations are implemented as separate functions:

* `moveForward()`
* `moveBackward()`
* `turnLeft()`
* `turnRight()`
* `stopMoving()`

For turning, the two motors are driven in opposite directions, allowing the robot to rotate in place.

## Hardware

* ESP-based Wi-Fi controller
* L293D motor driver
* 2 × DC geared motors
* Robot chassis
* Wheels
* Webcam
* Battery/power supply
* Connecting wires

## Software

* **Python**
* **OpenCV**
* **MediaPipe**
* Python `socket` library
* **C/C++** for embedded firmware
* ESP Wi-Fi library
* TCP/IP communication

## Key Implementation Details

### Python Side

The computer vision program:

* Captures frames from the webcam.
* Converts BGR frames to RGB for MediaPipe processing.
* Detects a single hand using MediaPipe Hands.
* Extracts the hand landmark coordinates.
* Estimates the number of raised fingers.
* Displays the detected landmarks and gesture information using OpenCV.
* Sends a command only when the detected gesture changes.
* Maintains a TCP connection with the robot controller.

### Embedded Side

The embedded firmware:

* Connects to the configured Wi-Fi network.
* Starts a TCP server on port `80`.
* Waits for incoming movement commands.
* Decodes the received command.
* Controls motor direction using GPIO pins.
* Uses PWM signals to control motor speed.
* Stops the motors when the stop command is received.

## Project Highlights

* Real-time hand gesture recognition
* Contactless robot control
* Computer vision using MediaPipe
* Webcam processing using OpenCV
* Wireless TCP communication
* Dual DC motor control
* PWM-based motor speed control
* Embedded firmware development
* Integration of Python with an embedded robotic platform

## Learning Outcomes

This project provided practical experience with:

* Hand landmark detection and gesture recognition
* OpenCV-based real-time video processing
* MediaPipe Hands
* TCP socket programming in Python
* Wi-Fi communication between a computer and embedded controller
* GPIO and PWM-based motor control
* L293D motor driver interfacing
* Embedded C/C++ programming
* Integrating software-based computer vision with physical hardware
* Designing a simple command protocol between a PC and robot

## Future Improvements

Some possible improvements to the system include:

* Adding more gesture-based commands
* Improving gesture classification using a trained ML model
* Adding obstacle detection using ultrasonic or ToF sensors
* Implementing smoother acceleration and deceleration
* Adding command timeout handling for safer operation
* Using a more robust communication protocol
* Supporting multiple hand gestures for additional robot functions
* Improving recognition stability under different lighting conditions

## Conclusion

The Gesture Controlled Bot combines **computer vision, hand gesture recognition, wireless communication, and embedded motor control** to create an intuitive human-machine interface.

Instead of using a conventional remote controller, the robot receives movement commands generated from hand gestures detected through a webcam. The project demonstrates how software-based perception can be connected to a physical embedded system to achieve real-time robotic control.
