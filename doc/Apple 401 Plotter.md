# The Apple 401 Color Plotter

## General concepts

The way the 401 interprets a coordinate at any given time depends on the current
viewport and window settings. The "viewport" is a rectangle specified in hardware
absolute coordinates and defines the physical area in which coordinates are resolved.
The "window" defines the virtual coordinates of the drawing area, which are then
mapped to the viewport.

Commands sent to the 401 are terminated by the ASCII "end of text" delimiter, 0x03.

### Serial connection

The 8 DIP switches (SW1-SW8) on the back of the plotter are used to configure the
serial interface.

SW1 selects the data bits. ON = 7 bits, OFF = 8 bits.

SW2 selects whether parity is used. ON = no parity bit, OFF = use a parity bit.
If parity is selected, SW3 determines the type of parity bit. ON = odd parity,
OFF = even parity.

SW4 and SW5 select the number of stop bits:
| SW4  | SW5  |  Stop bits    |
|------|------|---------------|
| OFF  | OFF  | 2 stop bits   |
| ON   | OFF  | 1.5 stop bits |
| OFF  | ON   | 1 stop bit    |
| ON   | ON   | invalid       |

SW6, SW7, and SW8 select the baud rate:
| SW6 | SW7 | SW8 | Baud       |
|-----|-----|-----|------------|
| OFF | OFF | OFF |  75 baud   |
| OFF | OFF | ON  | 150 baud   |
| OFF | ON  | OFF | 300 baud   |
| OFF | ON  | ON  | 600 baud   |
| ON  | OFF | OFF | 1200 baud  |
| ON  | OFF | ON  | 2400 baud  |
| ON  | ON  | OFF | 4800 baud  |
| ON  | ON  | ON  | 9600 baud  |

**The 401 uses hardware handshaking.** It never returns data to the computer
over the serial line and sets DSR when its internal buffer is full. Sending even
one byte after the DSR line has been set will corrupt the 401's command buffer
and trigger an error. Most USB to RS232 cables will attempt to bundle up several
bytes before transmission, so unless you're sure that your cable can handle it
I recommend flushing the serial connection after every byte and checking the DSR
line yourself.

## Command Reference

### Move Absolute (MA)

```
MAx,y
```

Raise the pen and move the plotter head to the position specified by the x,y coordinates.

Params: 2

### ??Move relative (MR)??

```
MRx,y
```

Raise the pen and move the plotter head to a position offset x,y from its
current position.

Params: 2
### Draw absolute (DA)

```
DAx,y(,x,y..)
```

Lower the pen and draw a line from the current position to the position
specified by the x,y coordinates. Continues 

Params: arb.

==??Draw relative (DR)??==

DRx,y(,x,y)*

lower pen and draw line from current position to an x,y offset. Multiple
offsets may be specified in a single command.

Params: arb.

==Circle (CA)==

CAr,x,y

lower pen and draw a circle of radius r centered at x,y.

Params: 3

==Letter size (LS)==

LSs

sets the font size to s

==Letter rotation (LR)==

LRtheta

draw following text rotated by theta
in degrees, clockwise

==Print letters (PL)==

PLtext

lower pen and draw specified text at current position

==Pen select (PS)==

PSi

select pen i (where i is in 1-4)


LT - Line type -
   1 solid
   2 ??
   3 ??
   4 ??
   5 ?? param 
   6 ??

XT
XT3,120,12,2,2

XTa,b,c,d,e
c=# of ticks
d=tick length above line
e=tick length below line
b=distance (total)
a=style???

XT2,100,8,20,20 -- style 2: b is per tick
XT1,100,8,20,20 -- style 1: b is total distance



XT - ? xticks ? -- 5 params   0 100 100 5 5
YT - ? yticks ? -- 5 params
YT1,100,8,20,20
YT2,100,8,20,20


SOMETHING SETS SPEED
CH - ??? -- no params
PM - Point Marking -- 1 param
PS - pen select
PV - ??? - 1 param
WD - window - 4 params
AC - arc - 5 params
LI - ??? - no params/arb ???
IM - ??? - 2 params
PK - ??? - no params/arb
RS - ??? - ?0x80 as params? - reset error flag
VP - Viewport - 4 params
UL - ??? - no params/arb ? 1 param 0-9?
SP - ??? - 1 param ??
LF - ??? - 1 param ? 0-9
SL - ??? - 1 param  -- Slanted lettering (by %?)

