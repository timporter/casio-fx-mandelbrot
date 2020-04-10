# casio-fx-mandelbrot
A Mandelbrot set explorer for Casio FX Series programmable graphics calculators

![Mandelbrot Set](/imgs/main.png)

## What is it?

This is a [Mandelbrot Set](https://en.wikipedia.org/wiki/Mandelbrot_set) explorer for the FX Series of Casio programmable calculators. It draws the initial view of the complete set and unlike some other implementations online allows for zooming and panning to explore the set in detail. Due to having only a monochrome display this is strictly the in/out monochrome version of the Mandelbrot set and not a fancy shaded one.

## Use

Due to the serious limitations of both the hardware and programming language of the Casio FX series calculators please be aware that this is extremely slow to run. Be prepared for long waits, at extreme zoom levels this can be over an hour.

After entering the code and running it the initial view will be drawn, this takes about twenty minutes.

Before each draw some parameters are briefly displayed (current screen position and accuracy level).

### Controls

After a draw is complete you can then pan around with the D-Pad, or use the numbers 1-9 to zoom into the corresponding section of the screen (arranged the same as keypad layout, eg, 5 will zoom to the centre, 7 will zoom to the top left). Use the subtract symbol to zoom out. Any other key will force a re-draw of the current location.

The in progress draw is rendered to the screen but while drawing is happening a progress bar is also displayed along the top, once the bar disappears the drawing is completed and the program is awaiting the next key press.

Note that the 'busy' indicator (small square at top right of screen) will always be present even when the drawing has stopped and key press is being awaited.

## Code notes

### Notes on formatting

You're going to have to type this in to the calculator in the program editor and save it.

I've included some comments by some of the lines that you may wish to adjust, the comments don't need to be typed in (in fact, the Casio language doesn't support comments anyway so you can't)

I've indented some of the lines to aid readability, like with comments there is no way to enter the indentation to the calculator.

When starting a new line on the calculator the calculator will display the carriage return symbol (`↲`), this is ommitted in the code listing below.

### Built-in symbols and tokens used

Calculator built-in function names MUST be typed by pressing the button for the relevant function, not just keying the whole thing with the alpha buttons.

Here are the locations of the tokens used in the menu of an FX-9860GII , other models may have different locations.

#### Symbols

`→` = The assignment operator, bottom-right most of the dark keys

`i` = Imaginary number symbol, NOT the I alpha character. Press `SHIFT`, `0`. Or `OPTN`, `F3 (CPLX)`, `F1 (i)`

#### Maths

`Abs` = Return absolute value of input. Press `OPTN`, `F3 (CPLX)`, `F2 (Abs)`

#### Conditionals

`If` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F1 (If)`

`Then` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F2 (Then)`

`IfEnd` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F4 (I.End)`

#### Comparators

`=` = Press `SHIFT`, `.`. Or `SHIFT`, `PRGM (VARS)`, `F6`, `F3 (REL)`, `F1 (=)`

`>` = Press `SHIFT`, `PRGM (VARS)`, `F6`, `F3 (REL)`, `F3 (>)`

#### For loops

`For` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F6`, `F1 (For)`

`To` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F6`, `F2 (To)`

`Next` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F6`, `F4 (Next)`

#### While loops

`While` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F6`, `F6`, `F1 (Whle)`

`WhileEnd` = Press `SHIFT`, `PRGM (VARS)`, `F1 (COM)`, `F6`, `F6`, `F2 (WEnd)`

#### Display

`Cls` = Clear screen. Press `SHIFT`, `F4 (SKTCH)`, `F1 (Cls)`

`Locate` = Output text to specific location. Press `SHIFT`, `PRGM (VARS)`, `F6`, `F4 (I/O)`, `F1 (Lcte)`

`PixelOn` = Turns given pixel on. Press `SHIFT`, `F4 (SKTCH)`, `F6`, `F6`, `F3 (PIXL)`, `F1 (On)`

`PixelOff` = Turns given pixel off. Press `SHIFT`, `F4 (SKTCH)`, `F6`, `F6`, `F3 (PIXL)`, `F2 (Off)`

`ViewWindow` = Configure view window. Press `SHIFT`, `F3 (V.Win)`, `F1 (V.Win)`

#### Key input

`GetKey` = Gives the key code of the lass keypress. Press `SHIFT`, `PRGM (VARS)`, `F6`, `F4 (I/O)`, `F2 (Gtky)`

## Tested on

Casio fx-9860GII

Casio fx-9860GII SD (via emulator)

## Variables

A  Current pixel column being drawn
B  Bottom of view range
C  Adjusted coordinate of current column
D  Current pixel row being drawn
E  Adjusted coordinate of current row
F  Mandelbrot initial coordinate
I  Calculated pixel size (width)
J  Calculated pixel size (height)
K  Last key press
L  Left of view range
M  Screen width in pixels
N  Mandelbrot iteration counter
O  Screen height in pixels
P  Reserved - Used when calculating new view ranges (width)
Q  Reserved - Used when calculating new view ranges (height)
R  Right of view range
S  Cap on iterations of mandelbrot formula
T  Top of view range
W  Reserved - Used for a sleep counter
Z  Mandelbrot accumulator


## The code

```
ViewWindow 10,100,0,10,100,0    # Sets the view to any area that won't have main axis in it

# Set the (T)op, (B)ottom, (L)eft and (R)ight of the initial Mandelbrot area to plot
-2.5→L
1.5→R
1→T
-1→B

# Set (M,N) to one less than the resolution in pixels of the main display of your calculator model (almost certainly 128x64)
127→M 
63→O 

25→S  # Desired accuracy, can be reduced for faster render time

While 1
	Cls
  
  # Display the current view coordinates on the screen briefly before drawing
	Locate 1,1,"T=                   "   # 19 Spaces (Press SHIFT, A-Lock (ALPHA), and then 19 times press SPACE (.))
	Locate 3,1,T
	Locale 1,2,"B=                   "
	Locate 3,2,B
	Locate 1,3,"L=                   "
	Locate 3,3,L
	Locate 1,4,"R=                   "
	Locate 3,4,R
	Locate 1,5,"S=                   "
	Locate 3,5,S
  
  # The is no sleep function so just spin for a little moment
	For 1→W To 500
	Next
  
	Cls

	(T-B)÷(O-1)→I
	(R-L)÷(M-1)→J
	L-J→C
  
	For 1→A To M
		C+J→C
		B-I→E
		For 1→D To O-1
			E+I→E
			0→Z
			
			# This is where we decide if a point is in the Mandelbrot set or not
			C+E×i→F   # i here is the imaginary number symbol, NOT the I alpha character. Press SHIFT, 0. Or OPTN, F3 (CPLX), F1 (i)
			For 1→N to S
				Z×Z+F→Z
				If Abs Z>2  
				Then
					# The point isn't in the set (theres no neat way to break out of a loop so set N as though the loop went too far)
					S+1→N
				IfEnd
			Next
			
			# N went just far enough, so the point is in the set
			If N=S
			Then
				PixelOn (O+1)-D,A
			IfEnd
			
		Next
		
		# Draw a progress bar
		PixelOn 1,A
		
	Next
	
	# Finished drawing the set, so hide the progress bar
	For 1→A To M
		PixelOff 1,A
	Next
	
	# Wait for a keypress
	0->K
	While K=0
		GetKey->K
	WhileEnd
	
	# Deal with any presses to the D-pad to scroll around the screen
	(T-B)÷2→Q
	(R-L)÷2→P
	If K=28  # up
	Then
		T+Q→T
		B+Q→B
	IfEnd
	If K=37  # down
	Then
		T-Q→T
		B-Q→B
	IfEnd
	If K=38   # left
	Then
		R-P→R
		L-P→L
	IfEnd
	If K=27   # right
	Then
		R+P→R
		L+P→L
	IfEnd
	
	# Deal with any presses to the number pad to zoom to sections of the screen
	# When zooming in also increase the S value which increase accuracy (number of iterations for main calculation)
	(T-B)÷3→Q
	(R-L)÷3→P
	If K=72  # 1, down left
	Then
		B+Q→T
		L+P→R
		S+5→S
	IfEnd
	If K=62  # 2, down
	Then
		B+Q→T
		L+P→L
		R-P→R
		S+5→S
	IfEnd
	If K=52  # 3, down right
	Then
		B+Q→T
		R-P→L
		S+5→S
	IfEnd
	If K=73  # 4, left
	Then
		T-Q→T
		B+Q→B
		L+P→R
		S+5→S
	IfEnd
	If K=63  # 5, centre
	Then
		T-Q→T
		B+Q→B
		L+P→L
		R-P→R
		S+5→S
	IfEnd
	If K=53  # 6, right
	Then
		T-Q→T
		B+Q→B
		R-P→L
		S+5→S
	IfEnd
	If K=74  # 7, top left
	Then
		T-Q→B
		L+P→R
		S+5→S
	IfEnd
	If K=64  # 8, top
	Then
		T-Q→B
		L+P→L
		R-P→R
		S+5→S
	IfEnd
	If K=54  # 9, top right
	Then
		T-Q→B
		R-P→L
		S+5→S
	IfEnd
	
	# Deal with zooming out
	T-B→Q
	R-L→P
	If K=32  # -. zoom out
	Then
		T+Q→T
		B-Q→B
		R+P→R
		L-P→L
		S-5→S  # Incase of zooming out, reduce accuracy again
	IfEnd
WhileEnd
```
