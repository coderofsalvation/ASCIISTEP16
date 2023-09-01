%%%
Title = "ASCIISTEP16"
area = "Music"
workgroup = "Music"

[seriesInfo]
name = "ASCIISTEP16"
value = "draft-ASCIISTEP16-leonvankammen-00"
stream = "IETF"
status = "informational"

date = 2022-08-01 

[[author]]
initials="L.R."
surname="van Kammen"
fullname="L.R. van Kammen"

%%%

<!-- for annotated version see: https://raw.githubusercontent.com/ietf-tools/rfcxml-templates-and-schemas/main/draft-rfcxml-general-template-annotated-00.xml -->

.# Abstract

ASCIISTEP16 is pckeyboard standard & translation of popular hardware 16-step drum/midisequencers (electribe MX, electribe SX, mc303, tr909,tr808, mc707, arturia beatstep).
The goal of ASCIISTEP16 is to offer a pckeyboard mode (usually for tracker software) which allows music software to use pckeyboard-keys as music-stepsequencer keys.

{mainmatter}

# What is ASCIISTEP16

<img src="https://i.imgur.com/mDsOXqt.png" style="width:60%"/>

The goal of ASCIISTEP16 is to offer a pckeyboard mode (usually for tracker software) which:

* can act as a substitute for a hardware stepsequencer (on a pckeyboard)
* allows 'grid'-like liveperformance on pckeyboards
* allows people coming from the hardware stepsequencer scene to easily use ASCIISTEP16 compatible music-software
* allows any unix character-device, or implementation to send 16 stepsequencer-buttons over ascii

# Implementations 

* [MilkyTracker X-build](https://github.com/coderofsalvation/MilkyTrackerX)

> please comment on this gist if your software implements ASCIISTEP16

# Translation table

The table below focuses on QWERTY keyboards, but other keyboard layouts can follow the exact same logic of using the first 2x8 (rows-x-cols) alphabet keys (see image above).

> the uppercase character is preferred ('Q' instead of 'q' e.g.) to not interfere with normal keymappings of the software.

| step | character | comment |
|-|-|-|
| 0 | Q | |
| 1 | W | | 
| 2 | E | |
| 3 | R | |
| 4 | T | |
| 5 | Y | international equivalent 'Z' e.g.|
| 6 | U | |
| 7 | I | |
| 8 | A | |
| 9 | S | |
| 10 | D | |
| 11 | F | |
| 12 | G | |
| 13 | H | |
| 14 | J | |
| 15 | K | |

| channel/track | ascii key for muting/selecting |
|-|-|
| 1 | '1' |
| 2 | '2' |
| 3 | '3' |
| 4 | '4' |
| 5 | '5' |
| 6 | '6' |
| 7 | '7' |
| 8 | '8' |
| 9 | '9' |
| 10 | '0' |
| 11 | '-' |
| 12 | '=' |

## Example implementation in C/C++

```
/* 
  example usage: 
	  int stepsize = trackerGridSettings.getStepIncrement();                        // optional: allow variable stepsizes
	  int bar      = cursor.row / (16*stepsize);
	  int steprow  = ASCIISTEP16(character,bar) * stepsize;
	  int ch       = ASCIISTEP16_channel(character);                                // ctrl +1 jumps to ch 1 (cursor)
	  if( ch      != -1 ) muteOrSelectChannel( ch, CTRLpressed() );                 // shift+1 (un)mutes ch 1
	  if( steprow != -1 ) writeNote( 'c-4', steprow, getCurrentChannel() );         // write step
	  else writeNote( lowercaseKeyToNote(i), getCursorRow(), getCurrentChannel() ); // write keyjazz note
*/

int ASCIISTEP16(char ascii, unsigned int bar)
{
	int number = -1;
	switch (ascii)
	{
		// ASCIISTEP16 standard (https://gist.github.com/coderofsalvation/8d760b191f4bb5465c8772d5618e5c4b)
		case 'Q': number = 0; break;
		case 'W': number = 1; break;
		case 'E': number = 2; break;
		case 'R': number = 3; break;
		case 'T': number = 4; break;
		case 'Y': number = 5; break;
		case 'U': number = 6; break;
		case 'I': number = 7; break;
		case 'A': number = 8; break;
		case 'S': number = 9; break;
		case 'D': number = 10; break;
		case 'F': number = 11; break;
		case 'G': number = 12; break;
		case 'H': number = 13; break;
		case 'J': number = 14; break;
		case 'K': number = 15; break;
	}
	return number == -1 ? -1 : number + (bar*16);
}

int ASCIISTEP16_channel(char ascii)
{
	int number = -1;
	switch (ascii)
	{
		// ASCIISTEP16 standard (https://gist.github.com/coderofsalvation/8d760b191f4bb5465c8772d5618e5c4b)
		case '1': number = 0; break;
		case '2': number = 1; break;
		case '3': number = 2; break;
		case '4': number = 3; break;
		case '5': number = 4; break;
		case '6': number = 5; break;
		case '7': number = 6; break;
		case '8': number = 7; break;
		case '9': number = 8; break;
		case '0': number = 9; break;
		case '-': number = 10; break;
		case '=': number = 11; break;
	}
	return number;
}
```

# Quick win: trackers & 'keyjazz'-enabled software

Most trackers already support octave-switching, transposing-selected note(s), and instrument-switching using keyboard shortcuts. This creates very interesting ASCIISTEP16 workflows by simply implementing ASCIISTEP16.

> ASCIISTEP16 does not interfere with most trackers/keyjazz enabled daws, since the uppercase characters ('Q' not 'q') are usually unused (or simply translated to lowercase). 

Hint: CAPS-LOCK & SHIFT act as an ASCIISTEP16 enable/disable-toggle for free!

# Bar pages

Most stepsequencer support multiple bars (for example: the electribes offer 4), which allow for editing longer patterns by repurposing the same 16 step-buttons.

| bar page | step value                           |
|----------|--------------------------------------|
| 0        | 1,2,3,5,6,7,8,9,10,11,12,13,14,15,16 |
| 1        | 17,18,19,20,21,22,23,24,25,26,27     |
| ..       | and so on                            |

> Basically, each stepvalue is relative based on the current bar-page, and evaluated as `stepvalue * (page*16)` (page starting at 0).

# navigating bar pages

The ASCIISTEP16 standard is to use increment/decrement shortcuts to navigate pages:

*`page-up/down` (to switch to next/previous page, which turns step 1-16 into 17-24 e.g. )
* and/or `arrow up/down` in  `page-down` **in combination** with shift/ctrl/alt etc.

> This is easy to implement in 99% of trackers, which already support cursor-navigation.
So the implementation is deadsimple to determine & apply the current bar-page:

```
when entering a key ('E' which translates to asciistep16 value 2 e.g.):
  row = getCurrentRow()
  bar = row / 16
  editrow = asciistep16('Q') + (bar*16) // replace 'Q' with user input
  if( editrow > 0 ) writeNote( note, editrow) // step was entered
```

# Scope (limits)

ASCIISTEP16 v1 is very simple/limited (therefore an easy standard to implement):

* ASCIISTEP16 focuses on laptop-friendly shortcuts (first 3 alphanumeric rows + CTRL and SHIFT)
* ASCIISTEP16 does not dictate how to change metadata of a step (note-value, volume-value) (see `Quick win` above)
* ASCIISTEP16 does not dictate how to control the transport / clock (start/stop/record) e.g.
* ASCIISTEP16 does not dictate how to change keyboard mappings
* ASCIISTEP16 does not dictate how to toggle/control userinterface elements

> Most trackers/daws already have a default way of doing these things, therefore ASCIISTEP16 stay simple to implement. Ideas for ASCIISTEP16-NOTE, ASCIISTEP16-VOLUME, ASCIISTEP16-TRANSPORT extensions are welcome though.

# Contact

* leonvankammen|gmail.com

# IANA Considerations

This document has no IANA actions.

# Acknowledgments

TODO acknowledge.
