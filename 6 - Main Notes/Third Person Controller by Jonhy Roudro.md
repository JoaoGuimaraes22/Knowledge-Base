21-11-2025 20:11

Status:

Tags: [[game dev]] [[godot]]

# Third Person Controller

## Project Overview
* Movement states:
	* stand, walk, run and sprint
* MovementState:
	* id, movement_speed, accelearation, camera_fov, animation_speed
* Player -> movement_state:
	* Movement Controller
	* Animation Controller
	* Camera Controller
## Initial steps
- Create CSG box floor and boxes with collision enabled
- Import base model .glb as inherited scene and save as is
- Create a new Player CharacterBody3D
	- Create a CollisionSHape
	- Create a Node3D - MeshRoot
		- Mesh schene goes here
	- Create a Node3D - CamRoot
		- Create a Node3D - CamYaw (handles horizontal rotation)
			- Create a Note3D - CamPitch (handles vertical rotation)
	- Snap CamRoot to initial preferred position

## 3rd Person Camera
- Add a script to the CamRoot, camera_controller.gd
- Create the necessary variables:
- Create the input function
- Basically, when mouse motions is detected, yaw and pitch are modified
	- The event.relative is the mouse movement relative to the last frame
	- lerp, used to smooth transitions  (Variant lerp(from: Variant, to: Variant, weight: Variant)
- we then, clamp the pitch (vertical axis) 
- (We prob should clamp the yaw too)
- Finnaly, add a springArm3D as a parent of the camera (it defines how far the camera will be)
```gdscript
extends Node3D

@onready var yaw_node: Node3D = $CamYaw
@onready var pitch_node: Node3D = $CamYaw/CamPitch
@onready var camera: Camera3D = $CamYaw/CamPitch/SpringArm3D/Camera3D

var yaw: float = 0
var pitch: float = 0

var yaw_sensitivity: float = 0.07
var pitch_sensitivity: float = 0.07

var yaw_acceleration: float = 15.0
var pitch_acceleration: float = 15.0

var pitch_max: float = 75
var pitch_min:float = -55

func _ready() -> void:
	Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func _input(event: InputEvent) -> void:
	if event is InputEventMouseMotion:
		yaw += event.relative.x * yaw_sensitivity
		pitch += event.relative.y * pitch_sensitivity
	if event.is_action_pressed("show_mouse"):
		Input.set_mouse_mode(Input.MOUSE_MODE_VISIBLE)
		
func _physics_process(delta: float) -> void:
	pitch = clamp (pitch, pitch_min, pitch_max)
	yaw_node.rotation_degrees.y = lerp(yaw_node.rotation_degrees.y, yaw, yaw_acceleration * delta)
	pitch_node.rotation_degrees.x = lerp(pitch_node.rotation_degrees.x, pitch, pitch_acceleration * delta)
```

## Using resource to create State
- Can create a new Resource class base class
- Like for example, a base movement class all of the rest will derive from
```gdscript
extends Resource
class_name MovementState

@export var id: int
@export var movement_speed: float
@export var accelaration : float = 6
@export var camera_fov: float = 75
@export var animation_speed : float
```
- Then, create a New Resource, using this new movement_state resource, to create a new state
- Duplicate for walk, run, stand, sprint, and set the values for each
- Then, add a script to the player
- Add an exported dictionary `@export var movement_states : Dictionary`
- In the editor inspector, add manually key-value pairs
- 
# TIPS
- Ctrl + Alt + Down to duplicate line down on the code editor
- 
# References




