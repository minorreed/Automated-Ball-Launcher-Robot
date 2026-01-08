SCUTTLE Color Tracking and Firing Robot with Hallway Navigation

Overview
This project controls a SCUTTLE mobile robot that can navigate a hallway using LIDAR, detect door-like openings, turn into a room, and then use a USB camera to track colored targets. If the aligned target is red, the robot spins up flywheels and actuates a servo to fire a ball. If the target is green, it does not shoot.

What this robot does

1) Hallway navigation with LIDAR
- Follows a hallway while staying centered between left and right walls
- Stops when an obstacle is detected in front, then resumes when clear
- Detects a doorway on the left or right by watching for a side distance jump relative to a slowly-updated baseline
- Drives forward for a short time after detecting the doorway, then turns into it
- Calls the color tracking and firing routine while facing the doorway
- Turns back to the hallway heading and continues navigating

2) Color tracking and firing logic
- Uses OpenCV to threshold red and green in HSV space
- Finds the largest contour of the combined red or green mask
- Uses target angle and apparent width to center on the object and approach it
- When centered and at the desired range, checks whether red or green pixels dominate inside the bounding box
- If green is dominant, shooter stays off
- If red is dominant, spins flywheel motors and fires one ball using a servo

Repository contents
- LidarCode.py
  Hallway navigation state machine using LIDAR sectors, wall centering control, doorway detection, and turn-in/return behavior. This script calls the shooter routine when it reaches ENGAGE_ROOM.
- TrackingAndFiringCode.py
  Camera-based color tracking with OpenCV plus shooter motor PWM and servo firing control. Exposes an engage() function that runs the tracking loop.

Hardware
- SCUTTLE robot base with differential drive
- LIDAR supported by the L1_lidar interface used in your course stack
- USB camera or MJPG stream input
- Two flywheel motors driven via PWM pins
- One servo to push the projectile into the flywheels

Doorway detection
- Baselines for left and right wall distance are computed with an exponential moving average
- A doorway is detected when the measured side distance rises above baseline by a threshold and is beyond a minimum range
- After first detection, the robot continues forward for a fixed time before turning

Alignment and shooting rules
- The robot attempts to center the target horizontally in the camera frame
- It uses the bounding box width as a distance proxy to decide when it is close enough
- Only when aligned does it decide color dominance within the bounding box region
- Green means do not shoot
- Red means spin up the shooter and actuate the servo to fire one ball

Tuning parameters worth adjusting
Navigation script
- OBSTACLE_STOP_DIST and OBSTACLE_RESUME_DIST for obstacle handling
- KP_WALL and MAX_TURN_RATE for hallway centering
- DOOR_DELTA, DOOR_MIN_RANGE, and DOOR_ADVANCE_TIME for doorway detection timing
- TURN_RATE and DOOR_TURN_DURATION for turn-in and return

Tracking and firing script
- HSV thresholds for red and green
- target_width, angle_margin, and width_margin for alignment behavior
- COLOR_AREA_MIN for robust color classification
- Servo load and fire positions, and flywheel spin-up time
