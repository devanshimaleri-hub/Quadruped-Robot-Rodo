# Inverse Kinematics

## Overview

One of the main concepts I explored while building this quadruped was **Inverse Kinematics (IK)**.

Inverse kinematics determines the joint angles required to place a robot's foot at a desired position.

Instead of directly specifying every servo angle:

```text
Servo 1 → 80°
Servo 2 → 110°
Servo 3 → 65°
```

the desired foot position can be specified as:

```text
Foot → (x, y, z)
```

and the required joint angles can then be calculated.

The basic process is:

```text
Desired Foot Position
        ↓
Coordinate Transformation
        ↓
Inverse Kinematics
        ↓
Joint Angles
        ↓
Servo Calibration / Offsets
        ↓
Servo Commands
        ↓
Physical Leg Movement
```

---

## Leg Geometry

A simplified leg can be represented as a combination of three joints and links:

```text
                 Foot (x,y,z)
                      ●
                     /
                    / L3
                   /
                  ●
                 /
              L2/
               /
              ●
              │
              │ L1
              │
              ●
           Body Joint
```

The main parameters are:

- `L1` — first link / lateral offset
- `L2` — femur length
- `L3` — tibia length
- `(x, y, z)` — desired foot position

The exact joint orientation depends on the mechanical design and coordinate system of the robot.

---

## 1. Coxa / First Joint

The first joint determines the direction of the leg in the horizontal plane.

For a target position `(x, y, z)`:

```text
θ1 = atan2(y, x)
```

The horizontal distance from the body to the target is:

```text
R = √(x² + y²)
```

After accounting for the first link:

```text
r = R - L1
```

---

## 2. Distance to the Foot

The remaining two links can be treated approximately as a two-link planar mechanism.

The distance from the second joint to the target foot position is:

```text
D = √(r² + z²)
```

The target must lie within the physical workspace of the leg:

```text
|L2 - L3| ≤ D ≤ L2 + L3
```

If this condition is not satisfied, the desired position cannot be reached by the leg.

---

## 3. Femur Joint

Using the cosine rule:

```text
cos(A) = (L2² + D² - L3²) / (2 × L2 × D)
```

Therefore:

```text
A = acos((L2² + D² - L3²) / (2 × L2 × D))
```

The angle from the horizontal direction to the target is:

```text
B = atan2(z, r)
```

The required femur angle is calculated from these angles according to the coordinate convention used by the robot.

---

## 4. Tibia Joint

The tibia angle can also be calculated using the cosine rule:

```text
cos(C) = (L2² + L3² - D²) / (2 × L2 × L3)
```

Therefore:

```text
C = acos((L2² + L3² - D²) / (2 × L2 × L3))
```

The resulting angle is then converted into the corresponding servo position.

---

## Servo Calibration

The mathematical angles cannot always be sent directly to the servos.

The physical mounting position of each servo introduces an offset.

A simplified relationship is:

```text
Servo Angle = IK Angle + Servo Offset
```

Depending on the orientation of a particular servo, the direction may also need to be inverted:

```text
Servo Angle = Servo Offset - IK Angle
```

Therefore, each joint needs to be calibrated before the calculated angles can accurately represent the desired physical position.

---

## Real-World Considerations

The mathematical model assumes ideal geometry, while the physical robot contains several sources of error:

- 3D-printing tolerances
- Servo mounting errors
- Servo backlash
- Linkage flexibility
- Mechanical friction
- Limited servo resolution
- Differences between individual servos
- Mechanical joint alignment

Because of this, a mathematically correct solution may still require physical calibration.

The practical process becomes:

```text
Mathematical Model
        ↓
Calculate Joint Angles
        ↓
Apply Servo Offsets
        ↓
Test on Robot
        ↓
Measure / Observe Error
        ↓
Calibrate
        ↓
Repeat
```

---

## What I Learned

Before studying inverse kinematics, robot movement could be thought of simply as commanding individual servos:

```text
Move Servo 1
Move Servo 2
Move Servo 3
```

Inverse kinematics provides a higher-level way of thinking:

```text
Where should the foot be?
          ↓
What joint angles will place it there?
```

This makes the system much easier to extend toward more advanced robotic motion.

The same concept can be used for:

- Foot trajectory generation
- Gait generation
- Body movement
- Terrain adaptation
- Autonomous walking
- Dynamic legged motion

---

## Key Takeaway

This project helped me understand the connection between **mathematics, mechanical geometry and physical robot movement**.

The important lesson was that inverse kinematics is not only about solving equations. The mathematical model has to match the actual physical robot, including its dimensions, servo orientations, offsets and mechanical limitations.

The overall concept can be summarized as:

```text
        ROBOT GEOMETRY
               ↓
       Desired Foot Position
               ↓
       Inverse Kinematics
               ↓
          Joint Angles
               ↓
       Servo Calibration
               ↓
        Physical Movement
```

This was one of the most important concepts I learned while developing and testing this quadruped robot.
