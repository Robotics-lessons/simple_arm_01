# simple_arm_01
## A mini-project for RoboND's ROS Basics Module, Lesson 02.

Below is the complete code for the simple_mover node, in it’s entirety, followed by a step-by-step explanation of what is happening. You can copy and paste this code into the simple_mover script you created in the ~/catkin_ws/src/simple_arm/scripts/ directory like this:

## First, open a new terminal, next:
```
$ cd ~/catkin_ws/src/simple_arm/scripts/
$ nano simple_mover
```
You have opened the simple_mover script with the nano editor, now copy and paste the code below into the script and use ctrl-x followed by y then enter to save the script.
simple_mover
```
#!/usr/bin/env python

import math
import rospy
from std_msgs.msg import Float64

def mover():
    pub_j1 = rospy.Publisher('/simple_arm/joint_1_position_controller/command',
                             Float64, queue_size=10)
    pub_j2 = rospy.Publisher('/simple_arm/joint_2_position_controller/command',
                             Float64, queue_size=10)
    rospy.init_node('arm_mover')
    rate = rospy.Rate(10)
    start_time = 0

    while not start_time:
        start_time = rospy.Time.now().to_sec()

    while not rospy.is_shutdown():
        elapsed = rospy.Time.now().to_sec() - start_time
        pub_j1.publish(math.sin(2*math.pi*0.1*elapsed)*(math.pi/2))
        pub_j2.publish(math.sin(2*math.pi*0.1*elapsed)*(math.pi/2))
        rate.sleep()

if __name__ == '__main__':
    try:
        mover()
    except rospy.ROSInterruptException:
        pass
        ```
        
## The code: Explained
```
#!/usr/bin/env python

import math
import rospy
```
rospy is the official Python client library for ROS. It provides most of the fundamental functionality required for interfacing with ROS via Python. It has interfaces for creating Nodes, interfacing with Topics, Services, Parameters, and more. It will certainly be worth your time to check out the API documentation here. General information about rospy, including other tutorials may be found on the ROS Wiki.
```
from std_msgs.msg import Float64
```
From the std_msgs package, we import Float64, which is one of the primitive message types in ROS. The std_msgs package also contains all of the other primitive types. Later on in this script, we will be publishing Float64 messages to the position command topics for each joint.
```
def mover():
    pub_j1 = rospy.Publisher('/simple_arm/joint_1_position_controller/command',
                             Float64, queue_size= 10)
    pub_j2 = rospy.Publisher('/simple_arm/joint_2_position_controller/command',
                             Float64, queue_size=10)
```                         
At the top of the mover function, two publishers are declared, one for joint 1 commands, and one for joint 2 commands. Here, the queue_size parameter is used to determine the maximum number messages that may be stored in the publisher queue before messages are dropped. More information about this parameter can be found here
```
 rospy.init_node('arm_mover')
```
Initializes a client node and registers it with the master. Here “arm_mover” is the name of the node. init_node() must be called before any other rospy package functions are called. The argument anonymous=True makes sure that you always have a unique name for your node
```
 rate = rospy.Rate(10)
 ```
The rate object is created here with a value of 10 Hertz. Rates are used to limit the frequency at which certain loops spin in ROS. Choosing a rate which is too high may result in unnecessarily high CPU usage, while choosing a value too low could result in high overall system latency. Choosing sensible values for all of the nodes in a ROS system is a bit of a fine-art.
```
    start_time = 0

    while not start_time:
        start_time = rospy.Time.now().to_sec()
```
start_time is used to determine how much time has elapsed. When using ROS with simulated time (as we are doing here), rospy.Time.now() will initially return 0, until the first message has been received on the /clock topic. This is why start_time is set and polled continuously until a nonzero value is returned (more information here).
```
    while not rospy.is_shutdown():
        elapsed = rospy.Time.now().to_sec() - start_time
        pub_j1.publish(math.sin(2*math.pi*0.1*elapsed)*(math.pi/2))
        pub_j2.publish(math.sin(2*math.pi*0.1*elapsed)*(math.pi/2))
        rate.sleep()
```
This the main loop. Due to the call to rate.sleep(), the loop is traversed at approximately 10 Hertz. Each trip through the body of the loop will result in two joint command messages being published. The joint angles are sampled from a sine wave with a period of 10 seconds, and in magnitude from [−π/2,+π/2]. When the node receives the signal to shut down (either from the master, or via SIGINT signal in a console window), the loop will be exited.
```
if __name__ == '__main__':
    try:
        mover()
    except rospy.ROSInterruptException:
        pass
```
If the name variable is set to “main”, indicating that this script is being executed directly, the mover() function will be called. The try/except blocks here are significant as rospy uses exceptions extensively. The particular exception being caught here is the ROSInterruptException. This exception is raised when the node has been signaled for shutdown. If there was perhaps some sort of cleanup needing to be done before the node shuts down, it would be done here. More information about rospy exceptions can be found here.
Running simple_mover
Assuming that your workspace has recently been built, and it’s setup.bash has been sourced, you can launch simple_arm as follows:
```
$ cd ~/catkin_ws
$ roslaunch simple_arm robot_spawn.launch
```
Once ROS Master, Gazebo, and all of our relevant nodes are up and running, we can finally launch simple_mover. To do so, open a new terminal and type the following commands:
```
$ cd ~/catkin_ws
$ source devel/setup.bash
$ rosrun simple_arm simple_mover
```

Congratulations! You’ve now written your first ROS node!
