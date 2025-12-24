22-11-2025 01:00

Status:

Tags: [[game dev]] [[godot]] [[project]]

# Dream Game

## Core Game 
- War simulator, where you have powers
- BannerLord inspired battles +  romance and diplomacy

## Initial Setup

### Character controller
- Initial setup, pipeline and architecture for future development
#### Split Visuals, Model and Input (MVC pipeline) - initial setup:
- Setup the character in blender and animations named correctly
- Split the Player into visuals and model
- Get the .glb file:
	- Split the Joints and Surface into a separate scene, Visuals
	- Get the Skeleton (previously BoneMapped with a BoneMap scene) and Animations into Model
- Inject the model skeleton to work with the visuals, example code
```gdscript
extends Node3D
class_name PlayerVisuals

@onready var model: PlayerModel
@onready var alpha_joints: MeshInstance3D = $Alpha_Joints
@onready var alpha_surface: MeshInstance3D = $Alpha_Surface

func _process(_delta: float) -> void:
	pass

func accept_model(_model: PlayerModel):
	model = _model
	alpha_joints.skeleton = _model.skeleton.get_path()
	alpha_surface.skeleton = _model.skeleton.get_path()  
```
- This dependency injection pipeline is and will be used a lot
- INPUT:
	- Input node collects all Input from the player
	- Camera movement, clicks, etc.
- MODEL STATES:
	- States have several Moves
	- A Move base class:
```gdscript
# Move.gd
extends Node
class_name Move

# References (will be injected by container)
var player : CharacterBody3D
var animator : AnimationPlayer

# Identity
@export var move_name : String  # "idle", "run", "sprint"
@export var animation_name : String = ""

@export var gravity : float = ProjectSettings.get_setting("physics/3d/default_gravity")

func check_relevance(input: InputPackage) -> String:
	return "implement it"

func update(input: InputPackage, delta: float) -> void:
	pass
	
func on_enter_state() -> void:
	if animation_name != "" and animator:
		animator.play(animation_name)
	
func on_exit_state() -> void:
	pass
```
## States, animations, split torso and legs

### States
- You have a States node, the base states controller, used mainly for dependency injection. Each state will have its own logic.
	- Thin container, smart states
	- **State Machines:** Each state encapsulates behavior and transition logic, avoiding large, nested if-statements.
	- **Base State Class:** Contains methods like `check_relevance` (for transition logic), `update` (for simulation), and optional `on_enter` and `on_exit` hooks.
- **Input Actions as Strings:** Instead of converting inputs into booleans, actions are forwarded as strings in an array, enabling flexible and extensible input handling.
- **State Examples:** Idle, Running, and Jumping states illustrate typical behaviours and transitions.
- **Priority System:** Actions are prioritized via a dictionary mapping action strings to priority weights, allowing states to resolve transitions based on action importance.
- **Decoupling Input and Simulation:** Input gathering contains linear, simple if-statements that append actions to an array, while simulation logic reacts only to this structured data.

# References




