[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/AlBFWSQg)
# a14g-final-submission

    * Team Number: T05
    * Team Name: Fusion Maverick
    * Team Members: Qingyang Xu, Ruizhe Wang, Xinyi Wang
    * Github Repository URL: https://github.com/ese5160/a14g-final-submission-s25-t05-fusion-maverick.git
    * Description of test hardware: (development boards, sensors, actuators, laptop + OS, etc) 

## 1. Video Presentation
 a
## 2. Project Summary
### Device Description

### Device Functionality

### Challenges

### Prototype Learnings

### Next Steps & Takeaways

### Project Links


## 3. Hardware & Software Requirements
* <b><i>Hardware Requirements:</i></b>
    * <u><i>Magic Wand PCBA:</i></u>
        * HR01 - project shall be based on SAMW25 core.

        * HR02 - external-connected slide switch shall be used for activation of the wand.

        * HR03 - external-connected Force-Sensitive Resistor(FSR) Interlink Model 402 shall be used for for command detection. <br>
        The sensor will detect if continuous force exerted in the sensing area, as a start flag and maintaining state for the wand gesture recognition.

        * HR04 - mounted 6-axis IMU MPU6500 shall be used for for wand gesture recognition. <br>
        The sensor will collect 6-axis data(3-axis gyroscope and 3-axis accelerometer) while the FSR sensing area is continuously pressed.<br>
        The collected data shall be then used to recognize the swing trajectory of the wand for gesture recognition.
        The tentative way we've designed for gestures are:<br>
          * Gesture 1: Wave tilde---wave pattern;<br>
          * Gesture 2: Zigzag---twinkle;<br>
          * Gesture 3: Clockwise circle---Turn on the motor;<br>
          * Gesture 4: Swipe up---Speed up the motor and LCD shows volume up animation;<br>
          * Gesture 5: Swipe down---Slow down the motor and LCD shows volume down animation;<br>
          * Gesture 6: Anticlockwise circle---Turn off the motor;

        * HR05 - external-connected LED strip shall be used for command emission indication.<br>
        The LED strip will quickly flash simultaneously as the control command sent out via MQTT to the cloud, imitating the laser emission process.

        * HR06 - external-connceted vibration motor drived by DRV2605L haptic motor controller shall be used for actuator execution feedback.<br>
        The vibration motor will funtion as a feedback reponse to different actuators' actions. The tentative way we're going to execute it is:
            * LCD tasks --- Vibration Motor Soft Bump - 100% (Effect NO.7);
            * Motor ON/OFF --- Vibration Motor Strong Click - 60% (Effect NO.2);
            * Motor SPEED UP --- Vibration Motor Strong Buzz - 100% (Effect NO.14);
            * Motor SLOW DOWN --- Vibration Motor 1000 ms Alert 100% (Effect NO.16).

    <br>


    * <u><i>Acuator PCBA:</i></u>
        * HR07 - project shall be based on SAMW25 core.

        * HR08 - mounted state LED shall be used to reflect the state of the actuator. <br>
        If there is no control demand, the state LED maintains off; vice versa, the state LED will be turned on when instruction send until the task execution.

        * HR09 - external-connected gearmotor drived by DRV8874 motor driver shall be used as one of the actuator.<br>
        The motor will be drived to execuate the wand command. Such as clockwise to turn on, swipe up to increase the rotation speed, swipe down to decrease the rotation, and anticlockwise to turn off.

        * HR010 - external-connected LCD shall be used as the other actuator.<br>
        The LCD would have two function modes, one is the visulization of motor control, which would reflect the motion state of the motor, such as, as the rotation speed increase, the LCD will display volume up animation.<br>
        In addition, the LCD would solely interact with the magic wand, such as the LCD will antimate a wave pattern when wand has a 'Wave' tranjectory gesture, and animate a twinkle with respect to wand 'Zigzag' tranjectory gesture.

<br>



* <b><i>SRS:</i></b>
    * SR01 - slide switch on/off.<br>
        - configured as an external interrupt;
        - programmed state machine controlling the entire state(ON/OFF) of the system.
    
    <br>

    * SR02 - FSR based command detection.<br>
        - configured as an external interrupt;
        - a FSR will be used to monitor the use condition of the wand:
            - if no strain/stress detected(<b><i>logic 'high'</i></b>), the wand is a common stick used for fun;
            - if strain/stress detected(<b><i>logic 'low'</i></b>), the wand is utilized as our proposed "magic wand".

    <br>

    * SR03 - IMU-based gesture recognition.<br>
        - configued as a SPI(SERCOM0);
        - the 6-axis IMU MPU6500 will be used for collecting data for one samping period when the user touches the sensing area of the strain/stress sensor;<br>
        the collected data will be used for the recognition of trajectory gestures of the wand, and then send out the correspoding control demand to the cloud.

    <br>

    * SR04 - LED strip based command emission.<br>
        - configued as a digital output;
        - a LED strip will be programmed to flash simultaneously when the control demand is sent out after correct gesture recogonition.<br>
        Neopixel library is utilized for this implmentation.

    <br>

    * SR05 - actuartor execution.
        - the corresponding actuator(determined by the pre-defined gestures) will response to the magic wand gesture, executing the command and then sending feedback to the cloud to actiavte the vibration motor on the wand;<br>
            * state LED, configued as a a digital output:<br> 
            keep off while no command, turned on when received the command and turned off when the tasks are successfully executed.
            * micro metal gear motor, drived by DRV8874 motor driver, configued as one analogue output(PWM) and two digital outputs:
                * activation: clockwise circle drawn by the wand;
                * brake: anticlockwise circle drawn by the wand;
                * accelerate: wand swipes up;
                * decelerate: wand swipes down.
            * LCD, configued as a SPI(SERCOM0) and several digital I/Os:
                * mode 1 - visulization of motor motion state:
                    * motor accelerates: volume up animation;
                    * motor decelerates: volume down animation.
                * mode 2 - intaction with wand:<br>
                the LCD would solely interact with the wand. 
                  * the LCD will animates a twinkle with respect to wand 'Zigzag' tranjectory gesture;<br> 
                  * the LCD animates a wave pattern with respect to wand 'Wave' tranjectory gesture.

    <br>
        
    * SR06 - vibration motor based actuator exection feedback.<br>
        - drived by DRV2605L haptic motor controller, configured as an I2C(SERCOM3) and one digital output.
        - a vibration motor will be activated for about few seconds once the control demand has been successfully received and executed by the actuator. We proposed varying vibration effects/modes of the motor with respect to diffrent task accomplishment. The tentative way we're going to execute it is:
            * LCD tasks --- Vibration Motor Soft Bump - 100% (Effect NO.7);
            * Motor ON/OFF --- Vibration Motor Strong Click - 60% (Effect NO.2);
            * Motor SPEED UP --- Vibration Motor Strong Buzz - 100% (Effect NO.14);
            * Motor SLOW DOWN --- Vibration Motor 1000 ms Alert 100% (Effect NO.16).
    
    <br>
        
    * SR07 - Node-RED interface and interaction.<br>
        - Node-RED interface will take in the IMU gesture and actuator motion feedback JSON MQTT messages and print out in the UI dashboard.
        - In addition, we also enable Node-RED to directly control the actuator via different buttons.


<br>
<br>

## 4. Project Photos & Screenshots

* ***Framework of the boards:***
  * <u>*whole case:*</u>
    ![whole](/images/whole_case.jpg)

  * <u>*magic wand case and its inner:*</u>
    ![MW](/images/magic-wand_case.jpg)
    ![MW_IN](/images/magic-wand_inner.jpg)
    ![MW_UP](/images/magic-wand_case-up.jpg)
    ![MW_BOTTOM](/images/magic-wand_case-bottom.jpg)

  * <u>*actuator case:*</u>
    ![ATT](/images/actuator_inner.jpg)
    ![ATT-case](/images/actuator_case.jpg)

<br>

* ***The standalone PCBA, top:***
  * <u>*magic wand:*</u>
    ![mw_pcb-up](/images/mw-frontend.jpg)

  * <u>*actuator:*</u>
    ![att_pcb-up](/images/att-frontend.jpg)

<br>

* ***The standalone PCBA, bottomp:***
  * <u>*magic wand:*</u>
    ![mw_pcb-bottom](/images/mw-backend.jpg)

  * <u>*actuator:*</u>
    ![att_pcb-bottom](/images/att-backend.jpg)

<br>

* ***Thermal camera images of boards under load:***
  * <u>*magic wand:*</u>
  
    ![thermal-mw](/images/mw_thermal.jpg)

  * <u>*actuator:*</u>

    ![thermal-att](/images/att_thermal.jpg)

<br>

* ***Altium Designer board layout 2D view:***
  * <u>*magic wand:*</u>
    ![mw_pcb-2d](/images/mw_2d-layout.png)

  * <u>*actuator:*</u>
    ![att_pcb-2d](/images/att_2d-layout.png)

<br>

* ***Altium Designer board layout 3D view:***
  * <u>*magic wand:*</u>
    ![mw_pcb-2d](/images/mw_3d-layout.png)

  * <u>*actuator:*</u>
    ![att_pcb-2d](/images/mw_3d-layout.png)

<br>

* ***Node-RED dashboard:***
![node-red dash blank](/images/node-red_dash-blank.png)
![node-red dash](/images/node-red_dash.png)

<br>

* ***Node-RED backend:***
![mw](/images/node-red_mw.png)
![att](/images/node-red_att.png)
![otafu](/images/node-red_otafu.png)
![reset](/images/node-red_reset.png)

<br>

* ***Block diagram:***
![blcok 1](/images/Block_diagram1.jpg)
![blcok 2](/images/Block_diagram2.jpg)


<br>
<br>

## Codebase

* link to your final embedded C firmware codebases
* link to your [Node-RED dashboard code](https://github.com/ese5160/a14g-final-submission-s25-t05-fusion-maverick/blob/1c47a3a814a5b3925147e226cab0708e4901a165/T05%20Fusion%20Maverick.json).


