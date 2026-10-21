# What ExifTool Is

**Scope:** the mental model — the facts a file
carries inside itself, the tags and groups
they live in — plus your version, the Windows
and Perl editions, and the lab.

## The file's own diary

Every photograph carries two sets of facts
about itself. One set belongs to the
**filesystem**: the file's name, its size on
disk, the date the operating system last saw
it change. The sibling guide *Finding Files
for Mere Mortals* is built on those. The
other set lives **inside the file**, written
there by the camera or phone at the moment
of exposure and edited ever since by
software: when the picture was actually
taken, what camera and lens took it, the
exposure, and — for most phones — the GPS
coordinates of where you were standing.

That inner diary is called **metadata**, and
it is organised as **tags**: named facts
(`DateTimeOriginal`, `ISO`, `GPSPosition`),
each filed under a **group** that says which
standard it belongs to — **EXIF** (the camera
standard), **IPTC** (the press and caption
standard), **XMP** (Adobe's extensible
standard), and more. The same name can exist
in several groups at once, which matters
from chapter 2 onward.

**ExifTool**, written and maintained by Phil
Harvey, is the tool that reads this diary —
and, unusually, also writes it. One program,
every common format: JPEG, TIFF, PNG, HEIC,
camera RAW files, PDFs, audio and video
containers, and hundreds more, on any
operating system. Most of this guide works
on JPEGs, because JPEG's metadata is the
best standardised; the commands themselves
are the same everywhere.

Check: the version, on the build machine:

```
$ exiftool -ver
13.55
```

One number, no ceremony — scripts lean on
that. This guide's ExifTool is the original
**Perl** edition: a Perl program (the
`exiftool` script plus its library), needing
only Perl 5 and nothing else. It is the same
on Linux, on macOS (the official installer
package from exiftool.org, or Homebrew), and
in distribution packages — Debian and Ubuntu
call theirs `libimage-exiftool-perl`.

**Windows is the one different shape.** There
is no Perl on a stock Windows machine, so
exiftool.org offers a standalone package:
a self-contained `exiftool(-k).exe`, the
same engine with its Perl packed inside.
The `(-k)` means the window pauses for a
key press when you drag files onto it; for
command-line use you rename it to
`exiftool.exe`, and every command in this
guide then works as written, in PowerShell
or at a command prompt — quoting rules and
all. The build machine for this guide is
Linux, so Windows behaviour is described
from the tool's documentation, not captured;
`criteria.md` keeps that ledger.

## A first read

Point ExifTool at one file with no other
instruction and it prints everything it can
find. The lab's harbour photograph:

```
$ exiftool shoebox/harbour-morning.jpg
ExifTool Version Number         : 13.55
File Name                       : harbour-morning.jpg
Directory                       : shoebox
File Size                       : 1253 bytes
File Modification Date/Time     : 2026:10:21 19:26:31+00:00
...
Image Description               : Fishing boats in the harbour at first light
Make                            : Canon
Camera Model Name               : Canon EOS 80D
...
Modify Date                     : 2023:06:14 07:42:18
Artist                          : A. Rivera
...
Exposure Time                   : 1/250
F Number                        : 8.0
ISO                             : 200
...
Date/Time Original              : 2023:06:14 07:42:18
Create Date                       : 2023:06:14 07:42:18
...
Aperture                        : 8.0
Image Size                      : 120x90
Megapixels                      : 0.011
Shutter Speed                   : 1/250
Focal Length 35mm Equiv         : 24.0 mm
Light Value                     : 13.0
```

Forty-three lines in all (the full capture
is in the transcripts; two stretches are cut
above, and the cuts are marked). Read it in
three bands. At the top, the **filesystem's**
facts — name, size, the dates the *file*
last changed, which here are the day the lab
was built, not the day of the photograph.
In the middle, the **camera's** facts, of
which `Date/Time Original` is the one this
guide returns to most: the moment the photo
claims to have been taken. At the bottom,
facts ExifTool **computed** on the spot —
`Aperture`, `Shutter Speed`, `Image Size`,
`Light Value` — derived from other tags,
never stored in the file. Those are the
**Composite** group, met again in chapter 2;
they are answers, not contents, and you
cannot write to most of them.

For contrast, the lab's scan — a flatbed
scan of an old print, which arrived with
almost no diary at all:

```
$ exiftool shoebox/scan-1994.jpg
ExifTool Version Number         : 13.55
File Name                       : scan-1994.jpg
...
Software                        : Flatbed Scan Utility 2.1
...
Description                     : Scanned from a print, summer 1994
Image Width                     : 88
Image Height                    : 110
...
```

Twenty-eight lines, and nearly every one is
about the file rather than the photograph:
no camera, no exposure, no date taken — the
only date anywhere is the sentence a human
typed into the description. Much of real
metadata work is exactly this: files whose
diaries are thin, wrong, or missing, and
chapters 4 and 5 are about repairing them.

## The lab

Everything in this guide happens in one
specimen **shoebox**, built by a script so
your copy is identical to the one in the
captures:

```
$ sh build-shoebox.sh shoebox
shoebox built in shoebox
```

The script is in `examples/`, beside a
`lab-notes.txt` that describes every specimen
and why it exists, and a `trail.gpx`, a
hand-written GPS track for chapter 7. The
pictures themselves are minimal synthesised
JPEGs — flat colours, no real scene — because
**the metadata is the specimen**. The tags
were written onto them by ExifTool itself,
in the builder's one deliberate use of the
write commands the guide is about to teach
carefully.

The cast, all invented: a harbour photo from
a camera with no GPS; a phone photo of a
birthday with everything a phone records,
GPS included; a photo whose camera clock was
wrong; a trail photo with no position and a
track log to give it one; the scan; and two
frames of a street parade, one with full
metadata and one an editor stripped nearly
bare.

One habit to form now: **the shoebox is the
specimen, not your photo library.** Every
write in this guide is shown against the
shoebox or against a throwaway copy of one
of its files. ExifTool's writes are surgical
and reversible when you let them be — but
the rehearsal habit is what chapter 3 is
for, and it is cheaper to form it here.
