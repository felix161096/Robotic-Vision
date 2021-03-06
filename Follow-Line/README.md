# Follow Line
## Introduction
In this work was implemented a method to keep a f1 vehicle inside a circuit. The intended was to reach the goal only by the use of computer vision algorithms. 
## Materials and Methods
The materials avaliables to perform the work were an online-based plataform (https://unibotics.org/) and a user guide (http://jderobot.github.io/RoboticsAcademy/exercises/AutonomousCars/follow_line/). This last provided point to point instruction to get a good pipiline of the work. 

An application programming interface is avaliable to perform the work. The main instrucction are de following: 
- from HAL import HAL - to import the HAL(Hardware Abstraction Layer) library class. This class contains the functions that sends and receives information to and from the Hardware(Gazebo).
- from GUI import GUI - to import the GUI(Graphical User Interface) library class. This class contains the functions used to view the debugging information, like image widgets.
- HAL.getImage() - to get the image
- HAL.motors.sendV() - to set the linear speed
- HAL.motors.sendW() - to set the angular velocity
- GUI.showImage() - allows you to view a debug image or with relevant information
