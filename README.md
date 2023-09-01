<!DOCTYPE html>
<html>
<head>
  <title>ASCIISTEP16</title>
  <meta name="GENERATOR" content="github.com/mmarkdown/mmark Mmark Markdown Processor - mmark.miek.nl">
  <meta charset="utf-8">
</head>
<body>

<!-- for annotated version see: https://raw.githubusercontent.com/ietf-tools/rfcxml-templates-and-schemas/main/draft-rfcxml-general-template-annotated-00.xml -->

<h1 class="special" id="abstract">Abstract</h1>

<p>ASCIISTEP16 is pckeyboard standard &amp; translation of popular hardware 16-step drum/midisequencers (electribe MX, electribe SX, mc303, tr909,tr808, mc707, arturia beatstep).
The goal of ASCIISTEP16 is to offer a pckeyboard mode (usually for tracker software) which allows music software to use pckeyboard-keys as music-stepsequencer keys.</p>
<section data-matter="main">
<h1 id="what-is-asciistep16">What is ASCIISTEP16</h1>

<p><img src="https://i.imgur.com/mDsOXqt.png" style="width:60%"/></p>

<p>The goal of ASCIISTEP16 is to offer a pckeyboard mode (usually for tracker software) which:</p>

<ul>
<li>can act as a substitute for a hardware stepsequencer (on a pckeyboard)</li>
<li>allows &lsquo;grid&rsquo;-like liveperformance on pckeyboards</li>
<li>allows people coming from the hardware stepsequencer scene to easily use ASCIISTEP16 compatible music-software</li>
<li>allows any unix character-device, or implementation to send 16 stepsequencer-buttons over ascii</li>
</ul>

<h1 id="implementations">Implementations</h1>

<ul>
<li><a href="https://github.com/coderofsalvation/MilkyTrackerX">MilkyTracker X-build</a></li>
</ul>

<blockquote>
<p>please comment on this gist if your software implements ASCIISTEP16</p>
</blockquote>

<h1 id="translation-table">Translation table</h1>

<p>The table below focuses on QWERTY keyboards, but other keyboard layouts can follow the exact same logic of using the first 2x8 (rows-x-cols) alphabet keys (see image above).</p>

<blockquote>
<p>the uppercase character is preferred (&lsquo;Q&rsquo; instead of &lsquo;q&rsquo; e.g.) to not interfere with normal keymappings of the software.</p>
</blockquote>

<table>
<thead>
<tr>
<th>step</th>
<th>character</th>
<th>comment</th>
</tr>
</thead>

<tbody>
<tr>
<td>0</td>
<td>Q</td>
<td></td>
</tr>

<tr>
<td>1</td>
<td>W</td>
<td></td>
</tr>

<tr>
<td>2</td>
<td>E</td>
<td></td>
</tr>

<tr>
<td>3</td>
<td>R</td>
<td></td>
</tr>

<tr>
<td>4</td>
<td>T</td>
<td></td>
</tr>

<tr>
<td>5</td>
<td>Y</td>
<td>international equivalent &lsquo;Z&rsquo; e.g.</td>
</tr>

<tr>
<td>6</td>
<td>U</td>
<td></td>
</tr>

<tr>
<td>7</td>
<td>I</td>
<td></td>
</tr>

<tr>
<td>8</td>
<td>A</td>
<td></td>
</tr>

<tr>
<td>9</td>
<td>S</td>
<td></td>
</tr>

<tr>
<td>10</td>
<td>D</td>
<td></td>
</tr>

<tr>
<td>11</td>
<td>F</td>
<td></td>
</tr>

<tr>
<td>12</td>
<td>G</td>
<td></td>
</tr>

<tr>
<td>13</td>
<td>H</td>
<td></td>
</tr>

<tr>
<td>14</td>
<td>J</td>
<td></td>
</tr>

<tr>
<td>15</td>
<td>K</td>
<td></td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr>
<th>channel/track</th>
<th>ascii key for muting/selecting</th>
</tr>
</thead>

<tbody>
<tr>
<td>1</td>
<td>&lsquo;1&rsquo;</td>
</tr>

<tr>
<td>2</td>
<td>&lsquo;2&rsquo;</td>
</tr>

<tr>
<td>3</td>
<td>&lsquo;3&rsquo;</td>
</tr>

<tr>
<td>4</td>
<td>&lsquo;4&rsquo;</td>
</tr>

<tr>
<td>5</td>
<td>&lsquo;5&rsquo;</td>
</tr>

<tr>
<td>6</td>
<td>&lsquo;6&rsquo;</td>
</tr>

<tr>
<td>7</td>
<td>&lsquo;7&rsquo;</td>
</tr>

<tr>
<td>8</td>
<td>&lsquo;8&rsquo;</td>
</tr>

<tr>
<td>9</td>
<td>&lsquo;9&rsquo;</td>
</tr>

<tr>
<td>10</td>
<td>&lsquo;0&rsquo;</td>
</tr>

<tr>
<td>11</td>
<td>&rsquo;-&rsquo;</td>
</tr>

<tr>
<td>12</td>
<td>&rsquo;=&rsquo;</td>
</tr>
</tbody>
</table>

<h2 id="example-implementation-in-c-c">Example implementation in C/C++</h2>

<pre><code>/* 
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
</code></pre>

<h1 id="quick-win-trackers-keyjazz-enabled-software">Quick win: trackers &amp; &lsquo;keyjazz&rsquo;-enabled software</h1>

<p>Most trackers already support octave-switching, transposing-selected note(s), and instrument-switching using keyboard shortcuts. This creates very interesting ASCIISTEP16 workflows by simply implementing ASCIISTEP16.</p>

<blockquote>
<p>ASCIISTEP16 does not interfere with most trackers/keyjazz enabled daws, since the uppercase characters (&lsquo;Q&rsquo; not &lsquo;q&rsquo;) are usually unused (or simply translated to lowercase).</p>
</blockquote>

<p>Hint: CAPS-LOCK &amp; SHIFT act as an ASCIISTEP16 enable/disable-toggle for free!</p>

<h1 id="bar-pages">Bar pages</h1>

<p>Most stepsequencer support multiple bars (for example: the electribes offer 4), which allow for editing longer patterns by repurposing the same 16 step-buttons.</p>

<table>
<thead>
<tr>
<th>bar page</th>
<th>step value</th>
</tr>
</thead>

<tbody>
<tr>
<td>0</td>
<td>1,2,3,5,6,7,8,9,10,11,12,13,14,15,16</td>
</tr>

<tr>
<td>1</td>
<td>17,18,19,20,21,22,23,24,25,26,27</td>
</tr>

<tr>
<td>..</td>
<td>and so on</td>
</tr>
</tbody>
</table>

<blockquote>
<p>Basically, each stepvalue is relative based on the current bar-page, and evaluated as <code>stepvalue * (page*16)</code> (page starting at 0).</p>
</blockquote>

<h1 id="navigating-bar-pages">navigating bar pages</h1>

<p>The ASCIISTEP16 standard is to use increment/decrement shortcuts to navigate pages:</p>

<p>*<code>page-up/down</code> (to switch to next/previous page, which turns step 1-16 into 17-24 e.g. )
* and/or <code>arrow up/down</code> in  <code>page-down</code> <strong>in combination</strong> with shift/ctrl/alt etc.</p>

<blockquote>
<p>This is easy to implement in 99% of trackers, which already support cursor-navigation.
So the implementation is deadsimple to determine &amp; apply the current bar-page:</p>
</blockquote>

<pre><code>when entering a key ('E' which translates to asciistep16 value 2 e.g.):
  row = getCurrentRow()
  bar = row / 16
  editrow = asciistep16('Q') + (bar*16) // replace 'Q' with user input
  if( editrow &gt; 0 ) writeNote( note, editrow) // step was entered
</code></pre>

<h1 id="scope-limits">Scope (limits)</h1>

<p>ASCIISTEP16 v1 is very simple/limited (therefore an easy standard to implement):</p>

<ul>
<li>ASCIISTEP16 focuses on laptop-friendly shortcuts (first 3 alphanumeric rows + CTRL and SHIFT)</li>
<li>ASCIISTEP16 does not dictate how to change metadata of a step (note-value, volume-value) (see <code>Quick win</code> above)</li>
<li>ASCIISTEP16 does not dictate how to control the transport / clock (start/stop/record) e.g.</li>
<li>ASCIISTEP16 does not dictate how to change keyboard mappings</li>
<li>ASCIISTEP16 does not dictate how to toggle/control userinterface elements</li>
</ul>

<blockquote>
<p>Most trackers/daws already have a default way of doing these things, therefore ASCIISTEP16 stay simple to implement. Ideas for ASCIISTEP16-NOTE, ASCIISTEP16-VOLUME, ASCIISTEP16-TRANSPORT extensions are welcome though.</p>
</blockquote>

<h1 id="contact">Contact</h1>

<ul>
<li>leonvankammen|gmail.com</li>
</ul>

<h1 id="iana-considerations">IANA Considerations</h1>

<p>This document has no IANA actions.</p>

<h1 id="acknowledgments">Acknowledgments</h1>

<p>TODO acknowledge.</p>
</section>

</body>
</html>

