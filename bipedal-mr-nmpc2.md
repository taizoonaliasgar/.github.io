[← Back to Home](./)

# Multi-Rate Nonlinear Model Predictive Control for Wall-Supported Bipedal Locomotion of Quadrupedal Robots

This project develops a **Multi-Rate Nonlinear Model Predictive Control (MR-NMPC)** framework that enables a Unitree Go2 quadruped to perform **wall-supported bipedal locomotion** over uneven terrain.

The controller jointly plans:
- Center of mass (CoM) position and orientation trajectories  
- Environment reaction forces (ERFs)  
- Discrete footstep locations  

within a **single optimization loop**, and is executed in a **multi-rate fashion** to remain real-time capable.

---

## Poster

> 📌 Click to view full resolution.

![MR-NMPC Bipedal Locomotion Poster](assets/bipedal/bipedal_poster.png)

*(Replace the path above with the actual filename you upload, e.g. `assets/bipedal/IROS_poster.png`.)*

---

## Motivation

Real-time footstep planning is essential for balance and adaptability in agile locomotion, especially when:

- Footholds are limited and constantly changing on **uneven terrain**  
- Conventional MPCs rely on **heuristic, decoupled** footstep planning  
- Instabilities arise when footstep planning and whole-body control are not tightly integrated  

**Goal:**  
Design a controller that **unifies robot position, orientation, reaction forces, and footstep planning** in a single optimization framework, while remaining fast enough for **on-hardware deployment**.

---

## MR-NMPC Formulation

The proposed **Multi-Rate Nonlinear MPC (MR-NMPC)** jointly optimizes:

- CoM position and orientation trajectories  
- Environment reaction forces (ERFs)  
- Discrete foot placements and step lengths  

The traditional **Single Rigid Body (SRB)** model is **augmented** with:
- Longitudinal foot positions  
- Step length as additional state and input variables  

This expands the SRB state/input dimensions and allows **footstep planning to live directly inside the MPC** rather than as an external heuristic.

### Multi-Rate Update Structure

A discrete **delta trigger** \( \delta(t+1) \) is introduced in the state update equation to enable **multi-rate inputs**:

- **3 Hz (slow input):** step-length and footstep updates  
- **50 Hz (fast input):** CoM and reaction-force updates  

The upcoming contact configuration is encoded via \( \delta(t+1) \), ensuring that **footstep updates anticipate the next contact domain**.

In compact form, the SRB dynamics with multi-rate input can be written as:

\[
x(t+1) = f\big(x(t), u(t), v(t)\big), \\
v(t+1) = v(t) + \delta(t+1)\,\Delta v(t),
\]

where \( x \) collects SRB states, \( u \) are fast inputs (ERFs), and \( v \) is the slow step-length variable.

---

## Whole-Body Control Integration

The MR-NMPC produces:

- Optimal SRB trajectory  
- Optimal contact forces  
- Planned step lengths and foot placements  

These are tracked on the Unitree Go2 by a **Hybrid Zero Dynamics-based Whole-Body Controller (WBC)** running at **500 Hz**.

The WBC:

- Enforces task-space tracking of the SRB references  
- Maps desired ground reaction forces to joint torques  
- Respects contact constraints and torque limits  

This **MR-NMPC + WBC stack** closes the loop from **high-level planning** to **low-level torque control**.

---

## Experimental Setup

- **Robot:** Unitree Go2 in a dynamically unstable **upright bipedal** configuration  
- **Environment:** Random wooden-block terrain with limited and uneven footholds  
- **Gait:** A **monkey-like contact sequence**, in which agile upper-limb (front leg) interactions with the wall help stabilize the torso during rapid transitions  

You can include one or more images here:

```md
![Unitree Go2 bipedal experimental setup](assets/bipedal/go2_setup_1.png)

![Wall-supported bipedal locomotion on uneven terrain](assets/bipedal/go2_bipedal_sequence.png)
