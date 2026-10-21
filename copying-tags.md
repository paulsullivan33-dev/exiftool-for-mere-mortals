# Copying Tags Between Files

**Scope:** `-tagsFromFile` — taking some or
all of one file's diary and writing it into
another — and the everyday job behind it:
a file an editor stripped, restored from
its untouched sibling.

## The stripped frame

The lab's parade pair tells the story. Two
frames of the same moment, seconds apart.
The first kept everything. The second went
through some editor that kept the picture
and dropped the diary. Through the
magnifying glass, all of it (the `...`
lines in this chapter's listings mark
cuts; the transcripts hold the whole):

```
$ exiftool -a -G -s shoebox/parade-02.jpg
[ExifTool]      ExifToolVersion                 : 13.55
[File]          FileName                        : parade-02.jpg
...
[EXIF]          ImageDescription                : Street parade, the brass band turning the corner
[EXIF]          XResolution                     : 1
[EXIF]          YResolution                     : 1
[EXIF]          ResolutionUnit                  : None
[EXIF]          YCbCrPositioning                : Centered
[Composite]     ImageSize                       : 104x78
[Composite]     Megapixels                      : 0.008
```

No date, no camera, no exposure, no
position — a description and the structural
boilerplate (the resolution entries are the
defaults any JPEG write leaves behind).
Nothing about the file is broken; its facts
are simply gone. Re-typing them from the
sibling would work and would be exactly
the kind of chore transcription errors are
made of. Copying is one command:

```
$ exiftool -tagsFromFile shoebox/parade-01.jpg shoebox/parade-02.jpg
    1 image files updated

$ exiftool -a -G -s -time:all -ISO -GPSPosition shoebox/parade-02.jpg
[File]          FileModifyDate                  : 2026:10:21 19:27:56+00:00
...
[EXIF]          ModifyDate                      : 2024:07:20 10:15:03
[EXIF]          DateTimeOriginal                : 2024:07:20 10:15:03
[EXIF]          CreateDate                      : 2024:07:20 10:15:03
[EXIF]          ISO                             : 320
[Composite]     GPSPosition                     : 47 deg 36' 33.12", 122 deg 20' 27.60"
```

The dates, the exposure, and the position
crossed over. Read the shape of the
command: **`-tagsFromFile SOURCE` names
the donor; the file at the end is the
patient.** With no list of tags, the copy
is *everything copyable* — the donor's
whole diary, laid over the patient's.
(The patient's own `ImageDescription`
survived here because the donor's was
identical; a differing tag of the same
name would have been overwritten. Copying
is writing, with the donor's values.)

And because copying is writing, chapter
3's rules came with it unasked:

```
$ ls shoebox | grep parade
parade-01.jpg
parade-02.jpg
parade-02.jpg_original
```

There is the pre-copy state of the patient,
kept by default, in case the donor was the
wrong file.

## Copying some of it

Wholesale copying carries whatever the
donor has — including, in this pair, a GPS
position. Often you want the facts without
the whereabouts, or the dates without the
camera. List the tags after the donor and
only those travel:

```
$ exiftool -tagsFromFile shoebox/parade-01.jpg "-EXIF:DateTimeOriginal" "-ISO" shoebox/parade-02.jpg
    1 image files updated

$ exiftool -a -G -s -time:all -ISO -GPSPosition shoebox/parade-02.jpg
[File]          FileModifyDate                  : 2026:10:21 19:31:28+00:00
...
[EXIF]          DateTimeOriginal                : 2024:07:20 10:15:03
[EXIF]          ISO                             : 320
```

The date and the ISO arrived; the
`GPSPosition` question returns silence —
the position did not cross. The named tags
may be group-qualified (chapter 2), and
a whole group travels with a wildcard:
the form `"-all:all"` copies every group,
the explicit spelling of the default.
Selective copying is how a position gets
left behind, as above: you name what may
travel, and everything un-named — the GPS
family included — stays home. (The mirror
operation, deleting the GPS family from a
file that has it, is chapter 8's subject,
and uses the same wildcard in the other
grammar: `"-GPS*="`.)

**Check:** the discipline is identical to
chapter 3's — read the patient before
(`-a -G -s`), copy, read it after, and mind
the `_original`. The copy is proven by the
second read, not by the summary line.

One boundary, stated plainly: copying
moves values between files that are
*supposed* to share them. These two frames
stood side by side at one event, so shared
dates and position are the truth. Copying
a date onto a file from a different day
manufactures a false diary with great
efficiency — the tool cannot tell the
difference, so the judgement is entirely
yours. Chapter 5's scan gets its date a
more honest way: typed in, from knowledge,
not borrowed.
