### Abstract

interactive robots mission is a complex mission it need to nagvigate in complex and dynamic environment also need to fully flexable to planing the sequentially sub tasks. In order to achieve the goal we demo a method by utlize LLMs(large language model) and Reenforcement Learning. Our idea focuses on using an LLM's capacity for precise, short-horizon physical reasoning to dynamically select or modify the parameters of complex, learned robotic trajectories , effectively allowing the robot to autonomously execute tasks that previously required initial human intervention.

### Introduction

1. Penetrative Control Feedback

In current LLM-based manipulation, environmental information is primarily derived visually (e.g., YOLOv5 for object position). However, fine-grained tasks often depend on non-visual physical feedback (e.g., force or torque required to open a tight hinge). In DPTA, the **Penetrative AI** paradigm is employed to process **digitized sensor signals** from the robot's end effector (e.g., force/torque sensors, joint current feedback)

• **Objective:** The LLM's task is not to determine a broad state (like "indoors/outdoors"), but to execute a **real-time, micro-level physical classification** of the object/environment state during the initial phase of interaction (e.g., the first 100 milliseconds of grasping a handle or pushing a button).


• **Prompt Design:** The prompt utilizes the **procedural guidance** and **fuzzy logic** methods demonstrated in the heart rate detection task. It contains a short sequence of raw numerical feedback (avoiding the token limit constraint associated with long digitized sequences) and instructs the LLM to classify the physical anomaly based on relative changes in the sequence.

• **Example Output:** Based on the input torque sequence, the LLM reasoning determines the precise physical condition, such as "Horizontal-Axis Oven Door, stiff hinge" or "Press-Pull Cabinet, high friction."

• **Dynamic Controller Role:** The LLM functions as a **dynamic feedback controller**, selecting between:

    1. **Basic Library Function:** If the Penetrative AI module reports "Normal," the LLM selects the standard, zero-shot basic motion function (e.g., `base cycle move()`).

    2. **DMP Library Function:** If the Penetrative AI module reports a known anomaly (e.g., "Horizontal-Axis Oven Door"), the LLM selects the complex, **human-demonstrated trajectory (DMP)** corresponding to that exact physical condition (e.g., `dmp_pub(open_oven_handle)`). The DMP library is populated using the HRC teleoperation method.


• **Parameter Adaptation:** If the physical condition is novel or requires slight adjustment, the LLM controller can modify the parameters of an existing DMP or basic function. This leverages the LLM's ability to operate on normalized numerical inputs/outputs and its capacity for autoregressive prediction of low-level control actions. For instance, if the LLM detects "slightly stiffer hinge," it may output a specific, normalized weight wi​ for the DMP forcing term f(x) to increase the force applied during the motion.



### Highlight in our Algorithm



• **Mitigating Visual Errors:** By relying on sensor-derived physical parameters analyzed by Penetrative AI, the system reduces reliance on visual accuracy (e.g., YOLOv5 bounding box inaccuracies), which typically accumulate errors over long-horizon tasks.

• **Generalization Beyond Visual Input:** The robot can generalize learned trajectories (DMP) to new objects that share identical **non-visual physical properties**, even if the visual appearance is different or ambiguous.

• **Reduced HRC Overhead:** Human teleoperation (HRC) remains necessary to instruct and save new complex skills. However, by linking the DMP to a precise _physical condition classification_ (via Penetrative AI), the system minimizes repeated intervention, allowing the LLM to autonomously retrieve and execute the precise dynamic control needed whenever that condition is detected in a zero-shot fashion thereafter.

This integration transforms the LLM from a primarily high-level planneror a general sensor interpreter into a **dynamic, adaptive, and physically grounded feedback policy** for complex manipulation tasks.
