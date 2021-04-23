# Custom Controls

User inputs are handled by the `InputHandler` which is part of `SceneryBase` and `Hubable`. Adding inputs can be done in an overwrite of the `inputSetup` method of `SceneryBase`. First a `Behavior` needs to be added before a key can be assigned.

## Adding a Behavior

A behavior defines an action which is executed when the corresponding keys are pressed. There are currently the following base behaviors from which can be inherited: `ClickBehavior`, `DragBehavior`, `ScrollBehavior`.

In most cases a `ClickBehavior` is used. Example:

```kotlin
val helloClick = object : ClickBehaviour {
    val output = "Hello World"

    override fun click(x: Int, y: Int) {
        logger.info(output + "at $x and $y")
    }
}
```

For cases where the `x` and `y` screen positions of the cursor are not needed they are just ignored. Adding a behavior to the `InputHandler` is straightforward:

```kotlin
inputHandler?.addBehaviour("hello_click", helloClick)
```

`inputHandler` should be available from an overwrite of the `inputSetup` method.

## Assigning Keys to Behaviors

Assigning keys to a behavior works in a similiar fashion:

```kotlin
inputHandler?.addKeyBinding("hello_click", "button1")
```

This code assigned the earlied added behavior to the first mouse button.

```kotlin
inputHandler?.addKeyBinding("hello_click", "M")
```

Does the same but for the "M" key.

For more on the available keys and combinations thereof see [InputTrigger syntax](https://github.com/scijava/ui-behaviour/wiki/InputTrigger-syntax)

