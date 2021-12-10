# Modes
This documentation is for creating a mode for Techmino.

Original: https://github.com/26F-Studio/Techmino/blob/a64edc70ed2bb3c17f38485fa77fed3e27e8e941/document/mode.txt , updated 2021-08-26

## Specs
A mode file is a valid Lua file which returns a `table`. The table should contain:

### color
Required. Color of the mode's description text (the text that shows on top right after you select it)

### env
Required. Mode environment variable, decides properties of the game.

*Translator's note: while the table is required, all properties seem to be optional. Also for some of the parameters, the default is taken from the player's settings, eg rotation system.*

<details>
  <summary>Click to expand full table of properties</summary>

  Property name | Default | Description
  -|-|-
  `drop`|60|Falling delay (frames. You can use integers or 1-over-power-of-2 fractions, eg 0.5, 0.25, 0.125)
  `lock`|60|Lock delay (frames)
  `wait`|0|Spawn delay / ARE (frames)
  `fall`|0|Line delay (frames)
  `bone`|`false`|Whether to use bone blocks
  `fieldH`|20|Field (skyline) height
  `heightLimit`|1e99|Max allowed field height (i.e. threshold for garbage out)
  `nextCount`|6|How many pieces to display in next queue
  `nextStartPos`|1|Display next queue starting from which one
  `holdCount`|1|How many pieces you can hold
  `infHold`|`false`|Whether you can swap hold infinitely
  `phyHold`|`false`|Whether in-place hold can be enabled
  `ospin`|`true`|Whether you can do O-spins
  `deepDrop`|`false`|Whether deep drop can be enabled
  `RS`|`'TRS'`|Name of the rotation system
  `das`|10|DAS
  `arr`|2|ARR
  `sddas`|2|Soft drop DAS
  `sdarr`|2|Soft drop ARR
  `mindas`|0|Lower limit for DAS
  `minarr`|0|Lower limit for ARR
  `minsdarr`|0|Lower limit for soft drop ARR
  `ihs`|`true`|Allow initial hold
  `irs`|`true`|Allow initial rotation
  `ims`|`true`|Allow initial movement
  `skinSet`|(Player's settings)|Which skin (spritesheet) to use. You can only use built-in skins
  `skin`|(Player's settings)|Block colors. A table (array) with 25 integers from 1 to 16
  `face`|(Player's settings)|Piece spawn orientation. A table (array) with 25 integers from 0 to 3
  `block`|`true`|Whether to display currently controlling piece
  `ghost`|0.3|Ghost opacity (0-1)
  `center`|1|Rotation center opacity (0-1)
  `smooth`|`false`|Whether block falling should be animated smoothly
  `grid`|0.16|Grid opacity (0-1)
  `bagLine`|`true`|Whether to display bag separator in the next queue, if it exists
  `lockFX`|2|Lockdown VFX level (integer 0-5)
  `dropFX`|2|Hard drop VFX level (integer 0-5)
  `moveFX`|2|Movement VFX level (integer 0-5)
  `clearFX`|2|Line clear VFX level (integer 0-5)
  `splashFX`|2|Splash VFX level (integer 0-5)
  `shakeFX`|2|Matrix sway level (integer 0-5)
  `atkFX`|2|Attack VFX level (integer 0-5)
  `text`|`true`|Whether to display line clear text
  `score`|`true`|Whether to display score for each piece dropped
  `highCam`|`false`|Whether to enable screen scrolling (pan camera upwards if stack is tall)
  `nextPos`|`false`|Whether to enable next preview (display X in cells where the next piece will spawn)
  `showSpike`|`false`|Whether to enable spike counter
  `hideBoard`|`false`|Whether, and how, to hide part or whole of the board (`"down"`, `"up"`, or `"all"`)
  `flipBoard`|`false`|Whether, and how, to flip the board (`"U-D"`, `"L-R"`, or `"180"`)
  `sequence`|`'bag'`|How next queue will be generated. You can use strings to refer to existing piece generator functions, or write a new function here. Note: use coroutine
  `seqData`|`{1,2,3,4,5,6,7}`|Argument passed to the function above, usually a bag (but not always)
  `mission`|`false`|A table containing line clear missions
  `life`|0|How many times you can revive
  `garbageSpeed`|1|How fast garbage is released
  `pushSpeed`|3|How fast garbage is pushed onto the field
  `noTele`|`false`|Whether to disable the 10 advanced control keys
  `visible`|`'show'`|Visibility of locked pieces (use the few predefined strings)
  `freshLimit`|1e99|Lockdown timer reset limit
  `easyFresh`|`true`|Whether to use easy LD timer reset rules
  `bufferLimit`|1e99|Limit to the incoming garbage buffer queue
  `fkey1`|`false`|Function to run when pressing Function1 key
  `fkey2`|`false`|Function to run when pressing Function2 key
  `keyCancel`|`{}`|Which controls to disable. A table (array) of integers (ID of the buttons), eg. {1,2} disables moving left and right
  `fine`|(Player's settings)|Whether to enable finesse error sound
  `fineKill`|`false`|Whether to kill player on finesse error
  `b2bKill`|`false`|Whether to kill player on breaking Back to Back
  `missionKill`|`false`|Whether to kill player when clearing a line that is not current mission
  `dropPiece`|`NULL`|Function to run after a piece is placed, input is the Player object
  `task`|`NULL`|Function to *continue to run* every frame, input is the Player object. Note: use coroutine
  `noInitSZO`|`false`|Whether to avoid S, Z, and O pieces on game start. If set to true, will skip up to 5 S/Z/O pieces from the beginning of the next queue
  `bg`|`'none'`|Background. You can only use built-in backgrounds
  `bgm`|`'race'`|Music. You can only use built-in songs. You can also provide a playlist (eg, `{'race','push'}`) to play random from
  `allowMod`|`true`|Whether to allow mods

</details>

### load
~~Required. Initialization function of the mode. Usually you just need to create a player.~~

*Translator's note: It has since changed but the docs didn't update. A more accurate description is:**

*Optional. Initialization function of the mode. If omitted, automatically creates a player with ID of 1.*

No inputs.

No return values.

### mesDisp
Required. A function that draws stats on the screen. (Coordinate 0,0 is the top left of the Player)

Input: a Player object

No return values. (You call functions that do the drawing.)

### score
Optional. Returns data to save for the purpose of leaderboards.

Omitting this function disables leaderboards.

Input: a Player object.

Return: a table that contains information deciding the score. A `date` property will be automatically added for you.

### scoreDisp
Optional. Converts the table from `score` to a string that displays on-screen.

If the mode does not appear on the map, this is safe to omit.

Input: a Player object

Return: a string

### comp
Optional. Compares two scores for leaderboard sorting.

Can safely omit if the mode lacks a `score` function.

Input: two tables from the `score` function

Return: boolean representing whether the first score places higher than the second

### getRank
Optional. Gives ranks to a gameplay.

Input: a Player object

Return: nothing if this playthrough should not be logged to the leaderboard, integers 0-5 otherwise. 0 means no rank but still records to the leaderboard. 1 through 5 are D/C/B/A/S ranks, can unlock modes behind this mode, and will display the rank on the map.

## Example
Below is the mode file for Sprint 40L:

```lua
return{ --Return a table. Of course you can define a few constants or functions or something before this
	color=COLOR.green, --The color
	env={ --Mode environmental variables
		drop=60,lock=60,
		dropPiece=function(P)if P.stat.row>=40 then P:win('finish')end end,
		bg='bg2',bgm='race',
	},
	load=function() -- Mode loading function. Here we've just created a player, which is what you do for most single player mdoes.
	                -- You can omit this function in your mode, and the function here will be used by default.
		PLY.newPlayer(1) --The 1 is the player ID. By default the player controls Player 1
	end,
	mesDisp=function(P) --Display info for Sprint 40
		setFont(55)
		local r=40-P.stat.row
		if r<0 then r=0 end
		mStr(r,63,265) --Display the remaining lines (r)
		PLY.draw.drawTargetLine(P,r) --Draw the target line using a built-in function
	end,
	score=function(P)return{P.stat.time,P.stat.piece}end, --Key information to keep at the end of a game
	scoreDisp=function(D)return STRING.time(D[1]).."   "..D[2].." Pieces"end, --Converts score to a human-readable string
	comp=function(a,b)return a[1]<b[1]or a[1]==b[1]and a[2]<b[2]end, --Sort by time, or pieces used if time is the same
	getRank=function(P) --Calculate the rank
		if P.stat.row<40 then return end --Return nothing to indicate we are not saving to leaderboard when you top out early
		local T=P.stat.time
		return
		T<=26 and 5 or --S rank requirements, <= 26 seconds
		T<=32.6 and 4 or --A rank requirements
		T<=52.6 and 3 or --B rank requirements
		T<=92.9 and 2 or --C rank requirements
		T<=183 and 1 or --D rank requirements aka requirements to unlock modes
		0 --Requirements to have score logged to leaderboards
	end,
}
```
