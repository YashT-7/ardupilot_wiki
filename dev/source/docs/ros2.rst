.. _ros2:

=====
ROS 2
=====

.. image:: ../images/logos/ros2_logo.jpg
    :target: ../_images/logos/ros2_logo.jpg

ArduPilot capabilities can be extended with `ROS <http://www.ros.org/>`__ (aka Robot Operating System).

`ROS <http://www.ros.org/>`__ provides libraries, tools, hardware abstraction, device drivers, visualizers, message-passing, package management, and more to help software developers create robot applications. ROS has been succeded by `ROS2 <http://design.ros2.org/articles/why_ros2.html>`__, and Ardupilot now natively supports it through its library `AP_DDS <https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_DDS>`__


Prerequisites
=============

- Learn on to use ArduPilot first by following the relevant wiki for `Rover <https://ardupilot.org/rover/index.html>`__, `Copter <https://ardupilot.org/copter/index.html>`__ or `Plane <https://ardupilot.org/plane/index.html>`__. In particular, make sure the vehicle works well in Manual and Autonomous modes like Guided and Auto before trying to use ROS.
- Learn how to use ROS 2 by reading the `beginner tutorials <https://docs.ros.org/en/humble/Tutorials.html>`__.  In the case of a problem with ROS, it is best to ask on ROS community forums first (or google your error).

    We are keen to improve ArduPilot's support of ROS 2 so if you find issues (such as commands that do not seem to be supported), please report them in the `ArduPilot issues list <https://github.com/ArduPilot/ardupilot/issues>`__ with a title 

First, make sure that you have succesfully installed `ROS humble <https://docs.ros.org/en/humble/Installation.html>`__ and create a `ROS 2 workspace <https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Creating-A-Workspace/Creating-A-Workspace.html#id4>`__. This page assumes that your workspace is named `ros2_ws`

Before anything else, make sure that you have `sourced your ROS 2 environment <https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html#source-the-setup-files>`__ and check if it is `configured correctly <https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html#check-environment-variables>`__.

Finally, follow the `Installing Build Dependencies <https://github.com/ArduPilot/ardupilot/tree/master/libraries/AP_DDS#installing-build-dependencies>`__ section of `AP_DDS`'s README

Installation (Ubuntu)
=====================

To make installation easy, we will install the required packages using `vcs` and a `ros2.repos` files:

.. code-block:: bash

    cd ~/ros2_ws/src
    wget https://raw.githubusercontent.com/ArduPilot/ardupilot/master/Tools/ros2/ros2.repos
    vcs import --recursive < ros2.repos

Now update all dependencies:

.. code-block:: bash

    cd ~/ros2_ws
    sudo apt update
    rosdep update
    rosdep install --rosdistro ${ROS_DISTRO} --from-paths src

And finally, build your workspace:

.. code-block:: bash

    cd ~/ros2_ws
    colcon build --cmake-args -DBUILD_TESTING=ON

If you'd like to test your installation, run:

.. code-block:: bash

    cd ~/ros2_ws
    source ./install/setup.bash
    colcon test --packages-select ardupilot_dds_tests
    colcon test-result --all --verbose


Installation (MacOS)
====================

To make installation easy, we will install the required packages using `vcs` and a `ros2_macos.repos` files:

.. code-block:: bash

    cd ~/ros2_ws/src
    wget https://raw.githubusercontent.com/ArduPilot/ardupilot/master/Tools/ros2/ros2_macos.repos
    vcs import --recursive < ros2_macos.repos

Now update all dependencies:

.. code-block:: bash

    cd ~/ros_ws
    source /{path_to_your_ros_distro_workspace}/install/setup.zsh

Build microxrcedds_gen:

.. code-block:: bash

    cd ~/ros2_ws/src/microxrcedds_gen
    ./gradlew assemble
    export PATH=$PATH:$(pwd)/scripts

And finally, build your workspace:

.. code-block:: bash

    cd ~/ros2_ws
    colcon build --symlink-install --cmake-args \
    -DBUILD_TESTING=ON \
    -DCMAKE_BUILD_TYPE=RelWithDebInfo \
    -DCMAKE_MACOSX_RPATH=FALSE \
    -DUAGENT_SOCKETCAN_PROFILE=OFF \
    -DUAGENT_LOGGER_PROFILE=OFF \
    -DUAGENT_USE_SYSTEM_LOGGER=OFF \
    -DUAGENT_USE_SYSTEM_FASTDDS=ON \
    -DUAGENT_USE_SYSTEM_FASTCDR=ON \
    --event-handlers=desktop_notification-

If you'd like to test your installation, run:

.. code-block:: bash

    cd ~/ros2_ws
    colcon test \
    --pytest-args -s -v \
    --event-handlers console_cohesion+ desktop_notification- \
    --packages-select ardupilot_dds_tests

Installation (Docker)
=====================

Clone the ArduPilot docker project:

.. code-block:: bash

    git clone https://github.com/ArduPilot/ardupilot_dev_docker.git

Build the container:

.. code-block:: bash

    cd ~/ardupilot_dev_docker/docker
    docker build -t ardupilot/ardupilot-dev-ros -f Dockerfile_dev-ros .

Start the container in interactive mode:

.. code-block:: bash
    
    docker run -it --name ardupilot-dds ardupilot/ardupilot-dev-ros

Connect another bash process to the running container:

.. code-block:: bash
    
    docker container exec -it ardupilot-dds /bin/bash

The remaining steps are the same as for Ubuntu. You may need to install MAVProxy if it is not available on the container.

.. code-block:: bash
    
    pip install -U MAVProxy