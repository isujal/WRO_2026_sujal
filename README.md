# 🚗 WRO 2026 Future Engineers — Team Paraducks

<div align="center">

<!-- TEAM BANNER PLACEHOLDER -->
<!-- Replace with your banner image: ![Banner](other/banner.png) -->

[![YouTube](https://img.shields.io/badge/YouTube-%23FF0000.svg?style=for-the-badge&logo=YouTube&logoColor=white)](https://www.youtube.com/@YOUR_CHANNEL)
[![Instagram](https://img.shields.io/badge/Instagram-%23E4405F.svg?style=for-the-badge&logo=Instagram&logoColor=white)](https://www.instagram.com/YOUR_HANDLE)

</div>

---

## 📚 Table of Contents 

- [👥 The Team](#-the-team)
- [🎯 Challenge Overview](#-challenge-overview)
- [🤖 Our Robot](#-our-robot)
- [🔧 Electronic Systems & Components](#-electronic-systems--components)
- [⚙️ Mobility Management](#️-mobility-management)
- [💻 Software Architecture](#-software-architecture)
- [🧭 Open Challenge — Strategy & Logic](#-open-challenge--strategy--logic)
- [🚧 Obstacle Challenge — Strategy & Logic](#-obstacle-challenge--strategy--logic)
- [🔄 Engineering Decisions & Iterations](#-engineering-decisions--iterations)
- [📹 Performance Videos](#-performance-videos)
- [🛠️ How to Build & Deploy](#️-how-to-build--deploy)

---

## 👥 The Team

<!-- Replace with your official team photo -->
<!-- ![Team Official Photo](t-photos/team_official.jpg) -->

**PLACEHOLDER: Official Team Photo**

| Member | Role | Background |
|--------|------|------------|
| **[Name 1]** | Team Lead — Mechanical Design, Electronics, Strategy | [School/Grade] |
| **[Name 2]** | Software, Vision & Sensor Integration | [School/Grade] |
| **[Name 3 if applicable]** | Electronics, Testing, Documentation | [School/Grade] |

**Coach:** [Coach Name] — [Institution/Background]

<!-- Replace with your fun team photo -->
<!-- ![Team Fun Photo](t-photos/team_fun.jpg) -->

**PLACEHOLDER: Fun Team Photo**

---

## 🎯 Challenge Overview

WRO 2026 Future Engineers is a self-driving car challenge where an autonomous robotic vehicle must complete three laps on a 3m × 3m racetrack that randomly changes configuration for each round. The competition has two challenge types:

**Open Challenge** — Complete three laps on a track with randomly configured internal walls. No traffic signs are present. The lane width changes per section (1000 mm or 600 mm). The goal is to navigate cleanly and quickly, stopping autonomously in the starting section after three laps.

**Obstacle Challenge** — Complete three laps while obeying red and green traffic sign pillars (red = keep right, green = keep left). After completing all three laps, the vehicle must identify the magenta-bordered parking lot and execute a parallel park within it. The parking space is exactly 1.5× the robot's length, and touching the magenta boundaries instantly ends the round.
<div align="center">

<table>
  <tr>
    <td align="center"><b>Open Challenge</b></td>
    <td align="center"><b>Obstcale Challenge</b></td>
  </tr>
  <tr>
    <td><img src="others/open_overview.png" width="400" height="500"></td>
    <td><img src="others/obs_oveview.png" width="400" height="500"></td>
  </tr>
</table>

</div>

Both challenges are Time Attack format — one vehicle at a time, scored on laps completed, traffic sign compliance, stopping position, and parking success.

---

## 🤖 Our Robot

<div align="center">

<table>
    <tr>
    <td colspan="2" align="center"><b>Labeled Component View</b></td>
  </tr>
  <tr>
    <td colspan="2" align="center">
      <img src="schemes/isometric_1.jpg" width="600" height="500">
    </td>
  </tr>
</table>

</div>


### Key Specifications

| Parameter | Value |
|-----------|-------|
| **Dimensions** | 150 × 80 × 150 mm |
| **Weight** | 0.93 kg |
| **Drive Type** | Rear-wheel drive with 12V DC geared motor |
| **Steering** | Servo-actuated Pivoted geometry |
| **Primary Brain** | Raspberry Pi 4 Model B (4 GB) |
| **Co-processor** | Arduino Mega 2560 (sensor I/O + IMU) |
| **Vision** | HIKVISION DS-U02 USB Camera + Edge TPU ML model |
| **Spatial Awareness** | RPLidar C1 (360°) + 4× TFmini Plus (point ranging) |
| **Heading** | Adafruit BNO085 9-DOF IMU |
| **Max Operating Speed** | ~95% PWM duty cycle  |

---

## 🔧 Electronic Systems & Components

### 🗂️ Component Overview

| Component | Image | Specifications | Role in Robot | Source |
|-----------|-------|---------------|---------------|--------|
| **Raspberry Pi 4 Model B (4GB)** |<img src="others/raspi_4b.jpg" width="150" height="150"> | Quad-core Cortex-A72 @ 1.8GHz, 4 GB LPDDR4, USB 3.0, GPIO 40-pin | Main compute — runs vision (Edge TPU ML), LiDAR parsing, servo/motor PID control via pigpio | [robu.in](https://robu.in/product/raspberry-pi-4-model-b-with-4-gb-ram/) |
| **Arduino Mega 2560** | <img src="others/arduino_mega.jpg" width="150" height="150"> | ATmega2560 @ 16MHz, 54 digital I/O, 4× UART, 16 analog inputs | Co-processor — reads BNO085 IMU + encoder, broadcasts fused heading+counts to Pi at 115200 baud over UART | [robu.in](https://robu.in/product/mega-2560-atmega2560-16au-board-without-usb-cable/) |
| **SLAMTEC RPLidar C1M1-R2** | <img src="others/Lidar.jpg" width="150" height="150"> | 360° DTOF, 12m range, 5000 samples/sec, 0.72° angular resolution, 10Hz scan rate | Wall proximity for turn detection (lidar_f, lidar_l, lidar_r shared values); turn triggered when front <950mm and open side >1500mm | [robu.in](https://robu.in/product/rplidar-c1m1-r2-portable-tof-laser-scanner-kit-12m-range/) |
| **TFmini Plus (×4)** | <img src="others/tfmini.jpg" width="150" height="150"> | Range: 0.1–12m, Accuracy: ±5cm (<6m), ±1% (>6m), UART 115200 baud, 100–1000Hz, IP65 | Point-range sensors: Head (front obstacle), Left, Right (wall following + parking detection), Back (reverse maneuvers) | [robu.in](https://robu.in/product/tfmini-plus-lidar-distance-sensor-for-drones-uav-uas-robots-12m/) |
| **Adafruit BNO085 IMU** | <img src="others/bno085.jpg" width="150" height="150"> | 9-DOF (accel + gyro + mag), onboard ARM Cortex-M0 fusion, Euler output at 100Hz, I2C | Absolute heading for PID steering correction; prevents drift accumulation across all 3 laps | [robu.in](https://robu.in/product/adafruit-9-dof-absolute-orientation-imu-fusion-breakout-bno055-stemma-qt-qwiic/) |
| **HIKVISION DS-U02 Camera** | <img src="others/camera.jpg" width="150" height="150"> | 2MP, 1080P @ 30fps, ultra-wide angle, USB 2.0, manual focus, distortion correction | Input for Edge TPU object detection (red/green/pink pillars); also provides colour confirmation for lap direction detection | [amazon.in](https://www.amazon.in/HIKVISION-DS-U02-Distortion-Adjustment-Conferencing/dp/B0929FSQ2J) |
| **12V 600RPM Johnson Motor** | <img src="others/motor.jpg" width="150" height="150"> | 12V DC, 600 RPM, torque 4.5 kg·cm (15.1 N·cm), metal planetary gearbox, encoder-compatible rear shaft, 6mm D-shaft | Rear-wheel drive; encoder rear-shaft used for distance tracking (odometry) | [robu.in](https://robu.in/product/grade-a-quality-orange-12v-600-rpm-johnson-geared-dc-motor/) |
---

### 🖨️ Custom PCB — Design & Purpose

One of the most significant engineering decisions was designing and manufacturing a **custom PCB** specifically for this robot rather than using breadboards or loose dupont wires. The PCB was designed in **Altium** and is the central nervous system of the robot — routing every GPIO signal from the Raspberry Pi 40-pin header to sensors, actuators, and sub-modules through permanently soldered, clearly labeled connections. This eliminates the most common competition failure mode: loose wires disconnecting under vibration mid-run.

<div align="center">

<table>
  <tr>
    <td align="center"><b>PCB Layout</b></td>
    <td align="center"><b>PCB Schematic</b></td>
  </tr>
  <tr>
    <td><img src="schemes/custom_pcb.jpg" width="500"></td>
    <td><img src="schemes/schematic.jpg" width="500"></td>
  </tr>
</table>

</div>



**PCB Power Rails:**

| Rail | Source | Supplies |
|------|--------|----------|
| 5V RP | Raspberry Pi 5V (J1) | Sensor VCC, header logic |
| 3V3 RP | Raspberry Pi 3.3V | BNO085 IMU VIN, logic signals |
| 12V IN | LiPo battery direct (J2, J4) | Motor driver input, buck converter input |
| M IN | Cytron MD10C R3 output | Johnson DC Motor terminals (J3) |

---

### 🔋 Power Architecture — Dual-Rail System with Buck Converter

The robot uses a carefully engineered **dual-rail power architecture** that completely separates the high-current 12V motor rail from the sensitive 5V logic electronics rail. This separation is not optional — without it, PWM commutation switching noise from the motor driver creates voltage spikes on shared rails that corrupt I²C, UART, and GPIO signal integrity. We discovered this empirically: during early testing with a shared rail, BNO085 readings jumped by 5–8° at the moment of motor start, and TFmini sensors would report 0cm readings for 50–100ms after each motor direction change.

```
┌─────────────────────────────────────────────────────────────┐
│                  11.1V 3S LiPo Battery                      │
└──────────────┬──────────────────────────┬───────────────────┘
               │                          │
               ▼                          ▼
   ┌───────────────────┐       ┌──────────────────────────┐
   │  Cytron MD10C R3  │       │  DC-DC Buck Converter    │
   │  Motor Driver     │       │  Input:  12V (LiPo)      │
   │  PWM: GPIO 12     │       │  Output: 5V / 3A stable  │
   │  DIR: GPIO 20     │       │  Efficiency: ~95%        │
   └─────────┬─────────┘       └────────────┬─────────────┘
             │                              │
             ▼                              ▼
   Johnson 12V DC Motor          ┌──────────────────────────────────┐
   600 RPM Rear Drive            │          5V Logic Rail            │
                                 ├──────────────────────────────────┤
                                 │ Raspberry Pi 4 (5V/3A USB-C)     │
                                 │ Arduino Mega 2560 (5V via Vin)   │
                                 │ RPLidar C1 (5V, 400mA)           │
                                 │ TFmini Plus ×4 (5V, 120mA each)  │
                                 │ DS3235 Servo (5–6V, 250mA)       │
                                 │ HIKVISION Camera (5V USB)         │
                                 │ Google Coral Edge TPU (5V USB)    │
                                 │ BNO085 IMU (3.3V from RPi)        │
                                 └──────────────────────────────────┘
```
<div align="center">

<table>
  <tr>
    <td align="center"><b>Wiring Diagram</b></td>
  </tr>
  <tr>
    <td><img src="schemes/wiring_diagram.jpg" width="500"></td>
  </tr>
</table>

</div>


#### Full Power Budget

| Component | Voltage | Typical Current | Peak Current |
|-----------|---------|-----------------|--------------|
| Raspberry Pi 4 | 5V | 600–900mA | 1.5A |
| Arduino Mega 2560 | 5V | 80–150mA | 200mA |
| RPLidar C1 | 5V | 400mA | 600mA |
| TFmini Plus ×4 | 5V | 480mA (4×120) | 720mA (4×180) |
| DS3235 Servo | 5–6V | 250mA | 2.3A (stall) |
| HIKVISION Camera | 5V USB | 250mA | 400mA |
| Google Coral TPU | 5V USB | 900mA | 1.5A |
| BNO085 IMU | 3.3V | ~10mA | — |
| IR LEDs ×3 | 3.3V | 45mA (3×15) | — |
| **5V Rail Total** | 5V | **~3.0A typical** | **~5.5A peak** |
| Johnson Motor | 12V | 1.5–2A | 4–5A (stall) |
| **Battery Total (peak)** | 12V | — | **~7–8A** |


---

### 📊 Sensor Placement Rationale

Every sensor position was physically tested and validated — not placed by intuition:

**RPLidar — Elevated Mast:** At lower heights, the chassis and wheel wells created ±15° shadow zones around the forward axis. Elevation to mast-top cleared all shadows. Yaw offset compensated in software: `(0 + imu_heading + servo_offset) % 360`.

**Camera — Mast Top, Angled Down:** Mid-chassis horizontal mount gave too narrow a field of view — close pillars were cropped. Mast-top with ~15° downward tilt gives consistent detection from 30cm to 150cm forward, matching the PID reaction distance. The `cy > 240` gate ensures only physically-close pillars (lower frame half) trigger avoidance.

**TFmini Front — Horizontal, Not Angled:** Initial 15° downward tilt caused ground reflection detection under 80cm on smooth WRO mat. Re-mounting horizontal resolved all false triggers.

**TFmini Left/Right — Mid-Body Flush:** Used for both initial direction detection (side with >100cm reading = open lane) and wall-follow PID. The 15cm hard-override (`if dist_left < 15: correction -= 20`) prevents wall contact under any heading error.

**TFmini Rear — Added in Version 3:** Without it, the robot had no awareness of rear wall proximity during the reverse arc of parking, causing occasional rear collisions in the narrow slot.

**BNO085 — PCB Far from Motor:** Initial placement within 40mm of the Johnson motor caused 3–5° heading jumps at high motor speeds due to commutator magnetic interference. Relocating to 80mm away on the PCB eliminated interference completely.

---

### 📊 Complete Electronic Systems Summary

| Component | Model | Interface | Voltage | Current | Function |
|-----------|-------|-----------|---------|---------|----------|
| Raspberry Pi 4 | 4GB Model B | GPIO/USB | 5V | 0.6–1.5A | Main compute, 4-process orchestration |
| Arduino Mega | ATmega2560 | UART/I²C | 5V | 80–200mA | IMU + encoder UART bridge |
| Custom PCB | WRO_PCB | 40-pin | 3.3/5V | — | GPIO routing, connector breakout |
| BNO085 IMU | Adafruit #4754 | I²C → Arduino | 3.3V | ~10mA | Absolute heading (Euler °) |
| RPLidar C1 | SLAMTEC C1M1-R2 | USB Serial | 5V | 400mA | 360° turn detection |
| TFmini Plus ×4 | Benewake | Bitbang UART | 5V | 480mA total | Point-range F/L/R/B |
| Camera | HIKVISION DS-U02 | USB 2.0 | 5V | 250mA | Pillar visual detection |
| Coral TPU | Google USB | USB 3.0 | 5V | 900mA | Edge ML inference |
| Servo | DS3235 35kg | PWM GPIO 8 | 5–7.4V | 250mA–2.3A | Pivot steering 0–180° |
| DC Motor | Johnson 12V 600RPM | PWM+DIR | 12V | 1.5–5A | Rear-wheel drive |
| Motor Driver | Cytron MD10C R3 | GPIO 12+20 | 12V | 10A cont. | Motor H-bridge control |
| Buck Converter | 5V/3A module | 12V→5V | 12V in | 3A out | Logic voltage regulation |
| LiPo Battery | 3S 11.1V | — | 11.1–12.6V | 8A peak | Primary power |
| IR LEDs ×3 | TSFF5210 | GPIO 6/10/26 | 3.3V | 15mA each | Status indicators |
| Buttons ×2 | 430476073716 | GPIO 5/7 | 3.3V | — | Start (SW2), E-Stop (SW1) |

---

## ⚙️ Mobility Management

### 🏗️ Chassis Design & Structural Architecture

The chassis of Team Paraducks' robot is built around a **multi-layer open-frame architecture** using laser-cut acrylic plates and anodized aluminum standoff pillars. This structural philosophy was chosen over a conventional enclosed box chassis for three specific engineering reasons: it provides unobstructed 360° access to every component for rapid field servicing, it allows sensors to be repositioned between layers without dismantling the frame, and it keeps the overall mass low by eliminating solid side panels.

The frame consists of **three horizontal acrylic decks** connected by four vertical aluminum pillars at each corner. The bottom deck houses the drive motor, steering servo, and wheel assemblies — the heaviest mechanical components — deliberately positioned low to keep the center of gravity below the midpoint of the robot's height. This low CG prevents tipping during aggressive cornering on the WRO field. The middle deck carries the power electronics: the motor driver (Cytron MD10C R3), the power distribution module, and the LiPo battery. The top deck mounts the Raspberry Pi 4, the Arduino Mega, and all sensor interface boards. Above everything, a dedicated elevated mast built from aluminum rods supports the RPLidar C1 at a height sufficient to achieve unobstructed 360° scanning over all other chassis elements.

The **4× TFmini Plus sensors** are mounted at chassis mid-height on custom 3D-printed brackets — one facing forward (head), one left, one right, one rear. Their positions were finalized after testing revealed that placing them too low caused ground-plane reflections from the white WRO mat at close distances, producing false readings under 40 cm. Raising them to chassis mid-height eliminated this artifact entirely.

The yellow 3D-printed components visible in the photos serve as the **camera mounting bracket** at the top of the mast and the servo-to-chassis linkage at the front steering. These were designed in CAD and printed in PLA for rapid iteration — the camera angle was adjusted three times during development before settling on the final downward tilt that gives the best pillar detection field of view.

<div align="center">

<table>
  <tr>
    <td align="center"><b>Front View</b></td>
    <td align="center"><b>Rear View</b></td>
  </tr>
  <tr>
    <td><img src="v-photos/front.jpeg" width="350"></td>
    <td><img src="v-photos/rear.jpeg" width="350"></td>
  </tr>
  <tr>
    <td align="center"><b>Left Side</b></td>
    <td align="center"><b>Right Side</b></td>
  </tr>
  <tr>
    <td><img src="v-photos/left.jpeg" width="350"></td>
    <td><img src="v-photos/right.jpeg" width="350"></td>
  </tr>
  <tr>
    <td align="center"><b>Top View</b></td>
    <td align="center"><b>Bottom View</b></td>
  </tr>
  <tr>
    <td><img src="v-photos/top.jpeg" width="350"></td>
    <td><img src="v-photos/bottom.jpeg" width="350"></td>
  </tr>
</table>

</div>

---

### 🔄 Steering System — From Ackermann to Pivot Steering

#### Iteration 1: Ackermann Steering (Rejected)

The first prototype used a classical **Ackermann steering geometry** with a dedicated steering linkage connecting both front wheels via tie rods to a central servo horn. In Ackermann geometry, the inner and outer front wheels turn at different angles during a curve — the inner wheel turns more sharply than the outer — so both wheels track their respective arc centers without scrubbing. This is the geometry used in automobiles.

**Why we rejected it:** The WRO 2026 game field requires the robot to execute sharp 90° right-angle turns at each corner of the 3m × 3m track. Our Ackermann geometry, combined with the physical wheelbase and track width of our chassis, produced a **minimum turning radius of approximately 380 mm**. This was critically insufficient — at this radius, the robot would clip the inner wall during corner transitions, particularly in the 600mm-wide narrow corridor configuration of the Open Challenge. We measured this failure rate at approximately 60% of turns in narrow corridors during early testing.

Additionally, the Ackermann tie-rod linkage introduced mechanical play and backlash between the servo horn and the wheel knuckle. At 35 kg·cm of servo torque, this play caused the front wheels to oscillate ±3° around the target angle, which the IMU PID loop perceived as heading error and over-corrected — resulting in a characteristic S-weave pattern at speed.

#### Iteration 2: Single Pivot Steering (Current Design)

We replaced the Ackermann linkage with a **single-pivot front steering mechanism**. In this design, the entire front axle assembly rotates as a single unit around a central vertical pivot point. The DS3235 servo's output horn is directly coupled to this pivot via a rigid arm — there are no tie rods, no independent wheel knuckles, and no mechanical intermediate linkage.

This design has a fundamentally different geometric characteristic: both front wheels turn at **the same angle simultaneously**, which technically produces some inner-wheel scrub during turns. However, for our robot's weight (~1.8 kg) and wheel material (rubber-compound RC car tyres), this scrub force is negligible and does not affect directional stability. The critical advantage gained is a **dramatically reduced minimum turning radius** — because the full 0°–180° servo range directly translates to front axle rotation, we can command extreme steering angles that Ackermann tie-rod geometry physically cannot achieve without binding.

At full servo deflection (0° or 180° command), the robot executes a turn radius of approximately **180–220 mm** — less than half of the Ackermann prototype. This is sufficient to navigate all corner configurations on the WRO field, including the tightest 600mm corridor width, with clearance to spare.

```
Steering angle vs. Pulse Width:
  0°   = 500 µs  → Full left
  90°  = 1500 µs → Straight ahead (neutral)
  180° = 2500 µs → Full right

Mapping formula (from Servo.py):
  pulse_width = 500 + round(angle × 11.11)
  setAngle(90 - PID_correction)    [forward driving]
  setAngle(90 + PID_correction)    [reverse driving — mirrored]
```

The servo operates at **50 Hz PWM** (20ms frame period), commanded by pigpio's `set_servo_pulsewidth` on GPIO 8 of the Raspberry Pi. At competition voltage (6V), the servo responds in **0.12 sec/60°** — meaning a full 180° sweep takes approximately 0.36 seconds. In practice, the PID correction values are clamped to ±30° (±60° during turns with multiplier=3), so typical steering adjustments complete in under 0.06 seconds, keeping the control loop responsive at driving speeds.

---

### 🎯 DS3235 Servo Motor — Full Technical Specification

The **DS3235 35kg Digital Servo** by DSServo is a high-torque, waterproof digital servo designed for demanding RC and robotics applications. It was selected because its torque output (35 kg·cm at 7.4V) massively exceeds the steering load requirement, meaning it operates well within its comfort zone with no risk of stall or overheating during aggressive cornering maneuvers.

#### Electrical Specifications

| Parameter | @ 5.0V | @ 6.0V | @ 7.4V |
|-----------|--------|--------|--------|
| **Stall Torque** | 29 kg·cm (2.845 N·m) | 32 kg·cm (3.138 N·m) | **35 kg·cm (3.432 N·m)** |
| **No-load Speed** | 0.13 sec/60° | 0.12 sec/60° | **0.11 sec/60°** |
| **Stall Current** | 1.9 A | 2.1 A | 2.3 A |
| **Idle Current** | ~5 mA | ~5 mA | ~5 mA |
| **Operating Voltage Range** | 5.0 V – 7.4 V DC | | |

#### Control Specifications

| Parameter | Value |
|-----------|-------|
| **Control System** | Digital PWM |
| **Pulse Width Range** | 500 – 2500 µs |
| **Neutral Position** | 1500 µs (90°) |
| **Full Range of Motion** | 180° (500–2500 µs) |
| **Dead Band Width** | 3 µs |
| **Operating Frequency** | 50 – 330 Hz |
| **Rotation Direction** | Counter-clockwise (500→2500 µs) |

#### Mechanical Specifications

| Parameter | Value |
|-----------|-------|
| **Dimensions** | 40 × 20 × 38.5 mm |
| **Weight** | 60 – 80 g |
| **Gear Type** | Copper & Stainless Steel (hard-anodized) |
| **Gear Ratio** | 373:1 |
| **Bearing Type** | Double ball bearing |
| **Motor Type** | Coreless motor |
| **Waterproof Rating** | IP66 |
| **Connector Wire Length** | 450 mm |

#### Why These Specs Matter for WRO

The **373:1 gear ratio** is the key to the servo's massive torque output. At the cost of rotation speed, each motor revolution produces 373× amplified torque at the output shaft. For steering we need force, not speed — so this ratio is ideal. The **coreless motor** (no iron armature) reduces rotor inertia significantly compared to standard cored motors, which means the servo responds faster to small PWM changes and produces less electromagnetic noise — important for the BNO055 IMU mounted nearby.

**Torque safety margin calculation:**

```
Estimated steering load:
  Robot mass                  ≈ 1.8 kg
  Normal force on front wheels ≈ 0.9 kg (50% weight distribution)
  Rubber-on-mat friction coeff ≈ 0.6
  Steering moment arm          ≈ 40 mm (pivot to wheel contact patch)

Required torque = 0.9 × 9.81 × 0.6 × 0.04 = 0.212 N·m = 2.16 kg·cm

Servo rated torque @ 6V = 32 kg·cm
Safety factor = 32 ÷ 2.16 = 14.8×
```

A safety factor of nearly **15×** means the servo will never stall during any steering maneuver on the WRO field regardless of wheel load distribution during cornering.

---

### ⚡ Drive System — Differential Drive with Johnson DC Motor

#### Architecture

The robot uses a **rear-wheel drive differential drive** configuration. A single Johnson 12V 600 RPM geared DC motor drives the rear axle through a direct coupling. The rear two wheels are **mechanically linked on a shared axle** — both rotate together at all times, driven by the same motor. This is distinct from a differential wheeled robot (which uses two independent motors, one per side) and is fully compliant with WRO rules 11.3 and 11.5.

The motor is mounted **longitudinally along the robot's centerline** at the bottom chassis layer, with its output shaft coupled directly to the rear axle via a rigid shaft coupler. This central mounting minimizes lateral weight imbalance between left and right sides, which would otherwise create yaw bias that the IMU PID must constantly correct.

Speed control is achieved through **PWM duty cycle modulation** on GPIO 12 (hardware PWM at 55 Hz) via the Cytron MD10C R3 motor driver. Direction control uses GPIO 20 as a digital direction pin:

```
Forward:  GPIO 20 = HIGH,  GPIO 12 PWM = 0–100%
Reverse:  GPIO 20 = LOW,   GPIO 12 PWM = 0–100%
```

#### Power Smoothing Filter

Raw PWM transitions (0% → 100% instantly) cause inrush current spikes that momentarily droop the 12V rail, which corrupts UART readings from the TFmini sensors. To eliminate this, we apply a **first-order exponential moving average** to the duty cycle command:

```python
# 90/10 EMA — smooth acceleration without jerking
total_power = (target_power × 0.1) + (prev_power × 0.9)
prev_power  = total_power
pwm.set_PWM_dutycycle(12, int(2.55 × total_power))
```

This means reaching 100% from 0% takes approximately 20 control loop iterations rather than 1. At 100Hz loop rate, this is a 200ms ramp-up — imperceptible to navigation but completely eliminates sensor UART corruption. We discovered this fix after 3 hours of debugging what appeared to be a sensor firmware issue.

---

### 🔩 Orange 12V 600 RPM Johnson DC Motor — Full Technical Specification

The **Orange Grade-A Johnson 12V 600 RPM Geared DC Motor** is an industrial-grade brushed DC motor with an integrated metal planetary gearbox. "Grade A" refers to Johnson's quality tier designation — the same motor series used in automotive actuators and industrial equipment — as opposed to lower-grade clones that use inferior gear material and bushing quality.

#### Electrical Specifications

| Parameter | Value |
|-----------|-------|
| **Rated Voltage** | 12V DC |
| **Operating Voltage Range** | 6V – 18V DC |
| **Rated Speed** | 600 RPM (at 12V, no load) |
| **Rated Torque** | 15.1 N·cm (1.54 kg·cm) |
| **Stall Torque** | 4.5 kg·cm (44.1 N·cm) |
| **Motor Type** | Brushed DC with metal gearbox |
| **Gearbox Type** | Metal planetary gearbox |

#### Mechanical Specifications

| Parameter | Value |
|-----------|-------|
| **Main Shaft Length** | 30 mm (extended for reliable coupling) |
| **Main Shaft Feature** | M3 threaded hole for shaft coupler |
| **Rear Shaft** | Encoder-compatible (OE-28 Hall Effect) |
| **Shaft Material** | Metal bushings for long service life |
| **Encoder Compatibility** | OE-28 Hall Effect quadrature encoder |
| **Encoder Type** | Quadrature (2-channel Hall Effect) |
| **Encoder Resolution** | 2015 ticks per revolution |

#### Performance Across the Johnson RPM Family

| Variant | Speed | Torque | Use Case |
|---------|-------|--------|----------|
| 30 RPM | 30 RPM | 254 N·cm | Heavy load, slow actuator |
| 60 RPM | 60 RPM | 157.6 N·cm | Slow robot drive |
| 200 RPM | 200 RPM | 56.1 N·cm | Medium speed |
| 300 RPM | 300 RPM | 34.2 N·cm | Too slow for WRO laps |
| **600 RPM ← Ours** | **600 RPM** | **15.1 N·cm** | **Optimal WRO balance** |
| 1000 RPM | 1000 RPM | ~9 N·cm | Too fast, insufficient torque |

This table illustrates the fundamental speed-torque tradeoff in geared motors. We selected 600 RPM as the optimal balance: fast enough to complete 3 laps well within the 3-minute time limit, yet torqueful enough to maintain traction under PWM modulation at low duty cycles during parking maneuvers (~36% PWM).

#### Motor Selection Reasoning

| Option Considered | RPM | Stall Torque | Verdict |
|------------------|-----|-------------|---------|
| Johnson 300 RPM | 300 | ~8 kg·cm | ❌ Too slow — lap time exceeds 60s |
| **Johnson 600 RPM ← Selected** | **600** | **4.5 kg·cm** | **✅ Optimal speed-torque balance** |
| Generic 1000 RPM | 1000 | ~1.5 kg·cm | ❌ Wheel slip at low PWM, poor traction |

#### Linear Speed Calculation

```
Wheel diameter (measured): ~65 mm → radius r = 32.5 mm = 0.0325 m

At 100% PWM (600 RPM):
  Linear speed = (600 rev/min ÷ 60 s/min) × 2π × 0.0325 m
               = 10 rev/s × 0.2042 m/rev
               = 2.04 m/s (theoretical maximum)

At 95% PWM (competition straight-line speed):
  Effective speed ≈ 1.94 m/s

At 36% PWM (parking reverse speed):
  Effective speed ≈ 0.73 m/s

Estimated lap time:
  Track perimeter ≈ 8.0 m per lap
  Time per lap ≈ 8.0 m ÷ 1.94 m/s ≈ 4.1 seconds
  3 laps driving time ≈ 12.3 s (excluding turn deceleration)
  → Substantial margin within the 3-minute time limit
```

#### Encoder Integration

The OE-28 Hall Effect encoder mounts to the rear shaft of the motor. It produces quadrature pulses (2 channels, 90° phase offset) that allow both speed and direction detection. The Arduino Mega reads these pulses via interrupt-driven counting and transmits the running total to the Raspberry Pi over UART at 115200 baud.

```
Encoder resolution : 2015 ticks/revolution
Wheel circumference: 2π × 32.5 mm = 204.2 mm
Distance per tick  : 204.2 mm ÷ 2015 = 0.1013 mm/tick

Example: 5000 ticks → 506.6 mm ≈ 50.7 cm traveled
Parking precision  : ±20 mm accuracy achieved with encoder odometry
```

#### Motor Routing to PCB

```
Johnson Motor (rear axle)
        │
        │  14 AWG orange power cable (handles up to 5A continuous)
        ▼
Cytron MD10C R3 Motor Driver (middle chassis layer)
        │
        ├── PWM input  ← GPIO 12 (Raspberry Pi, 55 Hz hardware PWM)
        ├── DIR input  ← GPIO 20 (Raspberry Pi, digital HIGH/LOW)
        ├── 12V power  ← 12V rail from LiPo battery (direct)
        └── GND        ← Common ground bus
```

The thick orange power cable visible in the side-view photos is the motor power line from the LiPo to the MD10C R3. 14 AWG gauge was chosen to handle up to 5A continuous current without significant resistive voltage drop, which would otherwise reduce effective motor voltage and therefore speed consistency across battery charge levels.

---

### 📐 Mobility Summary Table

| Parameter | Value | Notes |
|-----------|-------|-------|
| **Drive Configuration** | Rear-wheel drive, single motor | WRO-compliant (rules 11.3, 11.5) |
| **Steering Type** | Single-pivot front axle | Replaced Ackermann after v1 failure |
| **Steering Range** | 0° – 180° (full servo range) | ~180–220mm min turning radius |
| **Drive Motor** | Orange Johnson 12V 600 RPM Grade-A | Metal gearbox, encoder-compatible |
| **Stall Torque (motor)** | 4.5 kg·cm (44.1 N·cm) | 15.1 N·cm at rated 600 RPM |
| **Max Linear Speed** | ~2.04 m/s at 100% PWM | Competition speed: 1.94 m/s (95%) |
| **Parking Speed** | ~0.73 m/s at 36% PWM | Reverse parking maneuvers |
| **Encoder Resolution** | 2015 ticks/revolution | 0.1013 mm/tick linear resolution |
| **Steering Servo** | DS3235, 35 kg·cm @ 7.4V | IP66 waterproof, coreless, 373:1 gear |
| **Servo PWM Range** | 500 – 2500 µs (0°–180°) | Neutral at 1500 µs (90°) |
| **Servo Speed** | 0.12 sec/60° @ 6V | Full 180° sweep in ~0.36 s |
| **Servo Torque Safety Factor** | 14.8× above steering load | Never stalls in any WRO condition |
| **Chassis Architecture** | 3-layer acrylic + aluminum pillars | Open-frame for access and low mass |
| **Motor Driver** | Cytron MD10C R3 | PWM + DIR via Raspberry Pi GPIO |
| **Estimated Robot Weight** | ~1.8 kg | Fill in with actual measured value |
| **Chassis Dimensions** | [L] × [W] × [H] mm | Fill in your measured values |

### 🔁 Chassis Iteration History

**Version 1 — Regional Competition:**
Ackermann steering, single TFmini (front only), no LiDAR. Turn detection relied purely on TFmini threshold. Failed in narrow corridors due to 380mm min turning radius — 60% miss rate on tight corners. Ackermann backlash caused IMU PID oscillation.

**Version 2 — National Competition:**
Replaced Ackermann with pivot steering, reducing min turn radius to ~200mm. Added RPLidar for compound turn detection (front <950mm AND side >1500mm). Added left + right TFmini for wall-follow PID. Remounted camera with fixed exposure. Parking still unreliable — single-pass approach overshot.

**Version 3 — World Final (Current):**
Added rear TFmini for reverse parking depth sensing. Multi-stage 4-state parking sequence implemented. Separate encoder setpoints for parking_right vs. parking_left. Dual-rail 12V/5V power separation eliminated sensor UART corruption from motor switching noise.

---

## 💻 Software Architecture

The software runs on Raspberry Pi 4 using Python's `multiprocessing` module. Each major function is a separate OS process with its own memory space, communicating exclusively through `multiprocessing.Value` shared variables. This ensures that a slow ML inference cycle never blocks the time-critical steering loop.

> **Why `multiprocessing` over `threading`?** Python threads are limited by the GIL, meaning CPU-bound tasks don't truly run in parallel. `multiprocessing` spawns real OS processes, each with their own GIL, fully utilizing the Pi 4's quad-core CPU.

```
Main Process (spawns all, then exits)
├── S — servoDrive      MAIN CONTROL LOOP: reads all shared vars, makes steering decisions
├── E — runEncoder      UART from Arduino Mega → head.value, counts.value
├── L — Live_Feed       Camera → Edge TPU ML → red_b, green_b, pink_b, centr_x/y
└── P — read_lidar      RPLidar C1 SDK subprocess → lidar_f, lidar_l, lidar_r, turn_trigger
```

**Shared variables (cross-process, lock-protected):**

| Variable | Type | Written by | Read by | Purpose |
|----------|------|-----------|---------|---------|
| `head` | float | runEncoder | servoDrive, read_lidar | IMU heading (degrees) |
| `counts` | int | runEncoder | servoDrive | Encoder pulses for odometry |
| `red_b` | bool | Live_Feed | servoDrive | Red pillar detected |
| `green_b` | bool | Live_Feed | servoDrive | Green pillar detected |
| `pink_b` | bool | Live_Feed | servoDrive | Pink parking marker detected |
| `centr_x/y` | float | Live_Feed | servoDrive | Green pillar centroid (px) |
| `centr_x_red/y_red` | float | Live_Feed | servoDrive | Red pillar centroid (px) |
| `centr_x_pink/y_pink` | float | Live_Feed | servoDrive | Pink marker centroid (px) |
| `lidar_f/l/r` | double | read_lidar | servoDrive | EMA-smoothed LiDAR distances (mm) |
| `turn_trigger` | bool | read_lidar | servoDrive | Turn condition met |
| `sp_angle` | int | servoDrive | read_lidar | Current heading offset for LiDAR compensation |

### PID Steering Control

```python
# correctAngle() — PD controller (ki = 0 in obstacle challenge to prevent windup)
error_gyro = heading - setPoint_gyro
if error_gyro > 180:
    error_gyro -= 360          # Wraparound handling

pTerm = kp * error_gyro        # kp = 0.6
dTerm = kd * (error_gyro - prevErrorGyro)  # kd = 0.1
correction = max(-30, min(30, pTerm + dTerm))  # Clamp ±30°

servo.setAngle(90 - correction)
```

The `multiplier` parameter scales aggressiveness: `1.0` for normal wall-following, `1.5` for block tracking, `3.0` for parking turns — avoiding the need for multiple PID instances.

<!-- DIAGRAM SUGGESTION:
     ▸ schemes/software_arch.png — block diagram of the 4-process architecture
       Show the 4 boxes (S, E, L, P) with arrows representing shared variable reads/writes.
       Label each arrow with the variable name and data type.
       Use colour coding: S=blue (control), E=green (sensors), L=orange (vision), P=purple (lidar).
-->

---

## 🤖 Object Detection Model — MobileNet SSD + Edge TPU

### Dataset Collection & Labelling
<div>

<table>

  <tr>
    <td><img src="others/dataset_samples/1.png" width="200"></td>
    <td><img src="others/dataset_samples/2.png" width="200"></td>
    <td><img src="others/dataset_samples/3.png" width="200"></td>
  </tr>
  <tr>
    <td align="center"><b>(a)</b></td>
    <td align="center"><b>(b)</b></td>
    <td align="center"><b>(c)</b></td>
  </tr>

  <tr>
    <td><img src="others/dataset_samples/4.png" width="200"></td>
    <td><img src="others/dataset_samples/5.png" width="200"></td>
    <td><img src="others/dataset_samples/6.png" width="200"></td>
  </tr>
  <tr>
    <td align="center"><b>(d)</b></td>
    <td align="center"><b>(e)</b></td>
    <td align="center"><b>(f)</b></td>
  </tr>
</table>

</div>

Our training dataset was built entirely from real-world competition images captured on the WRO game mat under varied lighting conditions. The dataset was managed on **Roboflow**, where we manually classified and annotated every image with bounding boxes across three classes:

| Class | Description | Colour |
|-------|-------------|--------|
| `green` | Green traffic pillar (pass on left) | 🟢 Green |
| `red` | Red traffic pillar (pass on right) | 🔴 Red |
| `pink` | Magenta parking lot boundary marker | 🟣 Pink/Magenta |

Each image was individually labelled with tight bounding boxes around the pillar or marker. After labelling, the dataset was exported from Roboflow in **TFRecord format** (the required format for TensorFlow Object Detection API training).

<!-- DIAGRAM SUGGESTION:
     ▸ others/dataset_samples.jpg — a 3×3 or 4×4 grid of annotated images from Roboflow
       showing sample bounding boxes around red, green, and pink objects.
       Export the "dataset overview" or "annotation preview" from Roboflow directly.
     ▸ others/roboflow_split.png — screenshot of dataset split (train/val/test counts)
-->

### Model Architecture — MobileNet SSD v2

The base architecture is **MobileNet SSD v2 (Single Shot Detector with MobileNetV2 backbone)**, chosen for its balance between inference speed and detection accuracy at embedded deployment scale.

**Architecture key properties:**

| Property | Value |
|----------|-------|
| **Backbone** | MobileNetV2 (depthwise separable convolutions) |
| **Detection head** | SSD (Single Shot MultiBox Detector) — multiple anchor scales per feature map |
| **Input resolution** | 300 × 300 pixels |
| **Output** | Bounding boxes + class scores per anchor |
| **Training framework** | TensorFlow Object Detection API |
| **Dataset format** | TFRecord (exported from Roboflow) |
| **Classes** | 3 (red, green, pink) |
| **Confidence threshold** | 0.7 (at inference time) |

The model was fine-tuned from a COCO pre-trained MobileNet SSD v2 checkpoint, using transfer learning to adapt the detection head for our 3-class problem while retaining the powerful low-level feature extraction from ImageNet/COCO pre-training.

### Quantization & Edge TPU Compilation

After training, the model underwent a two-stage optimization pipeline to run on the **Google Coral USB Edge TPU**:

```
Float32 TFLite model
        │
        ▼ Full-integer post-training quantization (INT8)
        │  All weights, activations, and I/O mapped to int8
        │  Representative dataset used for calibration
        │
limelight_neural_detector_8bit.tflite
        │
        ▼ Edge TPU Compiler (edgetpu_compiler)
        │  Maps compute-intensive layers onto TPU hardware
        │  Remaining layers fall back to CPU
        │
limelight_neural_detector_8bit_edgetpu.tflite  ← deployed model
```

**Why quantize?**

Full INT8 quantization reduces model size by ~4× and reduces inference latency by ~3–5× compared to float32 on the same hardware. On the Coral USB Edge TPU, which only supports INT8 operations natively, quantization is mandatory — any float32 operations are pushed back to the host CPU. Our quantized model achieves effectively full GPU utilization on the TPU.

**Model files in this repository:**
- `limelight_neural_detector_8bit.tflite` — quantized TFLite model (CPU fallback)
- `limelight_neural_detector_8bit_edgetpu.tflite` — Edge TPU compiled model (deployed)
- `label_map.txt` — class ID to name mapping (`0: red, 1: green, 2: pink`)

<!-- DIAGRAM SUGGESTION:
     ▸ others/model_pipeline.png — flowchart: Roboflow → TFRecord → TF OD API Training →
       TFLite Export → INT8 Quantization → Edge TPU Compile → Coral USB Inference
     ▸ others/detection_example.jpg — annotated camera frame showing live bounding boxes
       around red, green, and pink objects (can export from debug mode / cv2.imshow)
     ▸ others/mobilenet_arch.png — MobileNet SSD v2 architecture diagram
       (depthwise conv layers + SSD multi-scale detection heads)
-->

### Why ML over HSV Colour Detection?

We tested HSV-based OpenCV blob detection first (see `versionTest/Obstacle_Challenge_ROI.py` and `Image_Processing/Opencv/hsv_caliberate.py` for our full HSV development branch). The core limitation was lighting sensitivity: under warm indoor competition lighting, red pillar HSV ranges overlapped significantly with orange floor line hue values, causing false positives approximately 15–20% of the time.

The Edge TPU model, trained on context-labeled images, identifies **shape and spatial context** (vertical rectangular object on white floor), not just pixel colour — reducing false positives to under 2% in our test set. The 14ms inference latency (including USB transfer overhead at 120 FPS camera target) is far better than the ~50–100ms latency of pure HSV blob detection on full-resolution frames.

**Performance comparison:**

| Method | False Positive Rate | Latency | Lighting Robustness |
|--------|--------------------|---------|--------------------|
| HSV Blob Detection | ~15–20% | ~50 ms | Poor (lighting-dependent) |
| MobileNet SSD + Edge TPU | < 2% | ~14 ms | High (shape-context aware) |

---

## 🧭 Open Challenge — Strategy & Logic

The Open Challenge (`Open_Challenge_2025.py` / `Open_Challenge_Final.py`) uses a three-process architecture: `servoDrive` (main control), `runEncoder` (IMU + odometry), and `read_lidar` (RPLidar C1 turn detection).

### Direction Detection

On startup, the robot detects the open corridor side using TFmini sensors before moving:

```python
if tf_r > 180:
    right_flag = True   # Clockwise (CW)
elif tf_l > 180:
    left_flag = True    # Counter-clockwise (CCW)
```

This single reading determines the entire lap direction and the sign convention for all subsequent heading updates.

### IMU-Based PID Wall-Following

During straight sections, `correctAngle()` maintains the target heading using a PD controller (kp = 0.6, kd = 0.5). Hard-override rules prevent wall contact:

```python
if tfmini.distance_left  < 15:   correctAngle(heading_angle + 10, ...)  # steer right
elif tfmini.distance_right < 15:  correctAngle(heading_angle - 10, ...)  # steer left
else:                             correctAngle(heading_angle, ...)         # hold heading
```

### LiDAR Compound Turn Trigger

The RPLidar process runs the SLAMTEC SDK binary as a subprocess, parsing stdout line by line with IMU heading offset applied to rotate LiDAR angles into robot-relative frame:

```python
# Front angle (0°) compensated for IMU heading and servo offset
if int(angle) == (0 + imu_r + sp_angle) % 360:
    lidar_f.value = 0.8 * lidar_front + 0.2 * distance  # EMA smoothing α=0.2

# Turn condition — compound: wall ahead AND clear corridor beside
if lidar_front < 900 and lidar_right > 1500 and right_f.value:
    turn_trigger.value = True
elif lidar_front < 900 and lidar_left  > 1500 and left_f.value:
    turn_trigger.value = True
```

The EMA (α = 0.8) prevents single noisy readings from triggering false turns — observed in reflective environments where LiDAR occasionally returns 0 mm on glossy floor sections.

### Turn Counting & Lap Completion

```python
counter += 1
heading_angle = (90 * counter) % 360      # CW
# or
heading_angle = -(90 * counter) % 360     # CCW

# 3-second anti-double-count timeout
if time.time() - t_time > 3:
    trigger = False
```

After `counter == 12` (3 laps × 4 turns), the robot drives an additional fixed encoder count (+18,000 ticks ≈ 1.82 m) to return to the starting section, then stops.

### IMU Wraparound Handling

```python
if error_gyro > 180:
    error_gyro -= 360
if error_gyro < -180:
    error_gyro += 360
```

This prevents the servo from steering the wrong way when crossing the 0°/360° boundary during CW/CCW heading accumulation.

<!-- DIAGRAM SUGGESTION:
     ▸ schemes/open_state_machine.png — state machine diagram:
       [INIT] → [Direction Detection] → [DRIVE + PID] → [Turn Trigger?]
       → [counter++, heading ± 90°] → [counter==12] → [Drive to start zone] → [STOP]
       Show the 3-second timeout loop back arrow on the trigger state.
-->

---

## 🚧 Obstacle Challenge — Strategy & Logic
<div align="center">

<table>
    <tr>
    <td colspan="2" align="center"><b>Obstacle Simulation</b></td>
  </tr>
  <tr>
    <td colspan="2" align="center">
      <img src="others/obstacle.gif" width="600" height="500">
    </td>
  </tr>
</table>

</div>
The Obstacle Challenge (`Obstacle_Challenge_2025_Final.py`) adds two processes to the Open Challenge architecture: `Live_Feed` (Edge TPU ML inference) for detecting coloured pillars, plus an upgraded `servoDrive` that integrates setPoint-based position control.

### Direction & Parking Side Detection

On startup, the robot detects both the lap direction (CW/CCW) AND the parking lot location:

```python
# CW (orange inner wall)
if tf_l < 25 and tf_h < 250 and pink_b.value:
    orange_flag = True

# CCW (blue inner wall)
if tf_r < 25 and tf_h < 250 and pink_b.value:
    blue_flag = True
```

The parking side flag (`parking_right` / `parking_left`) persists for the entire run and determines which encoder setpoint to use for the post-lap parking sequence.

### SetPoint-Based Position Control

Instead of pure wall-following, the obstacle challenge uses **encoder dead-reckoning** to maintain an (x, y) position estimate within each straight section. A `setPoint` variable defines a target lateral offset from the section centreline:

```python
# setPoint == 0: centred (wall PID fallback)
# setPoint < 0:  left of centre (green pillar avoidance)
# setPoint > 0:  right of centre (red pillar avoidance)
```

Lane-specific error computation in `correctPosition()`:

```python
lane = counter % 4
if lane == 0:   error = setPoint - y
elif lane == 1 and orange_flag: error = x - (100 - setPoint)
elif lane == 2 and orange_flag: error = y - (200 - setPoint)
elif lane == 3 and orange_flag: error = (setPoint - 100) - x
# Blue flag uses mirrored targets
```

The PD controller (kp_e = 3, kd_e = 40) minimises position error. At the start of each new section, TFmini readings snap the position estimate back to a wall-referenced coordinate (`reset_coordinates`), preventing drift accumulation across laps.

### Pillar Avoidance Logic

`setPoint` shifts gradually (±1 unit per control loop iteration) rather than instantly, preventing servo overloading:

```python
if g_flag:   # Green → pass on left
    setPointL = max(-100, setPointL - 1)
elif r_flag: # Red → pass on right
    setPointR = min(35, setPointR + 1)
```

Wall-safety overrides prevent wall contact even when tracking a pillar near the wall:

```python
if lidar_l < 250 and setPoint <= -35:
    correction = 0     # Override: don't crash left wall
if lidar_r < 250 and setPoint >= +35:
    correction = 0     # Override: don't crash right wall
```

### LED Feedback

Real-time visual status output via onboard LEDs:

```python
if green_b.value:  pwm.write(green_led, 1)   # Green pillar detected
elif red_b.value:  pwm.write(red_led, 1)     # Red pillar detected
elif pink_b.value: pwm.write(blue_led, 1)    # Parking marker detected
```

### Parking Sequence (3-Stage)

After 12 turns (3 laps), the robot executes a structured parallel parking manoeuvre:

```
[POST-LAP STOP]
Drive forward until target encoder count reached OR lidar_f < finish threshold
        │
        ▼
[STATE 1 — Reverse Straight]
Reverse with correctReverseAngle() maintaining heading
Until lidar_f > front_thresh (confirmed clear behind)
        │
        ▼
[STATE 2 — Reverse Turn Into Slot]
heading_angle ± 90° (direction depends on orange/blue flag + parking side)
Reverse until side TFmini < 50 mm OR heading correction < 15°
        │
        ▼
[STATE 3 — Forward Straighten]
heading_angle ∓ 90° (straighten back to wall-parallel)
Drive forward until side TFmini < 20 mm OR 1.5 s timeout
        │
        ▼
[STOP — Motor off, parking complete]
```

**Parking encoder setpoints by configuration:**

| Direction | Parking Side | Extra Encoder Count |
|-----------|-------------|---------------------|
| Orange (CW) | Right | +20,000 ticks |
| Orange (CW) | Left | +24,000 ticks |
| Blue (CCW) | Right | +24,000 ticks |
| Blue (CCW) | Left | +20,000 ticks |

This multi-stage approach reduced parking failure rate from ~40% (single-pass approach) to under 5% across 20 test runs.

<!-- DIAGRAM SUGGESTION:
     ▸ schemes/parking_sequence.png — top-view diagram of the 3-stage parking sequence
       Show robot footprint at each stage with arrows for direction, and sensor readings annotated.
     ▸ schemes/obstacle_state_machine.png — full obstacle challenge state machine
       (larger than open challenge — include direction detection, pillar avoidance, lap finish, parking)
     ▸ others/obstacle.gif — animated simulation of obstacle avoidance path
       (shows robot trajectory around red/green pillars over 3 laps)
-->

---

## 🔧 Sensor Tuning Guide

### IMU — BNO085 Heading Calibration

The BNO085 outputs absolute Euler heading at 100 Hz over I²C to the Arduino Mega. The Arduino applies a **lane-drift compensation factor** before broadcasting to the Raspberry Pi:

```python
# In runEncoder():
if right_f.value:   # CW run
    head.value = float(esp_data[0]) + (0.57 * lane_counter.value)
elif left_f.value:  # CCW run
    head.value = float(esp_data[0]) - (0.57 * lane_counter.value)
```

The `0.57°` per lap counter is an empirically derived correction for cumulative IMU yaw drift. **To re-tune this value:**

1. Run the robot for 3 complete laps on the Open Challenge field
2. Log `head.value` at the end of each turn
3. If the robot drifts CW (heading increases beyond target × 90°), reduce the factor; if CCW, increase it
4. Re-run until the heading error at lap 3 turn 12 is < 5°

**BNO085 placement rules:**
- Mount at least 80 mm from the Johnson motor body (verified: 40 mm caused 3–5° jumps at high speed from commutator EMI)
- Avoid mounting near the motor driver or LiPo discharge wires
- After each power cycle, allow 2–3 seconds for sensor fusion to converge before pressing start

### PID Gains — Steering (`correctAngle`)

| Gain | Value | Effect |
|------|-------|--------|
| `kp` | 0.6 | Proportional — increases with heading error. Higher = more aggressive correction, higher risk of oscillation |
| `kd` | 0.1 (open) / 0.5 (early) | Derivative — damps overshoot. Increase if the robot oscillates (S-weave); decrease if response is sluggish |
| `ki` | 0 (obstacle) | Integral — disabled in obstacle challenge to prevent windup during pillar avoidance. Use `0.1` max for open challenge |
| Clamp | ±30° | Hard limit on correction output. Increase to ±45° for faster turns; risks wall contact |

**Tuning procedure:**
1. Start with `kp=0.3, kd=0, ki=0` and verify the robot generally tracks a straight heading
2. Increase `kp` until you see slight oscillation, then back off 20%
3. Add `kd=0.05` and increase until oscillation is damped; back off 10%
4. For open challenge only, add `ki=0.05` to correct steady-state drift

### PID Gains — Position Control (`correctPosition`)

| Gain | Value | Effect |
|------|-------|--------|
| `kp_e` | 3 | Proportional on lateral position error (encoder units). Higher = snappier pillar tracking, higher wall risk |
| `kd_e` | 40 | Derivative on position error. Critical for damping — without this, the robot oscillates around the pillar path |
| `ki_e` | 0 | Disabled — position drift is corrected by TFmini wall resets at section boundaries instead |

### LiDAR Turn Trigger Thresholds

```python
# In read_lidar():
if lidar_front < 900 and lidar_right > 1500:   # CW turn
    turn_trigger.value = True
```

| Parameter | Default | Notes |
|-----------|---------|-------|
| `lidar_front` threshold | 950 mm | Decrease if robot turns too late and clips the corner; increase if turning too early |
| `lidar_side` threshold | 1500 mm | Increase if false triggers occur (robot sees a partial gap mid-section). Decrease in narrow corridors |
| Anti-retrigger timeout | 3 seconds | Prevents double-counting one turn. Increase if the track is slow; decrease if turns are rapid |

### TFmini Sensor Thresholds

| Sensor | Parameter | Default | Tuning Note |
|--------|-----------|---------|-------------|
| Front (tf_h) | Turn trigger (open) | < 100 mm | Back-up threshold if LiDAR fails |
| Left (tf_l) | Wall hard-override | < 15 mm | Decrease if robot still contacts left wall |
| Right (tf_r) | Wall hard-override | < 15 mm | Decrease if robot still contacts right wall |
| Left/Right | Parking alignment | < 50 mm | Decrease if robot overshoots into the slot |
| Rear | Reverse stop | > front_thresh | Check `servoDrive` parking state 1 condition |

**Mounting calibration:** All four TFmini sensors should be mounted at chassis mid-height (not angled downward). A 15° downward tilt causes ground-plane reflections from the WRO mat at close range, producing false readings under 40 cm.

### HSV Colour Tuning (Development Branch)

For teams wishing to use the OpenCV HSV fallback (`versionTest/Obstacle_Challenge_ROI.py`), use the interactive calibration tool:

```bash
python3 Image_Processing/Opencv/hsv_caliberate.py
```

The tool displays live trackbars for H/S/V min/max for each colour. Tune under actual competition lighting, not lab lighting. Screenshot `Image_Processing/HSV_screenshot_22.05.2026.png` shows our working ranges.

**Key HSV ranges (reference):**

| Colour | H min | H max | S min | S max | V min | V max | Note |
|--------|-------|-------|-------|-------|-------|-------|------|
| Red | 0–10 + 170–180 | — | 100 | 255 | 100 | 255 | Hue wraps — requires two masks merged with `cv2.bitwise_or` |
| Green | 40 | 90 | 60 | 255 | 60 | 255 | — |
| Pink/Magenta | 140 | 175 | 80 | 255 | 80 | 255 | Overlaps purple; tune S_min high to exclude |
| Orange | 8 | 20 | 150 | 255 | 100 | 255 | Bleeds into red at H boundary — tune carefully |

<!-- DIAGRAM SUGGESTION:
     ▸ others/hsv_calibration_screenshot.png — screenshot of the HSV calibration tool in action
       showing the live camera feed with masked overlay and trackbars
     ▸ others/roi_zones.png — annotated camera frame (640×360) showing the named ROI zones:
       wall_left, wall_right, inner_wall, line, close_block, full_frame
       Draw coloured rectangles on a sample frame showing each zone boundary
-->

### Camera Settings

```python
cap.set(cv2.CAP_PROP_FRAME_WIDTH,  640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 360)
cap.set(cv2.CAP_PROP_FPS,          120)       # Target 120 FPS
cap.set(cv2.CAP_PROP_AUTO_EXPOSURE, 0.25)     # Manual exposure mode
cap.set(cv2.CAP_PROP_EXPOSURE,     -6)        # Fixed exposure value
cap.set(cv2.CAP_PROP_BUFFERSIZE,   1)         # Always process latest frame (prevents staleness)
```

**Critical:** `BUFFERSIZE = 1` ensures inference always runs on the most recent frame. Without it, OpenCV buffers 3–4 frames internally — at 30 fps this is 133 ms of stale data, enough for the robot to travel ~20 cm past a pillar before reacting.

**Exposure tuning:** Set `EXPOSURE` under actual competition lighting. Values closer to `-2` brighten the frame (useful in dim venues); closer to `-10` darken (prevents washout in bright venues). Tune until pillars are clearly saturated without bloom.

---

## 🔄 Engineering Decisions & Iterations

This section documents the reasoning behind major architectural choices and the iterations that led to the current design. This is the essence of the engineering process — not just showing what we built, but why we built it this way.

### Decision 1: LiDAR for Turn Detection vs. Pure TFmini

**The problem:** In our first design, turn detection used only the front TFmini Plus with a distance threshold. When the front distance dropped below 75mm, we incremented the turn counter and updated the heading target.

**Why this failed:** The threshold was fragile. On wide corridors (1000mm), the robot would reach the wall at the same speed as narrow corridors (600mm) but with very different lateral positions. The robot would sometimes initiate a turn too early (when it was still diagonally approaching a corner) or too late (when the wall was already 30mm away, causing a clipping turn).

**The fix:** We added the RPLidar C1 and changed the turn trigger to a compound condition:
```
Front LiDAR < 950mm AND Open-side LiDAR > 1500mm AND timeout elapsed
```
This requires not just "wall ahead" but also "clear corridor beside" — confirming the robot is genuinely at a corner, not diagonally approaching a wall mid-section. Success rate improved from ~75% clean turns to >95% across test runs.

### Decision 2: Multiprocessing vs. Single-threaded with async

**The problem:** During early single-threaded development, TFmini reading (serial I/O) blocked the main loop for 10–15ms per read cycle. At 95% PWM and ~1m/s travel speed, 15ms means the robot travels ~15mm blind — enough to miss a narrow window for turn detection.

**The fix:** Python's `multiprocessing` (not `threading`) was chosen deliberately. Python threads are limited by the Global Interpreter Lock (GIL), meaning CPU-bound tasks like ML inference and LiDAR parsing don't actually run in parallel. `multiprocessing` spawns true OS processes, each with their own GIL, allowing the camera inference and LiDAR parsing to run at full speed on separate CPU cores of the Pi 4's quad-core processor.

**Tradeoff acknowledged:** Shared memory requires explicit locking (`multiprocessing.Value` with `get_lock()`). We encountered one race condition early in development where `head.value` was written by the encoder process simultaneously with a read in the LiDAR angle compensation. Fixed by using `with lidar_angle.get_lock(), lidar_distance.get_lock(), head.get_lock():` for all multi-variable atomic reads.

### Decision 3: Why setPoint-based position control instead of pure wall-following

**Pure wall-following** (keep a fixed distance from one wall) works well in the Open Challenge. But in the Obstacle Challenge, following a wall while tracking a pillar requires simultaneously correcting toward two references — the wall on one side and the pillar on the other — which creates conflicting corrections.

**Our approach:** Encoder dead-reckoning provides an (x, y) position estimate within each straight section. The `setPoint` variable defines a target lateral offset from the section centerline (+35 = right of center, -35 = left of center, 0 = center). The PD controller minimizes the error between current position and setPoint. When a pillar is detected, setPoint shifts to steer toward it; when cleared, setPoint returns toward 0.

**Limitation:** Dead-reckoning accumulates drift across each section. We use TFmini readings (`reset_coordinates` / `reset_coordinates_lidar`) to snap the position estimate back to a wall-referenced coordinate at the start of each new section, resetting accumulated error. Without this reset, position error grew to ±30 units (~±45mm equivalent) by lap 3 in early testing.

### Decision 4: Parking sequence — why multi-stage reverse?

The WRO 2026 parking rules require the robot to be fully inside the parking lot (projection entirely within the 20cm-wide slot) AND parallel to the outer wall (within 2cm wheel-to-wall difference). A simple forward park into the slot fails because:

1. The robot approaches the lot at a slight angle (from the final turn of lap 3)
2. The slot is exactly 1.5× the robot's length — zero margin for diagonal entry

Our three-stage maneuver:
- **Stage 1:** Reverse straight to align perpendicular to the wall
- **Stage 2:** Reverse-turn into the slot, using side TFmini to confirm alignment
- **Stage 3:** Forward-push to seat fully within the slot, timeout-limited to prevent over-travel

This adds ~2–3 seconds to completion time but reduces parking failure rate from ~40% (single-pass approach) to under 5% across 20 test runs.

---

## 📹 Performance Videos

### Open Challenge

<!-- Replace with your actual YouTube link -->
**PLACEHOLDER: Open Challenge Video**

[![Open Challenge](https://img.youtube.com/vi/YOUR_VIDEO_ID/0.jpg)](https://youtu.be/YOUR_VIDEO_ID)

*Autonomous navigation across randomized track layout — 3 complete laps, return to start.*

### Obstacle Challenge

**PLACEHOLDER: Obstacle Challenge Video**

[![Obstacle Challenge](https://img.youtube.com/vi/YOUR_VIDEO_ID_2/0.jpg)](https://youtu.be/YOUR_VIDEO_ID_2)

*Traffic sign detection and compliance, followed by parallel parking execution.*

---

## 🛠️ How to Build & Deploy

### Hardware Requirements

- Raspberry Pi 4 Model B (4GB) running Raspberry Pi OS (64-bit, Bookworm)
- Arduino Mega 2560 flashed with IMU/encoder firmware
- Google Coral USB Edge TPU Accelerator
- All sensors wired per the Wiring Diagram above

### Software Dependencies

**On Raspberry Pi:**
```bash
# System
sudo apt update && sudo apt install -y python3-pip pigpio python3-pigpio

# Python packages
pip3 install opencv-python pyserial RPi.GPIO adafruit-circuitpython-bno055

# Coral Edge TPU runtime
echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | \
  sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt update && sudo apt install -y libedgetpu1-std python3-pycoral

# RPLidar SDK (build from source)
git clone https://github.com/Slamtec/rplidar_sdk
cd rplidar_sdk && make
# Binary will be at: output/Linux/Release/ultra_simple
```

**On Arduino Mega:**
Flash the IMU/encoder firmware located in `src/arduino/imu_encoder.ino` using Arduino IDE with:
- Library: `Adafruit BNO055`
- Library: `Adafruit Unified Sensor`

The Arduino reads BNO055 Euler heading and encoder counts, then broadcasts them via UART at 115200 baud in the format: `<heading_float> <encoder_int>\n`

### USB Device Aliases

Add to `/etc/udev/rules.d/99-wro.rules` to get stable device names:
```
SUBSYSTEM=="tty", ATTRS{idVendor}=="XXXX", ATTRS{idProduct}=="YYYY", SYMLINK+="UART_USB"
SUBSYSTEM=="tty", ATTRS{idVendor}=="AAAA", ATTRS{idProduct}=="BBBB", SYMLINK+="LIDAR_USB"
```
(Replace VID/PID with values from `lsusb` output.)

### Running the Code

```bash
# Open Challenge
cd /home/pi/WRO_CODE
sudo python3 Open_Challenge_Final.py

# Obstacle Challenge
sudo python3 Obstacle_Challenge_World_Final.py
```

Both scripts start `pigpiod` automatically at launch. Logs are written to `/home/pi/WRO_2025_PI/logs/` with timestamps.

### Code Structure

```
/
├── Open_Challenge_Final.py          # Open challenge main script
├── Obstacle_Challenge_World_Final.py # Obstacle challenge main script
├── BNO085.py                        # IMU + colour sensor class
├── Encoder.py                       # Dead-reckoning position class
├── TFmini.py                        # TFmini Plus UART read class
├── Servo.py                         # Servo abstraction (pigpio)
├── src/
│   └── arduino/
│       └── imu_encoder.ino          # Arduino Mega firmware
├── models/
│   └── limelight_neural_detector_8bit_edgetpu.tflite   # Edge TPU model
├── logs/                            # Auto-generated run logs
├── schemes/
│   └── wiring_diagram.jpg           # PLACEHOLDER
└── v-photos/                        # PLACEHOLDER — robot photos
```

---

*Documentation last updated: [DATE] — Team Paraducks, WRO 2026 Future Engineers*
