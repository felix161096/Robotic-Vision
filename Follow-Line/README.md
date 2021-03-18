# Follow Line
## Introduction
In this work was implemented a method to keep a f1 vehicle inside a circuit. The intended was to reach the goal only by the use of computer vision algorithms. 
## Materials and Methods
The materials avaliable to perform the work were an online-based plataform (https://unibotics.org/) and a user guide (http://jderobot.github.io/RoboticsAcademy/exercises/AutonomousCars/follow_line/). This last provided point to point instruction to get a good pipiline of the work. 

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111540814-e64f2c00-876f-11eb-8369-7094bafaf225.png' />
    <font size="2">
    <figcaption> Figure 1. Circuit (left) and simulator (right).
    </figcaption>
    </font>

&nbsp; 

As for how the simulator is implemented, two main features must be described: the circuit and the vehicle, see Figure 1. On the one hand, the circuit was implemented with a red line in the center of the road. It follows the road along all the circuit, including both straight and curved segments. Besides, only the flat circuit was built, that is, without ascents or descents. On the other hand, the vehicle possesses a camera in its front side. This camera is not centered with the symmetry of the car, right side looking at by front side being displaced.

An application programming interface is avaliable to perform the work. The main instrucction are de following: 
- from HAL import HAL - to import the HAL(Hardware Abstraction Layer) library class. This class contains the functions that sends and receives information to and from the Hardware(Gazebo).
- from GUI import GUI - to import the GUI(Graphical User Interface) library class. This class contains the functions used to view the debugging information, like image widgets.
- HAL.getImage() - to get the image.
- HAL.motors.sendV() - to set the linear speed.
- HAL.motors.sendW() - to set the angular velocity.
- GUI.showImage() - allows you to view a debug image or with relevant information.

Considerations to be considered:
- The system can start with the vehicle positioned in any position.
- The car must follow a stable conduction. 
- As much generalizable as it could be. The car should be able to complete a lap in another circuit.

As for how to maintain the car inside the road, considering the curved segments: the red line can be thresholding from HSV space. This implies a change from the original color space of the image retrieved,  i.e. BGR. For this aim, OpenCV library was used, being the boundaries in HSV: Hue range is [0, 179], Saturation range is [0, 255] and Value range is [0, 255] (see Figure 2). The threshold for red color segmentation of the image were fixed as follow: 

- H = [0, 30]
- S = [190, 255]
- V = [200, 255]

In addition, to decrease computational cost of thresholding the whole image, only the significant rows for the next steps were considered. Due to the image size (480x640 pixels), the pixels considered were those in the range [240, 260] (see Figure 2).

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111031240-bb2eaa80-8406-11eb-993f-722424f343f7.png' />
    <font size="2">
    <figcaption> Figure 2. BGR to HSV scace color transformation. Also was indicated the rows which were considered for the resolution.
    </figcaption>
    </font>

&nbsp; 

The PID controller required of a reference to keep the car inside the road. In this case, the center of the red line in a straight segment was considered, i.e. the middle point of the columns of the image. Besides, it is needed to obtain a value from the image to compare with the reference and obtain the error (see Figure 3). This value was the numbers of pixels in column axis i where the mass center of segmented image was.

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=D = M_{10} / M_{00}">
</p>
where M10 and M00 are image moments. It is a particular weighted average of image pixel intensities. To find the centroid of the image, it is generally converted to binary format and then is found its center.

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111691361-84a1c700-882e-11eb-92da-c0f055af0cb7.png' />
    <font size="2">
    <figcaption> Figure 3. Basic sketch of the PID controller implemented (left). Reference and deviation of an example frame (right).
    </figcaption>
    </font>
&nbsp;

Because there were different curved sections along the road, with different radial and straight sections, 5 PID controllers were implemented. Each depended on the associated E input error. When the E was lower, it meant the car was in a straight segment. Therefore, the linear velocity was set at its maximum value. In contrast, when E was greater, the linear velocity was set at its minimum value. Furthermore, it was considered that within each section E the linear velocity changes as E, with a simple P controller. Regarding the angular velocity, a PID controller was set for each section E. Besides, angular velocity output was limited in all the controller. A summary of how it was implemented is shown in Figures 4 and 5. 

As for angular velocity limiter, the limits for each controller were:
- Controller #1: angular velocity ϵ [-0.5, 0.5]
- Controller #2: angular velocity ϵ [-0.5, 0.5]
- Controller #3: angular velocity ϵ [-0.5, 0.5]
- Controller #4: angular velocity ϵ [-0.7, 0.7]
- Controller #5: angular velocity ϵ [-0.2, 0.2]

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111079227-5902a200-84f9-11eb-9bd2-917f7578a93d.png' />
    <font size="2">
    <figcaption> Figure 4. Controller detail. #1, #2, #3 and #4 controllers correspond to normal operation. #5 controller corresponds to safety mode.
    </figcaption>
    </font>

&nbsp; 

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111224895-5a0dff00-85df-11eb-82ad-387dcc4f323d.png' />
    <font size="2">
    <figcaption> Figure 5. Linear speed limits over error ranges.
    </figcaption>
    </font>

&nbsp; 

A safety mode was implemented for the cases when E is higher than 230 pixels. In this case, the linear speed decreases significatively and other PID controller is implemented. In the Figure 4, this safety mode is the 5th controller. In this mode, only when E in higher than 3, the car return to the "normal" operation. By this way an hysteresis cicle is completed.

To avoid errors regarding the loss of the red line, a robust algorithm was implemented:
- If the car starts in a random position, the driver forces the car to turn until the red line appears. In this case, it is not possible to detect whether the red line is detected in the correct direction.
- If the car loses the red line during its operation, then the car continues in the direction of turn in which it was turning.


## Results

[14/03/2021 23:15] This first attempt was made with a Brain Frequency of 12.5 and a GUI Frequency of 12.5: 

[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/37750255/111085971-26b56c80-851a-11eb-8a05-56ed9840a101.png)](https://user-images.githubusercontent.com/37750255/111085635-772bca80-8518-11eb-8a4f-a7369c906951.mp4)

[15/03/2021 22:58] Safety mode working.

[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/37750255/111085971-26b56c80-851a-11eb-8a05-56ed9840a101.png)](https://user-images.githubusercontent.com/37750255/111226992-31d3cf80-85e2-11eb-9610-5fa91438a559.mp4)

[17/03/2021 21:57] In this sesion was attempted to rise the brain frequency and/or GUI frequency. However, the limitations of the avaliable workstation avoided a good performance in this regard.

Other point that was attempted was the implementation of a robust algorithm to avoid loss line problems. Somo examples of how these addon worked are showed in the folloing:

- First: what error ocurred?

[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/37750255/111085971-26b56c80-851a-11eb-8a05-56ed9840a101.png)](https://user-images.githubusercontent.com/37750255/111538697-4abcbc00-876d-11eb-81d9-07e5ed1dda60.mp4)

- Second: fisrt problem resolved:

[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/37750255/111085971-26b56c80-851a-11eb-8a05-56ed9840a101.png)](https://user-images.githubusercontent.com/37750255/111538792-6a53e480-876d-11eb-9a94-6f4f3359ff7e.mp4)

- Third: second + speeding up in line detection:

[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/37750255/111085971-26b56c80-851a-11eb-8a05-56ed9840a101.png)](https://user-images.githubusercontent.com/37750255/111538933-953e3880-876d-11eb-8fb3-cb6b05073e32.mp4)


[18/03/2021 20:21] In this sesion was attempted to rise the brain frequency and/or GUI frequency. However, the limitations of the avaliable workstation avoided a good performance in this regard.

Due to that facts, the ultimate video of the resolution is the following (Brain Frequency: 12.5 and a GUI Frequency: 12.5):

[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/37750255/111085971-26b56c80-851a-11eb-8a05-56ed9840a101.png)](https://user-images.githubusercontent.com/37750255/111085635-772bca80-8518-11eb-8a4f-a7369c906951.mp4)
