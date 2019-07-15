# Model-Predictive-Control (MPC)

In this project, I have implemented a Model Predictive Control in C++ which is used to drive an autonomous vehicle around a lake race track.

### Reflection

### 1. What is MPC?

MPC (Model Predictive Control)  is an advanced method of process control, which relies on dynamic models.
It has the the ability to anticipate future events and can take actions accordingly.

### 2. The Model

The model state consists of 5 components:
  1. The position -  **x,y**
  2. The heading -  **psi**
  3. The heading error -  **epsi** 
  4. The velocity -  **v**
  5. The cross track error -  **cte**

The actuators are:
  1. The steering angle -  **delta**
  2. The throttle -  **a**

The update equations are:

![](http://i.imgur.com/NHS3nIM.png)

### 3. Timestep Length (N) and Elapsed Duration (dt)

The prediction horizon  _(T = N*dt)_  is the duration over which future predictions are made. We would like  **T**  to be large (so we can get more information) , but not too large because the environment might change in the meantime .  **T**  should be only a few seconds.
I chose  **N**  to be 7 and  **dt**  to be 0.14 - a little bit more than the latency. This means that the prediction horizon **T** would be 0.98 second, which is best considering all the turns of the track.
The chosen values for **N**  and **dt**  enable the vehicle movement to be fast and safe.

Some of the values I tried:

  * N=5,dt=0.1
  
  * N=20,dt=0.1
  
  * N=10,dt=0.5
  
  * N=10,dt=1.5
  
  * N=20,dt=0.5
  
  * N=25,dt=0.05

### 4. Polynomial Fitting and MPC Preprocessing

The server returns waypoints using the map's coordinate system, which is different from the vehicle's coordinate system. The coordinates of the waypoints are the reference trajectory that the vehicle needs to follow.
The coordinates need to be transformed to the vehicle coordinate system.
After the transformation , the position and the heading of the vehicle are zero, since the vehicle is at the origin of its own coordinate system.
The transformation is done by first shifting the origin to the vehicle position and then rotating it to the vehicle’s heading:

![](http://i.imgur.com/iuot4s1.png)

A 3d polynomial is then fitted to the waypoints.

### 5. Model Predictive Control with Latency

To compensate for the 100 ms latency , I predicted the vehicle state 100ms into the future before passing it to the solver. I applied the following equations:

![](http://i.imgur.com/NHS3nIM.png)

The implementation is in lines : 125-140 in main.cpp In the implementation:

* I converted the velocity from mph m/s.
* Implemented all of the model update equations as shown above.
* Used a negative sign of the steering angle:

![](http://i.imgur.com/PJC7o6u.png)

##### **The car achieved top speed of over 95mph and drove safely**

![](http://i.imgur.com/TDXopmV.png)



### 5. The final video output

[![MPC](http://i.imgur.com/AlnycxF.png)](https://vimeo.com/229699668 "MPC")
