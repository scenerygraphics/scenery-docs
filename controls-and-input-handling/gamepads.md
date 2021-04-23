---
description: >-
  This page describes input bindings for gamepads, how to add and modify them,
  and lists mappings for different gamepad controllers.
---

# Gamepads

### Axis and Buttons

Gamepad axis and buttons are handled differently in scenery. Axis are used for analog input and can e.g. lead to movement, or rotation of the camera, or of an object. Buttons in turn can trigger simple behaviours.

#### GamepadClickBehaviour

A `GamepadClickBehaviour` can be used to e.g. toggle functionality:

```kotlin
val toggleProteins = object : GamepadClickBehaviour {
    override fun click(p0: Int, p1: Int) {
        // finds the currently active protein, un-highlights it
        activeProtein.children.forEach {
            if(it is BoundingGrid) {
                it.gridColor = Vector3f(0.0f, 0.0f, 0.0f)
            }
        }

        // selects the new active protein
        activeProtein = if(activeProtein.name == "2zzm") {
            scene.find("4yvj") as Mesh
        } else {
            scene.find("2zzm") as Mesh
        }

        // highlights the newly active protein
        activeProtein.children.forEach {
            if(it is BoundingGrid) {
                it.gridColor = Vector3f(1.0f, 0.0f, 0.0f)
            }
        }

    }
}
```

This snippet has been taken from `ProteinComparisonExample`. When this behaviour is triggered, another object in the scene will be highlighted. In order to bind this behaviour to a button on the gamepad, run the following:

```kotlin
inputHandler += (toggleProteins
    called "toggle_proteins"
    boundTo GamepadButton.PovRight)
```

This snippet adds the `toggleProteins` behaviour defined above to the inputHandler, gives it the name "toggleProteins", and binds it to the right directional pad button. In order to remove the behaviour again from the input handler, use

```kotlin
inputHandler -= "toggle_proteins"
```

#### GamepadMovementControl and GamepadRotationControl

These behaviours can be used to either move or rotate nodes. With scenery's default key bindings, the left-hand stick is bound to movement in the plane, while the right-hand stick is used to look around. These bindings of course can be modified:

```kotlin
inputHandler -= "gamepad_camera_control"
inputHandler += (GamepadRotationControl(
      listOf(Component.Identifier.Axis.RX, 
             Component.Identifier.Axis.RY), 0.03f) { activeProtein }
    called "protein_rotation"
    boundTo GamepadButton.AlwaysActive)
```

This snipped, again from `ProteinComparisonExample`, removes the default `gamepad_camera_control` behaviour, and adds a new behaviour bound to the RX and RY axis, that'll rotate the node bound to `activeProtein`. Movement and rotation controls are always active and should be bound to `GamepadButton.AlwaysActive`.

Vertical movement is not part of the default input bindings, but can also be easily added:

```kotlin
inputHandler += (GamepadMovementControl(listOf(Component.Identifier.Axis.Z), { cam })
    called "vertical_movement"
    boundTo GamepadButton.AlwaysActive)
```

This snippet binds vertical movement to the Z axis of the controller.

### Button Mappings

Buttons printed in italics are controller axis, they cannot be used for `GamepadClickBehaviours`, but only for `GamepadMovementControl` or `GamepadRotationControl`.

#### Xbox/Xbox One Wireless Controller

| On controller | Identifier in scenery |
| :--- | :--- |
| A | `GamepadButton.Button0` |
| B | `GamepadButton.Button1` |
| X | `GamepadButton.Button2` |
| Y | `GamepadButton.Button3` |
| LB | `GamepadButton.Button4` |
| RB | `GamepadButton.Button5` |
| View \(⧉\) | `GamepadButton.Button6` |
| Menu \(≡\) | `GamepadButton.Button7` |
| Directional Pad | `GamepadButton.PovUp`, `GamepadButton.PovDown`, `GamepadButton.PovLeft`, `GamepadButton.PovRight` |
| _LT/RT \(Analog shoulder buttons\)_ | `Component.Identifier.Z` |
| _Left analog controller_ | `Component.Identifier.X`, `Component.Identifier.Y` |
| _Right analog controller_ | `Component.Identifier.RX`, `Component.Identifier.RY` |

