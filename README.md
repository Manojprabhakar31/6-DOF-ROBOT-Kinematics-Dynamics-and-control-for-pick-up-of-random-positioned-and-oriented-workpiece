<p align="center">
<h1>6-DOF (UR 5) ROBOT KINEMATICS DYNAMICS AND CONTROL
SIMULATION USING ANALYTIC MODELS</h1>  
<b>Technologies:</b> PyBullet · Python · Robotics Kinematics · Optimization-Based Control · Computer Vision
</p>

<h2>Overview</h2>
<p align="justify">
This project presents a physics-based simulation of a <b>6-DOF UR5 robotic manipulator</b> designed to detect, track,
and intercept <b>randomly oriented workpieces on a moving conveyor</b>. The system integrates vision-based localization,
analytic kinematics, and real-time closed-loop inverse kinematics (CLIK) to achieve smooth and stable pick-and-place
operation under dynamic conditions.
</p>



<h2>Key Features</h2>
<ul>
  <li>Real-time tracking of moving workpieces with arbitrary orientation</li>
  <li>Analytic Forward and Inverse Kinematics for UR5</li>
  <li>Cubic joint-space trajectory generation for smooth motion</li>
  <li>Closed-Loop Inverse Kinematics with Damped Least Squares (DLS)</li>
  <li>Reduced-order dynamic modeling for physically consistent torque control</li>
  <li>Full digital twin simulation using PyBullet</li>
</ul>



<h2>System Architecture</h2>

<h3>1. Vision-Based Workpiece Localization</h3>
<p align="justify">
Canny edge detection is used to estimate workpiece pose (position and yaw). Pose updates are continuously synchronized
with conveyor motion for real-time interception.
</p>

<h3>2. Kinematic Control</h3>
<p align="justify">
Forward Kinematics (FK) computes the real-time end-effector pose, while Analytic Inverse Kinematics (IK) generates joint
configurations to reach the predicted interception region.

<h3>UR5 Denavit–Hartenberg(DH) Parameters</h3>

<table align="center">
  <tr>
    <th>Joint (i)</th>
    <th>a<sub>i</sub> (m)</th>
    <th>d<sub>i</sub> (m)</th>
    <th>α<sub>i</sub> (rad)</th>
  </tr>
  <tr><td>1</td><td>0.00000</td><td>0.08920</td><td>−π/2</td></tr>
  <tr><td>2</td><td>0.42500</td><td>0.00000</td><td>0</td></tr>
  <tr><td>3</td><td>0.39225</td><td>0.00000</td><td>0</td></tr>
  <tr><td>4</td><td>0.00000</td><td>0.11000</td><td>−π/2</td></tr>
  <tr><td>5</td><td>0.00000</td><td>0.09475</td><td>π/2</td></tr>
  <tr><td>6</td><td>0.00000</td><td>−0.12150</td><td>π</td></tr>
</table>


</p>

<h3>3. Reduced-Order Dynamic Approximation</h3>

<p align="justify">
To enable real-time torque application without full rigid-body dynamics, a simplified
dynamic approximation is adopted:
</p>

<ul>
  <li>
    <b>Base and arm joints:</b> Modeled as a yaw-dominant 3-link pendulum capturing the primary inertial effects.
    <br><br>
    <img src="https://github.com/user-attachments/assets/a126c2fb-e6ee-4188-993a-71e5e1be2934"
         width="800" style="height:auto;" />
  </li>

  <br>

  <li>
    <b>Wrist joints:</b> Approximated as a simple pendulum with a roll axis to model rotational dynamics.
    <br><br>
  </li>
</ul>

<p align="justify">
This captures dominant inertial effects while remaining computationally efficient.
</p>


<h3>4. Trajectory Planning (Joint Space)</h3>
<p align="justify">
The system utilizes cubic joint-space polynomials, guaranteeing continuity in both position and velocity. This provides a smooth path for real-time interception.
</p>

<p align="center">
q(t) = at<sup>3</sup> + bt<sup>2</sup> + ct + d
</p>

<p align="center">
d = q<sub>0</sub>, &nbsp; c = q̇<sub>0</sub><br>
a = [2(q<sub>0</sub> − q<sub>f</sub>) + (q̇<sub>0</sub> + q̇<sub>f</sub>)t_catch] / t_catch<sup>3</sup><br>
b = [3(q<sub>f</sub> − q<sub>0</sub>) − (2q̇<sub>0</sub> + q̇<sub>f</sub>)t_catch] / t_catch<sup>2</sup>
</p>

<h3>5. Conveyor Synchronization via CLIK (DLS)</h3>
<p align="justify">
The moving workpiece is tracked using a Closed-Loop Inverse Kinematics (CLIK) algorithm. To maintain system stability and handle potential singularities, we implement the Damped Least Squares (DLS) method.
</p>


<p align="center">
H = J<sup>T</sup>J + λI , &nbsp; λ = 10<sup>−6</sup><br>
g = J<sup>T</sup>ẋ<sub>d</sub>
</p>

<p align="center">
q̇ = clip(H<sup>−1</sup>g, −q̇<sub>max</sub>, q̇<sub>max</sub>)
</p>

<p align="center">
$$\mathbf{q}_i = inverse kinematics(x_i, y_i, \psi_i)$$
</p>

<h2>Visual Results</h2>

<div align="center">

  <img src="https://github.com/user-attachments/assets/15f96110-3e61-4159-be6d-f8f8f350e3d8"
       width="800" style="height:auto;" />
  <h4>Figure 1: UR5 CAD Assembly (PTC Creo)</h4>
  <p><i>High-fidelity CAD model used for mass properties and center-of-gravity estimation.</i></p>
  <br><br>

  <img src="https://github.com/user-attachments/assets/0064310f-ba77-4c0c-8835-1862cf67750a"
       width="800" style="height:auto;" />
  <h4>Figure 2: Physics-Based Simulation Environment (PyBullet)</h4>
  <p><i>Digital twin including conveyor, tray system, and UR5 manipulator.</i></p>
  <br><br>

  <img src="https://github.com/user-attachments/assets/3faf17c4-9afb-4a6b-af81-cb04a94e0620"
       width="800" style="height:auto;" />
  <h4>Figure 3: Vision-Based Workpiece Localization</h4>
  <p><i>Canny edge detection pipeline for estimating workpiece position and orientation.</i></p>
  <br><br>

  <img src="https://github.com/user-attachments/assets/0525d75f-e6a8-42ad-915b-7f41ce874ac5"
       width="800" style="height:auto;" />
  <h4>Figure 4: End-Effector Trajectory during Catch-Point Approach</h4>
  <p><i>Predicted interception trajectory synchronized with conveyor motion.</i></p>
  <br><br>

  <img src="https://github.com/user-attachments/assets/62af23bf-d1eb-4836-b2ec-4b0143c540ac"
       width="800" style="height:auto;" />
  <h4>Figure 5: Control System Performance</h4>
  <p><i>Joint torques, joint angles, and tracking errors across all operational phases.</i></p>
  <br><br>

  <img src="https://github.com/user-attachments/assets/e10de7a7-748e-4931-afc4-60235f826d1f"
       width="800" style="height:auto;" />
  <h4>Figure 6: Task-Space Tracking Correlation</h4>
  <p><i>Correlation between end-effector pose and actual moving workpiece configuration.</i></p>
  <br><br>

  <img src="https://github.com/user-attachments/assets/bf706bcb-3b8b-495e-bc05-8b0b0f653a7a"
       width="800" style="height:auto;" />
  <h4>Figure 7: Full Cycle Path Visualization</h4>
  <p><i>End-effector path from tray to randomly oriented moving workpieces.</i></p>

</div>



<h2>Results and Limitations</h2>
        
  <h3>Key Performance Results</h3>
  <ul>
      <li><strong>Stable tracking</strong> of moving workpieces under constant conveyor speeds.</li>
      <li><strong>Smooth interception trajectories</strong> maintained within bounded joint velocities.</li>
      <li><strong>High-precision task-space tracking</strong> with minimal pose error.</li>
      <li><strong>Physically consistent interaction</strong> within the simulated environment.</li>
  </ul>

  <h3>Technical Limitations</h3>
  <ul>
      <li><strong>Perception Constraints:</strong> The system currently requires synchronized image inputs from a dual-camera setup.</li>
      <li><strong>Collision Sensitivity:</strong> While the object-following method has a 90% success rate, it is susceptible to workpiece collisions during high-error states.</li>
      <li><strong>Reliability Gap:</strong> A 10% failure margin exists; further refinement of the following methodology or collision-avoidance logic is required to resolve these edge cases.</li>
  </ul>
  </section>

  <h2>Future Extensions</h2>
  <ul>
      <li>Implementation of <strong>full rigid-body dynamics</strong> with computed-torque control.</li>
      <li>Integration of <strong>Model Predictive Control (MPC)</strong> for optimized interception timing.</li>
      <li>Development of <strong>learning-based grasp planning</strong> for varied object geometries.</li>
      <li>Expansion to a <strong>multi-camera perception pipeline</strong> for increased system redundancy.</li>
  </ul>
