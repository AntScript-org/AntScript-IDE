# All syntax/code examples as of Egg stage (V0.5):
## Notes:
- Variables, etc. use camelCase.
- You NEED to declare the world type, or it defaults to ant.3D
- Variables are recommended to be declared as local, folder, device, server or world variables, or they will default to device variables.
- Main scripts for web based or external (not on compiler and notepad) sites, are named Queen.ant and others may be named as pleased.

## World declaration
3D:
```
worldType ant.3D
```
2D:
```
worldType ant.2D
```
Webpages:
```
worldType ant.web
```
gui's:
```
worldType ant.gui
```
## Comments
```
#Single line
#* Multiple
line
comment *#
```

## Variables
```
localVar number 42
folderVar text "AntScript is fun!"
deviceVar flag true
serverVar serverTime time.now.seconds # time.now.seconds means seconds since start
worldVar leaderBoard [ ]
localList fruits ["apple", "banana", "cherry"]
```

## Entities
```
entity Player:
  kind "Sphere"
  position (0, 0, 0)
  size (1, 2, 1)
  color red
  opacity 50%

  entity Camera:
    kind "MainCamera3D"
    position rel(5, 5, 0)ToEntity Player
    lookAt Camera Player
    follow Camera Player
```

## Materials and Physics
```
entity Block:
  kind "Cube"
  position (0, 0, 0)
  size (10, 2, 5)

  color #00ff00
  material "wood"     # wood, stone, metal, ice, rubber, glass
  gravity 1           # strength
  friction 0.5
  density 100%
  solid true
  weight auto         # auto-estimation
  state solid         # solid or liquid
```

## Time & Time System
```
time.tick          # current tick
time.now.seconds   # since start
time.now.minutes
time.fps           # frames/sec
time.sinceInit     # total runtime
time.real.Africa   # real-world clock by zone
time.real.auto     # auto-detect local timezone
time.wait(1)       # pause for 1 sec
```

## Functions
```
func greet(name):
  print("Hello " + name + "!, Welcome!")
end

greet("World")

#Output: Hello World!, Welcome!
```

## Conditionals
```
localVar score 6

if score > 10 then
  print("Winner!")
elseif score == 10 then
  print("Close!")
else
  print("Keep going!")
end

#Output: Keep going!
```

## Loops
```
repeat 5 times
  print("Looping...")
end

repeat forever
  time.wait(1)
  print("Tick...")
end
```

## Events
```
onStart:
  print("Game started!")

onCollision Player Block:
  print("Player hit block!")

onKey [W] press:
  Player.position + (1, 0, 0)

onVariable score == 100:
  print("Score reached 100!")

onTime 10 seconds:
  print("Ten seconds passed!")
```

## Parent/Script handling
```
script PlayerScript fil
parent MainCamera3D Player
```

## Embedding other languages
```
embed html {
  <h1>Hello from AntScript!</h1>
}
```

