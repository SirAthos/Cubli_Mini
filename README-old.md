# Cubli Mini
This is the code repository for a small version of the Cubli self-balancing cube. The Cubli is a well-known mechanical system used for teaching and research purposes. It consists of a cube that can balance itself on one of its edges or corners by using three reaction wheels, which are controlled by a microcontroller.

The Cubli Mini is a simplified version of the Cubli, designed to be smaller and less complex, while still demonstrating the basic principles of self-balancing and control. It uses an MPU6050 sensor to measure the angle and angular velocity of the cube, and three small DC motors with wheels to create the reaction forces.

This repository contains the firmware code for the Cubli Mini, written in Arduino C++, as well as the STL file for the 3D printed cube model, and a manual for assembling and using the device.

# Contents
Documents: README.md file and manual
Firmware: code for the Cubli Mini, including Arduino C++ files and MPU6050 and PID libraries
Libraries: additional libraries for the project, including Adafruit NeoPixel and Cubli Mini Libraries
Models: the STL file for the 3D printed cube model
Images: pictures of the Cubli Mini
Videos: a video of the Cubli Mini in action
Configurations: an original configuration file for the Cubli Mini
Getting started
To use the Cubli Mini, you will need to assemble the physical device by 3D printing the cube model, connecting the MPU6050 sensor and the DC motors, and uploading the firmware code to an Arduino board. The manual in the Documents directory provides detailed instructions for the assembly process.

Once you have assembled the device, you can upload the firmware code to the Arduino board using the Arduino IDE or another compatible software. The code includes several example programs that you can use to test the functionality of the Cubli Mini.

# Credits
The Cubli Mini project was developed by a team of students from Zhejiang University, China. The project was supervised by Dr. Ji-Hu Zhao, and the team members were Yu-Xuan Li, Yi-Ming Chen, and Shuai Li.

# License
The Cubli Mini project is released under the MIT License. Please see the LICENSE file for details.
