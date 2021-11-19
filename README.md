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

* Show a reticle at the tip of the ray in a right/left hand controller: 
  * Add an XR Interactor Reticle Visual to the controller 
  * Drag a reticle prefab to the Reticle Prefab slot 
  * Reticle should be a canvas facing up (toward sky)

## Animation

* Check out DOTween package for an animation API. 

### Timeline

* Unity Timelines can have one or more tracks of audio, activation,
  animation, and signal. Timelines are a type of Unity Playable.

* Create a Timeline and add an audio file:
  * Create an empty object and name it "Director"
  * Select Window -> Sequencing -> Timeline
  * Click on Create and save it (e.g., in a "Playables" folder)
  * Drag an audio file to the left panel of the Timeline editor

* Activate or deactivate objects in a Timeline:
  * Drag the object to the Timeline editor, then choose Activation Track
  * Adjust the start and ending rectangle, which specifies when the object
    will be active (it will be inactive outside of the rectangle).
  * Note: Objects may be children of a parent already on the Timeline

* Animate the scale (i.e., size) of an object in Timeline:
  * Drag the object to the Timeline Editor, then choose Animation Track
  * Click on the record button
    * If unable to record animation, set Unity Layout to Default and back
  * Set the playhead to 0:00 and set the object scale
  * Set the playhead where desired and set the new object scale
  * Stop recording

* For more flexibility in animation, including adding more keyframes,
  use the Animaton Window:
  * Click on the three-dot icon next to a track
  * Select Edit in Animation Window

* Create a Track Group to organize tracks in the Timeline

* You can also animate a script component's serialized properties.

* But you can't animate non-GameObjects, so to animate something
  like a property of the Skybox (which is a Material, not a GameObject),
  write a script that controls it and animate the script's property.

* Create a custom animation for an object:
  * Select the object to animate
  * Select Window -> Animation -> Animation
  * Click on Create and name the animation file
  * Click on the Curves button
  * Click on Add Property -> (object) -> Transform -> Rotation -> +
    * Can add other properties, too
  * Add key frames, and edit the curves manually
  * Add to Timeline:
    * Click + in Timeline and choose Animation Track
    * Drag object w/ animation to Animator slot
    * Select Create Animator
    * Click on track menu (three dots) and choose Add From Animation Clip
    * Choose the saved animation
    * Move it to where desired and edit the Extrapolate variable (if desired)

* Animation concepts:
  * Animation clip is one animation (like a property changing over time)
* Animator controller references animation clips and organizes them
  in a flowchart
  * Double-clicking opens the Animator window
* Animator component goes on a GameObject and takes an animator controller
  and avatar

* Signal track: Track that lets you emit signals, which then call a function
  from a script. Can be used to customize animating an object
  to move from one location to the next.

## UI

* There's a new UI system in the UnityEngine.UIElements namespace.

### Canvas and Panel

* Create a UI canvas for XR:
  * Add canvas (GameObject -> XR -> UI Canvas)
    * Do not use GameObject -> UI -> Canvas
    * Automatically adds EventSystem to Hierarchy
  * Set Width and Height (e.g., 1000x750)
  * Scale X, Y, Z down to 0.001
  * Position to 0, 1.5, 0
  * Add child panel to canvas (right-click canvas, then UI -> Panel)
    to help see the canvas better

* You can resize and/or move a canvas interactively in the Scene view
  using the Rect Tool, not the Scale Tool.

* Create a "visor" HUD (UI is attached to your head) in XR: 
  * Make canvas a child of the Main Camera 
  * Position to 0, 0, 1 
  * If canvas has a panel, disable it by unselecting its Image component 

* Create a "windshield" HUD (UI does not move) in XR:
  * Make canvas a child of the XR Rig
  * Position to 0, 1.5, 1
  * Optionally disable the helper panel Image

* Fade canvas after a few seconds: 
  * Add CanvasGroup component to the Canvas 
  * Write script to change the alpha of the CanvasGroup:

        public class HideAfterDelay : MonoBehaviour
        {
            public float delayInSeconds = 5f;
            public float fadeRate = 0.25f;

            private CanvasGroup canvasGroup;
            private float startTimer;
            private float fadeoutTimer;

            void OnEnable()
            {
                canvasGroup = GetComponent<CanvasGroup>();
                canvasGroup.alpha = 1f;

                startTimer = Time.time + delayInSeconds;
                fadeoutTimer = fadeRate;
            }

            void Update()
            {
                if (Time.time >= startTimer)
                {
                    fadeoutTimer -= Time.deltaTime;

                    if (fadeoutTimer <= 0)
                        gameObject.SetActive(false);
                    else
                        canvasGroup.alpha = fadeoutTimer / fadeRate;
                }
            }
        }

* Note: It's possible to force the canvas to be drawn after all other objects
  (to prevent them from blocking the UI if they come in front of it).
  See link in Unity 2020 book (Chapter 6). 

* For a curved UI panel, use the package Curved UI.

### UI Elements

* Add text to panel:
  * Right-click panel and choose UI -> TextMeshPro

* Update the text of a TextMeshPro component programmatically: 
  * Create a field for a TextMeshPro object in a MonoBehavior
    * Drag the TextMeshPro object to the script's slot in Inspector 
  * Update its ``text`` property programmatically 

* Make the Toggle do something when toggled/untoggled: 
  * In the Inspector, there is a Toggle component,
    which has an "On Value Changed" event 
  * Drag an object and select the function to run 
  * Or, code what to do programmatically 

### Info Bubble

* Create an "info bubble" above a game object: 
  * Make the canvas a child of the game object 
  * Position the canvas above the object (e.g., 0, 0.2, 0) 
  * Declare the info bubble Canvas as a Transform,
    and declare the actual text object as a TextMeshPro
  * In ``Start()``, get the text object using
    ``GetComponentInChildren<TextMeshPro>()``
  * In ``Update()``, set the text and then use
    ``infoBubble.LookAt(camera.position)`` to make the info bubble face
    the same direction as the camera 
    * Call ``infoBubble.Rotate(0, 180f, 0)`` to make the info bubble face
      the camera (me) 

* Health bar above game object: 
  * Use a panel, with Image Type set to Filled and Fill Method to Horizontal 
  * Update the Fill Amount programmatically 

### XR UI

* Stop rays from XR controllers from going past a Panel: 
  * Create a solid background, but disable its renderer 

* Interact with UI components by touching the Panel: 
  * Make the controller have a Direct Interactor 
    * Add a collider (e.g., sphere collider) and check Is Trigger 
  * Add a Simple Interactable to the UI element 
    * Uncheck Use Gravity and check all Freeze checkboxes (in Constraints) 
    * Add a collider (e.g., box collider) around the UI element 
  * Write the script of what should happen when the UI element is activated 
  * Hook up the code with the appropriate event (e.g., On First Hover Enter) 

* For a wrist-band menu: 
  * Make the dashboard a child of a controller 
  * Resize and position appropriately

### Other

* Event System 
  * The Event System object must be in the Hierarchy 
    * It is created automatically when a Canvas is created 
  * It should have an XRUI Input Module (not the Standalone Input Module)

## Terrain

* Unity comes with a Terrain editor:
  * [https://docs.unity3d.com/Manual/script-Terrain.html](https://docs.unity3d.com/Manual/script-Terrain.html)

## Code

### General

* ``OnEnable()`` is called when the object is enabled and active. 

* ``Time.time`` is the number of seconds since the game started. 

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
