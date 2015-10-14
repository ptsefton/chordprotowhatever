# chordprobook

## What does this do? Formats song charts


This is a Python 3 script to convert collections of
[chordpro](http://blossomassociates.net/Music/chopro.html) formatted
song charts to PDF, HTML, epub, and word processing doc formats. You can
convert a directory full of files to a single book, or a set of
song-sheets. Uses Pandoc and wkhtmltopdf to do all the hard work.

NOTE: Unlike most chordpro software this does not display chords above
the text (not yet anyway). Displaying chords inline is more compact. If it's good enough for Rob Weule and his
[Ukulele Club Songbook](http://katoombamusic.com.au/product/ukulele-club-songbook/)
and for [Richard G](http://www.scorpexuke.com/ukulele-songs.html) it's
good enough for me.

## Features

* Generate books from files passed on the command line

* Generate books from a book file which is a list of files

* Reorder songs using a setlist file using either of the above ways of
selecting files

*  Transpose songs

If you play with a group you can maintain a songbook for the
group to play from, then create setlists which are ordered subsets of that book by typing abbreviated titles
into a text file, and generating a book from that. The setlist will be printed as a table of contents
for the book, on the back of the cover page so you can rip it off an
put it on the floor, unless you're viewing in on a tablet.

### PDF

* **Formats songs to fit the page** as best it can (good for ageing eyes
   and working in the dark). This feat is accompished by creating an HTML document, via Pandoc, with CSS to render the pages at A4 size then using wkhtmltopdf to create a PDF.

* **Generates individual song-sheets including multiple keys** transposed from the original.

* **Produces a PDF table of contents** which works well on tablets for navigation.


### Word output (.docx)

* Gives you a start on creating a word document (via pandoc) from a set of chordpro files. Each song begins on a new page.
* You can change the styles in the included ```reference.docx``` to your taste. At the moment it does not auto-scale the text to fit the page

### HTML output

This is still experimental, but the idea is to produce HTML that fills the screen for use on tablets, phones, etc with swipe navigatoin.

### TODO

When I get time I'll convert these TODOs into github milestones.

* Chord charts for various instruments

* Generate error reports as text files when things fail

* Allow comment/notes  in setlists

* Better doco including in the code

### Probably won't do
* Make a GUI, but see the comment below about auto-creating PDF

* Display chords above text.

## Audience

This is for people running a unix-like operating system who know how
to install packaged software scripts and python modules.

Status: Alpha / mostly works for me  on OS X 10.10.5.


## Installation on OS X (you're on your own on other platforms)

Requires pandoc 1.15.0.6 or later  and wkhtmltopdf installed on on your path.

* Install Pandoc HEAD using [brew](http://brew.sh/):

    ```brew install pandoc --HEAD```
* Download and install [wkhtmltopdf](http://wkhtmltopdf.org/downloads.html)
* Install dependencies using pip3:

    ```pip3 install pypandoc```

## The local dialect of Chordpro format

Chordpro format has no formal definition, and many different
implementations. Chordpro files are plain-text files with chords
inline in square brackets, eg [C]. It uses formatting 'directives'.

### Chords
Chords are anything in square brackets that starts with a capital
C..G, followed by any mixture of lower case, slashes, and numbers. Eg:

```[C] [Csus4] [C/B] [Cmaj7]```

Note that when transposing, any capital A...G inside a chord will get transposed so
don't write [CAug] use [Caug].


### Other formatting

Directives are lines beginning and ending with { and }.

None of the directives are case sensitive and they are all optional. Title, subtitle, key and transpose can be placed anywhere, but by convention are put at the top of the file.

Formatting / Directive         |      Description  | Rendered as
------------------------------ | ----------------- | -----------
{Title: \<Song title>} {t: \<Song title>}  | Song title | A top-level heading
{Subtitle: \<Artist / songwriter name>} | Subtitle, by convention this is the composer or artist | An second-level heading
{key: \<A...G>} | The key of the song     | Will be added to the title in brackets like ```(Key of G)``` if present.
{transpose: +1 +2 -2} | A space separted list of semitone deltas.  | When called in single-song mode the software will automatically produce extra versions transposed as per the directive. In this can if the song is in C it would be transposed to C#, D and Bb.
{C: Some comment} {Comment: Some comment} | Notes on the song  | A third level heading
{new+page} {np} | New page | A page break. When generating HTML and PDF the software will attempt to fill each page to the screen or paper size respectively as best it can.
{start_of_chorus} {soc} | Start of chorus. Usually followed by some variant of {c: Chorus} | Chorus is rendendered as an indendented block. TODO: make this configurable via stylesheets. In .docx format the chorus is rendered using ```Block Text``` style.
{start_of_bridge} {sob} | Start of bridge. Usually followed by some variant of {c: Bridge} | Same behaviour as chorus
{eoc} {end_of_chorus} | End of chorus | Everything between the {soc} and {eoc} is in an indented block 
{eob} {end_of_bridge} | End of bridge | Same behviour as chorus
{sot} {start_of_tab} | Start of tab (tablature) | Rendered in a fixed width (monospace) font, as per the HTML \<pre> element. NOTE: Tabs that are acutal text-formatted representations of the fingerboard will not be transposed, although chords in square brackets will, so you can use tab-blocks to format intros or breaks where chords line up under each other 
{eot} {end_of_tab} | End of tab | Finishes the fixed-width formatting
{book: path_to_book} | For use in setlist files, a path to a book file relative to the setlist file or an absolute path | 


### Book files
A book file is a text file with a list of paths with and optional title (see [samples/sample-book.txt](samples/sample-book.txt)).

To transpose the song, add a positive or negative integer at after the path, separated by a space. eg:
```./songs/my-song.cho +2```

TODO: Allow the book to have additional notes for songs and sub sections.

### Setlist files

The setlist consists of an optional {title: } directive, and optional {book: <path>} directive followed by a list of songs, one per line.   (see [samples/sample-book.txt](samples/setlist.txt)).

If there is no {book: } directive then the setlist will be selected from the song files passed in as arguments:  see the examples below.

Identify songs by entering one or more words from the title, in order. So "Amazing" will match "Amazing Grace" and "Slot Baby" would match "Slot Machine Baby".

TODO: Allow the setlist to have transpositions, and to have additional notes for songs and sub sections.


## usage

To see  usage info, type:
```

./chordprobook.py -h

chordprobook.py [-h] [-a] [-k] [--a4] [-e] [-f FILE_STEM] [--html] [-w]
                       [-p] [-r REFERENCE_DOCX] [-o] [-b] [-s SETLIST]
                       [--title TITLE]
                       [files [files ...]]

positional arguments:
  files                 List of files

optional arguments:
  -h, --help            show this help message and exit
  -a, --alphabetically  Sort songs alphabetically
  -k, --keep_order      Preserve song order for playing as a setlist (inserts
                        blank pages to keep multi page songs on facing pages
  --a4                  Format for printing (web page output)
  -e, --epub            Output epub book
  -f FILE_STEM, --file-stem FILE_STEM
                        Base file name, without extension, for output files
  --html                Output HTML book, defaults to screen-formatting use
                        --a4 option for printing (PDF generation not working
                        unless you chose --a4 for now
  -w, --word            Output .docx format
  -p, --pdf             Output pdf
  -r REFERENCE_DOCX, --reference-docx REFERENCE_DOCX
                        Reference docx file to use (eg with Heading 1 having a
                        page-break before)
  -o, --one-doc         Output a single document per song: assumes you want A4
                        PDF
  -b, --book-file       First file contains a list of files, each line
                        optionally followed by a transposition (+|-)\d\d? eg
                        to transpose up one tone: song-file.cho +2
  -s SETLIST, --setlist SETLIST
                        Use a setlist file to filter the book, one song per
                        line and keep facing pages together. Setlist lines can
                        be one or more words from the song title
  --title TITLE         Title to use for the book


```



## Examples

Create a PDF book from all the files in a directory. 

* To make a PDF book (defaults to songbook.pdf) from a set of chordpro
  files:

   ```./chordprobook samples/*.cho```

    Which is equivalent to:

    ```./chordprobook --pdf --a4  --title="My book" samples/*.cho```

*  To add a file name and a title to the book.
 
    ```./chordprobook --file-stem=my_book --title="My book"  samples/*.cho```

*  If you'd like it sorted alphabetically by title:

    ```./chordprobook -a --file-stem=my_book --title="My book"  samples/*.cho```

* To build a book from a list of files use a book file and the -b
   flag. This will preserve the order you entered the songs except
   that it will make sure that two-page songs appear on facing pages.
  
    ```./chordprobook.py -b samples/sample-book.txt```

* To make sure the order of songs is preserved exactly, for example to
  use as a setlist, use -k or --keep-order. This will insert blank
  pages if necessary.

    ```./chordprobook.py -k -b samples/sample-book.txt```

*  To sort songs alphabetically add the -a or --alphabetical flag:

    ```./chordprobook.py -a -b samples/sample-book.txt```
    
* To choose a subset of the songs in a book in a particular order use
  a setlist file.  

   Use this to filter all the songs in a directory using a setlist:

    ```./chordprobook.py -s samples/setlist.txt samples/*.cho```
    
*  Or use a book file:

    ```./chordprobook.py -s samples/setlist.txt -b samples/sample-book.txt```


