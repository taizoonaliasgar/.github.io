[← Back to Home](./)

# Robust MPC for Tremor-Suppression Wrist Exoskeleton (TAWE)

<!--This project develops a **Robust Model Predictive Control (RMPC)** framework for a wearable **wrist exoskeleton** designed to suppress tremor-induced disturbances while preserving voluntary motion. The controller is built for the **Tremor Alleviating Wrist Exoskeleton (TAWE)** and targets the dynamics and uncertainty patterns observed in **Essential Tremor (ET)**.

 > ✅ *This project is entirely **simulation-based** and focuses on controller design, robustness analysis, and performance benchmarking under clinically representative tremor disturbances.* -->

This project implements a **Robust Model Predictive Controller (RMPC)** for a **wearable wrist exoskeleton** to suppress tremor-induced motion while preserving natural voluntary movement. The controller is developed for the **Tremor Alleviating Wrist Exoskeleton (TAWE)** and targets the uncertainty patterns observed in **Essential Tremor (ET)**.

<!-- > ✅ *Simulation-based validation under clinically representative tremor disturbances, with consistent RMSE reduction over standard MPC.*-->

---

## 🦾 System Overview – TAWE Exoskeleton

- **Device:** Tremor Alleviating Wrist Exoskeleton (TAWE)  
- **DOFs:** 6-DoF rigid-link mechanism, with **2 actuated joints**:
  - Wrist Flexion/Extension (FE)
  - Radial/Ulnar Deviation (RUD)
- **Goal:** Suppress pathological tremor while allowing **natural voluntary wrist motion**
- **Sensing (for model & validation):** Encoders + IMU
- **Validation Mode:** High-fidelity numerical simulation
<div style="text-align:center; margin-bottom: 24px;">
  <img src="assets/exoskeleton/TAWE.png"
       style="max-width: 85%; border-radius: 10px;">
  <p style="margin-top: 8px;">
    <em>Tremor Alleviating Wrist Exoskeleton (TAWE) used for RMPC controller design and validation.</em>
  </p>
</div>
---

## 🎯 Problem Addressed

Patients with **Essential Tremor (ET)** experience involuntary oscillatory wrist motion (typically 4–12 Hz) that overlays voluntary movement and degrades task performance. A tremor-suppression controller must:

- **Reject involuntary tremor motion**
- **Preserve intended voluntary motion**
- Accommodate and remain robust to **patient-to-patient variability**

Standard MPC assumes nominal dynamics and degrades when tremor torques and user-specific variability deviate from the model. This motivates the need for an **explicitly robust control formulation**.

---

## 🧠 RMPC Approach – Key Ideas

The control strategy is based on a **vertex-based Robust MPC formulation** with a data-driven uncertainty set:

- **Reduced-order dynamic model:**  
  The constrained multibody dynamics of TAWE are reduced to a **2-DoF wrist model** in flexion/extension(FE) and radial/ulnar deviation(RUD) for control synthesis.

- **Data-driven disturbance modeling:**  
  - Tremor-like torque inputs are generated in the clinically relevant **3–7 Hz band**  
  - Resulting joint-velocity deviations are analyzed in **velocity space**  
  <!-- - A minimum-area **convex polygon** is fit to define a **bounded disturbance set**-->

- **Vertex-based uncertainty representation:**  
  - The uncertainty set is encoded as the **convex hull of worst-case vertices**
  - RMPC enforces feasibility and robustness across **all vertices simultaneously**

- **State-dependent cost weighting (agility):**  
  - Each disturbance vertex is weighted based on **alignment with the current tracking error**
  - Vertices aligned with the dominant deviation direction are **penalized more heavily**
  - This increases **disturbance rejection exactly where tremor is most harmful**

<!-- - **Real-time oriented design:**  
  - Discrete-time control with **20 ms sampling time (50 Hz)**
  - Prediction horizon: **N = 15**
  - Balanced for **robustness vs. computational load**-->

---

## ⚙️ Controller Configuration (High Level)

- **State:** Wrist angles and angular velocities in FE and RUD  
- **Inputs:** Two actuator torques (FE and RUD)
- **Cost structure:**
  - High penalty on **position tracking error**
  - Moderate penalty on **velocity error**
  - Moderate penalty on **control effort**
- **Constraints:**
  - Joint limits
  - Torque limits
  - Wearable safety & comfort bounds
- **Real-time oriented design:**  
  - Discrete-time control with **20 ms sampling time(50 Hz)**
  - Prediction horizon: **N = 15**
  <!-- - Balanced for **robustness vs. computational load**-->

A **single robust optimization problem** is solved at each time step with a **shared first control input across all uncertainty vertices** to guarantee causality and robustness.

---

## 🧪 Simulation-Based Validation

The RMPC framework is validated through high-fidelity numerical simulations of the TAWE wrist exoskeleton under **clinically representative tremor-like torque disturbances** applied in the **3–7 Hz range**.

Simulation studies evaluate:
- Reference tracking under involuntary tremor inputs  
- Robustness to bounded user-specific disturbance uncertainty  
- Comparative performance against a **standard MPC controller** under identical conditions.

<!--All controller comparisons use identical:
- Initial conditions  
- Reference trajectories  
- Actuator limits  
- Joint constraints  

This ensures a **controlled and fair comparison** between MPC and RMPC.-->

---

## 📈 Representative Simulation Plots

<div style="display: flex; justify-content: center; gap: 24px; flex-wrap: wrap;">

  <!-- Steady Reference -->
  <div style="text-align: center; width: 45%;">
    <div style="height: 260px; display: flex; align-items: center; justify-content: center;">
      <img src="assets/exoskeleton/steady_reference_tracking3.png"
           style="max-width: 100%; max-height: 100%; object-fit: contain;">
    </div>
    <p style="margin-top: 8px;">
      <em>Steady reference tracking under tremor disturbance: RMPC maintains tighter tracking and reduced oscillation compared to MPC.</em>
    </p>
  </div>

  <!-- Sinusoidal Reference -->
  <div style="text-align: center; width: 45%;">
    <div style="height: 260px; display: flex; align-items: center; justify-content: center;">
      <img src="assets/exoskeleton/sin_reference_tracking3.png"
           style="max-width: 100%; max-height: 100%; object-fit: contain;">
    </div>
    <p style="margin-top: 8px;">
      <em>Sinusoidal reference tracking under tremor disturbance: RMPC achieves improved phase alignment and lower tracking error than MPC.</em>
    </p>
  </div>

</div>

## 📊 Key Quantitative Results

- **≈ 2× reduction in joint-angle Root Mean Square Error(RMSE)** under tremor disturbances compared to standard MPC
<div style="display: flex; justify-content: center; gap: 20px; flex-wrap: wrap;">

  <!-- Constant reference card -->
  <div style="flex: 1 1 260px; max-width: 360px; border: 1px solid #ddd; border-radius: 10px; padding: 12px 16px;">
    <h4 style="text-align:center; margin: 4px 0 8px 0;">Constant Reference</h4>
    <hr style="border:none; border-top:1px solid #eee; margin:6px 0 10px 0;">
    <p style="margin: 4px 0;"><strong>Joint q₁:</strong> 0.031 → <strong>0.014</strong>  (~55% lower)</p>
    <p style="margin: 4px 0;"><strong>Joint q₂:</strong> 0.072 → <strong>0.034</strong>  (~53% lower)</p>
  </div>

  <!-- Sinusoidal reference card -->
  <div style="flex: 1 1 260px; max-width: 360px; border: 1px solid #ddd; border-radius: 10px; padding: 12px 16px;">
    <h4 style="text-align:center; margin: 4px 0 8px 0;">Sinusoidal Reference</h4>
    <hr style="border:none; border-top:1px solid #eee; margin:6px 0 10px 0;">
    <p style="margin: 4px 0;"><strong>Joint q₁:</strong> 0.031 → <strong>0.015</strong>  (~52% lower)</p>
    <p style="margin: 4px 0;"><strong>Joint q₂:</strong> 0.072 → <strong>0.034</strong>  (~53% lower)</p>
  </div>

</div>

- **Consistent performance improvement** across both FE and RUD joints  
- **Improved phase alignment and reduced oscillation amplitude**  
- **More decisive and anticipatory control behavior** relative to reactive MPC  
- Robust performance maintained across:
  - Multiple reference trajectories
  - Multiple tremor frequencies (3–7 Hz)
  - Multiple disturbance amplitudes

---

## 📄 Related Publication (Under Review)

This work is currently **under peer review** and will be made publicly available once the review process is complete.

**Manuscript Title:**  
*Robust Model Predictive Control of a Tremor-Suppression Wrist Exoskeleton*

📌 *The full paper (PDF) will be uploaded here immediately after the final decision.*

<!-- Uncomment once accepted / available -->
<!-- 📄 [Download Paper (PDF)](assets/exoskeleton/rmpc_tremor_exoskeleton.pdf) -->

---

## 🏭 Industry Relevance

This project demonstrates end-to-end capability in designing **robust, real-time controllers for human-interactive robotic systems** under uncertainty. The methods and insights directly transfer to:

- **Medical & assistive robotics:** tremor suppression, rehabilitation exoskeletons, prosthetics  
- **Human–robot interaction:** control design under unpredictable, user-dependent disturbances  
- **Embedded control systems:** real-time MPC with tight computation and safety constraints  
- **Wearable robotics & mechatronics:** torque-limited actuators, joint safety, and comfort-aware control  

### Key Engineering Skills Demonstrated
- Robust Model Predictive Control (RMPC)
- Data-driven uncertainty modeling
- Reduced-order dynamic modeling
- Real-time constrained optimization
- Simulation benchmarking & performance validation
- Safety-critical control for human-in-the-loop systems

This work reflects the ability to take a problem from **disturbance characterization → dynamic modeling → robust controller synthesis → quantitative performance validation**, which directly aligns with industrial R&D workflows in **medical devices, robotics startups, and advanced control teams**.




