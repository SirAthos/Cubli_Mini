# Cubli_Mini project

Chinese version: https://github.com/ZhaJiHu/Cubli_Mini

This project ID is derived from the Cubli of ETH Zurich, and the motor drive used is Simple FOC. It is low-cost, miniaturized, and simplified as my first open-source project.

**The project is completely open-source, and anyone can use the contents of this Github repo for free. You can also give it a Star if you like.**

### Introduction

This project removes the active braking, reduces the size and simplifies the original Cubli. It requires manual adjustment near the balance point to achieve edge and point balance. Cubli_Mini is a 10x10x10CM cube that integrates motor drive, charging and discharging. In a fully charged state, the point balance can last ≥5 hours without disturbance.

Project cost: ≤800 RMB

**Video introduction:**[https://www.bilibili.com/video/BV1UR4y1c74B?spm_id_from=333.999.0.0](https://www.bilibili.com/video/BV1UR4y1c74B?spm_id_from=333.999.0.0)

YouTube: [https://youtu.be/JwiJBd6I_vY](https://youtu.be/JwiJBd6I_vY)

![Image](https://github.com/SirAthos/Cubli_Mini/blob/master/5.Doc/Pic/Cubli_Mini.JPG)

### Document Update Notes (2022/6/7)

- **Fixed**: Fixed some incorrect purchase links in the PCB BOM table, **a total of 4, marked in red**. Students who have already placed orders need to verify whether the components are correct
- **Added**: **A backup link for the SMD button in the PCB BOM table purchase links.**

### Document Update Notes (2022/5/22)

- Added purchase links to the PCB BOM table.
- Updated tutorial for beginners on how to use this project for free (Code and Project), explaining how to solve header file error problems with illustrations.

### Document Update Notes (2022/5/21) c23

- /6.Process/Sheet Metal Parts/, added corresponding step files.
- /6.Process/3D printing/, added corresponding step files.
- **Safety Instructions**
  - **The flywheel is quite dangerous, be careful and keep your hands away from the flywheel.**
  - /2.Firmware/MCU1/control/cubli_mini.h, modifying the VOLTAGE_LIMIT voltage can limit the maximum output of the flywheel. When starting to use, it is recommended to reduce this parameter, starting from 4 or 5V.

### Document Update Notes (2022/5/19)

- It was recently discovered that two corners of the flywheel were not chamfered, which theoretically affects dynamic balance at high speeds. Therefore, this has been modified. Students who have already ordered samples can still use them, as the motor KV value is very low and I am currently using a flywheel with missed chamfers. Updated /4.Model/Cubli_Mini.STEP file. Also updated /6.Process/Sheet Metal Parts/YC.JGJ.000002.4.*, the file version number is updated to .4.
- The purchase quantity and purchase link for structural parts are in /6.Process/BOM/structural BOM.xls
- When purchasing materials, note that 3 sets of materials are needed for the encoder module.

### Document Description

- 3D model design source file
- Main control module, IMU module, encoder module, and download module hardware project
- Main control 1, Main control 2 software project
    - Two ESP32s are used for the main control
- Network debugging assistant
    - For WIFI parameter tuning
- Various documentation
    - Some explanations about the project, such as parameter tuning, button operations, control principles, etc.
- Sample files
    - STL format 3D printing files
    - DWG format sheet metal files
    - Bill of Materials
        - Structural BOM
        - PCB component BOM

### Structural Design

Since I only learned SolidWorks for a week, some parts may be missing details, such as screw threads, internal threads of copper pillars, etc.

The frame and other sheet metal parts are made of low-cost fiberglass boards, ensuring rigidity while reducing cost. The flywheel is made of 304 stainless steel, providing a large moment of inertia in a small volume. It is combined with a few 3D printed parts. To maintain aesthetics, anodized aluminum pillars are used for connectors, although lower-cost copper pillars can also be used. The overall cost of the structural parts is within 350 RMB, and replacing aluminum pillars with copper ones can reduce the cost to under 290 RMB.

**Notes:**

- The eight corners of Cubli_Mini are composed of two different corners, named Corner 1 and Corner 2, with 4 of each
- The connection between the frame and the corners is not 45° symmetrical, so there is an installation direction. Please refer to the DoC/Structural and Installation Instructions for details.

### Circuit Modules

**Master control module:**

- The main control module integrates 3 motor drive circuits, battery charging and discharging circuits, voltage reduction and stabilization circuits, and CAN communication circuits.
- As a single ESP32 only has 6 motor PWM channels, two ESP32 modules are used. CAN is used for MCU-to-MCU communication. Since both ESP32s are integrated on the same main board, the CAN communication can be changed to IIC, UART, SPI, or other short-path communication methods to save IC cost. However, changes to the main control hardware and software are required. Those interested can modify it themselves.
- The motor drive circuit refers to the open-source Simple FOC circuit. Simple FOC link: [https://github.com/simplefoc](https://github.com/simplefoc). Battery charging and discharging reference: [https://oshwhub.com/qqj1228/zi-ping-heng-di-lai-luo-san-jiao_10-10-ban-ben](https://oshwhub.com/muyan2020/zi-ping-heng-di-lai-luo-san-jiao_10-10-ban-ben_copy)

**IMU module:**

- Uses the common MPU6050
- To prevent stress, a groove is cut in the area where the IMU is located to reduce installation stress. However, using 3 hard-connected screws cannot avoid the stress issue. An improved solution is to use a soft pad/gasket and tighten only one screw to further reduce stress.
- As the IMU installation requirements are relatively high, during SMT, ensure the MPU6050 is parallel to the PCB and centered on the solder pad.

**Encoder module:**

- Uses the low-cost AS5600, which is cheap and useful at low speeds.

**Automatically download modules:**

- In order to minimize the main control area, the automatic download circuit is separated and based on the official automatic download circuit

**Notes:**

- Under normal circumstances, the automatic download module is soldered with pin headers, and the main control module does not solder any pin headers/female headers.

### Firmware Description

Cubli_Mini's core content consists of the Simple FOC motor drive library and point balance control algorithm, which runs on MCU1. MCU2 only uses the Simple FOC library to control motors 2 and 3, utilizing CAN to provide feedback and receive control commands.

**MCU1 firmware description:**

- bsp: A wrapper around the Arduino library, handling LED, KEY, ADC, and non-volatile storage, among other things.
- comm: A general-purpose class that currently only encapsulates a time class, which is used to calculate time differences, non-delay waiting, and other simple tasks.
- control: The core library containing balance algorithms and tuning interfaces. Tuning refers to the Simple FOC format. For ease of use, strings are employed instead of traditional hexadecimal protocols.
- main: The main file that initializes drivers and creates FreeRTOS tasks.
    - The official ESP32 library runs WIFI and Bluetooth classes on Core0. Therefore, when creating tasks, the WIFI task is created in Core0, while others are created in Core1. Mutex locks are only added for data interaction between tasks running on different cores.

**Notes:**

- Currently, WIFI SSID and PSW, TCP Server IP Port are hardcoded in the firmware. To use WIFI for tuning, you'll need to modify these settings. For details, please see Doc/Usage and Tuning Instructions.

### Sampling Instructions

Considering that many students may be trying structural sampling for the first time, sample files are provided.

**3D printing:**

- Files are located in: /6.Process/**3D Printing**. These files are in STL format. For the number of samples, please refer to the **3D Printing Processing Instructions** in the same directory. You can use Future Factory or JLCPCB for sampling, with SLA 3D printing preferred.
- File names use part numbers for easy management. Files without a .X extension indicate the first version.

**Sheet Metal Sample:**

- Files are located in: /6.Process/**Sheet Metal Parts**, **Each part has corresponding PDF and DWG files, with PDF files for easy viewing and DWG files for laser cutting/sheet metal processing**. For the number of samples, please refer to the **Sheet Metal Processing Instructions** in the same directory. You can find a manufacturer on Taobao with the drawings. Since the required precision is not high for personal use, tolerances are subject to the manufacturer's discretion.
- You can also find the corresponding STEP files for processing on Taobao.

**PCB Sample:**

- Get the PCB file from the hardware project and go out and turn left to JLCPCB (Jiali Chuang) for free samples.

**BOM:**

- Files are located in: /6.Process/**BOM**
- The structural BOM list includes the **structural materials and links** to purchase.
- The PCB BOM list contains the electronic components to purchase. You can also export the list from the AD project.
- **Notes:**
    - The PCB BOM list is exported from the AD source file, which may have duplicate materials. Please be aware of this.
    - **Note that 3 encoders are needed, so you'll need to purchase 3 sets of materials.**
    - DRV8313, ESP32, AS5600, and IMU can be purchased on Taobao, while other components can be ordered from the LCSC BOM list.
    - **Pay attention to the packaging of surface-mount aluminum electrolytic capacitors, as there's a height limit of 8mm in the main control board area.**
    - Please notify us of any missing information in the files.

**Guide for Beginners on Free Sampling:**

- The file is located at: /Doc/Guide_for_Beginners_on_Free_Sampling.pdf. This guide will teach you step-by-step how to complete this project's free sampling process.

### Control Algorithm

Cubli_Mini uses LQR for control, and it can also use cascaded PID control. The code is located at /needs_modification/MCU1/control/cubli_mini.

**Edge Balancing:**:

- You can refer to a first-order inverted pendulum for modeling and parameter solving. Since it's difficult to obtain accurate parameters for the model, the parameters I've solved have some differences from the optimal parameters. Therefore, this project mostly relies on experienced tuning engineers to adjust the parameters.

**Point Balancing:**

- Building on the edge balancing, control is added for the X and Z axes. The same control method as edge balancing is used. For details, see Doc/Control Principle Explanation.

**Research-oriented:**

- Please refer to the original Cubli's papers.

### Tuning

**Tuning Methods:**

- You can use UART and WIFI for tuning. WIFI requires connection to a router, and Cubli_Mini acts as a TCP client.

**Tuning Commands:**

- For detailed commands, please refer to Doc/Tuning Command Instructions
