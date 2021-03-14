# Follow Line
## Introduction
In this work was implemented a method to keep a f1 vehicle inside a circuit. The intended was to reach the goal only by the use of computer vision algorithms. 
## Materials and Methods
The materials avaliable to perform the work were an online-based plataform (https://unibotics.org/) and a user guide (http://jderobot.github.io/RoboticsAcademy/exercises/AutonomousCars/follow_line/). This last provided point to point instruction to get a good pipiline of the work. 

As for how the simulator is implemented, two main features must be described: the circuit and the vehicle. On the one hand, the circuit was implemented with a red line in the center of the road. It follows the road along all the circuit, including both straight and curved segments. Besides, only the flat circuit was built, that is, without ascents or descents. On the other hand, the vehicle posseesses a camera in its front side. This camera is not centered with the simetry of the car, right side looking at by front side being displaced.

An application programming interface is avaliable to perform the work. The main instrucction are de following: 
- from HAL import HAL - to import the HAL(Hardware Abstraction Layer) library class. This class contains the functions that sends and receives information to and from the Hardware(Gazebo).
- from GUI import GUI - to import the GUI(Graphical User Interface) library class. This class contains the functions used to view the debugging information, like image widgets.
- HAL.getImage() - to get the image
- HAL.motors.sendV() - to set the linear speed
- HAL.motors.sendW() - to set the angular velocity
- GUI.showImage() - allows you to view a debug image or with relevant information

Considerations to be considered:
- The system can start with the vehicle positioned in any position.
- The car must follow a stable conduction. 
- As much generalizable as it could be. The car should be able to complete a lap in another circuit.

As for how to maintain the car inside the road, considering the curved segments: the red line can be thresholding from HSV space. This implies a change from the original color space of the image retrieved,  i.e. BGR. For this aim, OpenCV library was used, being the boundaries in HSV: Hue range is [0, 179], Saturation range is [0, 255] and Value range is [0, 255]. The threshold for red color segmentation of the image were fixed as follow: 

- H = [0, 30]
- S = [190, 255]
- V = [200, 255]

In addition, to decrease computational cost of thresholding the whole image, only the significant rows for the next steps were considered. Due to the image size (480x640 pixels), the pixels considered were those in the range [240, 260].

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111031240-bb2eaa80-8406-11eb-993f-722424f343f7.png' />
    <font size="2">
    <figcaption> Figure 1. BGR to HSV scace color transformation. Also was indicated the rows which were considered for the resolution.
    </figcaption>
    </font>

&nbsp; 

The PID controller required of a reference to keep the car inside the road. In this case, the center of the red line in a straight segment was considered, i.e. the middle point of the columns of the image. Besides, it is needed to obtain a value from the image to compare with the reference and obtain the error (see Figue XXX). This value was the numbers of pixels in column axis i where the mass center of segmented image was.

<img src="https://render.githubusercontent.com/render/math?math=e^{i \pi} = -1">

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111072120-76c00f00-84d9-11eb-811c-e8c911280143.png' />
    <font size="2">
    <figcaption> Figure 2. .
    </figcaption>
    </font>

&nbsp;

Because there were different curved sections along the road, with different radial and straight sections, 5 PID controllers were implemented. Each depended on the associated E input error. When the E was lower, it meant the car was in a straight segment. Therefore, the linear velocity was set at its maximum value. In contrast, when E was greater, the linear velocity was set at its minimum value. Furthermore, it was considered that within each section E the linear velocity changes as E, with a simple P controller. Regarding the angular velocity, a PID controller was set for each section E. A summary of how it was implemented is shown in Figures XX and XX.

<figure>
    <img src= 'https://user-images.githubusercontent.com/37750255/111079227-5902a200-84f9-11eb-9bd2-917f7578a93d.png' />
    <font size="2">
    <figcaption> Figure 3. .
    </figcaption>
    </font>

&nbsp; 






Computing 
This impliques a 
The first challenge to be outcomed is the first consideration. If the car is set in any positon in the initial moment, 


## Results
