21-11-2025 19:50

Status:

Tags: [[game dev]] [[godot]]

# Godot - Third Person Controller Tutorial ( 2023 ) by Lukky

## Environment setup

* Create floor and boxes using csg3 boxes
* Drago floor and boxes to origin
* Grab the "green" square to move with shift
* Add the materials in the Geometry material not the CSGBox3D Material
* Enable collisions on boxes

## Player
* Add a CharacterBody3D scene, with collision
* Drag to origin
* Add a "visuals" Node3D, where you will add the model (mixamo_base)
	* Forwards in Godot is Z negative, have player face Z negative (need to rotate 180º)
	* 


# References
https://www.youtube.com/watch?v=EP5AYllgHy8



