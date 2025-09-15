# Official AntScript Changelog
## Stages:
Egg, Larva, Pupa, Worker, Soldier, Major, Super Major, Queen
## Egg
### V0.01
- Create AntScript
- Add basic syntax ideas
- Brainstorm syntax
- "At a glance" cheatsheet:
```
  # comment

  game "My First AntGame"  # project title (optional)

  import math, time as t   # stdlib modules (aliased import)

  const MAX_ENEMIES = 25
  let speed = 6.0          # mutable
  let lives: int = 3       # optional type hint

  # --- Scene ---
  entity World:             # root node
  entity Player:
    mesh Cube
    position (0, 1.6, 0)
    scale (1, 1, 1)
    tag "player"
    on init:
      print "Player ready"
      on tick(dt):
      if key.down("W"): move forward speed * dt
      if key.down("S"): move backward speed * dt
      if key.down("A"): rotate (0, -90*dt, 0)
      if key.down("D"): rotate (0,  90*dt, 0)

  entity Camera:
    kind "Camera3D"
    parent Player     # parenting by name
    offset (0, 1.5, -4)
    look_at Player

  # Spawn sugar (runtime)
  spawn Enemy as e at (rand(-5,5), 0, rand(-5,5)) parent World
  # --- Functions ---
  func damage(target, amount: int):
  target.health -= amount
  if target.health <= 0:
    destroy target

  # --- Events (global) ---
  on tick(dt):
  # runs after all entity ticks
  if count(entity where tag == "enemy") < MAX_ENEMIES:
    spawn Enemy parent World

  # --- Components (opt-in) ---
  component Health:
    let health: int = 100
    func heal(n): self.health += n
  ```

### V0.02
#### Updated code:
- Variables:
```
localVar health 100        # scoped to entity + its children only
folderVar highScore 0      # shared across all scripts in this folder
deviceVar playTime 0       # per-device (local to the player)
serverVar serverTime 0     # shared across the game server
worldVar globalRank 0      # shared across all servers/devices
coins 50                   # default = deviceVar

```
- Comments:
```
# single line comment

#* 
   This is a multi-line comment
   It starts with #* and ends with *#
*#

```
- Entities and parenting:
```
entity Player:
   kind "Cube"
   position (0, 1, 0)

entity Camera:
   kind "Camera3D"
   position (0, 2, -5)

# parent manually
Camera parentIs Player

# Nested/auto-parenting:
entity Player:
   kind "Cube"
   entity Camera:
      kind "Camera3D"
      position (0, 2, -5)
```
- Scripts/files
```
script playerController None    # make new blank script
script moveScript "scripts/move.ant"   # attach existing file
```
- Operators and positioning:
```
# Math: + - * / % **

# Comparison: == != < > <= >=

# Boolean: and or not

# Vector: (x, y, z)

# Positioning:
position (0,0,0) to (5,0,0)     # move from one vec3 to another
```
- Flow control (if/elseif/else)
```
if health <= 0 then
   destroy Player
elseif health < 50 then
   print "Low health!"
else
   heal Player 10
end
```
- Flow control (loops)
```
for i = 1, 10 do
   print i
end

while lives > 0 do
   print "Still alive!"
end
```
- Functions:
```
function damage(target, amount)
   target.health = target.health - amount
   if target.health <= 0 then
      destroy target
   end
end
```
- Events:
```
on init do
   print "Game started"
end

on tick(dt) do
   if key.down("W") then
      Player.position = Player.position + (0,0,1) * dt
   end
end
```
- V0.02 Example game:
```
worldVar leaderboard 0
deviceVar coins 100

entity Player:
   kind "Cube"
   position (0,1,0)

entity Camera:
   kind "Camera3D"
   parentIs Player
   position (0,2,-5)

on init do
   print "Player ready!"
end

on tick(dt) do
   if key.down("W") then
      Player.position = Player.position + (0,0,1) * dt
   end
end
```
### V0.03
- New Positioning:
```
# Standard/World postion/ing:
entity Player:
   kind "Sphere"
   position (0, 0, 0)     # absolute world position
# _______________________________________________________

# Relative positioning:
entity Player:
   kind "Sphere"
   position (0, 0, 0)

   entity Camera:
      kind "Camera3D"
      position rel(1, 1, 0)ToEntityPlayer

# OR
entity Enemy:
   kind "Cube"
   position (5,0,0)

entity Camera:
   kind "Camera3D"
   position rel(0, 3, -8)ToEntityEnemy
```
### V0.04
- Mainfile name(external apps/not on AntScript compiler(AntScript IDE) or studio(basically roblox studio, but for AntScript \[STILL IN BRAINSTORMING]): Queen.ant
- Main Camera Naming:
```
entity MainCamera3D:
   kind "Camera3D"
   position (0, 2, -6)
   lookAt EntityPlayer
```
- LookAt system:
```
# lookAt ObjTypeObjName

# 3rd person style:
entity Player:
   kind "Sphere"
   position (0,0,0)

entity MainCamera3D:
   kind "Camera3D"
   position rel(0, 2, -6)ToEntityPlayer
   lookAt EntityPlayer

# 1st person style:
entity MainCamera3D:
   kind "Camera3D"
   parentIs Player
   position rel(0,1.6,0)ToParent
   lookAt Parent

# Overhead 2D (original draft)
entity MainCamera2D:
   kind "Camera2D"
   position rel(0,20,0)ToWorld
   lookAt EntityPlayer

# Updated 2D Camera
entity MainCamera2D:
   kind "Camera2D"
   position rel(0, 0, 0)ToPlayer  # Details: X, Y, Rotation
   lookAt EntityPlayer
```
- New rotational and positional parameters:
```
# 2D:
entity Player:
  kind "Square"
  position (1, 1) # x, y
  rotation 321

# 2D Ultra compact / saves space:
entity Player:
  kind "Square"
  position (1, 1, 321) # x, y, rot

# 3D
entity Player:
  kind "Cube"
  position (1, 1, 1)     # x, y, z
  rotation (0, 0, 321)   # rotx, roty, rotz

# Explicit dimension naming (naming the dimension of the item [3D or 2D])
project "MyGame":
  dimension "2D"
  mainCamera "MainCamera2D"

```
- Developement stage names:
  - Egg
  - Larva
  - Pupa
  - Worker
  - Soldier
  - Major
  - Super Major
  - Queen

- 3D items:
```
| Kind       | Purpose                                        |
| ---------- | ---------------------------------------------- |
| `Cube`     | Basic 3D cube primitive.                       |
| `Sphere`   | Basic 3D sphere primitive.                     |
| `Pyramid`  | Basic 3D pyramid primitive.                    |
| `Mesh`     | Imported mesh (e.g. `.obj`, `.fbx`).           |
| `Model`    | Collection of meshes/parts (hierarchy).        |
| `Player`   | Special controllable entity.                   |
| `Camera3D` | 3D camera (world-space).                       |
| `UI3D`     | UI in world-space (floating panels, VR menus). |
| `Light`    | Point/spot/ambient light.                      |

```
- 2D items:
```
| Kind       | Purpose                      |
| ---------- | ---------------------------- |
| `Square`   | 2D rectangle primitive.      |
| `Circle`   | 2D circle primitive.         |
| `Triangle` | 2D triangle primitive.       |
| `Sprite`   | 2D image or animated sprite. |
| `Map`      | Tile-based 2D map.           |
| `Camera2D` | 2D camera (screen-space).    |
| `UI`       | HUD/UI container.            |

```
- Shared items:
```
| Kind     | Purpose                              |
| -------- | ------------------------------------ |
| `Script` | Runs AntScript code.                 |
| `Folder` | Organizes entities/files.            |
| `Empty`  | Empty transform/anchor.              |
| `VFX`    | Visual effects (particles, shaders). |
| `SFX`    | Audio (sound effects, music).        |

```
- Mini 2D demo of all features in V0.04.1:
```
#* Mini 2D demo in Egg stage
   Shows a Player moving and a camera following
*#

# Variables
deviceVar score 0
localVar health 100

# Entities
entity Player:
  kind "Square"
  position (100, 200, 0)
  size (50, 50)

entity MainCamera2D:
  kind "Camera2D"
  follow Player
  posRel Player (0, -100, 0)   # camera stays above player

# Functions
function heal(target, amount)
  target.health = target.health + amount
end

function damage(target, amount)
  target.health = target.health - amount
  if target.health <= 0 then
    destroy target
  end
end

# Event: game start
on init do
  print "Mini demo started!"
end

# Event: key input
on tick(dt) do
  if key.down("W") then
    Player.position = Player.position + (0, -200 * dt)
  end
  if key.down("S") then
    Player.position = Player.position + (0, 200 * dt)
  end
  if key.down("A") then
    Player.position = Player.position + (-200 * dt, 0)
  end
  if key.down("D") then
    Player.position = Player.position + (200 * dt, 0)
  end
end
```
- V0.04.2(Updated/modified V0.04, dont ask.) Arithmetic:
```
a + b     # addition
a - b     # subtraction
a * b     # multiplication
a / b     # division
a % b     # modulo (remainder)
-a        # unary negation
```
- V0.04.2 Comparisms:
```
a == b    # equal
a != b    # not equal
a > b     # greater than
a < b     # less than
a >= b    # greater or equal
a <= b    # less or equal
```
- V0.04.2 Logic:
```
a and b   # both true
a or b    # either true
not a     # invert
```
- V0.04.2 Built-in functions:
  - Debug/output:
    ```
    print("Hello world")
    ```
  - Transforms:
    ```
    move Player (0, 1, 0)        # translate
    rotate Player (0, 90, 0)     # rotate in degrees
    scale Player (2, 2, 2)       # scale
    ```
  - Camera Aliases:
    ```
    lookAt Camera Player
    follow Camera Player
    ```
  - Math extensions(rng, etc.)
    ```
    rand(0, 10)   # random int
    sqrt(16)      # 4
    abs(-5)       # 5
    ```

- Functions:
```
function heal(target, amount)
  target.health = target.health + amount
end
```

- Return Values:
```
function add(a, b)
  return a + b
end

result = add(3, 5)
```
### V0.05
- Time features(core):
```
time.tick        # current tick/frame number
time.now         # time since start
time.now.seconds # total seconds
time.now.ms      # total milliseconds
time.now.min     # total minutes
time.now.hr      # total hours

time.fps         # frames per second
time.sinceInit   # total seconds since game init
```
- Time features (real-world time based):
```
time.real.China     # real-world time in China
time.real.NYC       # real-world time in New York
time.real.Japan     # real-world time in Japan
time.real.auto      # automatically detect player’s region
```
- Time in vars:
```
deviceVar localTime time.real.auto
deviceVar nyTime    time.real.NYC
deviceVar jpTime    time.real.Japan
```
- Wait
```
time.wait(2)       # wait 2 seconds
time.wait.ms(500)  # wait 500 milliseconds
```
- Example:
```
on init do
  deviceVar startTime time.real.auto
  print("Game started at " + startTime)
end

on tick do
  if time.now.seconds % 60 == 0 then
    print("One minute has passed (uptime). Local time: " + time.real.auto)
  end
end
```
- More...






