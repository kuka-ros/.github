# KUKA ROS 2 drivers

Reliable, real-time-capable ROS 2 drivers for KUKA robots running iiQKA.OS2.

This organization provides the official ROS 2 driver for KUKA robots running
iiQKA.OS2. The driver hides the underlying startup procedure and communication
technology behind a `ros2_control` API.

## Officially supported platform

- **iiQKA.OS2**: industrial robots using RSI 6.0.0 or newer

## Unofficial support for other KUKA operating systems

[Kroshu](https://github.com/kroshu) provides unofficial ROS 2 support for
KUKA robots running the other KUKA operating systems, including KSS, Sunrise,
and iiQKA.

## Documentation

The complete driver documentation is maintained in the [`kuka_drivers`
package](https://github.com/kuka-ros/kuka_drivers/tree/master/kuka_drivers/doc):

- [KSS and iiQKA.OS2 drivers (RSI)](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/1_RSI.md)
- [KUKA-specific controllers](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/2_Controllers.md)
- [Setting up the real-time patch](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/3_Realtime.md)
- [Driver project overview](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/Home.md)

## Common driver interface

### Real-time control

All drivers use [`ros2_control`](https://control.ros.org/master/doc/ros2_control/doc/index.html)
for cyclic control. The robot controller owns the control-cycle timing, so
the hardware `read()` call waits for the next robot update. For this reason
the drivers use a custom control node rather than the standard timed
`ros2_control_node`.

The public control modes are:

| Control mode | Command interfaces |
| --- | --- |
| Joint position | `position` |
| Joint impedance | `position`, `stiffness`, `damping` |
| Joint velocity | `velocity` |
| Joint torque | `effort` |
| Cartesian position | `cart_position` |
| Cartesian impedance | `cart_position`, `cart_stiffness`, `cart_damping` |
| Cartesian velocity | `cart_velocity` |
| Wrench | `wrench` |

The exact capabilities depend on the KUKA controller platform. See the
[supported-features matrix](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/Home.md#supported-features)
before selecting a control mode.

### Lifecycle startup

The driver uses a `robot_manager` lifecycle node so external control cannot
start until the hardware and required controllers are ready:

1. Start the robot-specific launch file.
2. Configure the driver:

	```bash
	ros2 lifecycle set robot_manager configure
	```

3. Activate the driver:

	```bash
	ros2 lifecycle set robot_manager activate
	```

The lifecycle states are:

- **unconfigured**: components are running, but no robot connection is needed
- **configured**: parameters and configuration controllers are ready
- **active**: external control and cyclic real-time communication are running

To stop external control:

```bash
ros2 lifecycle set robot_manager deactivate
```

## Repository map

- [`kuka_drivers`](https://github.com/kuka-ros/kuka_drivers): common drivers,
  interfaces, controllers, and the RSI simulator
- [`kuka_robot_descriptions`](https://github.com/kuka-ros/kuka_robot_descriptions):
  robot models, meshes, URDF/Xacro descriptions, and MoveIt support
- [`kuka_external_control_sdk`](https://github.com/kuka-ros/kuka_external_control_sdk):
  controller-side SDK integration for iiQKA and iiQKA.OS2
- [`examples`](https://github.com/kuka-ros/examples): MoveIt and multi-robot
  examples

## Multi-robot operation

Since ROS 2 Jazzy, asynchronous `ros2_control` hardware interfaces allow
multiple robots to share one controller manager. Each asynchronous hardware
interface can run in its own execution context while the main thread manages
controller updates.

The hardware descriptions expose these timing parameters:

- `async_thread_priority` (default `69`)
- `async_affinity` (default empty, allowing any CPU core)

The drivers also expose the internal
`runtime_config/interpolation_count` interface. The event broadcaster updates
it every controller cycle, and hardware interfaces use it to detect a missed
or duplicated controller update before writing commands. For the detailed
dual-arm timing scenarios and launch requirements, see the
[multi-robot documentation](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/Home.md#multi-robot-scenario).

## MoveIt integration

`ros2_control` integrates with MoveIt through the
`joint_trajectory_controller`. The recommended examples are in the
[`examples`](https://github.com/kuka-ros/examples/tree/master/moveit_example)
repository, including basic planning, collision avoidance, constrained
planning, and depalletizing.

When planning in impedance mode, start trajectories from the commanded joint
positions rather than the measured positions. The impedance controller
publishes those values on:

```text
/joint_group_impedance_controller/commanded_positions
```

For trajectory interpolation without MoveIt, configure the trajectory
controller with:

```yaml
open_loop_control: true
```

## Getting started

1. Install the ROS 2 dependencies and build the required packages in a ROS 2
	workspace.
2. Select a robot model and the matching robot description package.
3. Follow the platform-specific setup guide:
	- [RSI setup for KSS and iiQKA.OS2](https://github.com/kuka-ros/kuka_drivers/blob/master/kuka_drivers/doc/1_RSI.md)
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
