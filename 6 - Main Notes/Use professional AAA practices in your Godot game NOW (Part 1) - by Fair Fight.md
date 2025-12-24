09-12-2025 19:24

Status:

Tags: [[game dev]] [[godot]] [[ai note]]

# Use professional AAA practices in your Godot game NOW Part I - by Fair Fight

### Summary

This tutorial focuses on building a well-structured **3D player character controller** in the Godot engine, emphasizing the importance of **code organization and maintainability** through abstraction and the **Model-View-Controller (MVC)** pattern. The video begins by critiquing poorly organized code with numerous if statements and introduces a foundational concept: the player character is a system controlled by human input rather than autonomous scripts.

The Godot engine provides a basic 3D character controller template that already loosely follows MVC principles by separating input handling, model simulation, and visualization. However, the tutorial points out the need to improve this by reorganizing the update process: first **collect and compress all inputs**, then pass them to the model for simulation, and only after that update the visualization. This separation clarifies responsibilities, making the code modular and easier to maintain.

To handle complex gameplay mechanics (e.g., combat systems like Souls-like games), the tutorial recommends moving away from monolithic if-statement-heavy update functions toward **state machines**, which naturally model character states and transitions. State machines reduce complexity by breaking down behavior into manageable states with dedicated logic, improving scalability and readability.

The tutorial discusses two state machine architectures:
- **Thin container with smart states:** The container handles transitions, and states contain detailed logic.
- **Smart container with thin states:** The container holds most logic, delegating simple updates to states.

The presenter chooses the **thin container with smart states** approach here.

### Key Concepts and Implementation Details

- **Player Character:** Defined as a system controlled by player input, simulated by a model, and visualized separately.
- **MVC Pattern:** Separates input gathering (controller), game state and logic (model), and rendering (view).
- **Godot Node Hierarchy:** Child nodes inherit transformations from parents, facilitating visualization updates.
- **Input Handling:** Inputs are compressed into a single object or array, passed cleanly to the model.
- **State Machines:** Each state encapsulates behavior and transition logic, avoiding large, nested if-statements.
- **Base State Class:** Contains methods like `check_relevance` (for transition logic), `update` (for simulation), and optional `on_enter` and `on_exit` hooks.
- **Input Actions as Strings:** Instead of converting inputs into booleans, actions are forwarded as strings in an array, enabling flexible and extensible input handling.
- **State Examples:** Idle, Running, and Jumping states illustrate typical behaviors and transitions.
- **Priority System:** Actions are prioritized via a dictionary mapping action strings to priority weights, allowing states to resolve transitions based on action importance.
- **Decoupling Input and Simulation:** Input gathering contains linear, simple if-statements that append actions to an array, while simulation logic reacts only to this structured data.

### Timeline Table of Tutorial Progression

| Time       | Topic/Action                                                                                  |
|------------|-----------------------------------------------------------------------------------------------|
| 00:00-00:38| Introduction and critique of disorganized physics update function                             |
| 00:38-01:48| Definition of player character and introduction to MVC pattern                                |
| 01:48-02:25| Godot basic 3D character controller template and node hierarchy                              |
| 02:25-03:32| Proposal to reorder update steps and separate input, model, and visualization code           |
| 03:32-04:48| Need for abstraction and introduction of state machines for complex character behaviors       |
| 04:48-06:32| Explaining state machines as a way to manage many character states                            |
| 06:32-07:38| Architecture choices: thin container with smart states vs smart container with thin states    |
| 07:38-08:45| Godot-specific considerations: state storage and initialization                              |
| 08:45-09:52| Implementing base state class and improving input handling approach                           |
| 09:52-10:24| Detailed example states: idle, running, and jumping                                           |
| 10:24-12:51| Addressing code duplication, introducing action priority sorting and enhancing transition logic|
| 12:51-13:55| Benefits of state-based design and example of extending jump state into start and fall phases  |

### Comparison of State Machine Architecture Approaches

| Aspect                     | Thin Container with Smart States                       | Smart Container with Thin States                         |
|----------------------------|--------------------------------------------------------|----------------------------------------------------------|
| Transition Logic Location  | In individual states                                   | In the container class                                    |
| Model Update Responsibility| Delegated to states                                    | Mostly handled by container                               |
| Use Case                   | Small number of tightly coupled states                 | Highly centralized control of states                      |
| Ease of Extensibility      | Easier to extend states individually                   | Easier to manage transitions centrally                    |
| Complexity                 | More distributed logic                                 | More centralized logic                                    |

### Key Insights

- **Separating input, model, and visualization clearly reduces complexity and improves maintainability.**
- **State machines are essential for managing complex character behaviors beyond simple if-statement logic.**
- **Using strings for input actions rather than booleans increases flexibility and extensibility.**
- **Prioritizing player actions through a data-driven dictionary allows cleaner transition logic and removes redundant conditional checks.**
- **Breaking down large update functions into small modular states is liberating and simplifies feature implementation.**
- **Godot’s node system and signal processing are leveraged for organizing states and handling timers or animations.**

### Conclusions

The tutorial demonstrates a practical, scalable approach to building player character controllers in Godot using **MVC principles**, **state machines**, and **data-driven input handling**. This methodology helps avoid the pitfalls of bulky, tangled code and facilitates adding new features or complex behaviors in an organized way.

By embracing **modularity, prioritization, and clear separation of concerns**, developers can create robust, maintainable game character systems suitable for a wide range of genres and gameplay complexities. The presented concepts serve as a foundation for more advanced implementations like layered states, animation blending, and combat mechanics.

*Uncertain details such as precise code implementations or performance benchmarks are not specified in the transcript.*


# References

[[Build your Godot characters like AAA titles do (Part 2) - by Fair Fight]]

https://notegpt.io/

https://www.youtube.com/watch?v=FgO5edghqRE&list=PLzia-gCwY2G2AXn2hvAjKzLH2_rCvVQFQ&index=1



