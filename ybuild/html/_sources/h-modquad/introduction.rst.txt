.. _intro:

############
Introduction
############
A quadrotor has four propellers as actuators which give the controllability of at most 4 degrees of freedom (DoF). Commonly, the propellers are mouonted perpendicular to the control board, forming a square shape by design. This symmetric design helps simplify the dynamic and control models. However, if more propellers are placed in the same manner, i.e., perpendicular to the control board and forming a common polygon, or multiple quadrotors are placed side to side, the number of controllability will not increase, desipte the improvement in robustness and overall power output. In order to utilize the reducdency to increase the controllability, one can tilt the *4+n* propellers in different angles. The *n* extra propellers can contribute to the extra number of DoF's. This solution requires a different set of modelling work to control the mutirotor systems. Here, we present a modular quadrotor system which flies, by individual, and increases the number of DoF's when docking together as a new structure without much requirement of changing the models.
