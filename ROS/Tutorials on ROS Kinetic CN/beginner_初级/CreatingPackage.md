## 创建ROS程序包 CreatingPackage

---

http://wiki.ros.org/cn/ROS/Tutorials/CreatingPackage

---

### I. What makes up a catkin Package?

For a package to be considered a catkin package it must meet a few requirements:

- The package must contain a catkin compliant package.xml file.
- That package.xml file provides meta information about the package.
- The package must contain a CMakeLists.txt which uses catkin.

If it is a catkin metapackage it must have the relevant boilerplate `CMakeLists.txt` file.

Each package must have its own folder
This means no nested packages nor multiple packages sharing the same directory.
The simplest possible package might have a structure which looks like this:

```
my_package/
  CMakeLists.txt
  package.xml
```

### II. Packages in a catkin Workspace

The recommended method of working with catkin packages is using a catkin workspace, but you can also build catkin packages standalone. A trivial workspace might look like this:

```
workspace_folder/        -- WORKSPACE
  src/                   -- SOURCE SPACE
    CMakeLists.txt       -- 'Toplevel' CMake file, provided by catkin
    package_1/
      CMakeLists.txt     -- CMakeLists.txt file for package_1
      package.xml        -- Package manifest for package_1
    ...
    package_n/
      CMakeLists.txt     -- CMakeLists.txt file for package_n
      package.xml        -- Package manifest for package_n
```

Before continuing with this tutorial create an empty catkin workspace by following the Creating a workspace for catkin tutorial.

### III. Creating a catkin Package

This tutorial will demonstrate how to use the catkin_create_pkg script to create a new catkin package, and what you can do with it after it has been created.

First change to the source space directory of the catkin workspace you created in the Creating a Workspace for catkin tutorial:

```bash
# You should have created this in the Creating a Workspace Tutorial
$ cd ~/catkin_ws/src
```

Now use the catkin_create_pkg script to create a new $ cd ~/catkin_ws/src 'beginner_tutorials' which **depends** on std_msgs, roscpp, and rospy:

`$ catkin_create_pkg beginner_tutorials std_msgs rospy roscpp`

This will create a beginner_tutorials folder which contains a package.xml and a CMakeLists.txt, which have been partially filled out with the information you gave catkin_create_pkg.

catkin_create_pkg requires that you give it a package_name and optionally a list of dependencies on which that package depends:

This is an example, do not try to run this

```
# catkin_create_pkg <package_name> depend1 [depend2]
# catkin_create_pkg also has more advanced functionalities which are described in catkin/commands/catkin_create_pkg.
```

### IV. Building a catkin workspace and sourcing the setup file

Now you need to build the packages in the catkin workspace:

```bash
$ cd ~/catkin_ws
$ catkin_make
```

After the workspace has been built it has created a similar structure in the devel subfolder as you usually find under `/opt/ros/$ROSDISTRO_NAME`.

To add the workspace to your ROS environment you need to source the generated setup file:

`$ . ~/catkin_ws/devel/setup.bash`

### V. package dependencies

#### 5.1 First-order dependencies

When using catkin_create_pkg earlier, a few package dependencies were provided. These first-order dependencies can now be reviewed with the rospack tool.

`$ rospack depends1 beginner_tutorials `

`roscpp rospy std_msgs`

As you can see, rospack lists the same dependencies that were used as arguments when running catkin_create_pkg. These dependencies for a package are stored in the package.xml file:

```bash
$ roscd beginner_tutorials
$ cat package.xml
```

```xml
<package format="2">
...
  <buildtool_depend>catkin</buildtool_depend>
  <build_depend>roscpp</build_depend>
  <build_depend>rospy</build_depend>
  <build_depend>std_msgs</build_depend>
...
</package>
```

#### 5.2 Indirect dependencies

In many cases, a dependency will also have its own dependencies. For instance, rospy has other dependencies.

`$ rospack depends1 rospy`

```
genpy
roscpp
rosgraph
rosgraph_msgs
roslib
std_msgs
```

A package can have quite a few indirect dependencies. Luckily rospack can recursively determine all nested dependencies.

```bash
$ rospack depends beginner_tutorials
cpp_common
rostime
roscpp_traits
roscpp_serialization
catkin
genmsg
genpy
message_runtime
gencpp
```

### VI. Customizing Your Package

This part of the tutorial will look at each file generated by catkin_create_pkg and describe, line by line, each component of those files and how you can customize them for your package.

Customizing the package.xml
The generated package.xml should be in your new package. Now lets go through the new package.xml and touch up any elements that need your attention.

description tag
First update the description tag: