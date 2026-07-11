# Gesture and Computer Vision-controlled Robotic Arm

This project is a robotic arm with 4 degrees of freedom. It can be controlled by three different input modes: computer vision (the arm detects and tracks a colored object using a camera), gesture control (a Raspberry Pi camera reads hand gestures using MediaPipe AI and moves the arm), and joystick control. The biggest challenge was integrating the Raspberry Pi with computer vision and gesture control with an Arduino servo controller. The two different systems had to be able to talk to each other over a USB serial port, which kept disconnecting. My biggest success was the MediaPipe gesture control working on the Pi, and it being able to send commands to the Arduino that physically moved the robotic arm.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Arjun V | Basis Independent Fremont (Upper) | Electrical Engineering | Incoming Junior |

<img src="https://github.com/user-attachments/assets/6ed48da2-7abb-4af1-9afc-e8447abc4d1e" alt="Arjun headshot" style="width:250px; height:auto;">

# Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/b-72fta5Uw0" title="Arjun V. Milestone 3" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For the final milestone, I finished computer vision and made the arm's tracking more reliable so it properly follows a colored object using the camera. The Pi runs a color detection script that finds the largest colored blob in the camera's view and remembers its last known position. The arm rotates in the object's direction when it moves out of frame by going to the side it last saw the object visible. This ended up being more reliable than trying to track the position continuously.

Initially, I tried to use OpenCV's CSRT object tracker instead of the HSV color detection, since CSRT tracks visual features instead of just color. I thought it would be more accurate. But the plain color tracking with better background color filtering worked better for this specific setup and was easier for me to debug, so I switched back to it

**What I Accomplished Since My Last Milestone**

- Finished computer vision by using HSV color detection to track a blue object
- Fixed a bug where the arm kept turning in the wrong direction despite how the object was actually moved
- Added a part to the code where the arm remembers the last known position of the object and turns to that side instead of guessing

**Biggest Challenges and Triumphs**

The biggest challenge I faced in this project was continuous debugging. There were multiple issues in the Camera module, USB ports that kept shifting randomly, the Arduino that sometimes just stopped responding to commands from the Pi, and color detection that would not actually detect or follow the object. All these issues forced me to focus on one problem at a time and forced me to work backwards multiple times. The biggest triumph was getting the computer vision and gesture control to work because I struggled with them for a while, and when one of the simple solutions, like changing the HSV values of the object to bolster detection, instead of trying a more complex tracking method.

**Key Topics I Learned About**

- How HSV color detection works and why it is more reliable than RGB or CSRT tracking
- How communication between Raspberry Pi and Arduino works, and how to debug port shifts
- The fact that the Pi camera cannot sense depth, and how distance measurement needs another camera or an ultrasonic sensor
- How small code changes, like where a servo attach command sits in a loop, can cause a robot arm to behave completely differently

**What I Hope to Learn Next**

I want to learn more about inverse kinematics in physics so I can make the arm calculate the exact joint angles to reach a specific point, instead of trying to calibrate myself or using preset angles. I also want to try adding actual depth sensing, so the arm can see the distance to an object and pick it up with computer vision instead of estimating or just following it.

# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/9Rbzf-KVjp8" title="Arjun V. Milestone 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my second milestone, I got gesture control working on the arm. A Raspberry Pi camera reads my hand using MediaPipe, a hand tracking library from Google, and recognizes specific gestures. The Pi sends a command over USB serial to the Arduino, which moves the correct servo. An open hand opens the claw, a closed fist closes it, and pointing or holding up two fingers rotates the base or raises and lowers the arm.

Getting the two boards to work together was the main technical hurdle here. The Pi handles all the camera and AI processing, while the Arduino handles the actual servo movement. Splitting the work this way made sense because the Arduino simply does not have the processing power to run an AI hand tracking model, but it is very good at reliably driving motors.

**Technical Progress**

- Set up MediaPipe on the Raspberry Pi to detect hand landmarks in real time
- Built a gesture classification system that counts raised fingers and checks hand position to decide between open, close, rotate, and raise or lower actions
- Established serial communication between the Pi and Arduino so gesture detections translate into physical servo movement
- Added a stability buffer that requires the same gesture to be detected for several frames in a row before acting, which stopped the arm from twitching on noisy single frame misreads

**Challenges**

Camera orientation caused a lot of confusing behavior early on. When the camera was mounted upside down relative to how the code expected it, gesture detection was unreliable in ways that looked like a logic bug but were actually just the image being flipped. Once I corrected the physical mounting, detection accuracy improved immediately.

**What's Next**

With gesture control working, the last piece was computer vision, having the arm track and respond to a colored object using the camera instead of a human gesturing at it.

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/hxumSRNxsR0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For my first milestone, I got the robotic arm fully working with manual joystick control through the Arduino. The Arduino has a sensor shield stacked on top of it, which the two joystick modules wire into. From there the shield connects to all four servos on the arm. Pushing a joystick forward or backward extends or retracts the arm, the other joystick opens and closes the claw, raises and lowers the elbow, and rotates the base. Together this lets the arm pick up an object and place it down somewhere else.

I chose this project because I wanted to compare gesture control against computer vision in terms of accuracy and latency once both are built. Joystick control is the foundation that the other two control modes build on top of.

**Technical Progress**

- Wired the Arduino sensor shield to two analog joystick modules and all four arm servos
- Mapped each joystick axis to a specific joint: base rotation, elbow up and down, arm extend and retract, and claw open and close
- Got the full arm responding smoothly to joystick input, allowing basic pick and place movement

**Challenges**

My biggest challenge was burning out servos. I originally assumed it was a wiring problem and rewired everything, but the servos kept frying anyway. After digging into the code, I found the real issue: the angle ranges in my code let the arm push past its physical limits, so the servo kept trying to spin past where the arm could actually move. It had nowhere to go, so it stalled and overheated until it burned out.

Replacing servos also took a toll on the arm's structural integrity. Every time I disassembled and reassembled a joint, the acrylic frame got a little weaker, and eventually one part snapped completely. I chose to learn basic 3D modeling and printed a replacement part instead of gluing the old one back together, which restored the arm to full strength.

**What's Next**

Now that joystick control is solid, my next step is building out the other two control modes: gesture recognition using a Raspberry Pi camera, and computer vision to let the arm track a colored object on its own.

# Starter Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/13xkwgT1sZc?si=1F7VCC4R6qnPZDIC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my starter project at BlueStamp Engineering, I built a handheld retro arcade game console to learn the fundamentals of electronics, embedded systems, soldering, and hardware-software integration before beginning my main robotics project.

The console is powered by a central microcontroller that controls all inputs and outputs across the system. It includes a 16x8 LED dot matrix display for rendering retro pixel graphics, a 3-digit 7-segment display for displaying game scores, a 5V buzzer for generating arcade-style sound effects, and a custom soldered keypad that allows users to interact with multiple pre-programmed games.

**Components and Integration**

- **LED Matrix Display:** Draws game graphics and updates frames in real time
- **7-Segment Display:** Tracks and displays score values
- **Button Inputs:** Detect player movement and actions
- **Buzzer:** Produces audio feedback and game sounds
- **Microcontroller:** Processes user inputs and coordinates output behavior across the system

**Technical Progress**

- Learned safe soldering techniques and assembled electronic components
- Connected and tested display modules and button inputs
- Debugged wiring and signal issues
- Successfully ran playable retro-style games on the completed console

**Challenges**

One of the biggest challenges was soldering clean and reliable connections while making sure every component communicated correctly. Small wiring mistakes or poor solder joints could cause display glitches or unresponsive controls, so debugging required patience and systematic testing.

**Plan Moving Forward**

Completing this starter project gave me hands-on experience with electronics, real-time control systems, and hardware debugging. These skills prepared me to transition into my main project: a gesture and computer vision-controlled robotic arm that expands from simple embedded control into AI-guided robotics and automation.

# Schematics

<img width="400" alt="Screenshot 2026-07-11 at 11 01 45 AM" src="https://github.com/user-attachments/assets/b6fb3cf6-565f-4ac5-be4b-ae7b1e5bd006" />

*Complete wiring schematic showing the Arduino, breadboard, servos, joysticks, Raspberry Pi, and power supplies for the robotic arm.*

<img width="400" alt="claw_closeup" src="https://github.com/user-attachments/assets/591781c3-8af4-45b0-b2fa-06a32c1d0374" />

*Closeup of the claw assembly using the FS90MR continuous rotation servo.*

<img width="500" alt="CAD design of replacement part" src="https://github.com/user-attachments/assets/f189eea1-2b51-4505-bafb-8e32b1cd2057" />

*3D modeled and printed replacement part for the arm's structural joint after the original acrylic piece cracked.*


# Code

The project runs on two boards working together. The Arduino directly drives all four servos and reads the two joysticks on its own, but it also listens over USB serial for commands from the Raspberry Pi. The Pi runs a menu script that lets you choose between joystick, gesture, or computer vision control without re-uploading any code.

**Arduino, Combined Joystick and Serial Listener**

This sketch handles joystick input directly so the arm always responds to the joysticks, but if a command comes in from the Pi over USB, it executes that instead for that cycle. The claw uses a continuous rotation servo (FS90MR), so instead of moving to an angle it spins for a short burst and then stops. Holding the stick spins it continuously, releasing it stops it instantly.

```cpp
#include <Servo.h>

Servo myservo1;  // Base
Servo myservo2;  // Shoulder (lower arm)
Servo myservo3;  // Elbow (upper arm)
Servo myservo4;  // Claw — continuous rotation (FS90MR)

int pos1=90, pos2=90, pos3=90;  // tracked angles for the 3 positional servos

const int right_X = A2;  // right stick left/right -> base
const int right_Y = A5;  // right stick up/down    -> shoulder
const int left_X  = A3;  // left stick left/right  -> claw
const int left_Y  = A4;  // left stick up/down     -> elbow

void setup() {
  myservo1.attach(3);
  myservo2.attach(5);
  myservo3.attach(6);
  myservo4.attach(9);

  myservo1.write(90);
  myservo2.write(90);
  myservo3.write(90);
  myservo4.write(90);  // 90 = stopped for the continuous rotation servo

  Serial.begin(9600);  // open serial so the Pi can send commands
}

void loop() {
  // If the Pi sent a command, execute it and skip joystick reading this cycle
  if (Serial.available() > 0) {
    char joint = Serial.read();
    int val = Serial.parseInt();

    if (joint == 'B') { pos1 = constrain(val, 30, 150); myservo1.write(pos1); }
    if (joint == 'S') { pos2 = constrain(val, 20, 140); myservo2.write(pos2); }
    if (joint == 'U') { pos3 = constrain(val, 20, 140); myservo3.write(pos3); }
    if (joint == 'C') {
      if (val < 50)        { myservo4.write(80); delay(250); myservo4.write(90); }  // close
      else if (val > 100)  { myservo4.write(100); delay(250); myservo4.write(90); } // open
    }
    return;
  }

  // Otherwise read the joysticks
  int x2 = analogRead(right_X);
  int y2 = analogRead(right_Y);
  int x1 = analogRead(left_X);
  int y1 = analogRead(left_Y);

  // CLAW — spins only while the stick is held, stops instantly on release
  if (x1 < 200)       { myservo4.write(80); }   // hold left  = close
  else if (x1 > 800)  { myservo4.write(100); }  // hold right = open
  else                { myservo4.write(90); }    // released   = stop

  // BASE — range capped at 30-150 to protect the servo from its physical limits
  if (x2 < 200)      { pos1 += 2; if (pos1 > 150) pos1 = 150; myservo1.write(pos1); delay(8); }
  else if (x2 > 800) { pos1 -= 2; if (pos1 < 30)  pos1 = 30;  myservo1.write(pos1); delay(8); }

  // SHOULDER — range capped at 20-140
  if (y2 < 350)      { pos2 += 2; if (pos2 > 140) pos2 = 140; myservo2.write(pos2); delay(8); }
  else if (y2 > 680) { pos2 -= 2; if (pos2 < 20)  pos2 = 20;  myservo2.write(pos2); delay(8); }

  // ELBOW — range capped at 20-140
  if (y1 > 800)      { pos3 += 2; if (pos3 > 140) pos3 = 140; myservo3.write(pos3); delay(8); }
  else if (y1 < 200) { pos3 -= 2; if (pos3 < 20)  pos3 = 20;  myservo3.write(pos3); delay(8); }

  delay(15);
}
```

**Raspberry Pi, Master Control Menu (Python)**

This script lets you pick a control mode at runtime: joystick (handled entirely by the Arduino), gesture (MediaPipe hand tracking), or computer vision (colored object tracking). Press Ctrl+C at any time to return to the menu and switch modes.

```python
import cv2, time, serial
import numpy as np
import mediapipe as mp
from picamera2 import Picamera2

arduino = serial.Serial('/dev/ttyACM0', 9600, timeout=1)
time.sleep(2)

def send(joint, angle):
    arduino.write(f"{joint}{int(angle)}\n".encode())
    time.sleep(0.1)

def start_camera():
    cam = Picamera2()
    cam.configure(cam.create_preview_configuration(main={"size": (640, 480)}))
    cam.start()
    time.sleep(2)
    return cam

# ---------------- GESTURE MODE ----------------
def gesture_mode():
    hands = mp.solutions.hands.Hands(max_num_hands=1, min_detection_confidence=0.75)
    cam = start_camera()
    base = 90
    send('B',90); send('S',90); send('U',90)
    time.sleep(0.5)

    def count_fingers(lm):
        c = 0
        for tip in [8,12,16,20]:
            if lm.landmark[tip].y < lm.landmark[tip-2].y: c += 1
        if lm.landmark[4].x < lm.landmark[3].x: c += 1
        return c

    buf, last = [], ""
    print("GESTURE MODE — Ctrl+C to return to menu")
    try:
        while True:
            frame = cam.capture_array()
            rgb = cv2.cvtColor(cv2.cvtColor(frame, cv2.COLOR_RGB2BGR), cv2.COLOR_BGR2RGB)
            res = hands.process(rgb)
            if res.multi_hand_landmarks:
                h = res.multi_hand_landmarks[0]
                f = count_fingers(h)
                if   f>=4: g="OPEN"
                elif f==0: g="CLOSE"
                elif f==1:
                    cx=h.landmark[8].x
                    g="LEFT" if cx<0.4 else "RIGHT" if cx>0.6 else "NONE"
                elif f==2:
                    g="UP" if h.landmark[0].y<0.5 else "DOWN"
                else: g="NONE"
                buf.append(g); buf=buf[-3:]
                if len(buf)==3 and all(x==buf[0] for x in buf) and g!="NONE" and g!=last:
                    last=g
                    if g=="OPEN":    send('C',140)
                    elif g=="CLOSE": send('C',10)
                    elif g=="LEFT":  base=min(150,base+8); send('B',base)
                    elif g=="RIGHT": base=max(30,base-8);  send('B',base)
                    elif g=="UP":    send('S',130); send('U',130)
                    elif g=="DOWN":  send('S',50);  send('U',50)
            else:
                buf, last = [], ""
            time.sleep(0.2)
    except KeyboardInterrupt:
        cam.stop()
        print("\nReturning to menu...")

# ---------------- VISION MODE ----------------
def vision_mode():
    cam = start_camera()
    base = 90
    send('B', base)
    time.sleep(0.3)

    LOWER = np.array([95, 100, 100])
    UPPER = np.array([115, 255, 255])
    CAMERA_CENTER = 320

    print("VISION MODE — tracking colored object, Ctrl+C to return to menu")

    was_visible = True
    TURN_AMOUNT = 25
    gone_count = 0
    GONE_THRESHOLD = 5
    last_seen_x = CAMERA_CENTER

    try:
        while True:
            frame = cam.capture_array()
            frame = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)
            hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

            mask = cv2.inRange(hsv, LOWER, UPPER)
            kernel = np.ones((5,5), np.uint8)
            mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN, kernel)
            mask = cv2.dilate(mask, kernel, iterations=2)

            cnts, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

            biggest = None
            biggest_area = 0
            for c in cnts:
                area = cv2.contourArea(c)
                if area > biggest_area:
                    biggest = c
                    biggest_area = area

            is_visible_now = biggest is not None and biggest_area > 2500

            if is_visible_now:
                x, y, w, h = cv2.boundingRect(biggest)
                last_seen_x = x + w // 2
                gone_count = 0
                was_visible = True
                print(f"Object visible x={last_seen_x} area={int(biggest_area)}")
            else:
                gone_count += 1
                if gone_count >= GONE_THRESHOLD and was_visible:
                    direction = -1 if last_seen_x > CAMERA_CENTER else 1
                    target = max(30, min(150, base + (direction * TURN_AMOUNT)))
                    print(f"Object left frame, turning toward last seen side, base -> {target}")
                    base = target
                    send('B', base)
                    was_visible = False

            time.sleep(0.06)

    except KeyboardInterrupt:
        cam.stop()
        print("\nReturning to menu...")

# ---------------- JOYSTICK MODE ----------------
def joystick_mode():
    print("JOYSTICK MODE — the Arduino handles the joysticks directly.")
    print("Just move the joysticks. Ctrl+C to return to menu.")
    try:
        while True:
            time.sleep(0.5)
    except KeyboardInterrupt:
        print("\nReturning to menu...")

# ---------------- MENU ----------------
while True:
    print("\n========= ROBOTIC ARM CONTROL =========")
    print("1 - Joystick control")
    print("2 - Gesture control")
    print("3 - Computer vision (colored object tracking)")
    print("4 - Quit")
    choice = input("Pick a mode (1-4): ").strip()

    if choice == "1":
        joystick_mode()
    elif choice == "2":
        gesture_mode()
    elif choice == "3":
        vision_mode()
    elif choice == "4":
        arduino.close()
        print("Goodbye!")
        break
    else:
        print("Invalid choice, try again.")
```

# Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Raspberry Pi 4 Model B | Main computer, runs vision and gesture AI | $55 | <a href="https://www.raspberrypi.com/products/raspberry-pi-4-model-b/">Link</a> |
| Pi Camera OV5647 5MP | Captures hand gestures and detects the tracked object | $10 | <a href="https://www.amazon.com/dp/B07QNSJ32M">Link</a> |
| LAFVIN 4DOF Robotic Arm Kit | Acrylic arm with MG996R/MG90S servos | $40 | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| LAFVIN Uno R3 (Arduino clone) | Controls servos, receives commands from Pi | Included in kit | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| Sensor Shield v5.0 | Breaks Arduino pins into G/V/S headers for easy wiring | Included in kit | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| 2x Analog Joystick Modules | Manual control of arm joints | Included in kit | <a href="https://www.amazon.com/dp/B07ZYZVNY4">Link</a> |
| FS90MR Continuous Rotation Servo | Replacement claw servo after original burned out | $7 | <a href="https://www.amazon.com/dp/B07VK39N96">Link</a> |
| CanaKit 5.1V 3.1A USB-C Supply | Powers the Raspberry Pi | $10 | <a href="https://www.amazon.com/dp/B07TYQRXTK">Link</a> |
| 4xAA Battery Pack (6V) | Powers the arm servos externally | $5 | <a href="https://www.amazon.com/dp/B07TYQRXTK">Link</a> |
| Half-size Breadboard | Routes power and signal wires | $5 | <a href="https://www.amazon.com/dp/B082KBF7MM">Link</a> |
| Male-to-Female Jumper Wires | Connect Pi GPIO pins to breadboard | $5 | <a href="https://www.amazon.com/dp/B077X99KX1">Link</a> |

<!---
# Other Resources

- [MediaPipe Hand Tracking Documentation](https://developers.google.com/mediapipe/solutions/vision/hand_landmarker)
- [Raspberry Pi GPIO Pinout](https://pinout.xyz/)
- [OpenCV HSV Color Detection Guide](https://docs.opencv.org/4.x/df/d9d/tutorial_py_colorspaces.html)
- [BSE Example Portfolio 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [BSE Example Portfolio 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [BSE Example Portfolio 3](https://arneshkumar.github.io/arneshbluestamp/)
-->
