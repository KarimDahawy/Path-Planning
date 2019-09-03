# **Path Planning Project**

## Goals:
----------------------------------------------------
In this project your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. You will be provided the car's localization and sensor fusion data, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.

[//]: # (Image References)

[image1]: ./Pictures/No_Incidents.png
[image2]: ./Pictures/Top_Speed.png
[image3]: ./Pictures/Down_Speed.png
[image4]: ./Pictures/Max_acc_and_Jerk.png
[image5]: ./Pictures/Same_Lane.png
[image6]: ./Pictures/Changing_lane.png

## Project Points:
------------------------------------------------------
### 1. Compilation
---------------------------
The Code is compiling using cmake and make without any errors. The build folder include the path_planning that has been excuted.


### 2. Valid Trajectories
--------------------------------
#### 2.1 The car is able to drive at least 4.32 miles without incident
The following picture identify that the vehicle kept driving for 5 miles without any incident
![alt text][image1]

#### 2.2 The car drives according to the speed limit
The following picture identify that The car didn't drive faster than the speed limit
![alt text][image2]

Also the car didn't drive much slower than speed limit unless obstructed by traffic (another vehicle)
![alt text][image3]

#### 2.3 Max Acceleration and Jerk are not Exceeded
The car does not exceed a total acceleration of 10 m/s^2 and a jerk of 10 m/s^3
![alt text][image4]

#### 2.4 Car does not have collisions
The Vehicle was able to drive the whole 5 miles without any incidents
![alt text][image1]

#### 2.5 The car stays in its lane, except for the time between changing lanes
The Vehicle was able to keep the same lane even while passing between two cars.
![alt text][image5]

#### 2.6 The car is able to change lanes
The vehicle was able to change lanes smoothly when there's an obstacle in its way.
![alt text][image6]


### 3. Reflection
---------------------------
In this part we will discuss in details the code model for generating paths.

#### 3.1 Localization and Prediction Part: Lines (257-320)
Localization depends on Car States, and Sensor Fusion Data. Firstly, we depend on changing Fernet (d-component) into Lanes through the information of (Lane Width = 4m). This means that the first left lane ranges from 0 to 4 and the secong middle lane ranges from 4 to 8 and the last right lane ranges from 8 to 12, so we do not need to take other vehciles out of this range (i.e. 0 to 12) into our consideration. After that we calculated the distances of the other vehciles, detected by sensors, away from the ego vehcile. Then, we set some conditions to set 3 main flags:

* **FrontCar** is set when there is a car in front of our vehcile, so there must be an action must be taken in order to avoid collision.
* **LeftCar** is set when there is a car in the left lane of our vehcile's lane, so there will be a collsion if left lane change action is taken.
* **RightCar** is set when there is a car in the right lane of our vehcile's lane, so there will be a collsion if right lane change action is taken.

#### 3.2 Behavior Planning FSM Part: Lines (322-370)
Based on the previous flags, we construct a Finite State Machine in order to cover the majority of the whole cases instead of using Complex Cost Functions. The constructed Finite State Machine covers: keep going on the same lane with acceleration or deceleraion, left/right lane change then come back to the same lane.

#### 3.3 Trajectory Generation Part: Lines (374-494)
It is exactly like Walkthrough part video. In this part, the trajectory path is generated and sent to the simulator. The fist two points are considered as the last two points of the old path. The trajectory is generated from **three** 30m spaced points. Sline library is used here in order to generate the whole points of the trajectory depending on including (spline.h) header file to the project. WayPoints are extracted as the first 30m of the spline, and sent to the simulator. The distance between the path points is kept equals to (0.02 * Ref_vel / 2.24) in orer to keep the velocity constant and don not exceed the Speed Limit (50 MPH). 
**Note:** 2.24 to covert miles per hour to meters per second. 
