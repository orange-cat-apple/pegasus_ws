# Project Pegasus

Welcome to the Project Pegasus repository. This repository contains the ROS 2 software stack for a fully autonomous search-and-rescue quadrotor targeted for the NIDAR 2026-27 Competition[cite: 1].

## Reference Documentation

For deep dives into the system design, refer to the official specification documents included in this repository:
* [Pegasus_Software_Architecture_Spec.pdf](https://drive.google.com/file/d/1TzTBnPas7WcDgcZ-wT_qmfD6SKu9Gi7w/view?usp=drive_link)

* [Pegasus_Hardware_Architecture_Spec.pdf](./Pegasus_Hardware_Architecture_Spec.pdf)

## Repository Structure

The workspace is organized into isolated layers to ensure stable interfaces and modular development[cite: 1].

* `scripts/`: Contains system utilities like the `bootstrap_env.sh` script for environment setup.
* `src/pegasus_sensors/`: Layer 1 packages for hardware interfacing and sensor ingestion (LIDAR, cameras, ToF, Optical Flow)[cite: 1].
* `src/pegasus_perception/`: Layer 2 packages for vision and mapping (SLAM, YOLO26, monocular depth, panorama stitching)[cite: 1].
* `src/pegasus_localization/`: Layer 2 EKF fusion packages (IMU, Optical Flow, ToF)[cite: 1].
* `src/pegasus_navigation/`: Layer 2 packages for costmaps, path planning, and trajectory control (Nav2)[cite: 1].
* `src/pegasus_autonomy/`: Layer 3 packages containing the core mission state machine[cite: 1].
* `src/pegasus_sim/`: Packages for Gazebo Harmonic and ArduPilot SITL configurations[cite: 1].
* `src/pegasus_msgs/`: Standardized custom ROS 2 message interfaces used across all layers[cite: 1].
* `src/pegasus_bringup/`: Top-level launch files tying the complete system together[cite: 1].
* `src/pegasus_bringup_tests/`: Integration tests for the full pipeline.

## Contribution Rules

All code must pass strict acceptance criteria before merging into the main branch[cite: 1].

* **Pre-commit Linting:** All commits must pass formatting via `black`, `isort`, `flake8`, and `ament_lint_common`[cite: 1].
* **Automated Testing:** Pull requests must pass `colcon test` without breaking existing coverage[cite: 1].
* **SITL Drill Gate:** Modifications to autonomy, navigation, or localization require passing a 3-minute headless Gazebo + ArduPilot traverse and Return-to-Launch CI drill[cite: 1].
* **Interface Stability:** Changing custom message definitions in `pegasus_msgs` requires explicit sign-off from all developers utilizing that message[cite: 1].
* **Peer Review:** Every pull request requires two approving reviewers[cite: 1].
* **Merge Strategy:** Only squash-merges are permitted[cite: 1].

## How to Contribute (Step-by-Step)

1. **Sync and Branch:** Pull the latest main branch and create a new feature branch using the standard format `dev{N}/<short-feature-slug>`[cite: 1].
2. **Develop:** Implement your feature within the appropriate isolated `src/` package.
3. **Test Locally:** Run `colcon build` and `colcon test` in your local workspace to verify functionality.
4. **Format Code:** Run `pre-commit run --all-files` to ensure your code matches project styling standards.
5. **Commit and Push:** Stage your files, commit with a clear message, and push your branch to GitHub.
6. **Open a PR:** Open a Pull Request targeting the `main` branch.
7. **Pass Gates:** Wait for the GitHub Actions CI pipeline to complete and resolve any reviewer feedback.

# Pegasus Messages (`pegasus_msgs`)

This package contains the centralized custom ROS 2 interface definitions for Project Pegasus. It dictates the standardized data structures used for cross-layer communication between the hardware, perception, and autonomy nodes.

## Defined Interfaces

### 1. OpticalFlow.msg
Published by the Layer 1 sensors (`pmw3901_node`) to report planar velocity and sensor confidence.
* `std_msgs/Header header`: Timestamp and coordinate frame ID.
* `float32 velocity_x`: Pixel velocity in the X-axis.
* `float32 velocity_y`: Pixel velocity in the Y-axis.
* `uint8 surface_quality`: Ranging from 0 to 255, indicating the reliability of the flow measurement.

### 2. Survivor3DArray.msg
Published by the Layer 2 perception stack (`bbox_to_3d_projector`) to report localized targets.
* `std_msgs/Header header`: Timestamp and coordinate frame ID.
* `geometry_msgs/Point[] detections`: An array of 3D spatial coordinates indicating the metric location of identified targets.

## Compilation
This package uses the `rosidl_default_generators` pipeline. The C++ and Python headers are automatically generated during the colcon build phase.

## Modification Policy
Any structural changes to these interfaces require explicit architectural review and sign-off. Modifying these data types will directly impact the downstream Layer 2 EKF fusion and Layer 3 state machine dependencies.
EOF
git add src/pegasus_msgs/README.md
git commit -m "docs: add README for custom ROS 2 interfaces"
git push -u origin doc/pegasus-msgs-readme
