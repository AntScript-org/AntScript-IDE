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
lookAt ObjTypeObjName

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

# Overhead 2D
entity MainCamera2D:
   kind "Camera2D"
   position rel(0,20,0)ToWorld
   lookAt EntityPlayer
```

