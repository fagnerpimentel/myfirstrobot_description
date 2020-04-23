# myfirstrobot_description
In this class, it is presented how to built a simple robot description (visual, kinematics and dynamic properties) used to control a robot using [ROS](https://www.ros.org/) with [Gazebo](http://gazebosim.org/) (for simulation) or even in a real robot.

This class is based in the [urdf tutorials](https://wiki.ros.org/urdf/Tutorials) and uses the [R2-D2](https://en.wikipedia.org/wiki/R2-D2) robot as a model.

<div align="center">
<img src="https://upload.wikimedia.org/wikipedia/en/thumb/3/39/R2-D2_Droid.png/250px-R2-D2_Droid.png" >
<p>R2-D2</p>
</div>

Three different XML based languages are used in a complementary way:
* **SDF**: [Simulation Description Format](http://sdformat.org/)
* **URDF**: [Unified Robot Description Format](http://wiki.ros.org/urdf)
* **XACRO**: [XML Macros](https://wiki.ros.org/xacro)

---

## Desirable prior knowledge:
* [ROS basic understanding](http://wiki.ros.org/ROS/Tutorials)
* [XML basic understanding](https://www.w3schools.com/xml/)

---

<!-- ## Dependencies:
* ROS
* Gazebo
* rviz
* liburdfdom-tools -->

<!-- ## File structure
```
    |-- myfirstrobot_description
        |-- config
		    |-- myfirstrobot.rviz
	    |-- launch
		    |-- load_description.launch
	    |-- urdf
		    |-- myfirstrobot_visual.urdf
		    |-- myfirstrobot_move.urdf
		    |-- myfirstrobot.urdf.xacro
	    |-- CMakeLists.txt
	    |-- package.xml
        |-- README.md
``` -->



### Part 1: Links (visual) and joints

In this part it is initiated the construction of the [robot visual model](https://wiki.ros.org/urdf/Tutorials/Building%20a%20Visual%20Robot%20Model%20with%20URDF%20from%20Scratch) and how to [move some robot parts](https://wiki.ros.org/urdf/Tutorials/Building%20a%20Movable%20Robot%20Model%20with%20URDF). For this, is used the [robot](http://wiki.ros.org/urdf/XML/robot) tag form URDF language and the [link](http://sdformat.org/spec?ver=1.6&elem=link) and [joint](http://sdformat.org/spec?ver=1.6&elem=joint) tags from SDF language.

* **robot**: The root element in a robot description.
* **link**: The link element describes a rigid body with an inertia, visual features, and collision properties.
* **joint**: The joint element describes the kinematics and dynamics of the joint and also specifies the safety limits of the joint.


<!--

```check_urdf```

```urdf_to_graphiz``` -->

Test your robot:
```
roslaunch myfirstrobot_description load_description.launch model:=myfirstrobot.urdf enable_rviz:=true enable_jspublisher:=true
```

### Part 2: Xacro
In this part it is presented how to [organize and clean up the urdf code](https://wiki.ros.org/urdf/Tutorials/Using%20Xacro%20to%20Clean%20Up%20a%20URDF%20File) using the [XACRO](https://wiki.ros.org/xacro) language.

#### Xacro features used in this class:
There are a lot of features in the Xacro language. In this class is presented only the follows ones:
* **Property**: Values that can be inserted anywhere into the XML document. E.g.:
  * Definition:
  ```
  <xacro:property name="the_radius" value="2.1" />
  <xacro:property name="the_length" value="4.5" />
  ```
  * Use:
  ```
  <geometry type="cylinder" radius="${the_radius}" length="${the_length}" />
  ```
* **Property Blocks**: Property blocks are named snippets of XML that can be inserted anywhere that XML is allowed. E.g.:
  * Definition:
  ```
  <xacro:property name="front_left_origin">
      <origin xyz="0.3 0 0" rpy="0 0 0" />
  </xacro:property>
  ```
  * Use:
  ```
  <wheel name="front_left_wheel">
      <xacro:insert_block name="front_left_origin" />
  </wheel>
  ```
* **Math expressions**: You can also write simple math expressions. E.g.:
  * Definition:
  ```
  <xacro:property name="radius" value="4.3" />
  ```
  * Use:
  ```
  <circle diameter="${2 * radius}" />
  ```
* **Macros**: The main feature of xacro is its support for macros. E.g.:
  * Definition:
  ```
  <xacro:macro name="caster" params="suffix *origin">
      <link name="caster_${suffix}">
        <xacro:insert_block name="origin" />
      </link>
      <joint name="caster_${suffix}_joint">
        <axis xyz="0 0 1" />
      </joint>
    </xacro:macro>
  ```
  * Use:
  ```
  <xacro:caster suffix="front_left">
      <pose xyz="0 1 0" rpy="0 0 0" />
  </xacro:caster>
  ```
* **Includes**: You can include other xacro files. E.g.:
  * Use:
  ```
  <xacro:include filename="$(find package)/other_file.xacro" />
  ```

<!-- * Conditional Blocks -->
<!-- * Arguments -->
<!-- * YAML support -->
<!-- * Elements and Attributes -->

Test your robot:
```
roslaunch myfirstrobot_description load_description.launch model:=myfirstrobot.urdf.xacro enable_gazebo:=true enable_rviz:=true
```

### Part 3: Links (collision and inertial)

In this part it is presented the increment of the robot description [links](http://sdformat.org/spec?ver=1.6&elem=link) with [collision and inertial properties](https://wiki.ros.org/urdf/Tutorials/Adding%20Physical%20and%20Collision%20Properties%20to%20a%20URDF%20Model).

* **collision**: The collision properties of a link.
  * **origin**: The reference frame of the collision element.
  * **geometry**: The shape of the visual object.
    * **box**
    * **cylinder**
    * **sphere**
* **inertial**: The inertial properties of the link.
  * **mass**: The mass of the link
  * **inertia**: A 3x3 rotational inertia matrix:

    ```
    ixx ixy ixz
    --- iyy iyz
    --- --- izz
    ```

To determine the values of the rotational inertia matrix, it is used the formulas available in [List of moments of inertia](https://en.wikipedia.org/wiki/List_of_moments_of_inertia#List_of_3D_inertia_tensors):

* **For box inertia**:<br>
![box_inertia](https://wikimedia.org/api/rest_v1/media/math/render/svg/ee50feeecc84360e363188a9c32c67c5b2e0f627)

* **For cylinder inertia**:<br>
![cylinder_inertia_x_y](https://wikimedia.org/api/rest_v1/media/math/render/svg/bf6d4233b4abefa725ce3692fbb47758ca5a3e93)<br>
![cylinder_inertia_z](https://wikimedia.org/api/rest_v1/media/math/render/svg/4d442c3230245ff701b093049a7abd6f3eb12d35)

* **For sphere inertia**:<br>
![sphere_inertia](https://wikimedia.org/api/rest_v1/media/math/render/svg/52c780991451031f075e73210fd5b0e042bfd1c4)

Test your robot:
```
roslaunch myfirstrobot_description load_description.launch model:=myfirstrobot.urdf.xacro enable_gazebo:=true enable_rviz:=true
```

### Part 4: Plugins and simulation

The gazebo simulator provide a set of [actuators and sensors plugins](http://gazebosim.org/tutorials?tut=ros_gzplugins).
In this part of the class it is presented the increment of the robot description with two of these plugins to base actuator and camera sensor and finally simulate the robot with gazebo simulator.

* **Plugins**
  * **Base actuator**
    * [plugin libgazebo_ros_planar_move.so](https://github.com/ros-simulation/gazebo_ros_pkgs/blob/kinetic-devel/gazebo_plugins/include/gazebo_plugins/gazebo_ros_planar_move.h)
  * **Camera sensor**
    * [tag sensor](http://sdformat.org/spec?ver=1.6&elem=sensor)
    * [plugin libgazebo_ros_camera.so](https://github.com/ros-simulation/gazebo_ros_pkgs/blob/kinetic-devel/gazebo_plugins/include/gazebo_plugins/gazebo_ros_camera.h)

Test your robot:
```
roslaunch myfirstrobot_description load_description.launch model:=myfirstrobot.urdf.xacro enable_gazebo:=true enable_rviz:=true enable_teleop:=true
```

---

## Sources:
* [R2-D2](https://en.wikipedia.org/wiki/R2-D2)

* [ROS basic understanding](http://wiki.ros.org/ROS/Tutorials)
* [XML basic understanding](https://www.w3schools.com/xml/)

* [ROS](https://www.ros.org/)
* [Gazebo](http://gazebosim.org/)

* [urdf tutorials](https://wiki.ros.org/urdf/Tutorials)
* [robot visual model](https://wiki.ros.org/urdf/Tutorials/Building%20a%20Visual%20Robot%20Model%20with%20URDF%20from%20Scratch)
* [move some robot parts](https://wiki.ros.org/urdf/Tutorials/Building%20a%20Movable%20Robot%20Model%20with%20URDF)
* [organize and clean up the urdf code](https://wiki.ros.org/urdf/Tutorials/Using%20Xacro%20to%20Clean%20Up%20a%20URDF%20File)
* [collision and inertial properties](https://wiki.ros.org/urdf/Tutorials/Adding%20Physical%20and%20Collision%20Properties%20to%20a%20URDF%20Model)

* [Simulation Description Format (SDF)](http://sdformat.org/)
* [Unified Robot Description Format (URDF)](http://wiki.ros.org/urdf)
* [XML Macros (XACRO)](https://wiki.ros.org/xacro)

* [tag robot](http://wiki.ros.org/urdf/XML/robot)
* [tag link](http://sdformat.org/spec?ver=1.6&elem=link)
* [tag joint](http://sdformat.org/spec?ver=1.6&elem=joint)
* [tag sensor](http://sdformat.org/spec?ver=1.6&elem=sensor)

* [List of moments of inertia](https://en.wikipedia.org/wiki/List_of_moments_of_inertia#List_of_3D_inertia_tensors)

* [actuators and sensors plugins](http://gazebosim.org/tutorials?tut=ros_gzplugins)
* [plugin libgazebo_ros_planar_move.so](https://github.com/ros-simulation/gazebo_ros_pkgs/blob/kinetic-devel/gazebo_plugins/include/gazebo_plugins/gazebo_ros_planar_move.h)
* [plugin libgazebo_ros_camera.so](https://github.com/ros-simulation/gazebo_ros_pkgs/blob/kinetic-devel/gazebo_plugins/include/gazebo_plugins/gazebo_ros_camera.h)
