.. _Mathematical model:

##################
Mathematical Model
##################

.. math::
    \begin{eqnarray}    %
        m {\mathbf{\ddot{x}}} &=&
        -m g\, \mathbf{e}_3+ {}^W\mathbf{R} {f} \mathbf{e}_3\\
        %
        \mathbf{J}\dot{\boldsymbol{\omega}} &=&
        -\boldsymbol{\omega} \times \mathbf{J}\boldsymbol{\omega}
        + \boldsymbol{\tau},
    \end{eqnarray}

Where the rotation matrix is,

.. math::
    \boldsymbol{R}_{xzy} = \left [
   \begin{array}
      c\psi c\theta - s\phi s\psi s\theta & -c\phi s\psi & c\psi s\theta + c\theta s\phi s\psi\\
      c\theta s\psi + c\psi s\phi s\theta & c\phi s\psi & s\psi s\theta + c\psi c\theta s\phi\\
      - c\phi s\theta & s\phi & c\phi c\theta
   \end{array} \right ],

Each rotor has an angular velocity :math:`\boldsymbol{\omega}` and produce
a thrust :math:`\boldsymbol{f_i} = k_F \boldsymbol{\omega_i}^2`, Also The
rotors produce a moment by :math:`\boldsymbol{M_i} = k_M \boldsymbol{\omega_i}^2`


Inertial Matrix
----------------



kf and km
---------

