# Unity

## Prefabs

* Apply changes to all instances of a prefab:
  * Inspector -> Overrides -> Apply All

## Physics

### General

* Unity physics is based on the NVIDIA Physx engine.

* Unity also offers the Havok Physics engine for advanced simulations.

* Global physics settings are in Edit -> Project Settings -> Physics.

### Raycasting

* For efficiency, pass a Layer when Raycasting.

### Collisions and Rigidbody

* To detect a collision, both objects must have a Collider component.

* For an object to react in a realistic way, it must have a Rigidbody component

* If colliding objects contain both a Collider and Rigidbody,
  the forces of one are applied to the other.

* If an object moves too fast and collisions are not detected:
  * Set its Rigidbody's Collision Detection to Continuous
  * Or, modify the Fixed Timestep (Project Settings -> Time)
    to something like 0.01 or 0.001 (default is 0.02, which is 50 fps)
    * Can also do it in code with ``Time.fixedDeltaTime``

* When an object with a Collider enters another object's Collider,
  the ``OnCollisionEnter`` method is called.
  * me: Is it only on one of the objects or both?

* Instead of a mesh collider, it's best to use multiple basic colliders.

* You can constrain collision calculations to occur between specified layers
  (in Physics settings).

### Physic Material

* Only the object that has a Physic Material will experience the friction
  and bounciness specified by the material.

* Make an object bounce:
  * Create a Physic Material and set its Bounciness
    * Bounce Combine specifies how to calculate the total bounciness
      when it collides with another object
  * Drag the material to the Collider (Material slot) of the object

## Input

* When detecting button presses, use a UnityEvent to signal
  that the button has been pressed or released rather than
  doing the work directly. This allows you to determine what to do
  when the events are raised via the Inspector.

* ``Input.GetButtonDown/Up`` returns true only once:
  in the frame the button was pressed down/up, not while it's down/up

## Lighting

* Unity recommends choosing the lighting strategy early in development.

* Recommending lighting settings (Window -> Lighting Settings):
  * Check Baked Global Illumination (uncheck to always have real-time lighting)
  * For mobile VR, choose Subtractive (almost 100% baked,
    with directional light shadowing)
  * Otherwise, choose Baked Indirect (100% real-time lighting
    with baked indirect lighting)
  * Do not have Auto Generate on, but run it periodically

* When using Baked lighting, it only applies to Static objects,
  so any objects that are not dynamic (won't move), set them to Static.

* If a Light won't move, but some objects may, set the Light -> Mode to Mixed.

* Specify how much the skybox texture contributes to the Global Illumination
  in Window -> Rendering -> Lighting Settings -> Environment Lighting ->
  Intensity Multiplier.

* Light probes store information about light passing through empty space
  in a scene.

* A reflection probe stores a reflection at that location,
  then used by reflective objects.

* The ``RenderSettings`` object has several properties (e.g., fog)
  to customize programmatically.

* There is a maximum number of lights that can affect a single object.
  The default is 4 (for URP). To increase:
  * Select Edit -> Project Settings -> Quality. Click on the Rendering asset.
    In the Inspector, expand the Lighting section.
    Under the Additional Lights section, increase the Per Object Limit
  * It may work better to split a large wall or floor into multiple objects
    in order to have only a few lights affect them.

* Add environmental lighting (overall lighting):
  * Open Window -> Rendering -> Lighting Settings
  * Set Environmental Lighting to Color, then set the ambient color

* If there is a seam on a mesh after baking light,
  enable "Stitch Seams" under the Mesh Renderer -> Lightmapping properties.

* Guidance on creating good looking scenes:
  [https://docs.unity3d.com/Manual/BestPracticeMakingBelievableVisuals.htm](https://docs.unity3d.com/Manual/BestPracticeMakingBelievableVisuals.htm)

## Materials

* A Material is an asset that specifies a specific Shader script
  that drives the actual work of drawing the object on the screen.

* The default "Lit" shader offers a lot of variety and options,
  while being efficient at runtime.

* Use Unlit shader for object that don't need lighting; it's more efficient.

* Use Unity Shader Graph to build custom shaders.

* Material surface properties:
  * Base Map: basic color texture of the model
  * Metallic Map: texture that controls shininess
  * Normal Map: provides appearance of bumps and grooves
  * Occlusion Map: accenting surface shadows associated with occlusion

* Use Emission in Material to make the object glow.

* Setting Surface Type to Transparent allows the color of the material
  to have Alpha.

* Edit an object's material (like the mainTexture):

      object.GetComponent<Renderer>().material.mainTexture = image;

* Edit a material property (by name, like "\_Exposure"):

      material.SetFloat("_Exposure", 1.0f);

## Rendering

* A render pipeline performs a sequence of operations that displays
  the content of a scene onto the screen, frame by frame.

* Different render pipelines have different capabilities and performance
  and are suitable for different applications and platforms.

* Three major steps in a render pipline:
  * Culling: eliminate objects occluded by others
  * Rendering: drawing the objects into pixel buffers, including lighting,
    shadows, reflections
  * Post-processing: apply effects (like bloom) before copying to the display
    screen

* HDRP and URP and the new render pipeline templates in Unity (2019 and later).
  They use the new Scriptable Rander Pipeline.

* The old render pipeline is available via "2D", and "3D" project templates.

* Project Settings -> Graphics shows the Scriptable Render Pipeline settings
  in use, which are stored as an asset. This asset can be edited
  in the Inspector.

* More information on render pipelines at [https://docs.unity3d.com/Manual/BestPracticeLightingPipelines.html](https://docs.unity3d.com/Manual/BestPracticeLightingPipelines.html)

## Audio

* Add sound effects to an object:
  * Add an Audio Source component to an object (where sound is emitted from?)
  * Uncheck Play On Awake if the sound is controlled by a script
  * Drag an audio clip from Assets to AudioClip slot
  * Add Audio Listener component to the Main Camera
  * In code, get the Audio Source component (``GetComponent<AudioSource>()``)
    and call its ``Play()`` method

* Play sound when objects collide:
  * Add Audio Source to one of the objects, and uncheck Play On Awake
  * Drag the desired audio clip to the AudioClip slot
  * Add Audio Listener component to the Main Camera
  * In code, create method ``OnCollisionEnter(Collision collision)`` and
    then call ``Play()`` on the audio source

* Synchronize audio beats with the scene:
  * Download Unity-Beat-Detection from GitHub
  * Use the code's ``OnBeat`` event to script when a beat is detected

## Particle System

* Note: The Particle System will be soon replaced
  by the Visual Effects (VFX) Graph.

## XR

### Configuration and Building

* Enable XR on a project:
  * Go to Window -> Package Manager and enable preview packages
  * Install the XR Interaction Toolkit

* Add an XR Rig:
  * Create -> XR -> Stationary (or Room-scale) XR Rig
  * Note that there are two types of XR Rigs:
    * Device-based (under the Device-based menu): directly access controllers
    * Action-based: indirect access, takes advantage of the XR Input System

* Stationary vs room-scale XR Rig:
  * Stationary: the Tracking Origin Mode is set to Device
    * Best for VR experiences that don't require movement
    * I found that I need to set the Camera Offset Y to 1.75 m
  * Room-scale: the Tracking Origin Mode is set to Floor
    * Best for VR experiences where you can move around

* Build Settings:
  * Make sure the start scene is in the list (drag it, if not)
  * For Quest development, choose Android and click on Switch Platform
  * Click on Player Settings (lower left)
  * Select XR Plug-in Management and Install
  * Check Oculus
  * Go back to Player Settings
  * Open Other Settings expander
  * In Color Space, choose Linear (recommended by Oculus)
  * Edit the Package Name
  * Set the Minimum API Level to API 23
  * Set Install Location to Automatic
  * Back in Build Settings, change Texture Compression to ASTC

* Optimization settings for Oculus Quest:
  * Open Project Settings
  * Create a new Quality Level, name it Oculus Quest
    * Uncheck all other Quality Levels
  * Set Pixel Light Count to 1
  * Anisotorpic Texture: Per Texture
  * Anti Aliasing: 4x
  * Disable Soft Particles

* Quickly Build and Run for the Quest with File -> Build and Run.

### Player

* You can give yourself an avatar model, but to prevent the main camera
  from being blocked by your own model, you can put the models
  in a special layer, and then specify in the camera the Culling Mask,
  which will not show any of the models in that layer.

### Grabbing

* Make an object grabbable:
  * Add XR Grab Interactable component
  * Add Collider (e.g., box collider), so object doesn't fall through things
  * Add Layer called Interactable
  * Put object in layer Interactable
  * In XR Ray controllers, set Interaction Layer Mask to Interactable

* Properly grab objects at a specific location in object:
  * Add an Empty Object to the object, call it Attach Transform
  * Resize and position (make it point "forward", Z coordinate)
  * Drag it to the object's Attach Transform (in Interactable component)
  * Set the Interactable Movement Type to Instantaneous

* Use a Direct Interactor (instead of Ray Interactor):
  * Add XR -> Direct Interactor object to Hierarchy
  * Set its Controller Node to Left or Right hand
  * Move as child of Camera Offset (in XR Rig), reset its Transform
  * Optionally move any hand objects from an existing controller
    into the Direct Interactor, and reset their Transform
  * Delete any old hand controllers
  * Rename the Direct Interactor (e.g., LeftHand Controller)
  * Set its Interaction Layer Mask to Interactable (if using)
  * Optionally turn on Haptics for Select Enter and Hover Enter

* Use an "attach point" to attach an object (e.g., paddle) to
  a hand controller:
  * Create an empty object (e.g., "Paddle Attach") as a child
    of the hand controller
  * Set its Rotation to something appropriate (may need to play with it)
  * Drag the attach object to the Model Transform slot of the hand controller
  * Drag the prefab of the object to attach to the Model Prefab slot

* When grabbing an object for climing, prevent it from moving:
  * In Rigidbody component, uncheck Use Gravity
  * Check Constraints -> Freeze Position and Freeze Rotation to all

* Avoid grabbed objects pushing on player's collider:
  * Create a Layer called Player
  * Put the main VR player in the Player layer
  * Open Edit -> Project Settings -> Physics
  * Edit Collision matrix so that Player and Interactables don't collide

* Set the model of the XR controller:
  * Select the controller and go to Inspector -> XR Controller -> Model Prefab

### Teleportation

* Two main types of teleportation that come with
  the XRI Interaction Toolkit Examples:
  * Area teleportation: teleport anywhere within the specified area
  * Anchor teleportation: teleport to a specific location (and orientation)
  * [https://github.com/Unity-Technologies/XR-Interaction-Toolkit-Examples](https://github.com/Unity-Technologies/XR-Interaction-Toolkit-Examples)

* Teleportation and snap turn:
  * Add Locomotion System component to XR Rig, and drag XR Rig to slot
  * Add Teleportation Provider component, and drag Locomotion System to slot
  * Add Snap Turn Provider, and drag locomotion System to slot
  * Create two Ray Interactors inside Camera Offset
    * Rename to Left and Right Teleport Controllers
    * Reset Transforms
    * Set Controller Node to Left and Right Hand
  * In XR Rig | Snap Turn Provider, set Controllers Size to 1
    and drag a hand controller to the slot
  * Select the floor and add a Teleportation Area component
  * Set the Colliders Size to 1 and drag the floor's collider to the slot
  * Drag XR Rig to the Teleportation Provider slot
  * Drag a Reticle prefab (must get one) to the Custom Reticle slot
  * Create a Teleportation Layer and set the floor to that layer
  * In XR Ray Interactor components of the hand controllers,
    set the Interaction Layer Mask to Teleportation
  * Set Line Type to Projectile Curve
  * Set XR Ray Interactor Visual, set the Valid Color (e.g., green)
  * Optionally set the button to teleport in XR Controller > Select Usage

* To improve performance, put teleport areas/anchors on a separate layer,
  and change the "Interaction Layer Mask" to that layer in both
  the areas/anchors and XR Interactor(s) in the controller(s).

* To use multiple controller configurations (e.g., teleportation and
  direct interaction), checkout the XRRig\_Demo in the XR Interaction
  Toolkit Examples.

* Teleport to a different scene:
  * Use ``SceneManager.LoadScene("SceneName")``

* Show a reticle at the tip of the ray in a right/left hand controller:
  * Add an XR Interactor Reticle Visual to the controller
  * Drag a reticle prefab to the Reticle Prefab slot
  * Reticle should be a canvas facing up (toward sky)

* Unity XR Interaction Examples:
  * https://github.com/Unity-Technologies/XR-Interaction-Toolkit-Examples
  * Provides a Controller Script that has teleportation:
    * Attach it to the XR Rig
    * Drag interaction controllers to the Left/Right Base Controller slots
    * Drag teleport controllers to the Left/Right Teleport Controller slots

### Other

* Get all interactors (i.e., hand controllers) over an interactable object:
  * ``GetComponent<XRSimpleInteractable>().hoveringInteractors``

* Implement a custom Locomotion Provider:
  * Derive from LocomotionProvider
  * Use CanBeginLocomotion(), BeginLocomotion(), and EndLocomotion()
  * Benefit of using a locomotion provider is that it prevents other
    locomotion providers from interrupting the currently executing provider

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

* Loop through an object's children (it's important to not use ``var``
  in the ``foreach`` statement, otherwise it's interpreted as an Object,
  not Transform):

      // Use Transform, not var, so each child is of type Transform
      foreach (Transform child in parent)

* Create a ray originating from the camera pointing in the same direction:

      new Ray(camera.position, camera.rotation * Vector3.forward);

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

* Point an object toward another:

      object.transform.LookAt(anotherObject)

* Set the velocity of an object in the direction of another:

      object.velocity = anotherObject.forward * speed

## Debugging

* Show a ray in Play mode (in Scene view, or in Game view with Gizmos on),
  but it does not show in VR headset:

      Debug.DrawRay();

## Performance

* "Quality" is not only how it looks, but also how it feels.
  Optimizing for user experience, including high frame rates and low latency,
  is as fundamental a design decision as visual quality.

* For objects that don't change or move, make them Static.
  This can be refined (via the Static drop down).

* To show the FPS on a UI canvas, first add the canvas to the scene
  with a Text element as a child, then add the following component:

      public class FramesPerSecondText : MonoBehavior
      {
          private float updateInterval = 0.5f;
          private int framesCount;
          private float flamesTime;
          private Text text;

          void Start()
          {
              text = GetComponent<Text>();
          }

          void Update()
          {
              framesCount++;
              framesTime += Time.unscaledDeltaTime;
              if (framesTime > updateInterval)
              {
                  float fps = framesCount / framesTime;
                  text.text = string.Format("{0:F2} FPS", fps);
                  framesCount = 0;
                  framesTime = 0;
              }
          }
      }

* Performance tips on lighting:
  * Use baked lightmaps whenever possible.
  * Avoid dynamic shadows. Substitute with blurry blob underneath objects.
  * Use fewer Pixel Lights (1 or 2).
    Use High Resolution on Hard and Soft Shadows.
  * Have as many baked lights as desired.
    * Improve quality by increasing baked resolution (40-100 pixel res.)
  * Use light probes with baked lighting to illuminate dynamic objects.
  * Use reflection probes for reflective surfaces (can be static or dynamic).

* Destroy objects that will forever be off-scene (e.g., fall off an edge).
  * Use ``Destroy(gameObject)`` once it's out of bounds

* Destroy objects that the player will not care about after some time.

* Avoid the GC from collecting dead objects too often:
  * Instantiate objects in a list (object pool) and set to inactive
  * When the scene needs an object, activate the next inactive object
  * May need to allow expanding the list as needed
  * When the scene needs to destroy an object, set object to inactive

* Viewport clipping occurs on objects outside of the camera view.
  Occlusion culling disables the rendering of objects ocluded by other objects.
  * Occlusion culling is built-in, but it may also be "baked" manually.
    See https://docs.unity3d.com/Manual/OcclusionCulling.html
    and additional resources therein.

* See https://docs.unity3d.com/Manual/DrawCallBatching.html for recommendations
  on static and dynamic batching.
  * When scripting, use ``Renderer.sharedMaterial``, not ``Renderer.material``
    to avoid duplicating the material.

* Code recommendations:
  * Don't perform things on every Update if not needed; use a Coroutine instead
  * To find an object, don use Object.Find().
    Instead, set it in a slot in Inspector or use a Tag or Layer
    (to limit the search).
  * Avoid SendMessage(), use Unity Events instead.
  * Use an object pool.
  * See https://docs.unity3d.com/Manual/MobileOptimizationPracticalScriptingOptimizations.html

* Use Layers to limit ray casting and collision detection
  (Layer Collision Matrix).
  * See https://docs.unity3d.com/Manual/iphone-Optimizing-Physics.html
  * See https://learn.unity.com/tutorial/physics-best-practices

* Click on the Stats button in the Game tab to view statistics
  * CPU is time per frame
  * For Oculus, target at least 72 FPS
  * Tris/Verts includes only those currently visible
  * Batches indicate how hard the GPU is working
    * The number of batches is more important than their size
    * Reduce number of batches by combining geometry in the scene

* Profiler:
  * Deep Profile records all function calls in scripts
  * Works only in the Editor, not the build
  * First use without, and only turn on to go deeper

* Worflow for profiling:
  * Use the Profile and look for poor performance
  * Enable Deep Profile where you think the issue is
  * Change the project/code
  * Repeat the steps and see what the effects are

* CPU-bound:
  * Unity engine play loop (e.g., calls to Update and FixedUpdate)
  * Any C# code that I write
  * Physics calculations and animations
  * Selection of which objects to be rendered
  * Prepares geometry data as textures, vertices, triangles, normals, etc.
  * Complex and detailed geometry affects the CPU

* GPU-bound:
  * Rendering the geometry into pixels on every frame update
  * Code on GPU is the shader

* Create a model with multiple Levels of Detail (LOD):
  * Create an empty object and add the different models as children
  * To the root object, add component "LOD Group"
  * Select LOD0, then drag the highest quality child object to the Add button
  * Repeat for LOD1 and LOD2
  * (Instead of models, use prefabs for consistency)
  * You can drag the little camera to see models change w/ distance
  * Percentage shown is the object's bounding box height relative to
    the screen height
  * You can adjust the percentages by dragging

* Some mesh optimization can be done via the Unity import options.

## Editor

* Editor scripts extend the features of the Unity editor.
  For example, one could be used to add a menu option to instantiate
  prefab objects into the Hierarchy.
  * See https://docs.unity3d.com/Manual/ExtendingTheEditor.html
  * Tutorial: https://unity3d.com/learn/tutorials/topics/scripting/editor-scripting-intro
