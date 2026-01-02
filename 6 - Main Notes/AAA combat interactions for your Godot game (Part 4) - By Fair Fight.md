18-12-2025 14:29

Status:

Tags: [[game dev]] [[godot]] [[ai note]]

# AAA combat interactions for your Godot game (Part 4) - By Fair Fight

### Summary

This video presents an in-depth architectural overview of building a **character controller system with combat mechanics** using Godot Engine. It focuses on clean design patterns, state management, resource handling, and communication between game entities. The narrator critiques common but flawed approaches like excessive global signals or centralized "game manager" logic, advocating instead for **state machines and direct method calls** for entity interaction.

---

### Key Concepts and Architecture Decisions

- **State Machine as Core Entity Logic**  
  The character controller is modeled as a **state machine**, where only one active state governs the character’s reaction and behavior at a time. This avoids unnecessary broadcasting or signal spamming.

- **Discrete Time Model**  
  Game logic updates are tied to **physics update intervals**, ensuring transitions happen once per frame and preventing mid-frame spamming of transitions.

- **Transition Logic Workflow**  
  Each frame transition asks:
  1. Does the past state force a transition?
  2. Does current input or context modify behavior?
  3. Default to the base state behavior if none apply.

- **Parameter Animation via Back Animations**  
  To manage complex, time-dependent parameters across states:
  - Simple method: Use default and windowed parameters with conditional getters.
  - Advanced method: Use a **non-playing animation player** purely as a parameter database, where animations define parameter changes over time without actual playback. States query these animations for parameter values at given times.

- **Attacker-Victim Communication and Hit Detection**  
  Combat interactions require precise messaging:
  - Use a **Hitbox** following the character’s root bone.
  - Weapons are areas that detect collisions only during attack animations.
  - Only one hit per attack per victim is allowed, managed by tracking contacts in a victim list to avoid multiple hits from continuous collision.
  - A **Hit Package** class encapsulates damage, source, animation, and whether the hit is parryable.
  - Communication flows from attacker’s move → weapon hit detection → victim’s current state → reaction methods.

- **Parrying and Reactions**  
  Attacks check for parry windows via overridden hit methods and can call back the attacker’s reaction method if parried. This layered approach allows flexible, complex combat interactions such as parries and counters.

---

### Resource System and Integration

- **Core Resources: Health and Stamina**  
  These are modeled as separate systems with **encapsulated setters** for gain and loss to prevent accidental modifications.

- **One-to-One Relationship: Resources and States**  
  Each state can influence resource regeneration and depletion, while resource values govern state transitions (e.g., death state triggered on zero HP).

- **Resource Validation in Input and Combos**  
  - Inputs invalidated by insufficient resources are removed before state transitions.
  - Resource checks happen also during combo selections to avoid deadlocks or impossible moves.
  - Default lifecycle includes resource checks for non-attack actions like sprinting.

- **Encapsulation Advantage**  
  Centralizing damage and resource management reduces duplication and enforces consistent logic, such as a single death check rather than per-state checks.

---

### Animation and Root Motion Handling

- **Root Motion Implementation**  
  Instead of relying solely on Godot’s built-in root motion (which has unclear timing semantics), the narrator implements custom root motion:
  - Calculates delta position between animation frames.
  - Applies rotation matching character orientation.
  - Cancels vertical component to ensure the character stays grounded.
  
  This improves control and consistency, enabling features like interruption during strikes and correct floor detection.

---

### Move and State Modularization

- **Move Scripts vs Scenes**  
  Currently, most moves are scripts. The narrator proposes converting some to scenes when:
  - More than three parameters differ.
  - Backend animation changes are required.
  
  This modular approach aids scalability and flexibility in move variations.

- **Parry and Repost Implementation**  
  - Parry state pushes parried humanoid into a **parry group**.
  - Repost (counterattack after parry) is a combo input from the parry state.
  - Repost logic detects nearby parried humanoids and initiates grab-like actions.
  
  This system prioritizes parry and repost as special states in combat flow.

---

### Challenges and Development Notes

- Managing complex state transitions and resource validation is non-trivial, especially with combos and overlapping inputs.
- The backend animation parameter approach significantly eases balancing and tweaking.
- The narrator hints at plans to move from a code-heavy system to a more visual framework integrated with Godot Editor tools, but postpones it to avoid overwhelming the tutorial.
- Death is currently a simple fake state lasting a few seconds before resetting to idle.
- The system is designed for extensibility, allowing easy override of default behaviors per state.

---

### Timeline of Major Topics Covered

| Time Range       | Topic                                                                                   |
|------------------|-----------------------------------------------------------------------------------------|
| 00:00:00–00:01:16| Introduction to character controller with stamina, HP, and signals critique             |
| 00:01:16–00:02:35| State machine logic, discrete time management, transition checks                        |
| 00:02:35–00:04:50| Parameter animation methods: simple window vs animation-based parameter database        |
| 00:04:50–00:07:59| Combat messaging system: hitboxes, hit packages, weapon contact filtering, reaction flow|
| 00:07:59–00:09:45| Resource system design: encapsulation, setters, one-to-one resource-state relation       |
| 00:09:45–00:11:37| Resource validation in transitions and combos, input filtering, sprint example          |
| 00:11:37–00:14:42| Root motion handling: custom implementation, vertical component cancellation             |
| 00:14:42–00:16:04| Move modularization, parry and repost state logic, combo integration                    |

---

### Key Insights

- **Avoid global signals and game manager anti-patterns**; use direct method calls within a single controlling state.
- **Discrete frame-based updates** prevent transition spamming and ensure logical consistency.
- Using **non-playing animation players as parameter databases** decouples animation timing from logic and simplifies parameter management.
- Combat requires **precise messaging and filtering** to avoid multiple hits and properly handle parries and counters.
- **Resources are integral to state logic**; their encapsulation promotes clean, maintainable code.
- Custom root motion control enhances animation fidelity and gameplay responsiveness.
- Modular move implementation supports scalability in complex combat systems.

---

### Conclusion

The video offers a **robust, architecturally sound approach to character combat systems** in Godot, emphasizing scalability, maintainability, and clean separation of concerns. It balances code-based logic with animation-driven parameters and integrates resource management deeply into state and transition handling. While some advanced features and visual tooling are planned for the future, the current foundation is strong and adaptable for complex gameplay scenarios.

# References

[[Character Controller (Part 3) - by Fair Fight]]

https://www.youtube.com/watch?v=b3uFSh3GASs&list=PLzia-gCwY2G2AXn2hvAjKzLH2_rCvVQFQ&index=4

https://notegpt.io


