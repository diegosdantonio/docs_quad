.. _intro:

############
Introduction
############
A quadrotor has four propellers as actuators which give the controllability of at most 4 degrees of freedom (DoF). Commonly, the propellers are mouonted perpendicular to the control board, forming a square shape by design. This symmetric design helps simplify the dynamic and control models. However, if more propellers are placed in the same manner, i.e., perpendicular to the control board and forming a common polygon, or multiple quadrotors are placed side to side, the number of controllability will not increase, desipte the improvement in robustness and overall power output. In order to utilize the reducdency to increase the controllability, one can tilt the *4+n* propellers in different angles. The *n* extra propellers can contribute to the extra number of DoF's. This solution requires a different set of modelling work to control the mutirotor systems. Here, we present a modular quadrotor system which flies, by individual, and increases the number of DoF's when docking together as a new structure without much requirement of changing the models.

#################
Development Diary
#################
03/24/2021

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
