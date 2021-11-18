# Unity

## Prefabs

* Apply changes to all instances of a prefab:
  * Inspector -> Overrides -> Apply All

## Physics

### Raycasting

* For efficiency, pass a Layer when Raycasting.

### Collisions

* For a fast moving object, set its Rigidbody's Collision Detection
  to Continuous to prevent it from passing through another
  (e.g., falling through the ground).

* When an object with a Collider enters another object's Collider,
  the ``OnCollisionEnter`` method is called.
  * me: Is it only on one of the objects or both?

## Input

* ``Input.GetButtonDown/Up`` returns true only once:
  in the frame the button was pressed down/up, not while it's down/up

## XR

* Set the model of the XR controller:
  * Select the controller and go to Inspector -> XR Controller -> Model Prefab

## Terrain

* Unity comes with a Terrain editor:
  * [https://docs.unity3d.com/Manual/script-Terrain.html](https://docs.unity3d.com/Manual/script-Terrain.html)

## Code

### General

* Perform something every x seconds:
  * Use a coroutine that returns ``new WaitForSeconds(x)``

* The ``Destroy`` function can take the number of seconds to wait
  before destroying the object.

* Store player data (e.g., settings, player name, etc.) using ``PlayerPrefs``.

### ScriptableObject

* Inherit from ``ScriptableObject`` to store self-contained data
  (as properties) about a game object (e.g., health).

* Example: the player's health can be a ``ScriptableObject`` that is
  referenced by other objects that need to know about the player's health.
  There is no need to use events.

* Use the ``CreateAssetMenu`` attribute to add a ``ScriptableObject``
  to the Asset menu bar in the Unity editor.

* Popular video on ``ScriptableObjects``:
  [https://www.youtube.com/watch?v=raQ3iHhE_Kk](https://www.youtube.com/watch?v=raQ3iHhE_Kk)

### Animation

* Rotate smoothly an object from its current angle to upright:

      transform.rotation = Quaternion.Slerp(
          tranform.rotation, Quaternion.identity, Time.deltaTime * speed);

## Debugging

* Show a ray in Play mode (in Scene view, or in Game view with Gizmos on),
  but it does not show in VR headset:

      Debug.DrawRay();
