# Unity

## Prefabs

* Apply changes to all instances of a prefab:
  * Inspector -> Overrides -> Apply All

* Right-click on any property in a Component of an instantiatied object
  to choose to revert to its prefab value

* Any game object properties in bold have different values than the properties
  in the prefab it came from

## Physics

### General

* Unity physics is based on the NVIDIA Physx engine.

* Unity also offers the Havok Physics engine for advanced simulations.

* Global physics settings are in Edit -> Project Settings -> Physics.

* Edit how often physics is calculated:
  * Project Settings -> Time, then Fixed Timestep

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

* Collision event callbacks:
  * ``OnCollisionEnter``: collider enters another object's collider
    (i.e., a collision happens)
    * me: Is it called only on one of the objects or both?
    * For example, destroy itself if it collides with objects with tag
      "Destructor"
  * ``OnCollisionStay``: called as long as an object is colliding
    with self and the object has not entered "sleep" mode
    * To change Sleep threshold, go to Project Settings -> Physics
  * ``OnCollisionExit``: called when an object is done colliding with self

* Trigger events callbacks:
  * ``OnTriggerEnter``, ``OnTriggerStay``, ``OnTriggerExit``
  * Similar to collision events, but sleep mode does not apply
  * Example use case: player enters a trigger area that causes rocks to fall

* Rigidbody component:
  * Mass is in kilograms (by default)
  * Drag is air resistance
  * Angular drag is the resistance to the rotational value
    * Simulates as if an object was filled with liquid
  * Interpolate: fixes jitter due to the fact that graphics and physics
    run at different rates
    * Commonly used on the player's character, disabled for everything else
  * Collision Detection: how often to do collision detection
    * Use Continuous for fast moving objects that need collision detection
  * Specify constraints (position and rotation) to prevent physics
    for applying on those axes

* Is Kinematic releases control of the object from physics
  * Gravity no longer applies
  * Useful to let an animation take over rather than physics
  * For example, a character may be controlled by an animation
    (Is Kinematic = true) until it dies, where ragdoll physics
    can take over (Is Kinematic = false)

* Mesh collider is not performant (so use sparingly).
  * For a Mesh collider, you can specify a different mesh
    than the object itself (for example, to simplify the mesh)
  * You can also set to Convex to simplify the mesh
  * If not using Convex, and object is also a Rigidbody,
    you may get an error that is solved by making the mesh collider Convex
  * Instead of a mesh collider, it's best to use multiple basic colliders.

* Compound collider
  * Multiple colliders to represent the shape of an object

### Wheel Collider

* Build a car with wheel colliders:
  * Create a Rigidbody around the whole car model
  * Create a collider around the body of the car
    (don't let the collider touch the ground)
  * For each wheel, create an empty child object where the collider will go,
    and then move it out as a sister object (used child only to keep same
    position)
  * Add Wheel Collider component to each new object (can copy-paste)
    * Adjust mass (weight of wheel)
    * Adjust radius and suspension distance (e.g., 0.2)
    * Adjust center of collider
    * Adjust wheel damping rate
    * Adjust Force App Point Distance (rest position)
    * Adjust Suspension Spring (how stiff the suspension is)
    * Forward Friction: amount of friction each wheel has while rolling forward
    * Sideways Friction: amount of drift if car is sideways
  * Write a script so that the wheel mesh position matches the collider center,
    and then use a Raycast to ensure the wheel does not go into the ground
    * To make the wheels rotate, also write a script to match the collider
      rotation

### Collision Settings

* You can constrain collision calculations to occur between specified layers
  (in Physics settings).

### Joints

* Fixed joint (component)
  * Add the component to an object to attach the object to another
  * Specify a Break Force to allow it to detach based on a force applied,
    such as a character walking over the object
  * The other object (the "Connected Body") may need to be set to Is Kinematic,
    so it doesn't react to other forces

* Sprint joint (component)
  * Sample use case: effect of a rope bridge by connect planks with springs
  * Add one or more components to add springs that attach to other objects
  * Play with the settings to get the effect desired

* Hinge joint (component)
  * Works like a hinge on a door
  * Use Limits and specify Min and Max to set the permitted angles
    it can hinge around
  * If affected by gravity, you can set the Bounciness (and related settings)
    to give it a bounce once the object hits the ground (or another object)
  * Can use a Spring on the hinge to have it spring back to its
    original position after the object swings (because of gravity or
    another object pushing it), like Old West saloon doors
  * Can add a Motor to automatically rotate the object around the hinge

* Configurable joint (component)
  * Highly customizable joint
  * Set the main Axis to specify where the joint goes around
  * Angular Drive can specify the springiness

### Physic Material

* It is applied in Collider.

* Static friction: force it will take to get an object to begin moving

* Dynamic friction: force it will take to get an object in motion to stop

* Difference b/w friction and drag: with drag, it doesn't matter
  how much surface area the object is touching another

* Friction Combine: how to calculate the final friction between two objects
  with friction (physical materials)

* Make an object bounce:
  * Create a Physic Material and set its Bounciness
    * Bounce Combine specifies how to calculate the total bounciness
      when it collides with another object (similar to Friction Combine)
  * Drag the material to the Collider (Material slot) of the object

* Only the object that has a Physic Material will experience the friction
  and bounciness specified by the material.

### Ragdoll

* Create a ragdoll:
  * Create -> 3D Object -> Ragdoll
  * Drag model joints to Radgoll dialog box
    * Make sure joints are inclusive of child joints
      (e.g., knee joint in Ragdoll window may actually need a calf joint
      that includes the knee joint)
  * Unity will create a "Character Joint" component for each relevant model
    joint, and include a collider and rigidbody
  * May need to go through each collider and adjust size

* In Awake():
  * Disable the ragdoll by disabling all collider components within
    the root joint (using GetComponentsInChildren<Collider>)
  * Set all child Rigidobdy components IsKinematic to true
    (using GetComponentsInChildren<RigidBody>)

* To activate the ragdoll:
  * If model has an overall collider, disable it
  * If model has a RigidBody, set IsKinematic to true
  * If model has an Animator, disable it
  * If model is a NavMesh agent, disable it
  * Enable all child joint colliders
  * Set all child joint IsKinematic (in Rigidbody) to false

## NavMesh

* Create a NavMesh agent:
  * Add Component NavMeshAgent to object

* Move an agent in a NavMesh:

      // targetTransform would come from Inspector (e.g., set to the player)
      GetComponent<NavMeshAgent>().SetDestination(targetTransform.position)

  * By default, agent stops at target position, but in Inspector can set
    the "Stopping Distance" to stop the agent that amount away from the target

* Make agent react only if player is within range:

      enemyPosition = enemyTransform.position;
      playerPosition = targetTransform.position;
      distanceToTarget = Vector3.Distance(playerPosition, enemyPosition);
      if (distanceToTarget <= chaseRange)
          navMeshAgent.SetDestination(playerPosition);

* Make agent chase or attack depending on how close it is
  (StoppingDistance is set in Inspector for NavMeshAgent):

      if (distanceToTarget >= navMeshAgent.stoppingDistance)
          ChaseTarget();
      else
          AttackTarget();

* Add a NavMeshObstacle component to an object to avoid an agent
  from running into it. The object may be animated.
  * Green volume appears around object, which defines it as an obstacle
    if the box crosses into the NavMesh
  * Check the "Carve" option, and uncheck "Carve Only Stationary"
  * Test it's working by playing the game, and watch the Scene
    with the NavMesh showing

## Input

* When detecting button presses, use a UnityEvent to signal
  that the button has been pressed or released rather than
  doing the work directly. This allows you to determine what to do
  when the events are raised via the Inspector.

* ``Input.GetButtonDown/Up`` returns true only once:
  in the frame the button was pressed down/up, not while it's down/up

## Lighting

* Unity recommends choosing the lighting strategy early in development.

* Because blue and orange are kind of complementary, and humans have an orange
  skin tone, movies often use blue light for contrast.

* Three-point lighting
  * Used a lot in photography. Look up Wikipedia article for summary.
  * Key light: main source of illumination, creates shadows
  * Fill light: fill in shadows with lower intensity,
    opposite side of key light
  * Back (or rim) light: top light to help lift subject out of background

* Use Light Explorer to see all lights at once and change common properties
  * Window -> Rendering -> Light Explorer

* Guidance on creating good looking scenes:
  [https://docs.unity3d.com/Manual/BestPracticeMakingBelievableVisuals.htm](https://docs.unity3d.com/Manual/BestPracticeMakingBelievableVisuals.htm)

### Scene Settings

* Realtime Global Illumination
  * Turns on indirect lighting in real-time
    * Not truly "realtime" as Generate Lighting click is still required
  * Intensity Multiplier (on light) affects how much indirect lighting to show
  * Uses Enlighten (a 3rd party engine), which is being discontinued
  * Use for non-moving geometry (must be marked as Static)
  * Shadow quality is low, can instead use Emissive Light
  * Use an appropriate Indirect Resolution (under Lightmapping Settings)
    * Large terrain: 0.1-0.5 texels per unit
    * Outdoors: 0.5-1 texels per unit
    * Indoors: 2-3 texels per unit
  * Can work with various light types (point light, directional, etc.)
  * Make sure to enable Contribue Global Illumination on objects

* Baked Global Illumination
  * Another way of processing global illumination (indirect lighting)
    * Typically, better shadow quality
  * Lights must be in Baked mode
  * Directional Mode can be set to Non-Directional if scene has no Normal maps
    (saves diskspace)
  * Higher Lightmap Resolution will create more lightmaps and consume
    more diskspace
  * Baked Shadow Angle: blurs Soft Shadows
    * Located Shadow Type for the Directional Light object
    * Similar setting for Point and Spot light
  * Cannot do specular highlights

* Mixed lighting
  * Instead of having two lights (one for baked and another for realtime),
    you can have one (Mixed light) that does both
  * Subtractive mode is good for mobile devices because everything is baked
    * Non-static objects don't cast shadows on Static objects,
      except for the main Directional Light which can cast realtime shadows
  * Shadowmask: low quality shadows througout
  * Distance Shadowmask: high quality shadows for nearby objects,
    lower quality shadows for far away objects (can modulate this using
    "Shadow Distance" in Quality settings)
  * How light mode (Submode) and type of object (Static or not)
    interact to form direct and indirect lighting:

| Lighting Mode          | Dynamic Receiver | Dynamic Receiver  | Static Receiver | Static Receiver   |
| ---------------------- | ---------------- | ----------------- | --------------- | ----------------- |
|                        | Direct Lighting  | Indirect Lighting | Direct Lighting | Indirect Lighting |
| Realtime               | Realtime         | None              | Realtime        | None              |
| Realtime GI            | Realtime         |                   | Realtime        | Realtime Lightmap |
| Baked                  |                  |                   | Lightmap        | Lightmap          |
| Mixed - Baked Indirect | Realtime         |                   | Realtime        | Lightmap          |
| Mixed - Shadowmask     | Realtime         |                   | Realtime        | Lightmap          |
| Mixed - Subtractive    | Realtime         |                   | Lightmap        | Lightmap          |

* Progressive lightmapper
  * Can see progress of lightmapping, allowing you to cancel if necessary
  * Setting recommendations:
    * Outdoor scenes w/ directional lights
      * Direct samples: < 100
    * Indoor scenes:
      * Direct samples: > 100
  * Lightmap padding: increase if lightmap "bleeding" is noticeable

* Recommending lighting settings (Window -> Lighting Settings):
  * Check Baked Global Illumination (uncheck to always have real-time lighting)
  * For mobile VR, choose Subtractive (almost 100% baked,
    with directional light shadowing)
  * Otherwise, choose Baked Indirect (100% real-time lighting
    with baked indirect lighting)
  * Do not have Auto Generate on, but run it periodically

* When using Baked lighting, it only applies to Static objects,
  so any objects that are not dynamic (won't move), set them to Static.
  * If there is a seam on a mesh after baking light,
    enable "Stitch Seams" under the Mesh Renderer -> Lightmapping properties.

* To make baking faster while working:
  * Decrease Indirect resolution (e.g., 0.25)
    and lightmap resolution (e.g., 25).
  * And/or, disable anything not currently being worked on.
    Organize Hiearchy in such a way that it's easy to re-enable them back
    (e.g., use dummy "spacer" GameObjects).

* If a Light won't move, but some objects may, set the Light -> Mode to Mixed.

* Ambient occlusion in Lightmapping Settings:
  * Turn it on to add extra depth to objects
  * Max Distance extends how far the occlusion shadow goes out
  * Indirect Contribution makes the shadows darker
  * When there's direct light, like a Directional Light,
    the ambient occlusion may be less visible,
    but you can increase the "Direct Contribution" setting to add some back

### Environment Settings

* To add ambient color (overall lighting to the whole scene),
  use environmental lighting:
  * Open Window -> Rendering -> Lighting Settings
  * Set Environmental Lighting Source:
    * Skybox
    * Color: overall ambient color
    * Gradient source gives colors for the sky, equator, and ground
      * Typically set to blue, white, and brown
  * Note: When importing a model, and you'll use baked GI,
    make sure that the Generate Lightmap UVs is checked

* Environment Reflections
  * Turn on by setting Intensity Multiplier (under Environment Reflections
    in Lighting Settings) to 1
  * Increase material's Metallic and Smoothness properties
  * For higher reflection resolution, increase Resolution under
    Environment Reflections in Lighting Settings

### Other Settings

* There is a maximum number of lights that can affect a single object.
  The default is 4 (for URP). To increase:
  * Select Edit -> Project Settings -> Quality. Click on the Rendering asset.
    In the Inspector, expand the Lighting section.
    Under the Additional Lights section, increase the Per Object Limit
  * It may work better to split a large wall or floor into multiple objects
    in order to have only a few lights affect them.

* The ``RenderSettings`` object has several properties (e.g., fog)
  to customize programmatically.

### Skybox

* Specify how much the skybox texture contributes to the Global Illumination:
  * Window -> Rendering -> Lighting Settings -> Environment Lighting ->
    Intensity Multiplier.

* SkyBox types:
  * Procedural: Unity-generated; configure sun size, atmosphere, etc.
  * Cubemap: Image
    * Unity has free cubemaps in AssetStore

* Create a procedural SkyBox:
  * Create -> Material
  * Set Shader to Skybox/Procedural
  * Edit sky and ground tint (and other properties) in Skybox material
  * For consistency, set the Camera "Clear Flags" to Skybox
  * For lighting consistency, set the Environment Lighting Source
    (in Lighting settings) to Skybox
  * Set Environment Sun Source to the directional light
    * In Skybox material, you can adjust Sun properties

* Image-based lighting/skybox
  * Get a 360 HDR panoramic image
    * See Resources (lesson 23 of Udemy Lighting course) to read
      on how to do it yourself
    * Many provided free by Unity in Asset Store (search for HDRI)
    * Set Texture Type to Default, Texture Shape to Cube, and
      Convolution Type to None
  * Create a new material and set shader to Skybox/Cubemap
    * Drag HDR image to Cubmap field
    * In Lighting settings, drag material to Skybox Material field
  * May need to increase Environment Samples to 1000 and set Filtering
    to Auto (or Advanced > OpenImageDenoise in Indirect Denoiser)
    * See Comments in lesson 23 of Udemy Lighting course

### Directional Light

* Sun Source (in Lighting Settings):
  * Typically left empty because, when empty, Unity automatically
    looks for the strongest-intensity Directional light
    and assigns it as the sun source.

* Artifacts with directional light:
  * On some models, the shadow of an object may have holes
  * These can be fixed by playing with the Normal Bias and Bias settings
  * Try lowering the Normal Bias first, then play with Bias
    * Normal Bias may be down to 0
    * Bias of 0.05 works well

### Point Light

* No light extends past the Range (customizable sphere).

* Indirect Multiplier affects indirect light.

* Draw Halo shows an "aura" around the light, and is best used
  in nightime scenes, such as a street lamp in the dark.

### Spot Light

* Use two spot lights in opposite directions to simulate a lamp shade
  (light coming down and light coming up).

* Cookie: Mask to add an effect on the spotlight (e.g., flashlight lens).
  * Change all spot light cookies under Lighting -> Other Settings ->
    Spot Cookie
  * Black means the light does not go through; white the light goes through

### Area Light

* Use area light for things like office lighting.

* Area light is baked-only (are never real-time).

* Specify Width/Height and play with Intensity to see results.

* Tip: tilt light to create a more organic feel.

* Indirect Multiplier controls how much indirect light is caused by this light.

### Indirect Lighting

* Indirect lighting is light reflected from an object
  that affects another object.

* Only objects that are Static can receive indirect lighting.

### Emissive Lighting

* Only objects that are Static can emit light and illuminate other objects.

* Add emissive lighting:
  * The slot to the left of Color is the mask (itself a texture)
    where the emission should be applied
    * After selecting an emissive color, increase the intensity
      to emit more light
  * A Color value greater than 1 (HDR) can illuminate other objects
  * Larger objects emit more light

* Tip: suffix emissive objects w/ "Emissive" and group them.

### Masks

* Problem: you want to illuminate an area with a single light
  but some objects are static and others are not (so baking won't work
  for the dynamic objects).
  * Solution:
    * Duplicate the baked light and change the new light's mode to Realtime
    * Create a new Layer (e.g., "Props") and put dynamics objects there
    * Change the new light's Culling Mask to the new layer

* Problem: direct lighting bleeds through interior environment
  * Solution:
    * Put interior environment on its own layer
    * Change the direct lighting Culling Mask to exclude that layer
    * In some cases, may need to create dummy objects used only to cast shadows
      (set their "Cast Shadows" property to "Shadows Only")

### Light Probes

* Light probes store information about light passing through empty space
  in a scene.

* Use light probes when you want indirect lighting on dynamic
  (non-static) objects

* Create a light probe:
  * Create -> Light -> Light Probe Group
  * Resize the cube to encompass the scene (or part of the scene)
  * To select probes, first click on "Edit Light Probes" in Inspector
  * Add resolution by creating more light probes
  * Select some probes, then click on Duplicate
  * Focus on adding probes around objects producing light

* You can turn on/off light probles for an object in the "Light Probes"
  property (set to Blend Probes or Off).

* There are some debugging visualization settings in Lighting Settings
  for light probes.

* Tip: Some "static" objects could instead by made non-Static
  and lit by light probes rather than lightmapping.
  This could save a lot of diskspace. Best for non-important objects.

### Reflection Probes

* A reflection probe stores a reflection at that location,
  then used by reflective objects.

* By default, reflective objects (metallic, smooth material)
  only reflect the SkyBox.

* The sphere in the middle of a new reflection probe is a preview
  of the reflection. The cube around it specifies which game objects
  are influenced by the probe.

* Create a reflection probe:
  * Create -> Light -> Reflection Probe
  * Resize reflection cube to encompass what to reflect

* Baked reflection: works only with Static objects
  * Bake either in Inspector of the selected probe,
    or under Lighting Settings in the Generate Lighting drop down

* Realtime reflection: works with all objects (very expensive, so use sparingly)
  * True realtime: set Refresh mode to Every Frame
  * Time slicing helps spread out the work over multiple frames
    (helps performance)
  * Tip: To have a perfectly reflective sphere reflect as you move with it
    (as if holding it), make the reflective probe a child of the sphere
    (itself a child of the FPS Controller) and set it to realtime
    without time slicing

* Objects not marked as Reflection Probe Static won't show up
  on the baked reflection probe

* Probe has settings similar to a camera (clipping plane, culling mask, etc.).
  * Importance setting is used when there are multiple probes
  * Blend distance determines the softness of the edge of the reflection
    probe box
  * Use Box Projection for indoor spaces, and make the box
    about the size of what to reflect

* Even without fully reflective objects, it's good to have
  a reflection probe because it affects smooth objects

## Post-Processing

* The Universal Render Pipeline is not compatible with the Post-Processing
  Packgage (v2). Instead, use its integrated post-processing effects:
  * Select a camera and check the Post Processing checkbox
  * Create -> GameObject -> Volume -> Global Volume
  * Select the Global Volume object and next to the Profile label
    (in the Volume component) click on New
  * Click on Add Override to add an effect

* Add post-processing (version 2):
  * Add post-processing package (version 2) from Package Manager
  * Create a Post Processing Profile in Project View
  * Create an empty game object (named PostProcessingGlobal)
    * Add the Post-process Volume Component
      * Check Is Global
      * Drag the created Profile to the Profile slot
    * Set Layer to PostProcessing
  * Select the Camera and add Component Post-process Layer
    * Set Layer field of Component to PostProcessing
    * Set anti-aliasing (like FXAA) with Fast Mode (if it looks OK)
    * Uncheck Deferred Fog if not using
  * Go back to Profile and add desired effects
    * Common effects:
      * Ambient Occlusion: creates shadows around intersecting objects
      * Bloom: glowing around lights
      * Antialiasing
      * Grain
      * Color Grading
      * Screen-space reflections
      * Vignette: dark border around scene
    * Can edit here, or in PostProcessingGlobal object

* Post-processing volumes
  * Additional effects: lens distortion, chromatic aberration, vignette
  * To trigger different post-processing volumes:
    * Create a new GameObject with a PostProcessingVolume Component
    * Add a Box Collider with IsTrigger set to true
    * Uncheck IsGlobal
    * Adjust Blend Distance to transition smoothly
    * Set Priority to 1 (if it's overlapping with another, like Global,
      post-processing volume
    * Drag an appropriate object (e.g., FPS Controller) to the Trigger field
      in the Post Process Layer Component (in Camera) that will trigger
      the transition

## Materials

* A Material is an asset that specifies a specific Shader script
  that drives the actual work of drawing the object on the screen.

* Material surface properties:
  * Base Map: basic color texture of the model
  * Metallic Map: texture that controls shininess
  * Normal Map: provides appearance of bumps and grooves
  * Occlusion Map: accenting surface shadows associated with occlusion
  * Height Map: how objects occlude others

* Albedo map
  * Don't include highlights or shadows; just have color
  * Also called "diffuse"
  * Can apply a tint shift (color selection on the right)

* Alpha map (alpha channel of albedo map)
  * Best to use PNG or TGA
  * Rendering mode
    * Cutout: make parts of the texture completely transparent
      depending on threshold
    * Fade: apply entire alpha map on texture (as a normal picture
      with an alpha channel)
    * Transparent: Like fade but retains the reflective and shadow properties
      of the texture (good for glass materials)
  * In Photoshop, you can go to Channels (next to Layers tab)
    and add an Alpha channel (if not already there)

* Metallic map
  * With a map assigned, Red channel describes where metallic effect
    occurs and Alpha channel describes how smooth (shiny) it is
    * The Alpha channel may be from the metallic map or albedo map
      (designated by the Source option)
  * Without a map assigned, you can choose how metallic the whole material is
    (using the slider)
  * For efficiency in mobile games, you can turn off Specular Highlights
    or Reflections (in the Forward Rendering options)
    * Can also control programmatically (e.g., turn off when far from camera)

* Normal map
  * Make sure its type is set to Normal map
  * Can be made to come out as bumps or go in as if material was picked off

* Creating a normal map
  * Can use a Photoshop plug-in, like xNormal
  * Can use stand-alone program, like Blender, CrazyBump, 3DS Max
  * In Unity, can create a normal map from a grayscale image,
    such as a height map

* Height map
  * Similar to normal map, but produces shadows on self,
    creating a bigger effect of depth within the texture

* Occlusion map
  * Ambient occlusion is the shadow in corners and crevices of an object
  * For efficiency, occlusion can be included in the albedo map itself
    (baked occlusion)
  * If using a separate occlusion map, you have more control
    on the amount using the slider

* Emission
  * Emission map is similar to the metallic map in that it defines
    where and how much to apply emission
  * Global illumination
    * Baked: has effect only if baked global illumination is enabled
    * Realtime: (unclear)

* Detail map
  * Under Secondary Map, you can specify Detail and Normal maps
    for an additional "detail" map that's applied within the Detail Mask
  * For example, used to add little notches in stone
  * Increase Tiling to make the detail smaller
  * Play with Offset to remove any seams between tiles

* Fresnel reflections: reflections on the edges of an object.

* Use Emission in Material to make the object glow.

* Setting Surface Type to Transparent allows the color of the material
  to have Alpha.

* Edit an object's material (like the mainTexture):

      object.GetComponent<Renderer>().material.mainTexture = image;

* Edit a material property (by name, like "\_Exposure"):

      material.SetFloat("_Exposure", 1.0f);

* You can save material settings, and reload them if needed
  (settings icon, top right)

## Shaders

* The default "Lit" shader offers a lot of variety and options,
  while being efficient at runtime.

* Use Unlit shader for object that don't need lighting; it's more efficient.

* Metallic and specular shader types:
  * Specified per material
  * Each type defines how the channels of the maps (albedo, metallic, specular)
    are used to create the effect
  * Mostly a matter of preference, but in some cases you may want to use
    a specific type (metallic or specular)

* Other shader types:
  * Unlit: does not use any scene lighting, so highly performant
  * Particle: has blending properties
  * Mobile: performant (but simplified) shaders
  * Standard: performance decreases with more maps used

* Custom shader types:
  * Standard: comes w/ generated code, interacts with lighting
  * Vertex and Fragment: does not need to interact with lighting
  * Fixed function: legacy, simple effects

* Create a custom shader:
  * Use Unity Shader Graph
  * Or, Create -> Shader, then choose the type
    * Cg programming language

## Textures

* UV: texture coordinate (U is like X, and V is like Y)
  * Range is 0 to 1
  * Can have multiple UV shells per object

* Use texture sizes of power of 2.

* Procedural textures
  * Created algorithmically
  * Resolution independent
  * Example: clouds

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

* Deferred rendering:
  * No limits on the number of lights that can affect an object
  * Lights evaluated per pixel (smooth and natural)
  * Processor intensive
  * Does not support anti-aliasing
  * Cannot render semi-transparent objects

* Forward rendering:
  * Objects can only be lit by up to four lights
  * Some lights rendered per pixel, remaining per vertex
  * Does not support cookies or normal maps
  * Limit of one light that can cast shadows
  * Very fast (practically zero Processor cost)
  * Ideal for mobile devices

* HDRP and URP and the new render pipeline templates in Unity (2019 and later).
  They use the new Scriptable Rander Pipeline.

* The old render pipeline is available via "2D", and "3D" project templates.

* Project Settings -> Graphics shows the Scriptable Render Pipeline settings
  in use, which are stored as an asset. This asset can be edited
  in the Inspector.

* More information on render pipelines at [https://docs.unity3d.com/Manual/BestPracticeLightingPipelines.html](https://docs.unity3d.com/Manual/BestPracticeLightingPipelines.html)

## Audio

* Quick way to have ambient sound:
  * Drag audio clip to Hierarchy
  * Check Loop

* Emit a sound from an object:
  * Add an Audio Source component to the object
  * Drag an audio clip from Assets to AudioClip slot
  * Uncheck Play On Awake if the sound is controlled by a script
  * Add Audio Listener component to the Main Camera
  * To play in code:

        audioSource = GetComponent<AudioSource>();
        audioSource.Play();

  * To play any audio clip:

        // audioClip could be a private [SerializeField]
        audioSource.PlayOneShot(audioClip);

* Play sound when objects collide:
  * Add Audio Source to one of the objects, and uncheck Play On Awake
  * Drag the desired audio clip to the AudioClip slot
  * Add Audio Listener component to the Main Camera
  * In code, create method ``OnCollisionEnter(Collision collision)`` and
    then call ``Play()`` on the audio source

* Synchronize audio beats with the scene:
  * Download Unity-Beat-Detection from GitHub
  * Use the code's ``OnBeat`` event to script when a beat is detected

* Get free sounds from: https://freesound.org/

* To create your own sounds: Audacity (free, open source)

## Particle System

* Create a new particle system:
  * Create an Empty Object
  * Add component ParticleSystem
  * Or, simply Create Effect -> Particle System

* Play the particle system in code:

      particleSystem.Play();

* Many numerical properties of the particle system can be a constant,
  a curve, or random between two constants or curves.

* When creating projectile effects, use Rate over Distance in the
  Emission section, as it will produce effects only when it moves

* Turn on Collision to interact with the scene:
  * Type World collides with anything in the world
  * Turn on Send Collision Message
  * Detect with ``OnParticleCollision()``

* For more flexibility with the particle rendering, use a custom shader:
  * Create -> Shader -> Universal Render Pipeline -> Unlit Shader Graph
  * Double-click to edit in Shader Graph
  * Change the preview window to use a Quad
  * Add Color and Texture2D (name it "MainTexture") properties
    * Set Color of the Color to be HDR
    * Set Default of the Texture2D to be Default-Particle
  * Add a Sample Texture 2D node and a Multiply node
  * Connect Color to input of Multiply node
  * Connect Texture2D as input of Sample Texture 2D,
    and connect its output to Multiply node
  * Add a Vertex Color node and a second Multiply node
  * Connect output of first Multiply node to the second Multiply node
  * Connect output of Vertex Color to the second Multiply node
  * Add a Split node
  * Connect output of the second Multiply node to the Split node
  * Connect output of the Split node to the Alpha of the Fragment
  * Connect output of the second Multiply node (again) to the Base Color
    of the Fragment
  * Click on Save Asset
  * In Unity Editor window, right-click the new shader and then
    Create -> Material
  * Click on the new material and set its Color and MainTexture
    * MainTexture could be the Default-Particle or a custom texture (see below)
  * Drag and drop this material on the Material slot of the particle system
    (in the Render section)

* Create a custom texture to use for the particle material:
  * Open Photoshop and create a new image (e.g., 512x512)
  * Paint the whole image black
  * Add a new layer
  * Select the brush tool
    * Set brush size to half the image size, and the hardness to 0%
    * Set color to white
  * Click once in the middle of the image to create a single soft dot
    * Optionally, modify the dot to make a unique pattern
  * Select both layers and align horizontally and vertically to center the dot
  * Hide the background layer
  * Export as a PNG to the Unity project
  * Drag new texture to the particle material's texture slot

* Create sparks (from Udemy VFX course):
  * Name the ParticleSystem object as "Sparks"
  * Set Duration to 1.0
  * Set Emission rate to 50
  * Set Start Lifetime to be random between 0.7 and 2
  * Set Start Speed to be random between 1 and 15
  * Set Start Size to be random between 0.05 and 0.2
  * Set Gravity to 1
  * Set Simulation Space to World
  * Open Shape section
    * Set Radius to 0.1
    * Set Angle to 20
  * Open Render section
    * Set Render Mode to Stretched Billboard
    * Set Speed Scale to 0.1
    * Set Length Scale to 1
    * Set Max Particle Size to 3
  * Open and turn on Collision
    * Set Type to World
      * Make sure the scene's ground also has a collider
    * Set Dampen to 0.5
    * Set Bounce to 0.5
  * Open and enable Color over Lifetime
    * Set gradient to be between yellow and deep orange
    * Decrease Alpha of the end to around 128
    * Change Color to be random between two gradients
    * Copy/paste the first gradient into the second
    * Set second gradient to be between brighter yellow and orange/red
  * Open and enable Sive of Lifetime
    * Set to something that decreases in size
  * Duplicate the "Sparks" particle system and name it "Beam"
  * Set Start Lifetime to be random between 0.2 and 0.5
  * Set Start Speed to 0
  * Set Start Size to be random between 0.5 and 0.7
  * Set Gravity to 0
  * Set Simulation Space to Local
  * Open Emission and set Rate over Time to 5
  * Disable Shape and Collision
  * Open Render and set Type to Billboard
  * Open Size over Lifetime
    * Change curve to have an undulating shape (add 5 keys to do it)
      between around 0.9 and 1
  * Duplicate the "Beam" particle system and name it "Shiny"
  * Set Start Lifetime to be random between 0.3 and 0.6
  * Enable 3D Start Size and set to be random between (2.5, 0.25, 1)
    and (1, 0.1, 1)
  * Set Start Rotation to be between -360 and 360
  * Set Emission Rate over Time to 15
  * Set Color over Lifetime to be a single gradient,
    going from Alpha 0 to 255 to 0, and all colors are white
  * Set Start Color to be random between hsva (39, 77, 100, 14)
    and hsva (21, 21, 100, 20)
  * Set Size over Lifetime to decrease
  * Move Sparks and Shiny to be children of Beam,
    so you can see all particle systems play at the same time

* Create a "hit" with quick sparks:
  * Duplicate the object with the particle system above
  * Drag to Prefabs to treate a new prefab out of it
  * Select particle system and its children and disable Looping
  * Set Emission Rate over Time to 0
  * Select "Beam" particle system
  * In Emission section, add one Burst with Count of 1
  * Set Color over Lifetime to one Color,
    and make it go from Solid yellow/orange to pure Transparent
  * Set Size over Lifetime to a decreasing curve
  * Set Start Color to a lower Alpha
    * Instructor did 15, but it seemed too low, so I did 75
  * Set Start Size to 2
  * Set Start Lifetime to 0.4
  * Select "Sparks" particle system
  * In Emission section, add one Burst with Count of 30
  * Rotate "Beam" object -90 degrees in the X axis so it faces up
  * Change "Sparks" Shape Angle to 60 degrees
  * Set Start Lifetime to be random between 0.5 and 1.5
  * Add Burst to "Shiny" and set count to 10
  * Change Color over Lifetime to go from full opaque to full transparent
  * In Start Color, make the second color more orange
  * Change 3D Start Size X to 3.5
  * Change Start Lifetime to be random between 0.2 and 0.40
  * Duplicate the "Beam", name it "Flash", and delete its children
  * Set Start Lifetime to 0.2
  * Set Start Size to 6
  * Move as a child of "Beam"
  * Experiment with colors by changing yellow/green to blue or green
    * For those with two colors (because it's using random between colors),
      leave one of them as yellow to get a mix of sparks and beams

* When creating explosions, may want to instantiate a particle system prefab
  (rather than have it as a child object), so that it survives
  when the main object is destroyed (because of the explosion):

      // Rotation causes the effect to happen perpendicular to the object hit
      var impact = Instantiate(hitEffect, hit.point, Quaternion.LookRotation(hit.normal));

  * Put inside an empty parent (e.g., "Spawn at Runtime") to keep
    the Hierarchy organized
  * Destroy the explosion particle object when done
    (e.g., ``Destroy(gameObject, seconds)``)

* Standard Assets contains several ParticleSystem prefabs, including explosion,
  fire, and smoke.

## XR

### Configuration and Building

* Enable XR on a project:
  * Go to Window -> Package Manager and enable preview packages
  * Install the XR Interaction Toolkit
    * Also install Samples: Default Input Actions and XR Device Simulator
  * Install XR Plugin Management

* Add an XR Rig:
  * Create -> XR -> Stationary (or Room-scale) XR Rig
  * Note that there are two types of XR Rigs:
    * Device-based (under the Device-based menu): directly access controllers
    * Action-based: indirect access, takes advantage of the XR Input System
  * Browse to Assets -> Samples -> XR Interaction Toolkit ->
    1.0.0-pre8 -> Default Input Actions
  * Drag each XRI Default controller preset to the corresponding XR Controller
  * Create an empty object and call it "Input Action Manager"
  * Add the Input Action Manager component to it
  * Set the Action Assets to the XRI Default Input Actions

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
  * Open Project Settings -> Quality
  * Create a new Quality Level, name it Oculus Quest
  * Set the Oculus Quest level as the Default for Android
  * Close window
  * Duplicate UniversalRP-HighQuality and name UniversalRP-OculusQuality
  * Set Pixel Light Count to 1
  * Anti Aliasing: 4x
  * Go back to Player Settings -> Quality, and change the Rendering
    setting to UniversalRP-OculusQuality

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

* Teleportation and snap turn (new way, action-based):
  * Create an empty object, name it "Locomotion System" and
    add the Locomotion System component to it
  * Add Teleportation Provider component to XR Rig, and drag
    Locomotion System to slot
  * In Samples folder, drag XRI Default Snap Turn to the XR Rig,
    and drag Locomotion System to it
  * Rename the "LeftHand Controller" to "LeftHand Teleport"
  * Disable the XR Interactor Line Visual
  * In XR Controller component, drag the XRI LeftHand/Teleport Mode Activate
    to the Select Action Reference
  * Disable all other Actions
  * In XR Ray Interactor, set Interaction Layer Mask to Teleportation
    (will need to create one), Line Type to Projectile, and Velocity to 8
  * In XR Ray Interactor, add Select Entered Action (in Interaction Events)
    and drag the XR Interactor Line Visual, choose enabled function,
    and check the checkbox
  * Do the same thing for Select Exited Action but leave checkbox unchecked
  * Repeat the steps for the RightHand Controller
  * Drag XRI Default Left (and Right) Controllers to have a non-teleport set
  * Make the floor a Teleportation Area and set Layer Mask to Teleportation


* Teleportation and snap turn (old way):
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

* Create an animation clip:
  * Open animation window: Window -> Animation -> Animation
  * Select top-level object to animate
  * Click on Create
    * Save the animation with a given file name
  * Click on Add Property and choose the property to animate (e.g., Rotation)
  * Move the time slider to the last keyframe
  * Click on the record button
  * Change the property on the object itself
  * Stop recording
  * To not make the animation loop over, uncheck Loop Time in its properties
    under Inspector

* Edit animation curves:
  * In Animation window, click on the Curves button
  * Add key frames, and edit the curves manually by right clicking
    on the curve nodes and modifying the type of nodes and tangents to use

* Create another animation clip for the same object:
  * Click on the animation drop down and select Create New Clip
  * You can copy keyframes from one clip and paste into another
    if you need to get the same keyframe values

* Retiming animations
  * In the Dopesheet, you can left-click and drag to select keyframes
    and move the handles to change their timing
  * You can also select a single keyframe by clicking on a top diamond,
    and drag it to move the whole keyframe
  * In the Curves, you can also left-click and drag to select nodes
    and move the handles horizontally (effects timing)
    or vertically (affects values)

* Animation events
  * At any moment in time, you can add an event (click on the upper-right
    button, next to the add keyframe button)
  * Link the event to an existing method of a component attached to the object
    being animated
  * Useful for performing a sound or causing damage to player
    at exact enemy attack in animation

* Animator controller
  * When an animation clip is created, an animatior controller
    is automatically created and the Animator component on the animated object
    is linked to that new controller
  * The Entry box specifies which animation clip to start when the game starts
    * To not start animating anything, create an empty "idle" clip
      and assign it as the default state, so that Entry points to it
  * When creating a Transition, by default when an animatior clip ends,
    it goes to the next one in the transition.
    * To not go to the next clip, uncheck the Has Exit Time property
      of the Transition

* Control when to play the next animation clip:
  * Click on the add parameter button and choose a type (e.g., bool)
  * Click on a Transiton and set the Conditions based on the parameter value
    to specify when to activate the transition

* Scripting animator parameters
  * First, get the Animator component (example, call it "anim")
  * Call Set\* methods on the Animator object
  * Example, ``anim.SetBool("isOpen", true);``

* Rig import settings
  * Once a rigged model has been added to Assets, select it and click on Rig
    tab in the Inspector
  * Animation Type
    * Generic: Creates avatars based on bones (no humanoid features)
    * Humanoid: Helpful for animations that could be retargeted to other models
  * If choosing Humanoid Animation Type
    * Select Create From This Model for Avatar
    * Click on Apply, and then click on Configure
    * On the green map figure, solid circles are mandatory bones,
      and dashed circles are optional
    * Unity may have automatically mapped the bones; if not, click on
      Mapping dropdown, then on Automap
    * To accept, click on Apply
  * Click on the Muscles and Settings tab
    * In Muscle Group Preview, drag the preview sliders to ensure
      the mapping is correct
    * In the Per-Muscle Settings, you can adjust the mix and max range
      of motion

* Animation import settings
  * Import an animation
  * Select it and click on the Rig tab
  * Choose Humanoid animation type
  * Choose Copy from Other Avatar
  * Choose the Source for the avatar to an existing model and click Apply
    * I got an mis-match error when I tried it with a Mixamo animatior,
      so I chose Create from this Model, configured it, and clicked on Apply
  * Click on Animations tab
  * Can specify Start and End of animation to use
  * Set Loop Time if the animation should loop
    * Check Loop Pose if there's a mismatch between start and end of animation
      for looping
    * Root Transform Rotation/Position
      * Can be based on Original or Feet, and have an Offset
      * If Average velocity or angular speed are not exactly 0.000,
        check Bake into Pose to avoid drifting (especially in a loop animation)
  * For a walking animation, make sure that the average velocity only moves
    in the Z direction and the angular speed is 0
    * If they're not, play with the Offsets and/or Bake Into Pose
    * For sample walking Mixamo animation, this worked:
      * Loop Time (checked), Loop Pose (checked)
      * Root Transform Rotation, Bake Into Pose (checked),
        Based Upon Body Orientation, Offset 0
      * Root Transform Position (Y), Bake Into Pose (checked),
        Based Upon Feet, Offset 0
      * Root Transform Position (XZ), Bake Into Pose (unchecked),
        Based Upon Center of Mass

* Check out DOTween package for an animation API.

### Blend Trees

* Create a blend tree:
  * Right-click on Animator window, then Create State -> From New Blend Tree,
    then name it
  * Double-click it
  * Rename "Blend" paramater to something appropriate (like "Velocity")
  * In Inspector, click on Add button (in Motion) and select Add Motion Field
  * Select an animation clip
  * Add another motion field, and choose a second animation clip
  * Add as many as needed
  * In a script, use SetFloat to control the blend
  * To control the parameter value thresholds (when each animation starts),
    uncheck Automate Threshold and enter the thresholds for each animation
  * One can work with more complex blends by changing the Blend Type

* Animation layers
  * Allows you to blend layers together
  * Put common animations in the Base Layer
  * Put, for example, attack animations in additional layer
  * Create a new layer and name it
  * Add animations as needed
    * May need to create an Empty start as Entry
  * Click on the "cogwheel" to open layer settings
  * The Weight controls how much of a layer shows up
  * Blending Override overrides the base layer
  * Blending Additive adds animation on top of base layer
  * Is scripting, use SetLayerWeight of the Animator component

* Avatar masks
  * Create -> Avatar mask, and name it
  * Expand Humanoid in Inspector
  * Click on body part to mask that part
    * May want to mask root motion (and/or IK) as well
  * Set the mask on Animation Layer via the cogwheel

## Timeline

* Timeline is used to choreograph object animations, audio, events.

* Unity Timelines can have one or more tracks of audio, activation,
  animation, and signal. Timelines are a type of Unity Playable.

* Create a Timeline and add an audio file:
  * Create an empty object and name it "Director" (or "Master Timeline")
  * Select Window -> Sequencing -> Timeline
    * Click on the Lock button to always show the timeline
  * Click on Create and save it (e.g., in a "Playables" folder)
  * Drag an audio file to the left panel of the Timeline editor

* Activate or deactivate objects in a Timeline:
  * Drag the object to the Timeline editor, then choose Activation Track
  * Adjust the start and ending rectangle, which specifies when the object
    will be active (it will be inactive outside of the rectangle).
  * Note: Objects may be children of a parent already on the Timeline

* Animate the scale (i.e., size) of an object in Timeline:
  * Drag the object to the Timeline Editor, then choose Animation Track
    * Or click on Add button, then Animation Track
    * me: May need to select Create Animator based on a tutorial
  * Click on the record button
    * If unable to record animation, set Unity Layout to Default and back
    * Start with first keyframe by moving the object a bit to create a keyframe
  * Set the playhead to 0:00 and set the object scale
  * Set the playhead where desired and set the new object scale
  * Stop recording
  * Add more keyframes
    * May need to expand property to edit it

* For more flexibility in animation, including adding more keyframes,
  use the Animaton Window:
  * Click on the three-dot icon next to a track
  * Select Edit in Animation Window

* Create a Track Group to organize tracks in the Timeline

* You can also animate a script component's serialized properties.

* But you can't animate non-GameObjects, so to animate something
  like a property of the Skybox (which is a Material, not a GameObject),
  write a script that controls it and animate the script's property.

* Add animator to Timeline:
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

* Control a timeline within another timeline
  * Use case: control waves of enemies that have their own timeline
  * Add a Control Track to the parent timeline
  * Drag the child timeline to the Control Track
  * Adjust the child timeline (where to begin or stretch)

* Use Control Track to turn on/off UI elements
  * Drag UI element (e.g., Image) to the timeline and it will create
    a Control Track
  * Adjust the Control Track to determine when and how long element is active

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

* To use a custom font with TextMesh Pro:
  * Fonts: https://www.dafont.com
  * Drag font file to Assets
  * Go to Window -> TextMeshPro -> Font Asset Creator
  * Select font from Assets
  * Click on Generate button

* Make the Toggle do something when toggled/untoggled:
  * In the Inspector, there is a Toggle component,
    which has an "On Value Changed" event
  * Drag an object and select the function to run
  * Or, code what to do programmatically

* Image
  * The image to bring in should have Texture Type of Sprite (2D and UI)

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

* Unity guide on the Terrain editor:
  * https://docs.unity3d.com/Manual/script-Terrain.html

* Add Terrain
  * Add 3D Object -> Terrain

* Configure Terrain
  * Click on "Terrain Settings" button

* Create hills
  * Select "Paint Terrain" and paint hills on terrain
  * Press Shift while painting to remove hills
  * Decrease or increase brush size with [ and ]
  * Decrease Opacity for lower hills

* Paint to a specific height
  * Change paint mode to "Set Height"
  * Specify the Height
  * Use to set higher Height and create valleys
    * Will then want to set Y of terrain to negative to bring down sea level
      to 0

* For more Terrain Tools, install it from Package Manager

* Paint textures on terrain:
  * Select "Paint Texture" mode
  * Add Layers (e.g., dirt, rock from Terrain Tools)

* Add trees:
  * In Terran properties, click on "Paint Trees" button
  * Click on Edit Trees... then Add Tree
  * Select a tree Prefab
    * The Standard Assets package has some tree prefabs
    * Can add more trees if desired

## Code

### General

* ``OnEnable()`` is called when the object is enabled and active.

* ``Time.time`` is the number of seconds since the game started.

* Call a method after ``x`` seconds:

      Invoke("MethodName", x);

* To perform something every x seconds, use ``InvokeRepeating``.

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

### Coroutine

* A coroutine needs to return ``IEnumerable``. Use ``yield return null``
  to pause the execution of a coroutine and resume in the following frame.

      IEnumerator Fade()
      {
          Color c = renderer.material.color;
          for (float alpha = 1f; alpha >= 0; alpha -= 0.1f)
          {
              c.a = alpha;
              renderer.material.color = c;
              yield return null;
          }
      }

* To start a coroutine, call ``StartCoroutine`` and pass a call
  to the function:

      StartCoroutine(Fade());  // call the function inside

* Instead of resuming in the following frame, a coroutine can resume
  after a specific amount of time by using
  ``yield return new WaitForSeconds(0.1f)``.

* There are cases where you don't need to perform an action on every frame,
  so use a coroutine with ``WaitForSeconds``. For example:

      IEnumerator DoCheck()
      {
          for (;;)
          {
              if (ProximityCheck())
              {
                  // Do something here
              }

              yield return new WaitForSeconds(0.1f);
          }
      }

* A coroutine can be stopped with ``StopCoroutine`` and
  ``StopAllCoroutines`` (which applies to all coroutines for this behavior).

* A coroutine is automatically stops if:
  * ``SetActive`` is set to false
  * Called ``Destroy`` on the instance

* A coroutine is not automatically stops by setting ``enabled`` to false.

### Messaging

* ``BroadcastMessage("MethodName")``
  * Calls the method MethodName on the attached gameObject or its children
  * Eliminates the use of ``GetComponent<Component>().MethodName()``
    because it will automatically find MethodName in any Component of gameObject

### Shoot From Weapon

    // In Update() of the Weapon Component:

    // Use camera for FPS, but probably want to use weapon in VR
    if (Physics.Raycast(camera.transform.position, camera.transform.forward,
        out RaycastHit hit, range))
    {
        EnemyHealth target = hit.transform.GetComponent<EnemyHealth>();
        target.TakeDamage(30.0f);  // make 30.0 a SerializeField
    }

    // Track of enemy health (as a Component)
    public class EnemyHealth : MonoBehavior
    {
        [SerializeField] float hitPoints = 100.0f;

        public void TakeDamage(float damage)
        {
            hitPoints -= damage;
            if (hitPoints <= 0)
                Destroy(gameObject);
        }
    }

* Weapon zoom:
  * Decrease camera Field of View (``Camera.fieldOfView``)

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
    (with ``WaitForSeconds``).
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

* See Unity e-books:
  * https://resources.unity.com/games/unity-e-book-optimize-your-mobile-game-performance
  * https://resources.unity.com/games/performance-optimization-e-book-console-pc

## Build Settings

* Color space
  * Linear
      * More realistic
  * Gamma
      * Colors brighten to white
      * May be required on some mobile devices

## Editor

* Trick to quickly replacing an object with another in the same location:
  * Make the new object a child of the old one
  * Reset its position
  * Move it out of its parent (preserves world location)
  * Delete the old object

* Draw a sphere around an object when selected:

      // Method on object (like Update)
      void OnDrawGizmosSelected()
      {
          Gizmos.color = Color.red;
          Gizmos.DrawWireSphere(transform.positon, radius);
      }

* Editor scripts extend the features of the Unity editor.
  * See https://docs.unity3d.com/Manual/ExtendingTheEditor.html
  * Tutorial: https://unity3d.com/learn/tutorials/topics/scripting/editor-scripting-intro

## Resources

* Models
  * Gun with shooting animation and script, submodels for trigger,
    slider, etc.
    * Unity Asset store, search for "Modern guns nokobot"
    * Use Nokobot -> Modern Guns - Handgun -> \_Prefabs ->
      Handgun Black -> M1911 Handgun_Black (Shooting)

* Videos
  * [Scriptable Objects by Richard Fine (Unite 2016)](https://www.youtube.com/watch?v=6vmRwLYWNRo)

* Websites
  * Beginner Scripting: https://learn.unity.com/project/beginner-gameplay-scripting
  * Create with Code: https://learn.unity.com/course/create-with-code?uv=2020.3
