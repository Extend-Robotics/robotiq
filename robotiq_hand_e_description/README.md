# robotiq_hand_e_description (ROS Noetic)

URDF/xacro description of the Robotiq Hand-E gripper for ROS 1 Noetic, ported from
[robotiq/robotiq-ros `grippers/robotiq_description`](https://github.com/robotiq/robotiq-ros/tree/main/grippers/robotiq_description)
(v1.3.0, commit `91ca17b`). Both strokes are supported: the standard 50 mm Hand-E and the 100 mm version.

## Build

```bash
cd ~/catkin_ws/src
cp -r /path/to/robotiq_hand_e_description .
cd ~/catkin_ws
rosdep install --from-paths src --ignore-src -y
catkin_make        # or: catkin build
source devel/setup.bash
```

## View in RViz

```bash
roslaunch robotiq_hand_e_description display.launch                # 50 mm stroke
roslaunch robotiq_hand_e_description display.launch stroke:=0.100  # 100 mm stroke
```

Use the `hande_left_finger_joint` slider; the right finger follows through the mimic joint.

## Generate a plain URDF

```bash
rosrun xacro xacro $(rospack find robotiq_hand_e_description)/urdf/robotiq_hand_e.urdf.xacro \
  stroke:=0.050 > hand_e.urdf
```

## Attach to your own robot

```xml
<xacro:include filename="$(find robotiq_hand_e_description)/urdf/robotiq_hand_e_macro.urdf.xacro"/>

<xacro:robotiq_hand_e prefix="" parent="tool0" stroke="0.050">
  <origin xyz="0 0 0" rpy="0 0 0"/>
</xacro:robotiq_hand_e>
```

| Macro parameter    | Default                                   | Meaning                                              |
|--------------------|-------------------------------------------|------------------------------------------------------|
| `parent`           | (required)                                | Link the gripper is mounted on                       |
| `origin` (block)   | (required)                                | Pose of `hand_e_base_link` in `parent`               |
| `prefix`           | `''`                                      | Prefix for all link/joint names                      |
| `stroke`           | `0.050`                                   | Total opening in m, `0.050` or `0.100`               |
| `tcp_z_offset`     | `0.1325`                                  | TCP distance from base along z                       |
| `add_transmission` | `false`                                   | Add a ros_control transmission on the driven finger  |
| `hw_interface`     | `hardware_interface/PositionJointInterface` | Interface used by that transmission                |

## Frames and joints

`hand_e_base_link`, `hande_left_finger`, `hande_right_finger`, `tcp_link`.
`hande_left_finger_joint` (prismatic, driven, 0 = open, stroke/2 = closed),
`hande_right_finger_joint` (mimics the left), plus fixed `robotiq_hande_base_joint` and `tcp_link_joint`.
Names match upstream so existing configs carry over.

## Decision log

| Version | Change                                                                                                   |
|---------|----------------------------------------------------------------------------------------------------------|
| 1.0.0   | Initial port. ament_cmake → catkin; ROS 2 Python launch → roslaunch XML; rviz2 config → RViz 1 config.   |
| 1.0.0   | Package renamed `robotiq_hand_e_description` so it can coexist with other Robotiq description packages. |
| 1.0.0   | Macro renamed `robotiq_gripper` → `robotiq_hand_e`; only Hand-E 50/100 mm meshes kept (Hand-E C10, 2F-85, 2F-140 dropped). |
| 1.0.0   | ros2_control block, Isaac/topic-based sim args and serial driver params removed (ROS 2 only); optional ROS 1 `<transmission>` added instead. |
| 1.0.0   | `xacro.fatal` (not in Noetic xacro) replaced by an undefined-name error for invalid strokes.            |
| 1.0.0   | Placeholder finger inertials added (upstream has none) so Gazebo/KDL accept the moving links. Not measured values. |

## Notes

- Gazebo Classic does not enforce `<mimic>` by itself; add a mimic-joint plugin
  (e.g. `roboticsgroup_upatras_gazebo_plugins`) if you simulate the gripper.
- Driver/hardware interface is out of scope here; this package is description only.

## Licence

BSD-3-Clause, © Robotiq (see `LICENSE`). Meshes and kinematics are Robotiq's.
