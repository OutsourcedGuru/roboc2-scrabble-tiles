# roboc2-scrabble-tiles
Step-by-step instructions for printing a set of Scrabble tiles and holders.

You could use these to play the game Scrabble but they're also a fun desk accessory to spell words your coworkers could read or many other clever uses, I'd guess.

![SetOfTiles](https://user-images.githubusercontent.com/15971213/32306608-b7bfae52-bf39-11e7-8de1-6b8fbf163abf.png)

![Holder](https://user-images.githubusercontent.com/15971213/32306622-d19ae8f0-bf39-11e7-9086-7d220a8b90f9.png)

![Example](https://user-images.githubusercontent.com/15971213/32306638-ed061ccc-bf39-11e7-962f-8e1bb42cc3e3.png)

## Overview
For this, you'll need to get ready for multi-color printing if you've not done this before. Fortunately, I've taken the hard work out of this by separating each GCODE file by color. All you do is supply both white and black PLA filament and this repository does the rest.

Here's an overview of how the process works:

1. Using the LCD menu, preheat the hotend for PLA.
2. Using OctoPrint, send the first of a pair of jobs to the printer to print the white part of the tiles.
2. Leave those partially-completed tiles on the print bed and in the printer.
3. Using the LCD menu, perform the filament change wizard to black filament.
4. Using the LCD menu, preheat the hotend for PLA.
5. Using OctoPrint, send the second of a pair of jobs to the printer to print the black part of the tiles.
6. Prep for the next session by performing another filament change back to white and repeat for the remaining tile GCODE pairs.

Since the tile holders themselves are white, you won't have to switch colors in the middle of the job.

## Assumptions
* I assume that you have a level bed since the process isn't compatible with the G29 autoleveling routine.
* I assume that you're comfortable using the OctoPrint interface to start a print job since this will be required here.
* I assume that you have a roll of white and black PLA filament similar to that sold by Robo 3D.

## Optionally...
Technically, you don't have to print all the sets if you just want to print the tiles for a specific word you had in mind. You would think this would be easier...

For this, you'd need to slice the appropriate collection of STL files you had in mind. You can reasonably get about twenty on the print bed.

And yet, having sliced this in Cura you'd then need to chop the file into sub-jobs by color, say with [GetToDahChoppa](https://github.com/OutsourcedGuru/GetToDahChoppa), another program I've written for this (but it only works with Cura).

These were my settings in Cura when I sliced the tile sets which allow me to have adhesion without rafts/skirts/brims:

* Printer: Robo C2
* Profile: Low Quality Robo C2
* Filament: Robo Black PLA 1.75
* Temperature: 190°C
* Infill: Light (20%)
* Infill Pattern: Cubic
* Initial Layer Travel Speed: 20.0 mm/s
* Number of Slower Layers: 3
* Enable Jerk Control: 
* Travel Jerk: 25.0 mm/s
* Initial Layer Travel Jerk: 10.0 mm/s
* Z Hop Only Over Printed Parts: checked
* Z Hop Height: 0.4 mm
* Regular Fan Speed at Layer: 6
* Lift Head: checked
* Rafts and supports: disabled

## Prep
**Note:** You'll need to do these two things first so that these print sessions will work correctly in the printing instructions which follow!

### Autoleveling
The G29 nine-point autoleveling routine isn't compatible with the multi-job approach we're doing here. If you did fire off that command from OctoPrint's startup GCODE then the second sub-job would fail badly since there are already parts on the print bed. Fortunately, there's an easy way to toggle that off.

Follow the instructions to install the [OctoPrint-plugin-toggle-autoleveling](https://github.com/OutsourcedGuru/OctoPrint-plugin-toggle-autolevel) plugin which I've written. Using those instructions, verify that it's working.

(You can disable this plugin later when you're done printing this project.)

### Priming Line
It will be best to turn off the priming line for all of these prints. The priming line is printed normally at the very front of the print bed but for safety's sake we'll be temporarily disabling this with a manual edit.

This will result in a slow start of whichever tile is picked first by the slicer of a set but the overall result is good enough.

Edit this script in OctoPrint before printing these multi-color jobs. Feel free to put the script back to the way it was before when you're finally done, however.

OctoPrint -> Settings -> GCODE Scripts -> Before print job starts:

```
; ------- These are okay
; set to millimeters
G21
; set to absolute mode
G90
; zero extruder
G92 E0
; turn off fans
M107

; ------- This needs to be commented with semicolons
;non blocking heatup of extruder
;M104 S210

; ------- These are okay
; home all axis
G28
; ------- The toggle-autolevel plugin will take
; ------- care of this by ignoring it
; probe for bed autolevel plane
G29

; ------- These need to be commented with semicolons
; pause for 2 seconds
;G4 S2
; move bed down 15mm
;G1 Z15 F300
; move to front left corner
;G1 X1 Y1 F7200
; heat to priming line temp
;M109 S210
; move bed to printing position
;G1 Z0.3
; print 120mm priming line
;G1 X120 E15.0 F500
; move bed down
;G1 Z15 F300

; ------- These are okay
; zero extruder
G92 E0
; set movement speed
G1 F7200
```

## Instructions
Assuming that you've done the prep above, then you should be ready to start.

1. Fetch the GCODE files from this repository and upload them to your printer.
2. Using the LCD menu, preheat the hotend for PLA.
3. When the hotend has preheated and using OctoPrint, send the first of a pair of jobs to the printer to print the white part of the tiles. For example, this might be `RC_TilesA-T_1of2.gcode`.
4. Leave those partially-completed tiles on the print bed and in the printer.
5. Using the LCD menu, perform the filament change wizard to black filament.
6. Using the LCD menu, preheat the hotend for PLA again.
7. When the hotend has preheated and using OctoPrint, send the second of a pair of jobs to the printer to print the black part of the tiles. For example, this might be `RC_TilesA-T_2of2.gcode`.
8. Prep for the next session by performing another filament change back to white and repeat for the remaining tile GCODE pairs.

To remove the tiles from the print bed, I use the Robo-supplied spatula at a slight angle and tap its backend with a hammer until it's now under the edge of the file. I then push the spatula with my hand until the tile is removed.

## Miscellaneous
The STL filenames indicate how many of each piece is normally supplied in the standard Scrabble game but print as many as you'd like since the cost of filament is relatively inexpensive.

The holders as supplied here are slightly less than the standard Scrabble game versions, by perhaps only 20mm. This was necessary to fit on the bed of the Robo C2 printer, of course, noting that I've tweaked Cura's profile settings to use more of the print volume. These holders still accommodate eight tiles, which ought to be sufficient.

## Ideas
You could print and give a holder plus the tiles for a custom word you've picked as a gift for someone you know. It's a great way of showing off the printer's capabilities with multi-color even if you only have the default single extruder.

## Optionally slicing tiles
If you find yourself in Cura trying to position twenty of these on the virtual print bed, I've found the following to be of use:

1. Import all the tiles together at once
2. Using the translate tool, move all of them outside of the print area
3. One by one, rotate them 90° so that the lettering is facing up and then use the translate tool to change the X and Y as in the following table:

<table>
<tr><td>X -></td><td>-33</td><td>-13</td><td>7</td><td>27</td><td>47</td><td>Y</td></tr>
<tr><td rowspan="4"></td><td>A</td><td>B</td><td>C</td><td>D</td><td>E</td><td>-44</td></tr>
<tr><td>F</td><td>G</td><td>H</td><td>I</td><td>J</td><td>-21</td></tr>
<tr><td>K</td><td>L</td><td>M</td><td>N</td><td>O</td><td>2</td></tr>
<tr><td>P</td><td>Q</td><td>R</td><td>S</td><td>T</td><td>25</td></tr></table>

Cura has an annoying habit of dropping a new STL object into the center of the bed, disrupting all your hard work. So import them all, move them to outside of the print area and then position them as I've described.