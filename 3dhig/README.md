# 3DHIG: The three-dimensional Human Interface Guidelines

XR is currently the wild west when it comes to interaction patterns. We're all learning what UX works in three dimensions, with different input devices and layouts.

In the Alloverse, a multitude of apps are to coexist in shared spaces. Thus, interacting with one app should be similar to how you interact with another app, instead of each app designing their own interaction patterns; otherwise it will be a too fragmented and confusing experience that decreases productivity and enjoyment rather than increasing it.

This document thus collects interaction guidelines, so that apps can behave in a straightforward, consistent and fun manner. It's very much a work in progress. If you are an interaction designer, we would love your help in designing this document.

_TODO: each section should have instructionals for dual-stick, single-stick and hands._

## Moving

From the rule under "interactive", VR movement in the Alloverse doesn't use teleportation. Instead, movement happens with a static camera and an out-of-body experience. Hold down the A button, and use the dual sticks to move (left stick) and rotate (right stick). As you hold A, the camera stays in place while your avatar moves out in front of the camera, When you release A, the camera snaps into the head location of the avatar. 

## Expressiveness

Since the sticks are not dedicated to movement by default, they can be used for character expressiveness and interaction.

## Grabbing
Direct manipulation is the most intuitive way to interact with a computer.

You should:

* touch,
* grab, and
* and drag things.

You should avoid: 

* modal interactions, where you have to enter and exit modes to perform common actions. 
* indirection, where e g pushing a button to perform an action on an object away from the button, where you could have grabbed or pushed the original thing instead


## Pointing

Point gesture

Pointing at an entity reveals its widgets, which allow you to:

* See who it is owned by
* Ask its parent app to close

### Poking
Entities are informed when they are pointed at, and can react to it. The user can also push the trigger button to "poke" the entity at the pointed location. This is how you would push buttons, 

## Interactive
In real life, any change between two states has some sort of transition. Nothing pops into existence in front of us unannounced; nothing disappears suddenly into nothingness. Your interface should be the same: every change should have a clear transition from its original state. This allows the user's mind to understand what is happening, and build a mental model of the interaction.

Don't teleport; move. Don't swap meshes or textures; animate and transition.

## Interface elements
### Lists and scrolling
Grabbable widget for scrolling

### Buttons
Responds to poke

### Windows
