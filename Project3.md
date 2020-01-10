### Poses and Landmark positions

The pose is a point which represents the position or location of the robot at a given time, in 2D space. Likewise, the landmark position marks the location of a landmark in 2D space.

![](imgs/pose_lpos1.png)

Poses and landmark locations are given as pairs of values, where the first value is the horizontal(x) coordinate, and the second is the vertical(y) coordinate.


### Sensing Landmarks

A importance of the sense function is to allow the robot to measure its distance from landmarks. Among several reasons in the real world, this may help the robot prevent collision.
Thus, for the purpose of the project, when a robot senses, it measures distances of landmarks. The horizontal distance is obtained by subtracting the horizontal coordinate of the robot's current position from the horizontal coordinate of the landmark position. And, the vertical 
distance is obtained by subtracting the vertical coordinate of the robot's current position from the vertical coordinate of the landmark's position.

![](imgs/sense1.png)

In the illustration below, a robot senses Landmarks `0`, `1`, `2` which have their absolute horizontal and vertical distances less than or equal to the
specified measurement range of 3. A square measurement area based on the measurement range is highlighted as light blue. Landmarks 3, 4, 5 fall outside the range and, thus, cannot be sensed while the robot is at its current position.

![](imgs/sense2.png)

After sensing landmarks, the robot will store this information as a list of lists, with each
sub-list containing the `index of the landmark`, the `Horizontal distance of robot from landmark`, and 
the `Vertical distance of robot from landmark`, as demonstrated below.

```
Landmark 0: [0, 4-5, 2-5] = [0, -1, -3]
Landmark 1: [1, 3-5, 4-5] = [1, -2, -1]
Landmark 2: [2, 7-5, 3-5] = [2,  2, -2]
```

The measurement data, therefore, will be given as:
```
[[0, -1, -3], [1, -2, -1], [2,  2, -2]]
```

##### But there is more to it!

In the illustration above, we are sure of the robot's location. However, this is not the case in the real world. The robot's location can be inaccurate and we have to compensate for it.
We do this by adding an adjustment value, called noise, to the robot's position coordinates.

For example, Landmark 0 has coordinates `4, 2` and the robot is currently at position `5, 5`. Given a measurement noise of 0.3, the measurement for Landmark 0 can be derived as shown below.

```
Measurement Noise = 0.3

Index of Landmark = 0
Adjusted X coordinate of robot position = 5 + 0.3 = 5.3
Adjusted Y coordinate of robot position = 5 + 0.3 = 5.3

Horizontal distance of landmark from robot = 4 - 5.3 = -1.3
Vertical distance of landmark from robot = 2 - 5.3 = -3

Measurement Data = [0, -1.3, -3]
```



