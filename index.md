# Gesture and Computer Vision-controlled Robotic Arm

This project is a 4-degree-of-freedom robotic arm controlled by three different input modes: computer vision (the arm autonomously detects and tracks a red object using a camera), hand gesture recognition (a Raspberry Pi camera reads your hand gestures using MediaPipe AI and moves the arm accordingly), and manual joystick control. The biggest challenge was integrating a Raspberry Pi running AI vision software with an Arduino-based servo controller — two completely different systems that had to be made to talk to each other over USB serial. The biggest triumph was getting MediaPipe gesture recognition working on the Pi and having it send real-time commands that physically moved the robotic arm.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Arjun V | Basis Independent Fremont (Upper) | Electrical Engineering | Incoming Junior |

![Arjun and Robotic Arm](logo.svg)

<!--- ============================================================ -->
<!--- FINAL MILESTONE — comment out until complete -->
<!--- ============================================================ -->
<!---
# Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE
-->

<!--- ============================================================ -->
<!--- SECOND MILESTONE — comment out until complete -->
<!--- ============================================================ -->
<!---
# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone
-->

<!--- ============================================================ -->
<!--- FIRST MILESTONE — comment out until complete -->
<!--- ============================================================ -->
<!---
# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/CaCazFBhYKs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project
-->

# Starter Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/13xkwgT1sZc?si=1F7VCC4R6qnPZDIC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my starter project at BlueStamp Engineering, I built a handheld retro arcade game console to learn the fundamentals of electronics, embedded systems, soldering, and hardware-software integration before beginning my main robotics project.

The console is powered by a central microcontroller that controls all inputs and outputs across the system. It includes a 16×8 LED dot matrix display for rendering retro pixel graphics, a 3-digit 7-segment display for displaying game scores, a 5V buzzer for generating arcade-style sound effects, and a custom soldered keypad that allows users to interact with multiple pre-programmed games.

**Components and Integration**

Each hardware component performs a specific role and communicates through the microcontroller:

- **LED Matrix Display:** Draws game graphics and updates frames in real time
- **7-Segment Display:** Tracks and displays score values
- **Button Inputs:** Detect player movement and actions
- **Buzzer:** Produces audio feedback and game sounds
- **Microcontroller:** Processes user inputs and coordinates output behavior across the system

**Technical Progress**

During this milestone:
- Learned safe soldering techniques and assembled electronic components
- Connected and tested display modules and button inputs
- Programmed and uploaded firmware to the microcontroller
- Debugged wiring and signal issues
- Successfully ran playable retro-style games on the completed console

**Challenges**

One of the biggest challenges was soldering clean and reliable connections while making sure every component communicated correctly. Small wiring mistakes or poor solder joints could cause display glitches or unresponsive controls, so debugging required patience and systematic testing.

**Plan Moving Forward**

Completing this starter project gave me hands-on experience with electronics, real-time control systems, and hardware debugging. These skills prepared me to transition into my main project: a gesture and computer vision-controlled robotic arm that expands from simple embedded control into AI-guided robotics and automation.


# Schematics

Here's where you'll put images of your schematics.

<!--- Add your Tinkercad or Fritzing schematic images here once complete -->
<!--- ![Wiring Schematic](schematic.png) -->


# Code

The project uses two separate programs that work together: an Arduino sketch that controls the servos and listens for commands, and a Python script running on the Raspberry Pi that handles computer vision and gesture recognition.

**Arduino — Servo Listener (receives commands from Raspberry Pi)**

The Arduino listens over USB serial for single-character commands from the Pi. Each command is a joint letter (B=Base, S=Shoulder, U=Upper arm, C=Claw) followed by an angle number.

```cpp
#include <Servo.h>

Servo s1, s2, s3, s4;   // Base, Shoulder, Upper arm, Claw

void setup() {
  s1.attach(3);   // Base servo       on D3
  s2.attach(5);   // Shoulder servo   on D5
  s3.attach(6);   // Upper arm servo  on D6
  s4.attach(9);   // Claw servo       on D9

  s1.write(90); s2.write(90); s3.write(90); s4.write(10);
  Serial.begin(9600);
}

void loop() {
  if (Serial.available() > 0) {
    char joint = Serial.read();
    int val = Serial.parseInt();
    if (joint == 'B') s1.write(constrain(val, 0, 180));
    if (joint == 'S') s2.write(constrain(val, 10, 180));
    if (joint == 'U') s3.write(constrain(val, 5, 180));
    if (joint == 'C') s4.write(constrain(val, 10, 175));
  }
}
```

**Arduino — Joystick Control (standalone mode without Pi)**

```cpp
#include <Servo.h>

Servo myservo1;  // Base
Servo myservo2;  // Lower arm (Shoulder)
Servo myservo3;  // Upper arm
Servo myservo4;  // Claw

int pos1=90, pos2=90, pos3=90, pos4=90;

const int right_X = A2;
const int right_Y = A5;
const int right_key = 7;
const int left_X  = A3;
const int left_Y  = A4;
const int left_key = 8;

int x1, y1, z1, x2, y2, z2;

void setup() {
  myservo1.attach(3);
  myservo2.attach(5);
  myservo3.attach(6);
  myservo4.attach(9);

  myservo1.write(pos1);
  myservo2.write(pos2);
  myservo3.write(pos3);
  myservo4.write(pos4);
  delay(1500);

  pinMode(right_key, INPUT);
  pinMode(left_key, INPUT);
  Serial.begin(9600);
}

void loop() {
  x2 = analogRead(right_X);
  y2 = analogRead(right_Y);
  x1 = analogRead(left_X);
  y1 = analogRead(left_Y);

  claw();
  turn();
  upper_arm();
  lower_arm();
  delay(10);
}

void claw() {
  if (x1 < 200) { pos4 += 6; if (pos4 > 175) pos4 = 175; myservo4.write(pos4); delay(5); }
  if (x1 > 800) { pos4 -= 6; if (pos4 < 10)  pos4 = 10;  myservo4.write(pos4); delay(5); }
}

void turn() {
  if (x2 < 200) { pos1 += 4; if (pos1 > 180) pos1 = 180; myservo1.write(pos1); delay(5); }
  if (x2 > 800) { pos1 -= 4; if (pos1 < 0)   pos1 = 0;   myservo1.write(pos1); delay(5); }
}

void lower_arm() {
  if (y2 < 350) { pos2 += 4; if (pos2 > 180) pos2 = 180; myservo2.write(pos2); delay(5); }
  if (y2 > 680) { pos2 -= 4; if (pos2 < 10)  pos2 = 10;  myservo2.write(pos2); delay(5); }
}

void upper_arm() {
  if (y1 > 800) { pos3 += 4; if (pos3 > 180) pos3 = 180; myservo3.write(pos3); delay(5); }
  if (y1 < 200) { pos3 -= 4; if (pos3 < 5)   pos3 = 5;   myservo3.write(pos3); delay(5); }
}
```

**Raspberry Pi — Gesture Control (Python)**

```python
import cv2, time, serial
import mediapipe as mp
from picamera2 import Picamera2

arduino = serial.Serial('/dev/ttyACM0', 9600, timeout=1)
time.sleep(2)

def send(joint, angle):
    cmd = f"{joint}{int(angle)}\n"
    arduino.write(cmd.encode())
    time.sleep(0.05)

base_angle = 90
send('B', 90); send('S', 90); send('U', 90); send('C', 10)
time.sleep(1)

hands = mp.solutions.hands.Hands(max_num_hands=1, min_detection_confidence=0.75)

def count_fingers(lm):
    count = 0
    for tip in [8, 12, 16, 20]:
        if lm.landmark[tip].y < lm.landmark[tip-2].y:
            count += 1
    if lm.landmark[4].x < lm.landmark[3].x:
        count += 1
    return count

picam2 = Picamera2()
picam2.configure(picam2.create_preview_configuration(main={"size": (640, 480)}))
picam2.start()
time.sleep(2)

gesture_buffer = []
BUFFER_SIZE = 3
last_action = ""

try:
    while True:
        frame = picam2.capture_array()
        rgb = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)
        rgb = cv2.cvtColor(rgb, cv2.COLOR_BGR2RGB)
        res = hands.process(rgb)

        if res.multi_hand_landmarks:
            h = res.multi_hand_landmarks[0]
            f = count_fingers(h)

            if   f >= 4:       gesture = "OPEN"
            elif f == 0:       gesture = "CLOSE"
            elif f == 1:
                cx = h.landmark[8].x
                if   cx < 0.4: gesture = "LEFT"
                elif cx > 0.6: gesture = "RIGHT"
                else:          gesture = "NONE"
            elif f == 2:
                wy = h.landmark[0].y
                gesture = "UP" if wy < 0.5 else "DOWN"
            else:              gesture = "NONE"

            gesture_buffer.append(gesture)
            if len(gesture_buffer) > BUFFER_SIZE:
                gesture_buffer.pop(0)

            if (len(gesture_buffer) == BUFFER_SIZE and
                all(g == gesture_buffer[0] for g in gesture_buffer) and
                gesture != "NONE" and gesture != last_action):

                last_action = gesture
                if   gesture == "OPEN":  send('C', 10);  print("OPEN gripper")
                elif gesture == "CLOSE": send('C', 175); print("CLOSE gripper")
                elif gesture == "LEFT":
                    base_angle = min(180, base_angle + 10)
                    send('B', base_angle)
                elif gesture == "RIGHT":
                    base_angle = max(0, base_angle - 10)
                    send('B', base_angle)
                elif gesture == "UP":   send('S', 150); send('U', 150)
                elif gesture == "DOWN": send('S', 50);  send('U', 50)
        else:
            gesture_buffer = []
            last_action = ""

        time.sleep(0.15)

except KeyboardInterrupt:
    arduino.close()
    picam2.stop()
```

**Raspberry Pi — Red Object Detection (Python)**

```python
import cv2
import numpy as np
from picamera2 import Picamera2
import time

picam2 = Picamera2()
picam2.configure(picam2.create_preview_configuration(main={"size": (640, 480)}))
picam2.start()
time.sleep(2)

while True:
    frame = picam2.capture_array()
    frame = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)
    hsv   = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

    mask = (cv2.inRange(hsv, np.array([0,  150, 70]), np.array([10,  255, 255])) +
            cv2.inRange(hsv, np.array([170,150, 70]), np.array([180, 255, 255])))

    contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    for c in contours:
        if cv2.contourArea(c) > 5000:
            x, y, w, h = cv2.boundingRect(c)
            print(f"RED at x={x + w//2}, y={y + h//2}")
    time.sleep(0.4)
```


# Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Raspberry Pi 4 Model B | Main computer — runs vision and gesture AI | $55 | <a href="https://www.raspberrypi.com/products/raspberry-pi-4-model-b/">Link</a> |
| Pi Camera OV5647 5MP | Captures hand gestures and detects red objects | $10 | <a href="https://www.amazon.com/dp/B07QNSJ32M">Link</a> |
| LAFVIN 4DOF Robotic Arm Kit | Acrylic arm with MG996R/MG90S servos | $40 | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| LAFVIN Uno R3 (Arduino clone) | Controls servos, receives commands from Pi | Included in kit | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| Sensor Shield v5.0 | Breaks Arduino pins into G/V/S headers for easy wiring | Included in kit | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| 2x Analog Joystick Modules | Manual control of arm joints | Included in kit | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| CanaKit 5.1V 3.1A USB-C Supply | Powers the Raspberry Pi | $10 | <a href="https://www.amazon.com/dp/B07TYQRXTK">Link</a> |
| 4xAA Battery Pack (6V) | Powers the MG996R servo motors externally | $5 | <a href="https://www.amazon.com/dp/B07TYQRXTK">Link</a> |
| Half-size Breadboard | Routes power and signal wires | $5 | <a href="https://www.amazon.com/dp/B082KBF7MM">Link</a> |
| Male-to-Female Jumper Wires | Connect Pi GPIO pins to breadboard | $5 | <a href="https://www.amazon.com/dp/B077X99KX1">Link</a> |


# Other Resources

- [MediaPipe Hand Tracking Documentation](https://developers.google.com/mediapipe/solutions/vision/hand_landmarker)
- [Raspberry Pi GPIO Pinout](https://pinout.xyz/)
- [OpenCV HSV Color Detection Guide](https://docs.opencv.org/4.x/df/d9d/tutorial_py_colorspaces.html)
- [BSE Example Portfolio 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [BSE Example Portfolio 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [BSE Example Portfolio 3](https://arneshkumar.github.io/arneshbluestamp/)
