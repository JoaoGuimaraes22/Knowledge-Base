18-12-2025 14:08

Status:

Tags: [[game dev]] [[godot]] [[ai note]]

# How to build AAA combat under 15 minutes in Godot - by Fair Fight

### Summary

This video tutorial focuses on developing a robust character controller system, emphasizing the creation of **jumping mechanics** and a **3D combat system** using skeletal animations, state management, and input translation. The tutorial spans approximately 28 minutes and aims to deliver a functional result featuring **three jump types**, **three strike combos**, and camera integration by the end.

---

### Key Insights and Workflow

- **Core Objective:** Build a character controller capable of nuanced jumping and combat actions, prioritizing solving the hardest problems first—namely, **jump animations and timing synchronization**.
- **Jump Mechanics:**
  - Utilizes **two jump cycle animations** from Mixamo, dissected into jump start, mid-air, and landing phases.
  - Introduces a **Base Class modification** to record state activation time, allowing the system to track how long an animation/state has been active.
  - Implements **animation-end detection** by querying the skeletal animation timeline rather than relying solely on physics-based "is on floor" checks.
  - Uses **raycasting from a bone's position downward** to measure the character’s height above the floor for more accurate landing detection.
  - Addresses the issue of **root motion teleportation** causing unnatural character movement by opting to remove root motion from animations and simulate motion via physics or code.
  - Recommends **snap root bone positions to static values** in animations to avoid unwanted skeleton impulse.
  - Distinguishes between jump and sprint jump by modifying the landing logic based on horizontal speed.
  - Custom blending of animations is utilized to smooth transitions, but the tutorial highlights that **well-prepared assets and animator collaboration** produce superior results.

- **Combat System Architecture:**
  - Recognizes the **disparity between player inputs and numerous possible states**, necessitating an intermediate layer to translate inputs contextually based on the current weapon and situation.
  - Introduces a **combat system layer** that:
    - Receives raw inputs.
    - Asks the current weapon to translate inputs into basic moves.
    - Manages **combo logic** through a base combo class that checks if combos are triggered based on input sequences and context.
  - States maintain **transition logic** but rely on the combat system for input translation, preserving architectural cleanliness.
  - Combos are modelled as **nested structures**, allowing chaining of attacks and complex behaviors like rolling attacks.
  - To handle reusable and universal states like **stagger or roll**, the system employs **scriptable combo behaviors** (Godot scenes with editable constants for timing windows).
  - The system supports **dynamic combo queuing**, enabling players to queue inputs during animations for fluid gameplay.

- **Animation and Weapon Handling:**
  - Weapons are attached via sockets to character bones with optional true attachment and rotation corrections for visual accuracy.
  - Visual models are separated from collision models and handled as child nodes to maintain clean logic.
  - Animations for attacks deliberately have no root motion to keep physics simulation consistent.

- **Performance and Scalability:**
  - The system is **lightweight enough for potential AAA use**, with minimal frame rate impact.
  - A **memory leak** was detected due to input packages not being deleted properly after emission, which is fixable.
  - The overall design favors **pragmatism ("good enough school") over overly complex clean code**, balancing maintainability and performance.

- **Camera Integration:**
  - Adding a third-person camera to the controller is trivial, requiring minimal code.
  - For advanced camera setups, the tutorial references additional resources.

---

### Timeline Table of Major Development Stages

| Time Range        | Content Focus                                    | Key Actions/Concepts                                  |
|-------------------|-------------------------------------------------|------------------------------------------------------|
| 00:00:00–00:01:07 | Introduction & Overview                           | Tutorial goals, jump, strike, camera setup           |
| 00:00:34–00:02:07 | Jump Animation Setup & Timing                     | Animation slicing, state timing, base class changes  |
| 00:02:06–00:04:02 | Landing Detection & Model Movement Issues         | Raycasting for floor distance, root motion problems  |
| 00:04:02–00:06:50 | Root Motion Fix & Sprint Jump Logic               | Removing root motion, snapping root bone positions, sprint jump adjustments |
| 00:06:22–00:09:51 | Combat System Design & Input Translation          | Combat input layer, weapon input mapping, combo logic|
| 00:09:52–00:13:54 | Combo System Implementation & Scriptable Behaviors | Nested combos, stagger system, input queuing         |
| 00:13:53–00:15:42 | Performance Review & Camera Setup                  | Memory leak fix, lightweight design, camera integration|

---

### Definitions and Concepts Table

| Term                 | Definition/Explanation                                                                                 |
|----------------------|-------------------------------------------------------------------------------------------------------|
| Root Motion          | Animation-driven movement of a character’s root bone, often causing unwanted position changes if uncontrolled. |
| State Activation Time | Timestamp marking when a particular animation or state began, used for timing transitions.             |
| Combo                | A sequence of inputs resulting in chained attack moves, managed through a combo class and state logic. |
| Input Package        | Encapsulated player input data passed through the combat system for contextual interpretation.         |
| Scriptable Combo     | A reusable combo behavior defined in Godot scenes, allowing easy editing of timing and parameters.     |
| Raycast              | A physics query casting a ray to detect the distance to the floor, used here for landing detection.     |

---

### Highlights

- **Emphasis on animation timing and synchronization with state logic** to create responsive and natural character movement.
- **Innovative use of raycasting from a bone position to determine height over ground**, avoiding unreliable physics flags.
- **Clear separation between input handling, combat logic, and state transitions** enhances modularity and scalability.
- **Nested combo system** allows complex attack chains and flexible input queuing.
- **Performance-conscious design**, balancing code cleanliness with practical constraints.
- **Simple integration of third-person camera**, enabling quick visual setup.

---

### Conclusions

- The tutorial **successfully builds a functional foundation** for a character controller featuring jumping and combat mechanics.
- Effective use of **state timing, animation blending, and input translation layers** produces fluid gameplay.
- **Avoiding root motion conflicts by decoupling animation from physics** is critical for realistic character control.
- The combat system’s **thin, modular design** supports extensibility, combo complexity, and varied weapon logic.
- Performance remains stable despite the added complexity, and the system is adaptable for larger projects.
- The approach encourages **collaboration with animators** to improve asset quality, which significantly impacts final results.

---

### *Not Specified/Uncertain*

- Specific numerical values for jump velocities, raycast distances, or animation timings are not detailed.
- The exact method for fixing the memory leak is mentioned but not fully elaborated.
- Details about integrating character resources and combat state flags for multi-character interactions are acknowledged as future work.

---

This comprehensive tutorial provides a practical, scalable framework for character control in 3D games, balancing technical depth with performance and modularity considerations.

# References

[[Build your Godot characters like AAA titles do (Part 2) - by Fair Fight]]

[[AAA combat interactions for your Godot game (Part 4) - By Fair Fight]]

https://www.youtube.com/watch?v=RmdtA7G7cAE&list=PLzia-gCwY2G2AXn2hvAjKzLH2_rCvVQFQ&index=3

https://notegpt.io/


