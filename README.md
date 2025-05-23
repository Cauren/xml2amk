# MusicXML to AddmusicK converter

Translates musicxml parts into the .txt format expected by AddmusicK (version 1.0.11 though it may
work with earlier versions with a bit of tweaking).

It was developped and tested under Linux, but it should work without issue on any platform that can run perl
from a command line (including Windows and MacOS at least).

This is written in pretty straightforward perl 5 with only `XML::LibXML` and `XML::Parser` as dependencies.  Both
of those are usually available via every distribution's package managers or can be gotten via CPAN.


This was tested with exported
systems from MuseScore 4, though I expect it should work with (uncompressed) musicXML exports from other
notation software with possibly minor tweaks.

Usage: `xml2amk [-c] <FILE>`

Sends the converted FILE to standard output.  FILE must be an <ins>uncompressed</ins> MusicXML file
containing a __score-partwise__ section of version 3 or above.  In practice, every notation software
should offer this as an export option (MuseScore version 4 does so as default for a musicxml export).

If the `-c` option is specified, xmk2amk will attempt to optimize the voices by finding and looping
sections.  This will reduce the size of the output, at the cost of a fairly costly optimization
phase during conversion.  This is probably best reserved for a final conversion once you are
satisfied with the unoptimized result.

The result can serve directly as input to AddmusicK or can be edited further as needed.

## What it can do

xml2amk will properly translate all notes from all staves in the system, provided there are no chords
in any voice (split multiple notes in a staff into separate voices if you do not want to have one
staff per voice.

### Supported:
- dynamics
- articulations (accents, staccato, tenuto and slurs)
- any key signature/accidentals and transposing instruments

### AddmusicK specific trickery:
- The first coda of a system will be translated into an "intro" mark (`/`), from which a repeating
  song will restart after the first play (in effect, there is an implicit _da coda al fine_)
- Placing an expression text item can be interpreted as an instrument selection, with the form
  `@0` referencing a builtin instrument and `"filename.brr" $xx $yy $zz $aa $bb` into a custom
  instrument (the file will be added once to the `#samples` section as needed).  Multiple references
  to the same instrument will point to the same instance provided all values are identical.
- Document information (title, author, arranger) will be included, with the _source_ element
  translated to a `#game` entry

## What's missing

Currently, error handling is minimal.  The script will die if it encounters something it truly
cannot cope with, but is otherwise extremely liberal in what input it will accept (and ignore)
to avoid notation it doesn't know from breaking the conversion.

### Planned:
- Repeat sections (they are currently being ignored, except for a coda as noted above)
- smart(er) guesses at builtin or custom instruments based on what the staff instruments are (right
  now everthing defaults to `@0` unless you specify an instrument.

