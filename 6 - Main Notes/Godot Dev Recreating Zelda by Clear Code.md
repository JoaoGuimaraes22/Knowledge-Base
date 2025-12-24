11-11-2025 11:30

Status:

Tags: [[godot]] [[game dev]]
# Godot Dev Recreating Zelda by Clear Code

## Initial Notes
- Clear Code Tutorial in Godot https://www.youtube.com/watch?v=AoGOIiBo4Eg&t=19645s

## 3D Basics 
### Intro to 3D Models

+ A 3D object is defined by many points, connected by edges, who can form a face (if 3 points are connected). Via points (vertices), edges and faces you can define complex shapes. You can apply textures to faces. To manipulate animations, models, etc. use Blender
+ A texture is an image. For that we a UV texture. It is a mask on the image that points to parts of it and masks it on the intended model
+ For animations, we need to have a skeleton, with multiple bones, which have multiple vertices
+ File formats for 3D graphics are: glTF & glb, store objects, models and animations. Works well with Godot and Blender, intended to be used.
+ Initial file structure used is:
	+ Root folder:
		+ graphics (contains all textures, blend files, glTf & glb, etc.)
			+ characters
				+ character-name
					+ character-name.blend
					+ character-name.png
			+ environment
			+ grid 
		+ audio (all audio files)
		+ scenes (for the scene files, .tscn)

## The editor and basics

- To move around the camera in the editor:
	- Holding the middle mouse wheel:
		- Rotate
		- With Shift: pan around
		- With Ctrl: zoom
	- Use the "Perspective" button to change to switch to a particular helpful view:
		- eg. top down view
	- When moving a resource:
		- Use Ctrl to move in increments of one
- The AXIS:
	- X: left-right
	- Y: up-down (to go up, increase Y, symmetric to the 2D space)
	- Z: backwards-forwards
- Initial scene creation in scenes -> levels -> level.tscn (3D Node - Level)
- Creating a box mesh:
	- In the Mesh, select Box, and check the Editor:
		- To alter the Box side, click the Box Mesh in the Inspector
		- Change the "Size" x,y,z
		- You can update the subdivisions (more vertices)
			- To check the subdivisions, on "Perspective", click "Display Wireframe" to see all edges and vertices, multiple faces in each subdivided edged space
		- Material
			- If clicked, we can choose from a Standard, ORM and Shader Material
			- Standard Material will be the most used:
				- To define the Colours, Reflection, Transparency
			- ORM is more advanced and powerful
			- Shader -> to create shader
			- For now, using a Standard Material, and on it:
				- Albedo is, basically, the colour
					- In here, you can also add a Texture
					- When changing color, bar on side of the color circle to get darker, bottom to change RGB
				- Metallic and Roughness are used to change the materials response to light (reflectiveness, light spread)
				- UV can change the texture by scale and offset on an object

- On the Node3D, you can change the object's transform Position, Rotation, and Scale (based on size)
- You can create other objects with CSG (constructive solid geometry) nodes
- Change base environment and sun settings on the 3 dots on the editor on your scene, light and sun color/intensity change how every color in your world is. Generally for sunlight you want it to be light
- Creating a function, when dealing with the physics, use the func _physics_process(delta: float) -> void:
- When moving stuff around, multiply by delta:
```
	func _physics_process(delta: float) -> void:
		rotation_degrees += Vector3(0,10,0)
		position += Vector3(10,0,0) * delta
```
- When scaling multiply by delta too

# Player movement & Camera Control

## Basic Player Movement
- Create the CharacterBody3D, with collision shape and mesh
- Create the player script for movement
- Basic input gathering and translating the Vector2 into a 3D movement (no camera):
```gdscript
extends CharacterBody3D

@export var base_speed := 4.0

var movement_input := Vector2.ZERO

func _physics_process(delta: float) -> void:
	movement_input = Input.get_vector("left", "right", "forward", "backward")
	velocity = Vector3(movement_input.x, 0, movement_input.y) * base_speed
	move_and_slide()
```

## Camera Setup
- Create the camera in its own scene
- Create a new Node3D scene, CameraController
- Child of it, a Camera3D
- Add a script to Node3D CameraController
- The goal is to be able to rotate the camera through its axises in (its node3d parent)
```gdscript
extends Node3D

@export var horizontal_accelaration := 2.0
@export var vertical_accelaration := 1.0

func _input(event: InputEvent) -> void:
	if event is InputEventMouseMotion:
		rotate_from_vector(event.relative * 0.0035)  
		

func rotate_from_vector(v: Vector2):
	if v.length() == 0:
		return
	rotation.y -= v.x
	rotation.x -= v.y
	rotation.x = clamp(rotation.x, -0.1, 0.1)
	  
```
- What we are doing:
	- Getting the relative mouse motion
	- If there is mouse motion:
		- calling rotate_from_vector:
			- rotates the camera_controller based on the x, y relative values. We clamp the x value to limit vertical rotation
	- Then, on the Player script, we use the Camera rotation to dictate where player must move to: movement_input = Input.get_vector("left", "right", "forward", "backward").rotated(-camera.global_rotation.y)

- Now, we want the camera to handle collisions with walls and objects
- We want the camera to zoom in and "respect" the walls
- For that, we use the SpringArm3D node. We will replace the node controller with it. It starts at the centre position and tries to push the camera by a certain distance. But, if there is a wall, it will be pushed up to a point, still facing the player
- x rotation for vertical rotation and y rotation for horizontal rotation

## Player Jump
- Use some preset variables for optimal jumps
- Create a basic jump mechanic. 
- ```gdscript
  func jump_logic(delta: float) -> void:
	if is_on_floor():  
		if Input.is_action_just_pressed("jump"):
			velocity.y = -jump_velocity
	var gravity = jump_gravity if velocity.y > 0.0 else fall_gravity
	velocity.y -= gravity * delta
  ```
- New movement mechanic:
```gdscript
func move_logic(delta: float) -> void:
	movement_input = Input.get_vector("left", "right", "forward", "backward").rotated(-camera.global_rotation.y)
	var vel_2d = Vector2(velocity.x, velocity.z)
	
	var is_running : bool = Input.is_action_pressed("run")
	
	if movement_input != Vector2.ZERO:
		var curr_speed = base_speed
		if is_running:
			curr_speed = run_speed
		else:
			curr_speed = base_speed
		vel_2d += movement_input * curr_speed * delta
		vel_2d = vel_2d.limit_length(curr_speed)
		velocity.x = vel_2d.x
		velocity.z = vel_2d.y
	else:
		vel_2d = vel_2d.move_toward(Vector2.ZERO, base_speed * 4.0 * delta)
		velocity.x = vel_2d.x
		velocity.z = vel_2d.y
```
- Getting the current velocity 2d (Current player movement)
	- Then, detecting if there's any input
		- Then we accelerate from our current speed to our max speed
		- We then update velocity.x and velocity.z
	- If not
		- We deaccelerate to 0 

# Player animations
- Use the .glb file to create an inherited scene and use that as the inherited model (so it can be editable)
- This inherited model will have the animations, but if you want to loop them and other functions use the base model and reimport it
- We now need to use animations. Play the animations on your move functions
- Rotate model based on camera rotation
- Play around with the target angle rotation to get the correct model rotation
- Create an animation tree to handle transitions between animations
- We will initially use a node state machine 
- 

# Summary by Claude
## 1. 3D Model Fundamentals

### Core Concepts

- **Geometry Structure**: 3D objects are built from:
    - **Vertices** (points in 3D space)
    - **Edges** (connections between vertices)
    - **Faces** (formed when 3+ vertices connect)
- **Textures**: Images applied to faces using UV mapping
    - **UV Texture**: A 2D mask that maps parts of an image onto the 3D model
- **Animation Requirements**:
    - Skeleton system with bones
    - Vertices rigged to bones for deformation

### File Formats

- **Recommended**: glTF & GLB
    - Store objects, models, and animations
    - Excellent compatibility with Godot and Blender

### Tools

- **Blender**: Primary tool for manipulating models, animations, and assets

---

## 2. Project Structure

```
Root/
├── graphics/
│   ├── characters/
│   │   └── character-name/
│   │       ├── character-name.blend
│   │       └── character-name.png
│   ├── environment/
│   └── grid/
├── audio/
└── scenes/
    └── levels/
        └── level.tscn
```

---

## 3. Godot Editor Navigation

### Camera Controls

- **Middle Mouse Button**:
    - Hold: Rotate view
    - Hold + Shift: Pan
    - Hold + Ctrl: Zoom
- **Perspective Menu**: Quick view switching
    - Top-down view
    - Front/side orthographic views
- **Movement Snapping**: Hold Ctrl to move in increments of 1

### Display Options

- **Wireframe Mode**: View all edges and vertices
    - Access via: Perspective → Display Wireframe

---

## 4. 3D Coordinate System

| Axis  | Direction                               |
| ----- | --------------------------------------- |
| **X** | Left ↔ Right                            |
| **Y** | Down ↔ Up (increases upward, unlike 2D) |
| **Z** | Back ↔ Forward                          |

---

## 5. Creating and Editing Meshes

### Box Mesh Setup

1. Add MeshInstance3D node
2. Select BoxMesh in Inspector
3. Configure properties:
    - **Size**: Adjust X, Y, Z dimensions
    - **Subdivisions**: Increase vertex density (visible in wireframe)

### Materials (Standard Material 3D)

- **Albedo**: Base color and texture
    - Use color picker (side bar for darkness, bottom for RGB)
    - Can assign texture images
- **Metallic & Roughness**: Control light interaction
    - Reflectiveness and light spread
- **UV Settings**: Scale and offset textures on objects

### Material Types

- **Standard Material**: Most common (colors, reflection, transparency)
- **ORM Material**: Advanced, more powerful
- **Shader Material**: Custom shaders

---

## 6. Node Transforms

Every Node3D has three transform properties:

- **Position**: X, Y, Z location
- **Rotation**: Rotation in degrees/radians
- **Scale**: Size multiplier (based on original size)

---

## 7. Additional Tools

### CSG Nodes

- **Constructive Solid Geometry**: Create complex shapes through boolean operations

### Environment Settings

- Access via 3-dot menu in scene editor
- Configure:
    - **Sunlight**: Color and intensity (affects all world colors)
    - **Environment**: Ambient lighting and sky
- **Tip**: Use lighter tones for sunlight to maintain visibility

---

## 8. Scripting Essentials

### Physics Loop

gdscript

```gdscript
func _physics_process(delta: float) -> void:
    # Always multiply movement by delta for frame-independent motion
    rotation_degrees += Vector3(0, 10, 0) * delta
    position += Vector3(10, 0, 0) * delta
```

### Key Principles

- **Delta Time**: Always multiply position/rotation/scale changes by `delta`
    - Ensures consistent speed regardless of framerate
    - Units: per second

---

## Quick Reference

|Task|Action|
|---|---|
|Rotate camera|Middle mouse + drag|
|Pan camera|Middle mouse + Shift + drag|
|Zoom|Middle mouse + Ctrl + drag|
|Snap movement|Hold Ctrl while moving|
|View wireframe|Perspective → Display Wireframe|
|Change views|Use Perspective dropdown menu|

---

**Initial Scene Setup**: Create `scenes/levels/level.tscn` with a Node3D as root (named "Level")

## 9. Basic Player Movement

### Initial Setup

1. Create **CharacterBody3D** node
2. Add child nodes:
    - **CollisionShape3D** (define hitbox)
    - **MeshInstance3D** (visual representation)
3. Attach player script

### Simple Movement (No Camera)

gdscript

````gdscript
extends CharacterBody3D

@export var base_speed := 4.0
var movement_input := Vector2.ZERO

func _physics_process(delta: float) -> void:
    movement_input = Input.get_vector("left", "right", "forward", "backward")
    velocity = Vector3(movement_input.x, 0, movement_input.y) * base_speed
    move_and_slide()
```

**How it works**:
- `Input.get_vector()` returns normalized Vector2 from input actions
- Convert 2D input (x, y) → 3D movement (x, 0, z)
- Y-axis stays 0 (no vertical movement yet)

---

## 10. Camera System

### Camera Scene Structure
```
CameraController (Node3D) [Root Scene]
└── Camera3D
````

### Camera Controller Script

gdscript

```gdscript
extends Node3D

@export var horizontal_acceleration := 2.0
@export var vertical_acceleration := 1.0

func _input(event: InputEvent) -> void:
    if event is InputEventMouseMotion:
        rotate_from_vector(event.relative * 0.0035)

func rotate_from_vector(v: Vector2):
    if v.length() == 0:
        return
    
    rotation.y -= v.x  # Horizontal rotation
    rotation.x -= v.y  # Vertical rotation
    rotation.x = clamp(rotation.x, -0.1, 0.1)  # Limit vertical look
```

### Camera Logic Breakdown

1. **Input Detection**: Capture mouse motion events
2. **Relative Movement**: `event.relative` gives mouse delta (pixels moved)
3. **Sensitivity**: Multiply by `0.0035` to convert pixels to radians
4. **Rotation**:
    - **Y-axis**: Horizontal camera rotation (left/right)
    - **X-axis**: Vertical camera rotation (up/down)
5. **Clamping**: Prevent excessive vertical rotation

### Integrating Camera with Player Movement

Update player script to rotate input based on camera:

gdscript

```gdscript
movement_input = Input.get_vector("left", "right", "forward", "backward")
    .rotated(-camera.global_rotation.y)
```

- Movement now relative to camera direction
- Forward moves in camera's forward direction

---

## 11. Camera Collision Handling

### SpringArm3D Setup

- **Replace** Node3D controller with **SpringArm3D**
- **Purpose**: Automatically handle camera-wall collisions
- **Behavior**:
    - Extends camera to set distance from player
    - Compresses when obstacles detected
    - Maintains player visibility

### Configuration

- **X rotation**: Vertical tilt
- **Y rotation**: Horizontal rotation
- Camera stays child of SpringArm3D

---

## 12. Jump Mechanics

### Jump Variables (Add to player script)

gdscript

```gdscript
@export var jump_velocity := 4.5
@export var jump_gravity := 9.8
@export var fall_gravity := 15.0  # Falls faster than jumps
```

### Jump Logic

gdscript

```gdscript
func jump_logic(delta: float) -> void:
    if is_on_floor():
        if Input.is_action_just_pressed("jump"):
            velocity.y = -jump_velocity
    
    # Use different gravity for jump/fall
    var gravity = jump_gravity if velocity.y > 0.0 else fall_gravity
    velocity.y -= gravity * delta
```

**Key Concepts**:

- **`is_on_floor()`**: Built-in CharacterBody3D method
- **Asymmetric Gravity**: Jump feels floatier, fall feels snappier
- **Negative velocity.y**: Upward movement (Y+ is up in Godot 3D)

---

## 13. Advanced Movement System

### Enhanced Movement with Acceleration/Deceleration

gdscript

```gdscript
@export var base_speed := 4.0
@export var run_speed := 8.0

func move_logic(delta: float) -> void:
    movement_input = Input.get_vector("left", "right", "forward", "backward")
        .rotated(-camera.global_rotation.y)
    
    var vel_2d = Vector2(velocity.x, velocity.z)  # Current horizontal velocity
    var is_running : bool = Input.is_action_pressed("run")
    
    if movement_input != Vector2.ZERO:
        # Determine target speed
        var curr_speed = run_speed if is_running else base_speed
        
        # Accelerate toward target speed
        vel_2d += movement_input * curr_speed * delta
        vel_2d = vel_2d.limit_length(curr_speed)
        
        # Apply to 3D velocity
        velocity.x = vel_2d.x
        velocity.z = vel_2d.y
    else:
        # Decelerate to stop
        vel_2d = vel_2d.move_toward(Vector2.ZERO, base_speed * 4.0 * delta)
        velocity.x = vel_2d.x
        velocity.z = vel_2d.y
```

### Movement Breakdown

**With Input**:

1. Get 2D velocity from current 3D velocity
2. Check if running
3. Add input-based acceleration
4. Clamp to max speed using `limit_length()`
5. Update 3D velocity

**Without Input**:

1. Decelerate using `move_toward()`
2. Deceleration rate: `base_speed * 4.0` (faster stop)
3. Update 3D velocity

### Why This Approach?

- **Smooth acceleration**: Gradual speed increase feels natural
- **Speed limiting**: Prevents infinite acceleration
- **Quick stopping**: 4x base_speed deceleration = responsive controls
- **2D calculation**: Easier to work with horizontal movement separately

---

## Complete Player Script Structure

gdscript

```gdscript
extends CharacterBody3D

# Movement
@export var base_speed := 4.0
@export var run_speed := 8.0

# Jump
@export var jump_velocity := 4.5
@export var jump_gravity := 9.8
@export var fall_gravity := 15.0

# References
@onready var camera = $CameraController  # Adjust path as needed

var movement_input := Vector2.ZERO

func _physics_process(delta: float) -> void:
    jump_logic(delta)
    move_logic(delta)
    move_and_slide()

func jump_logic(delta: float) -> void:
    # [Jump code from above]
    
func move_logic(delta: float) -> void:
    # [Movement code from above]
```

---

## Key Takeaways

|Component|Purpose|
|---|---|
|**CharacterBody3D**|Player physics body with collision|
|**SpringArm3D**|Camera collision handling|
|**Camera3D**|Actual viewport|
|**Rotation axes**|Y = horizontal, X = vertical|
|**velocity.y**|Jump/gravity (positive = down)|
|**Vector2 for XZ**|Simplifies horizontal movement math|
|**Delta multiplication**|Frame-independent movement|

**Input Actions Needed**:

- `left`, `right`, `forward`, `backward` (movement)
- `jump` (jumping)
- `run` (sprint modifier)
# References


