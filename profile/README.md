# KUKA ROS 2 drivers

Reliable, real-time-capable ROS 2 drivers for KUKA robots running iiQKA.OS2.

This organization provides the official ROS 2 driver for KUKA robots running
iiQKA.OS2. The driver hides the underlying startup procedure and communication
technology behind a `ros2_control` API.

## Officially supported platform

- **iiQKA.OS2**: industrial robots using RSI 6.0.0 or newer

## Community support for other KUKA operating systems

[Kroshu](https://github.com/kroshu) provides community ROS 2 support for
KUKA robots running the other KUKA operating systems, including KSS, Sunrise,
and iiQKA.

## Documentation

- [Driver project overview](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/0_Overview.md)
- [External control setup for iiQKA.OS2](https://github.com/kuka-ros/kuka_external_control_sdk/blob/master/kuka_external_control_sdk_common/doc/iiqka_os2_setup.md)
- [iiQKA.OS2 driver (RSI)](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/1_RSI.md)
- [KUKA-specific controllers](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/2_Controllers.md)
- [Setting up the real-time patch](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/3_Realtime.md)


## Repository map

- [`kuka_drivers`](https://github.com/kuka-ros/kuka_drivers): common drivers,
  interfaces, controllers, and the RSI simulator
- [`kuka_robot_descriptions`](https://github.com/kuka-ros/kuka_robot_descriptions):
  robot models, meshes, URDF/Xacro descriptions, and MoveIt support
- [`kuka_external_control_sdk`](https://github.com/kuka-ros/kuka_external_control_sdk):
	C++ SDK for client applications and controller configuration for iiQKA.OS2

## Getting started

1. Install the ROS 2 dependencies and build the required packages in a ROS 2
	workspace.
2. Select a robot model and the matching robot description package.
3. Follow the platform-specific setup guide:
	- [RSI setup for iiQKA.OS2](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/1_RSI.md)
	- [Controller documentation](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/2_Controllers.md)
4. Configure and activate `robot_manager` only after the robot controller is
	ready for external control.

For real-time Linux setup, use the
[PREEMPT_RT guide](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/3_Realtime.md).

## Safety

External control can release robot brakes and move hardware. Test with the
robot in a safe state, use the correct operating mode and safety configuration,
and keep the Teach Pendant emergency stop available. In an emergency, use the
robot controller's safety stop rather than relying on a non-real-time ROS 2
lifecycle transition.

## Contributing

Please read the repository's
[contribution guidelines](https://github.com/kuka-ros/kuka_drivers/blob/master/CONTRIBUTING.md)
before opening an issue or pull request.
