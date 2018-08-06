---
description: klaas kelchtermans
layout: default
---
## Project Page: Future Depth as Value Signal for Learning Collision Avoidance

### Abstract 

The constant miniaturization of robots reduces the array of available sensors. 
This raises the need for robust algorithms that allow robots to navigate without collision based solely on monocular camera input.
In this work we propose a new learning-based method for the task of obstacle avoidance.
We propose a neural network policy for monocular collision avoidance with self-supervision that does not require actual collisions during training.

To this end, we demonstrate that a neural network is capable of evaluating an input image and action by predicting the expected depth in the future. 

In this sense, the future depth can be seen as an action-value-signal.
In comparison with our baseline model that is based on predicting collision probabilities, we show that using depth requires less data and leads to more stable training without need for actual collisions.
The latter can be especially useful if the autonomous robot is more fragile and not capable to deal with collisions (e.g. aerial robots).

### Requirements

All requirements are combined in a singularity image which can be run and build locally (without need for root permissions).

```
$ singularity build ros_gazebo_tensorflow.img docker://kkelchte/ros_gazebo_tensorflow:latest
```

### Add project code

Get the project code. This consists of a ros-catkin-package named simulation_supervised and a tensorflow project which is the q-learning branch of my pilot project.

```
$ singularity shell --nv kkelchte/ros_gazebo_tensorflow:latest
# create your local project folder and make it your home
$ mkdir depth_q_net
$ cd depth_q_net
$ export HOME=$PWD
# source ROS and drone_ws
$ source /opt/ros/$ROS_DISTRO/setup.bash
$ source /code/drone_ws/devel/setup.bash --extend
# get simulation_supervised code and source
$ mkdir -p ~/simsup_ws/src
$ cd ~/simsup_ws & catkin_make
$ cd ~/simsup_ws/src
$ git clone https://www.github.com/kkelchte/simulation_supervised
$ cd ~/simsup_ws & catkin_make
$ source $HOME/simsup_ws/devel/setup.bash --extend
# get tensorflow project
$ mkdir -p ~/tensorflow/log & cd ~/tensorflow
$ git clone --single-branch -b q-learning https://www.github.com/kkelchte/pilot q-learning
# add some more environment variables to be sure:
$ export ROS_PACKAGE_PATH=/code/drone_ws/src:$HOME/simsup_ws/src:/opt/ros/kinetic/share
$ export PYTHONPATH=$PYTHONPATH:$HOME/tensorflow/pilot
$ export GAZEBO_MODEL_PATH=$HOME/simsup_ws/src/simulation_supervised/simulation_supervised_demo/models
# create an entrypoint so you can easily load the environment
$ cat > entrypoint
export HOME=~/depth_q_net
source /opt/ros/$ROS_DISTRO/setup.bash
source /code/drone_ws/devel/setup.bash --extend
source $HOME/simsup_ws/devel/setup.bash --extend
export ROS_PACKAGE_PATH=/code/drone_ws/src:$HOME/simsup_ws/src:/opt/ros/kinetic/share
export PYTHONPATH=$PYTHONPATH:$HOME/tensorflow/pilot
export GAZEBO_MODEL_PATH=$HOME/simsup_ws/src/simulation_supervised/simulation_supervised_demo/models
CTR+D
```

### Get pretrained model

```
$ wget 
```

### Run 

Start singularity again, source environment and off you go!

```
$ singularity shell --nv kkelchte/ros_gazebo_tensorflow:latest
$ source depth_q_net/entrypoint
$ roscd simulation_supervised/python
$ python run_script.py -pe sing -p eva_params.yaml -n 10 -e -w canyon --fsm nn_turtle_fsm -m ref -t ref_eva
```

### Contact

Having trouble with the code? Feel free to contact me: kkelchte ~ at ~ esat.kuleuven.be. 
