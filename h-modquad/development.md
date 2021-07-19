# Development Diary
### 03/24/2021

- The recent few days in paper revision reminds me again of the importance of keeping good and detailed documentation for a project. I struggled remembering parts and components of this project even though it is THE project done by me less than half a year ago. Back then, I uploaded the firmware of our edition to Github and thought everything would be fine whenever we want to pick it up. It is not... We lost the PID coefficients, which was saved and deleted on the command computer instead of onboard... I panicked so much when seeing the structure flying around wildly and started to think whether I uploaded the wrong firmware. With the hints from dear David, we finally returned to where we were before the PID tuning after two days of frustration. We also lost the firmware that was specifically modified for motor calibration, rewriting which took me another day. If only I spent one hour muttering words like this (along with the coefficients) could I save a good weekend. Speaking of words like this, an alternative to a nice documentation could be development diaries. Although they can be contradicting each other as time goes, they give a trace of what has been done over time, trace that also motivates Ctrl+S the coefficients.

- The Ai matrix we are using for the 2x2 structure is:

{{-4., 0, 1.1547, -0.57735, 0.57735, 0.6},
{4., -8., 1.1547, -0.57735, -0.57735, 0.6},
{12., 0, 1.1547, 0.57735, -0.57735, 0.2},
{4., 8., 1.1547, 0.57735, 0.57735, 0.2},
{-8., 4., 1.1547, -0.57735, 0.57735, -0.2},
{0, -4., 1.1547, -0.57735, -0.57735, -0.6},
{8., 4., 1.1547, 0.57735, -0.57735, -0.6},
{0, 12., 1.1547, 0.57735, 0.57735, -0.2},
{-12., 0, 1.1547, -0.57735, 0.57735, 0.2},
{-4., -8., 1.1547, -0.57735, -0.57735, 0.2},
{4., 0, 1.1547, 0.57735, -0.57735, 0.6},
{-4., 8., 1.1547, 0.57735, 0.57735, 0.6},
{-8., -4., 1.1547, -0.57735, 0.57735, -0.6},
{0, -12., 1.1547, -0.57735, -0.57735, -0.2},
{8., -4., 1.1547, 0.57735, -0.57735, -0.2},
{0, 4., 1.1547, 0.57735, 0.57735, -0.6}}

- The current Mellinger Controller in the crazyflie firmware is inverted in roll and yaw errors, which needs to be compensated with negative PID gains.

- Currently, our PID is suboptimal which makes the motors saturate badly.

### 03/25/2021

- We spent most of the day updating the paper submission. The figures and equations are updated. The digest slide is completed. The summary for the current paper is finished. The copyright is transferred.

- I admit I did a bad job losing the PID coefficients. This should be strictly avoided! I tested some negative gains for pitch and yaw. No sign of success. Some ideas though:
  - Linear controller has some very nice "fade in" effects on the motor thrusts, which is realized by temporarily increase the maximum accumulated error in z-position while disabling the PD term on z-position. We can also have that in Mellinger controller (or just bypassing Mellinger controller when in the takeoff phase maybe?).
  - Mount the structure on the stand and test roll-pitch-yaw one by one (Nuuuuuu!)
  - `grep -r $update param service` on the command computer.
  - Do not mount the structure on the stand and test roll-pitch-yaw one by one (Nuuuuuuuuu!)

- After the submission of this revision, more efforts are needed in the lab, seriously.
  - Finish what we left off, tune the PID, SAVE THEM, and try to tilt without translation.
  - In the above items, if the last one does not work, try to find an alternative to pseudo-inverse $A$ because it generates results that are intuitively incorrect. For example, the resulted power distribution may require the motors that have no force component in one direction to generate force in that direction... Are we able to minimize other norms in closed-form? Probably not. What else?
  - Build our own quadrotors
    - The reason behind which is for better debugging experience, trying to bypass some inconsistency that exists because of old designs or at least trying to understand them, and cutting most modules that we do not use. We may get into WAY more and larger troubles, but we choose to do it not because it is easy but because it is hard!!! (sorry I am excited.)
    - Use the Crazyflie hardwares and build our firmware from scratch:
      - Understand the pins, the sensor/communication resources and we can start from driving one motor!
    - Use Arduino Nano 33 that runs on an ARM M0 processor and almost-equivalent system resources like RAM and ROM.
      - Understand the pins, the sensor/communication resources (which should be easier) and we can start from driving one motor too!
    - Use a MicroPy board v1.1 that runs on an STM32F405 chip, which is exactly the same as the one on Crazyflie Bolt.
      - Ehh, this one supports python. Would that allow us to have some convenience with linalg?
      - And this one does not have a gyro...

### 03/26/2021
- We got the Arduino Nano 33 IoT and BLE earlier today. Both of them runs on mediocre 32-bit processors and are impressively small in size.
  - IoT runs at 48 MHz with an ARM M0 core. WiFi/Bluetooth 4.2/accelerometer+gyro IMU.
  - BLE runs at 64 MHz with an ARM M4 core. Bluetooth 5.0/accelerometer+gyro+magnet IMU.
    - Some tests I ran to test bluetooth under peripheral mode are included in https://github.com/diegosdantonio/docs_quad/blob/363432c580b6276f0551e75675da821802ce74ba/h-modquad/development_diary_ws/arduino/bluetooth_periph_matrix_test/bluetooth_periph_matrix_test.ino : 100 Inversion operations on a 4x4 matrix took 11 milliseconds. Bravo!

### 04/01/2021
- April Fool! No, no way.
- Tried to connect the Crazyflie Bolt with STM32duino. The computer does not recognize the port. Maybe we have to performa full wipe on the existing firmware.
- We managed to log and have a peek at the required fx, fy, fz in PWM without boundary, which revealed something that we did not consider properly.
  - Because in the firmware, the PWM coefficients in `Ai` matrix for fx, fy are +-12, +-8, it makes a large required fx and/or fy almost determinant in the motor actuation. Since the input range to the motors is 0 ~ 65536, we want the contribution of fx, fy to the PWM no larger than one forth of the maximum (just like the moments.) Therefore, the required fx and fy *should* be smaller than 1500 in magnitude. 
  - However, in our test, a *relatively small* fx or fy can reach +-5000, which saturates the motors in both upper and lower bounds badly.
    - The short-term solution will be add clamp on fx and fy just like for the moments.
      - We will lose the feature like tilting  
    - The mid-term solution will be using 3-cell batteries and larger propellers to decrease the PID coefficients so that the required forces in PWM can decrease.
    - The long-term solution will be exploring the geometric representation of the solution space to Au = w to accquire a better solution of u.
- Now testing on M.x, M.y, and M.z to see if they make sense
  - First of all, we make sure that our power distribution works with the default PID controller. Since we did not change any parameter of the PID controller, that means the power distribution works despite the fd and Md calculation (modified Mellinger controller). What we do to get the power distribution is firstly getting A matrix in Mathematica then adding a negative sign to the row that gives fy. At last, we invert the A matrix with modified fy row. This method gives the same result as given in the official firmware when the number of motors is 4 and the tilting angle is zero.
  - Second, we did not change the equation as in Mellinger controller. We just projected the force vector in the x_F, y_F, z_F vectors.

### 07/19/2021
- It's been some time, hehehe. Now we got the 2x2 H-Modquad with T-modules working like a charm.
- The Ai matrix we are using is:
{{-0.14, -0.11, 0.0883883, -1.06066, 1.06066, -1.25},
{0.12, -0.11, 0.0883883, -1.06066, 0.353553, 0.416667},
{0.12, 0.13, 0.0883883, -0.353553, 0.353553, 0.416667},
{-0.14, 0.13, 0.0883883, -0.353553, 1.06066, 0.416667},
{0.13, 0.14, 0.0883883, -1.06066, -0.353553, 0.416667},
{-0.11, 0.14, 0.0883883, -1.06066, -1.06066, -1.25},
{-0.11, -0.12, 0.0883883, -0.353553, -1.06066, 0.416667},
{0.13, -0.12, 0.0883883, -0.353553, -0.353553, 0.416667},
{-0.12, -0.13, 0.0883883, 0.353553, -0.353553, 0.416667},
{0.14, -0.13, 0.0883883, 0.353553, -1.06066, 0.416667},
{0.14, 0.11, 0.0883883, 1.06066, -1.06066, -1.25},
{-0.12, 0.11, 0.0883883, 1.06066, -0.353553, 0.416667},
{0.11, 0.12, 0.0883883, 0.353553, 1.06066, 0.416667},
{-0.13, 0.12, 0.0883883, 0.353553, 0.353553, 0.416667},
{-0.13, -0.14, 0.0883883, 1.06066, 0.353553, 0.416667},
{0.11, -0.14, 0.0883883, 1.06066, 1.06066, -1.25}}
