# 🤖 Smart Café Robot

An autonomous café robot designed to navigate along a predefined path, detect obstacles, and deliver food to designated tables.

---

## 📌 Overview

The **Smart Café Robot** was developed as a team project to automate food delivery in a café environment.

The robot follows a fixed path using **IR sensors**, detects intersections, navigates toward the required destination, and uses an **ultrasonic sensor** for obstacle detection and avoidance.

The system combines **embedded programming, sensor interfacing, PWM-based motor control, UART communication, and autonomous navigation**.

---

## ✨ Features

* 🛣️ **Line Following** using three IR sensors
* 🚦 **Intersection Detection** using sensor states
* 🎯 **Table Selection** through UART communication
* 🔄 **90° Turns and U-Turns** for navigation
* 🚧 **Obstacle Detection and Avoidance** using an ultrasonic sensor
* ⚙️ **PWM Motor Control** for controlling motor speed
* 🔁 **Automatic Return** after reaching the delivery location
* 🧠 **State-based Robot Control** for managing different stages of operation

---

## 🧩 System Architecture

The robot consists of two main control systems:

### STM32 — Navigation and Motor Control

The STM32 handles:

* IR sensor readings
* Line following
* Intersection detection
* Motor direction and speed control
* PWM generation
* UART communication
* Robot state management
* Turning and U-turn routines

### VEGA — Obstacle Avoidance

The VEGA microcontroller handles:

* Ultrasonic sensor interfacing
* Obstacle detection
* Obstacle avoidance logic

---

## 🔌 Hardware Used

* STM32 Microcontroller
* VEGA Microcontroller
* IR / Line Sensors × 3
* Ultrasonic Sensor
* DC Motors
* Motor Driver
* Robot Chassis
* Power Supply

---

## 💻 Software & Technologies

* **Embedded C**
* **STM32 HAL**
* **Arduino IDE**
* **UART Communication**
* **PWM Motor Control**
* **IR Sensor Interfacing**
* **Ultrasonic Sensor Interfacing**

---

# 🧠 Navigation System

The robot uses three IR sensors:

```text
        ROBOT
   ┌─────────────┐
   │ L     C   R │
   │ ●     ●   ● │
   └─────────────┘
         ↓
      PATH/LINE
```

The sensor readings determine how the robot adjusts its motors.

### Line Following Logic

| Sensor Condition        | Robot Action          |
| ----------------------- | --------------------- |
| Center detects line     | Move forward          |
| Left detects line       | Adjust toward left    |
| Right detects line      | Adjust toward right   |
| All sensors detect line | Intersection detected |

The motor speeds are adjusted using PWM to correct the robot's position on the path.

---

# 🚦 Intersection Detection

An intersection is detected when all three sensors detect the path:

```text
L = 1
C = 1
R = 1
```

The robot uses an `atIntersection` flag to prevent the same intersection from being detected repeatedly.

When an intersection is detected, the robot can stop or execute a turning routine depending on its current state.

---

# 🔄 Robot States

The navigation software uses a state-machine approach:

```text
             ┌──────────┐
             │   IDLE   │
             └────┬─────┘
                  │
             Table command
                  ↓
             ┌──────────┐
             │  GOING   │
             └────┬─────┘
                  │
            Reach destination
                  ↓
          ┌────────────────┐
          │   DELIVERING   │
          └───────┬────────┘
                  │
                U-Turn
                  ↓
          ┌────────────────┐
          │   RETURNING    │
          └────────────────┘
```

### IDLE

The robot waits for a destination command.

### GOING

The robot follows the predefined path toward the destination.

### DELIVERING

The robot stops at the destination and waits for the delivery operation before returning.

### RETURNING

The robot performs a U-turn and follows the path back.

---

# 🎯 Table Selection

The STM32 receives table-selection commands through **USART3 UART communication**.

The received character is checked to determine whether it corresponds to a valid table number:

```text
'1' → Table 1
'2' → Table 2
'3' → Table 3
'4' → Table 4
'5' → Table 5
```

The UART is configured at:

```text
Baud Rate: 115200
Data: 8 bits
Stop Bits: 1
Parity: None
```

---

# ⚙️ Motor Control

The robot uses two DC motors controlled through GPIO direction pins and PWM.

The `Set_Motors()` function controls:

* Left motor direction
* Right motor direction
* Left motor speed
* Right motor speed

Different PWM values are used to correct the robot's position while following the line.

Example:

```c
if (C)
    Set_Motors(700, 700);
else if (L)
    Set_Motors(300, 800);
else if (R)
    Set_Motors(800, 300);
```

This allows the robot to continuously adjust its movement based on the sensor readings.

---

# 🔄 Turning Mechanism

The robot uses differential motor control to perform turns.

### Left Turn

```text
Left Motor  → Reverse
Right Motor → Forward
```

### Right Turn

```text
Left Motor  → Forward
Right Motor → Reverse
```

### U-Turn

The motors rotate in opposite directions to rotate the robot by approximately 180°.

---

# 🚧 Obstacle Avoidance

An ultrasonic sensor is used with the **VEGA microcontroller** to detect obstacles in the robot's path.

The basic process is:

```text
Ultrasonic Sensor
       ↓
Measure Distance
       ↓
Obstacle Detected?
     ↙       ↘
   YES        NO
    ↓          ↓
Avoid       Continue
Obstacle    Navigation
```

When an obstacle is detected, the robot uses the programmed avoidance routine before continuing its navigation.

---

# 🛠️ Implementation

The STM32 firmware uses the **STM32 HAL library** for peripheral control.

### Main peripherals

| Peripheral        | Purpose                        |
| ----------------- | ------------------------------ |
| GPIO              | IR sensors and motor direction |
| TIM2              | PWM motor control              |
| USART3            | Table-selection commands       |
| Ultrasonic Sensor | Obstacle detection             |

TIM2 is configured to generate PWM signals for the two motor channels.

---

# 🧪 Testing & Debugging

During development, the navigation system was tested for:

* Line-following accuracy
* Sensor response
* Intersection detection
* Turning accuracy
* Motor speed balance
* Path-width compatibility
* Obstacle detection

Navigation anomalies were debugged by adjusting the **sensor placement, sensor spacing, motor speeds, and path width**.

---

# 👩‍💻 My Contribution

My primary contribution was the **navigation and movement control** of the robot.

* Designed the line-following system using three IR sensors.
* Developed the navigation logic for following the predefined path.
* Implemented motor direction and PWM-based speed control.
* Implemented intersection detection and turning routines.
* Developed the robot's state-based navigation logic.
* Debugged navigation anomalies during testing.
* Adjusted sensor spacing and path width to improve tracking reliability.
* Worked on obstacle avoidance using an ultrasonic sensor with the VEGA microcontroller.

---

# 🚀 Future Improvements

* Implement a complete dynamic route-selection algorithm for all tables.
* Improve turning accuracy using encoder feedback.
* Replace fixed delays with sensor-based movement control.
* Add a dedicated delivery mechanism for placing food at the table.
* Improve obstacle avoidance for dynamic environments.
* Add wireless communication for real-time order and table selection.
* Implement PID-based line following for smoother navigation.

---


**Project:** Smart Café Robot
**Domain:** Embedded Systems & Robotics
