# Writing Criteria

Every guide in this repo is written to the same
standard. If you're adding or editing a guide —
human or AI — hold it to these.

1. **Plain words, real ideas.** Simple language,
   never dumbed-down concepts. Explain jargon on
   first use; collect terms in the glossary.
2. **No hype.** No marketing adjectives, no
   "revolutionary," no promises about what
   something will do for the reader.
3. **Honest about limits.** Say what the thing
   *can't* do. State uncertainty instead of
   smoothing it over. Validation notes include
   what remains unverified.
4. **No invented numbers.** Benchmarks, defaults,
   and specs are either measured or qualified with
   "depends on your setup." If it wasn't tested,
   say so.
5. **Checkable.** Commands are meant to be run,
   with "Check:" blocks showing what success looks
   like. Sources are linked, not asserted.
6. **Respect the reader's setup.** Call out
   Windows/Mac/Linux differences. Don't assume a
   GPU, a budget, or prior experience.
7. **Safety isn't skipped.** Real risks get
   stated plainly, not buried.
8. **Scoped.** Each guide says what it covers and
   points elsewhere for the rest. No guide
   pretends to be the whole subject.
9. **Structure over prose.** Tables, before/after
   examples, symptom → fix. Scannable beats essay.

## Verification notes for this guide

Standard 3 and 4 applied to this guide,
specifically. The build machine ran Ubuntu
with **ExifTool 13.55** — the original Perl
edition (the official distribution, run
under Perl 5.38.2), the only tool this guide
teaches — plus Python's PIL, which
synthesised the lab's base JPEGs, and
ffprobe, captured once in chapter 12. Every
quoted output in the chapters is a capture
from that ExifTool, run against the specimen
shoebox. Working transcripts are kept with
the build
(`exiftool-lab/transcripts/`).

**The lab.** One shoebox, built by
`examples/build-shoebox.sh`, identical on
every rebuild: seven JPEGs and a GPX track.
The pictures are minimal JPEGs synthesised
with PIL (flat colours; 88×110 to 120×90
pixels); the metadata is the specimen, and
it was written onto them by ExifTool itself,
in one `-overwrite_original` pass per file —
the builder, which creates the files from
nothing, is the guide's one stated exception
to its own backup rule. The specimens are
named in `examples/lab-notes.txt`: a Canon
harbour photo with full EXIF and no GPS; an
iPhone birthday photo with GPS (a public
waterfront's coordinates); a Nikon photo
whose three EXIF dates all read 03:14 for a
moment the story fixes at 04:19; a Sony
trail photo with a date and no position; a
scan with no EXIF dates and one XMP
description; and a parade pair — the first
frame complete, the second carrying only a
description. `trail.gpx` is hand-written:
four trackpoints spanning the trail photo's
stamped time. The chapters each worked on a
fresh copy of the shoebox, so no chapter's
writes leak into another's captures.

**Verified by running, per chapter:**

- *Ch. 1:* `-ver` (13.55); the full default
  read of `harbour-morning.jpg` (43 lines,
  filesystem band, EXIF band, and the
  computed Composite band all as described);
  the default read of `scan-1994.jpg`
  (28 lines, no capture date, no camera).
- *Ch. 2:* a three-tag read; the `-s`
  ladder in all three rungs on one tag;
  `-XResolution` with and without `-a`
  (one line, then the JFIF and EXIF copies),
  and with `-a -G` labelling both;
  `"-EXIF:DateTimeOriginal"` group-qualified;
  `-time:all` on the clock-wrong photo (six
  lines, two bands, as printed).
- *Ch. 3:* the Artist write and its
  read-back (A. Rivera → J. Rivera); the
  `_original` appearing beside the written
  file, with identical FileSize (1343 bytes,
  the values being the same length); a
  `-overwrite_original` write leaving no
  backup; adding an IPTC `Credit` to a file
  that had none, read back; deleting a tag
  with an empty assignment, the subsequent
  read printing nothing.
- *Ch. 4:* the stripped frame's full
  `-a -G -s` listing (a description and
  structural boilerplate only); a wholesale
  `-tagsFromFile` from its sibling (dates,
  ISO, and GPSPosition all present in the
  read-back, and `parade-02.jpg_original`
  created); a selective copy naming
  `"-EXIF:DateTimeOriginal"` and `"-ISO"`
  only — the read-back shows exactly those
  two, and no GPSPosition.
- *Ch. 5:* `"-AllDates+=1:05:00"` moving
  all three EXIF dates from 03:14 to 04:19
  — the bare shift value is
  hours:minutes:seconds, as captured;
  `"-DateTimeOriginal+=0:0:1 0:00:00"`
  moving a date by exactly one day in the
  years:months:days hours:minutes:seconds
  form; an absolute `-DateTimeOriginal=`
  set on the scan, read back; `touch -d`
  moving only the filesystem band of
  `-time:all`, the EXIF band unmoved.
- *Ch. 6:* `-testname` printing the
  proposed name and updating nothing; the
  directory rename (5 updated, and the two
  dateless files warned — `Tag
  'DateTimeOriginal' not defined` — and left
  unchanged); the collision run (the first
  file renamed, the second refused with
  `Error: '2024-09-02.jpg' already exists`),
  and the `%+c` retry producing
  `2024-09-02_1.jpg`; a `-directory<` move
  creating `by-date/2024-05` and filing the
  photo in it.
- *Ch. 7:* the empty `-GPSPosition` read
  before; `-geotag trail.gpx` writing a
  full GPS record; the read-back position
  (46 deg 51' 17.75" N, 121 deg 45' 20.58"
  W) and altitude (1443.041667 m) —
  interpolation verified by hand against
  the bracketing trackpoints (11:18 and
  11:30; the photo at 11:23:40 sits 47.2%
  between them, and both the coordinates
  and the altitude fall at that fraction);
  the out-of-window run against the harbour
  photo (both warnings verbatim, 0 updated).
- *Ch. 8:* `-a -G -s` on the phone photo
  counted at 52 lines; the surgical
  `"-GPS*="` strip (the position read back
  empty, Artist and UserComment intact);
  the `-all=` strip with its full read-back
  (only File facts and JPEG structure
  remain; 755 bytes against the original
  1343; `ImageSize` still 96x96); and the
  directory listing proving both writes
  left `_original` files containing the
  unstripped data.
- *Ch. 9:* the recursive `-s -s -s` date
  listing over the shoebox, `notes.txt`
  decoy included (8 files read); the
  `-ext jpg` run (7 files); `-T` table
  output with `-` for the missing values;
  `-json` output for two files (the count
  line rides the error stream, the JSON is
  clean); and, quoted in the recipes audit,
  `-if 'not $DateTimeOriginal'` selecting
  exactly the two dateless specimens with
  `5 files failed condition` in the
  summary.
- *Ch. 10:* the four-line config defining
  the `WhenWhere` shortcut; `-config`
  pointing at it, the three-tag answer on
  the birthday photo; the same shortcut
  with no `-config` at all, the file
  sitting at a scratch home's
  `~/.ExifTool_config` (the capture sets
  `HOME` to the scratch directory), and the
  position line simply absent for the
  GPS-less harbour photo.
- *Ch. 11 (recipes):* the directory-wide
  `"-AllDates+=0:02:00"` shift (2 files
  updated, both read back two minutes
  later — `0:02:00` in hours:minutes:seconds
  form); the selective `"-all:all"` copy
  form; every other recipe is an assembly
  of commands captured in its home chapter,
  and the chapter says so.
- *Ch. 12:* ffprobe's report on the
  harbour JPEG (an mjpeg stream; dimensions
  and encoding, no diary), captured live;
  `exiv2`, `identify`, and `mediainfo`
  confirmed absent from the build machine.
- *Ch. 13:* the undefined-tag warning and
  `Nothing to do.`; the TXT write refusal;
  the literal-name rename (`Invalid tag
  name 'one-name.jpg'. Use '=' not '<'…`);
  the missing-tag rename warnings (ch. 6);
  the geotag warnings (ch. 7); and the
  temporary-file refusal, captured by
  running the write as user `nobody`
  against a mode-444 file in a directory
  nobody cannot write (a fixture under
  /tmp, since the workspace's group
  permissions let nobody traverse nothing
  there). As root on the build machine the
  same write succeeded despite mode 444 —
  the rename mechanism ignores the file's
  own bits when the directory allows the
  replacement — and the chapter states
  that rather than staging a cleaner lie.

**Capture hygiene (declared trims):** some
long listings are shown shortened in the
chapters, always with a `...` line where
the cut falls and prose stating the full
count where one is claimed (43 and 28 lines
in chapter 1, 52 in chapter 8): the default
reads in chapter 1, the `-a -G -s` listings
in chapters 4 and 8, the `-time:all` and
recursive listings where bands repeat, and
the `-T` table (whose tabs render as spaces
in the chapter; the transcript holds the
real tabs). Nothing was cut silently; the
full captures are in the transcripts.
Commands are quoted as run, including the
backslash the shell needed before
`${DateTimeOriginal}` in the rename and
geotag patterns. One capture was retaken
after a drafting error in this ledger's
first pass (a permission error described
before it was captured); the transcript
holds only the genuine run, and the
incident is why the sentence above about
staging is worded as it is.

**Build-machine weather, stated once:** the
lab's home filesystem is an overlay that
assigns new files group `nogroup` and, with
the workspace's umask of 007, mode 660;
listings show `-rw-rw----` for that reason,
not by any specimen design. The sandbox
runs as root except where a capture says
otherwise. File dates in captures are the
build day's (2026-10-21, UTC as the
filesystem reports it); the specimens' EXIF
dates are the invented 1994–2024 story
dates, and the contrast between the two is
itself chapter 5's lesson.

**Documented-only (not run), and why:**

- *The Windows standalone build:*
  exiftool.org's package (a self-contained
  `exiftool(-k).exe`, renamed to
  `exiftool.exe` for command-line use) is
  described from the tool's documentation;
  there is no Windows machine on the build
  bench. No Windows output is quoted
  anywhere in the guide.
- *macOS installation:* the official
  installer package and Homebrew route are
  named in chapter 1 from the tool's
  documentation; not performed.
- *The `-geotime` offset expression* for
  the UTC trap (chapter 7): given in its
  documented form and flagged there as
  documented; the lab's track and photo
  share a clock by construction, so the
  trap itself is demonstrated by the
  out-of-window warning, which is captured.
  The numeric default of the Geosync
  tolerance is likewise not stated, the
  documentation being the reader's route
  to it.
- *The `-FileModifyDate<DateTimeOriginal`
  recipe* (chapter 11): stated as a
  documented form assembled from chapter
  4's verified copy mechanics; not run as
  a recipe.
- *`-testdir`,* the rehearsal sibling of
  `-testname` (chapters 6, 11): named from
  the documentation; `-testname` itself is
  captured.
- *exiv2, ImageMagick's `identify`, and
  mediainfo* (chapter 12): not installed on
  the build machine (their absence is
  captured); their descriptions come from
  their documentation and public record.
  ffprobe, which was installed, is the
  chapter's one live capture.
- *Config-file programming beyond
  shortcuts* (chapter 10): user-defined
  tags and Composite definitions are
  named as existing capabilities, not
  taught; the shortcut mechanism is the
  part captured end to end.
- *MakerNotes internals:* named in
  chapters 12 and 14 only. The lab's
  synthesised JPEGs carry no MakerNotes,
  and decoding maker's records is a scope
  decision, recorded here.

Specimen discipline, for the record: every
name, person, and date in the shoebox is
invented; the only coordinates are a
public waterfront and a national-park
ridge, chosen so no specimen points at
anyone's home; the base pictures are
synthetic flat-colour JPEGs, not
photographs of anything. No command in the
guide was aimed at a real photo library;
every write ran against the lab or a
throwaway copy of one of its files.
