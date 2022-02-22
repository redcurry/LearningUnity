# Area of Effect

Notes from Udemy course "Visual Effects for Games in Unity"

## Sketching

- Start by sketching out the three main parts of an area of effect:

  1. Anticipation (0.7-1.5 sec): e.g., energy gathering (build-up)
  2. Climax: e.g., explosion
  3. Dissipation: e.g., marks on the ground

- Search online for others' effects to use as reference

## Anticipation

### Beam on the Ground

- Create an empty object (name it "vfx\_AreaOfEffect\_v1)
  and add a Particle System to it (name it "BeamGround")
- Properties:
  - Looping: Off
  - Start Lifetime: 1
  - Start Speed: 0 (will not move)
  - Start Size: 7
  - Start Color: Purple, Alpha to 50%
  - Renderer
    - Render Mode: Horizontal Billboard (parallel to the ground)
    - Material: Beam01\_Add (from Sparks tutorial)
    - Max Particle Size: 3
  - Emission
    - Rate over Time: 0
    - Bursts: 1 (Count)
  - Uncheck Shape section
  - Color over Lifetime
    - Alpha 0 at Location 0%
    - Alpha 100 at Location 90%
    - Alpha 0 ot Location 100%
  - Size over Lifetime
    - Select curve that decreases over time (circular slow decrease),
      but move last key up, so it doesn't disappear completely

### Particles going into the beam

- Duplicate "BeamGround" (name it "ParticlesIn),
  and make it the child of BeamGround
- Properties:
  - Rotation (Transform component): X: -90
  - Duration: 0.75
  - Start Lifetime: Random between 0.2 and 0.25
  - Start Speed: Random between two curves
    - Curve from -1 to -10
    - Curve from -1.5 to -15
  - Start Size: Random between 0.05 and 0.18
  - Start Color: Random between Purple (Alpha 80%) and Blue-Purple (Alpha 15%)
  - Emission
    - Rate over Time: 75
    - Remove all Bursts
  - Check Shape section
    - Shape: Hemisphere
    - Radius: 2
  - Size over Lifetime: Curve from 0 to 1 (circular fast increase)
  - Renderer
    - Render Mode: Stretched Billboard
      - Speed Scale: 0.05

### Beam Cylinder (Mesh)

#### Blender

- In Blender, create a cylinder with 16 vertices
- In Edit Mode, delete the top and bottom faces
- Set the pivot point (origin) to the bottom of the cylinder
- Select the top vertices and move them up to make the cylinder taller
- Select the top and bottom edges, and one side edge and Mark Seam
- Select all faces, then press U and then Unwrap
- Go to the UV editor and extend the top edge to the top of the UV image
- Export as an FBX (Mesh-only)

#### Photoshop

- In Photoshop, create a new file with 1000 x 1000 pixels
- Paint everything to Black
- Add a Layer
- Switch to Brush tool and select a White color
- Paint "fire looking" spikes at the bottom of the image:
  - With a large brush size, paint the bottom third with all white
  - With smudge tool, bring down the blacks into the white, forming spikes
  - Still using the smudge tool, bring up the white spikes
    to make them more pointy
  - Reduce brush size and make smaller spikes from bottom to top
  - Use the erase tool (with low opacity) to fade out some of the strong
    whites in between the spikes
- Double-click on the layer and check the Outer Glow option
  - Change color to white
  - Decrease opacity to 14%
  - Increase size to 18 px
- With erase tool at very low opacity and large brush,
  fade out the very top of the spikes
- Hide black background and export as PNG

### Unity

- Import PNG into Unity
- Duplicate Beam01\_Add material and name it Impact01\_Add
- Drag imported PNG to Texture slot of material
- Import FBX into Unity (no import materials, and scale should be 10)
- Duplicate BeamGround and rename it "Cylinder",
  and make it a child of BeamGround
- Properties:
  - Transform: Rotation X: -90
  - 3D Start Size: X:10, Y:10, Z:20
  - Size over Lifetime
    - Check Separate Axes
    - Choose decrease curves for X and Y, but leave Z constant
  - Rotation over Lifetime
    - Angular Velocity: 360
  - Renderer
    - Render Mode: Mesh, then drag the mesh of the Cylinder to Mesh slot
    - Render Alignment: Local
    - Material: Impact01\_Add

## Climax

### BeamGround

- Duplicate "BeamGround", delete ParticlesIn
- Properties:
  - Start Delay: 1.2 (in order to start after anticipation)
  - Color over Lifetime
    - White (Alpha 100%) to Black (Alpha 0%)
  - Size over Lifetime: Curve from 0.85 to 1.0

### Particles going out from the beam

- Duplicate "ParticlesIn", make it a child of the second BeamGround,
  and rename to "ParticlesOut"
- Properties:
  - Start Delay: 1.2
  - Start Lifetime: Random between 0.4 and 0.9
  - Start Speed: Random between 2 and 15
  - Emission
    - Rate over Time: 0
    - Add Burst (Count 20 to 25)
  - Shape
    - Radius: 0.5
  - Size over Lifetime
    - Curve from 1.0 to 0.2 (circular slow decrease)
  - Color over Lifetime
    - White, Alpha 100% to Alpha 0%

### Beam (or Flash)

- Duplicate second BeamGround and name it "Beam" (or "Flash"),
  make it a child of second BeamGround, and delete ParticlesOut
- Properties:
  - Transform: Y: 0.5
  - Start Lifetime: 0.2
  - Start Size: 5
  - Size over Lifetime: Linear curve (decreasing)
  - Render
    - Rendom Mode: Billboard

### Impact Beam

#### Photoshop

- Create a new 1000x1000 image, color black, and create a new layer on top
- Select Brush, white, opacity about 20%, size 150 px, hardness 0
- Draw shape of impact (5 or 6 spikes going up and to the sides
  from bottom-center of image)
- With smaller brush, add more details of impact,
  and repeat with an even smaller brush for tiny details
- Use smudge, alternating brush sizes, to bring out more white streaks,
  and push in black to create more gaps
- Add outer glow (24% opacity)
- Hide black background
- Export as a PNG

#### Unity

- Import PNG
- Duplicate Impact01\_Add and name it Impact02\_Add
- Assign new texture to Impact02\_Add
- Duplicate Beam (in second BeamGround) and name it Impact
  - Properties:
    - Transform: Y: 0
    - 3D Start Size: Random between <4, 6, 1> and <3, 4, 1>
    - 3D Start Rotation: Random between <0, 360, 0> and <0, 0, 0>
    - Start Color: Random between Purple and Blue-Purple
    - Emission:
      - Bursts: Count between 4 and 5
    - Size over Lifetime: Curve decreasing slowly (circular)
    - Renderer
      - Material: Impact02\_Add
      - Pivot: Y:0.4

### Shockwave

#### Photoshop

- Create a new 1000x1000 image, paint it black, create new layer on top
- Add grid guides (horizontal and vertical) to cross at center
- Select Elipse tool, and at the top change to "Path" instead of "Shape"
- Create circle of about 600 pixels at the center
- Select brush (set to opacity of 65%) and change color to white
  - Set size to 125 and hardness 0
- Press P on top of circle and choose Stroke Path
- Choose Brush for Tool and make sure Simulate Pressure is off
- Get rid of the guide lines
- Add outer glow
- Hide black background and export as a PNG

#### Unity

- Duplicate Impact01\_Add and rename it to Circle01\_Add
- Assign created circle texture to it
- Duplicate Beam (under second BeamGround) and name "Shockwave"
- Properties:
  - Transform: Y:0
  - Start Size: 14
  - Start Color: Dark Purple
  - Size over Lifetime: Increasing curve (circular fast)
  - Renderer
    - Render Mode: Horizontal Billboard
    - Material: Circle01\_Add

## Dissipation

### Cylinder

- Duplicate Cylinder from anticipation and make it a child of second BeamGround
- Properties:
  - Start Delay: 1.2
  - Start Lifetime: 0.5
  - 3D Start Size: Z:25
  - Color over Lifetime: White, Alpha 100% to Black, Alpha 0%
  - Size over Lifetime: X/Y constant, Z grows

### Vertical Particles

- Duplicate ParticlesOut and name it ParticlesVertical
- Properties:
  - Start Lifetime: Random between 0.8 and 1.8
  - Start Speed: Random between 0.5 and 7
  - Start Size: Random between 0.01 and 0.1
  - Shape
     - Shape: Cone
     - Angle: 0
     - Radius Thickness: 0.2

### Cracks on Ground

#### Photoshop

- Create 1000x1000 image and name it Crack01, paint background to black,
  and add new layer
- Brush with opacity at 20%, color white
- Draw shape of the crack (irregular cracks around the center)
- Add white outer glow
- Remove black background and save as PNG

#### Unity

- Duplicate one of the texture materials
- Drag created PNG to MainTexture of material
- Duplicate Shockwave and rename it to Crack
  - Properties:
    - Start Lifetime: 1.5
    - Start Size: Random between 6 and 6.5
    - Start Rotation: Random between -360 and 360
    - Flip Rotation: 1
    - Start Color: Bright Purple
    - Uncheck Size over Lifetime
    - Renderer
      - Material: Crack01
- Duplicate ParticlesAdditive\_HDR Shader (from a previous lesson)
  and rename it to Particles AB\_HDR
- Open it in Shader Graph and change the Blend mode from Additive to Alpha
  (in Unlit Master properties), save it
- Back in Project window, right click on the shader and create a new Material,
  call it Crack01\_AB
- Assign the Crack01 texture to the Crack01\_AB material
- Duplicate Crack particle system and name it CrackAB
  - Properties:
    - Start Delay: 0
    - Start Lifetime: 3
    - Start Size: 1.025 (so mark is a bit bigger than main crack)
    - Start Rotation: 0
    - Flip Rotation: 0
    - Start Color: Black, Alpha 100%
    - Color over Lifetime
      - Remove right black color (so it is always white),
        and move first alpha to at around 30%
    - Renderer
      - Material: CrackAB
      - Order in Layer: -1 (so it appears below main crack)
- Back in Crack particle system, check Sub Emitters,
  and drag CrackAB to Birth slot (click on Yes, Reparent)
  - This feature simply creates another particle system when
    the selected event occurs (in this case, Birth)
- Set Inherit to Size and Rotation (select both)
  - For many properties, it actually multiplies the parent and child values

## Touches

### Contrast of BeamGround (of anticipation)

- Duplicate Beam01\_Add to Beam01\_AB and change its shader to ParticlesAB\_HDR
  - Change its Color Intensity to 0 and set it to White
- Duplicate first BeamGround (part of anticipation), delete its children,
  and call it BeamGroundAB
  - Properties
    - Start Color: Black, Alpha 100%
    - Start Size: 20
    - Renderer
      - Material: Beam01\_AB
      - Order in Layer: -1
- Make it a child of BeamGround

### Contrast of BeamGround (of climax)

- Duplicate second BeamGround, delete its children,
  rename to BeamGroundAB
  - Properties
    - Start Lifetime: 2
    - Start Size: 15
    - Start Color: Black, Alpha 100%
    - Renderer
      - Material: Beam01\_AB
      - Order in Layer: -1
- Make it a child of BeamGround

## Fire Version: Add Flame Icons

### Photoshop

- Create 1000x1000 image, paint background to black, add new layer
- Select Brush with 10-15% opacity, White color
- Draw a basic flame shape without lifting brush
- Use a combination of Eraser and Smudge to shape the flame, rounding the bottom
- Add highlights using a smaller brush to add details
- Use Eraser to create "holes" in flame that add interesting detail
- Add outer glow
- Hide the black background and export as PNG

### Unity

- Duplicate Beam01\_Add material and name it Flame01\_Add
- Drag PNG to its Texture slot
- Duplicate ParticlesIn and rename it to FireIn
  - Properties
    - Transform: Y:0.3
    - Start Speed
      - Change Y axis max to 10
    - Start Size: Random between 0.3 and 0.8
    - Start Rotation: -360 and 360
    - Flip Rotation: 1
    - Shape
      - Shape: Circle
      - Radius: 1.75
      - Radius Thickness: 0.1
    - Emissions
      - Rate over Time: 30
    - Rotation over Lifetime
      - Angular Velocity: Random between 360 and -360
    - Renderer
      - Render Mode: Billboard
      - Material: Flame01\_Add0
- Duplicate FireIn, name it FireOut, and make it a child of second BeamGround
  (of climax stage)
  - Property
    - Start Delay: 1.2
    - Start Lifetime: Random between 0.2 and 0.5
    - Start Speed
      - Maximum Y axis to 5
      - Extend upper left key point up to go to 5
    - Shape
      - Radius: 1
    - Color over Lifetime
      - Move middle-right alpha to be at location 10%
    - Size over Lifetime
      - Go from large size to small
    - Emission
      - Rate over Time: 0
      - Bursts
        - Count: Random between 5 and 7

## Nature Version

- In Blender, create a spiral
- In Photoshop, create a lightning/tree dentrite structure texture
- Duplicate Cylinder and replace mesh and material (with new texture)
- Duplicate entire particle system and rotate 180 to create a DNA-like structure
- Play with the properties to adjust size, lifetime, etc.
