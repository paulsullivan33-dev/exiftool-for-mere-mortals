# The Neighbours

**Scope:** the tools that share parts of
ExifTool's ground — what each is for, so
the reader can place ExifTool honestly:
it is the deepest reader and writer of
metadata, not the only one, and not a
photo manager at all.

## The shape of the neighbourhood

Metadata work divides three ways. Some
tools **read and write the diary**, as
ExifTool does. Some **read the container**
— the file as a stream of bytes in a
format — and can tell you everything about
the encoding and little about the
photograph. And some are **managers**:
they catalogue, display, and organise
collections, treating metadata as their
filing system. ExifTool is the first kind,
only that kind, and proud of it; the
neighbours below cover the other two.

A note on evidence: of the tools named
here, only one — ffprobe — was installed
on the build machine, and its capture
below is live. The rest are described from
their documentation and long public
record, and `criteria.md` lists them as
documented-only. Nothing in this chapter
is needed to use the rest of the guide.

## ffprobe and mediainfo: the container readers

From the sibling guide's family —
ffmpeg's inspector, pointed at a photo:

```
$ ffprobe -hide_banner shoebox/harbour-morning.jpg
Input #0, jpeg_pipe, from 'shoebox/harbour-morning.jpg':
  Duration: N/A, bitrate: N/A
  Stream #0:0: Video: mjpeg (Baseline), yuvj420p(pc, bt470bg/unknown/unknown), 120x90 [SAR 1:1 DAR 4:3], 25 fps, 25 tbr, 25 tbn
```

To ffprobe, a JPEG is a one-frame video,
and what it reports is the *stream*: the
encoding (mjpeg, Baseline), the pixel
format, the dimensions. Nothing about the
camera, the moment, or the place — it
does not read the diary because the diary
is not its subject. For video and audio
files the balance flips: containers and
streams are the subject, ffprobe and its
cousin **mediainfo** (the friendlier,
report-shaped inspector, described here
only) are the right first tools, and
ExifTool reads those files' tags as a
complement — it writes metadata into many
video containers too, though the camera
standards there are thinner than EXIF's.

## exiv2: the other diary tool

**exiv2** is the closest neighbour: a
C++ library and command-line tool for
EXIF, IPTC, and XMP metadata, used inside
many photo applications. For the common
work — reading the main EXIF fields,
setting a date, deleting GPS — it covers
much of the same ground with a different
grammar. Where ExifTool stands apart is
breadth and depth: the number of formats,
the number of tags (including the
maker-specific **MakerNotes** inside
camera files, which ExifTool decodes more
completely than any rival), the Composite
computations, geotagging, and the
config-file programmability. Where exiv2
stands apart is being a library first —
it is inside your other tools, quietly,
which ExifTool's Perl largely isn't. For
a reader of this series the practical
summary is: if a job is in this guide,
ExifTool is the instrument; exiv2 is the
name to recognise inside everything else.

## ImageMagick's identify

**ImageMagick** is an image *processor* —
resize, convert, compose — whose
`identify -verbose` command reports a
file's properties, metadata included.
If ImageMagick is already your tool, its
`-format` escapes can print chosen EXIF
values, and its writes can set some. It is
described here, not captured: the build
machine doesn't have it. The caution that
matters: a processor's first loyalty is
the pixels, and round-tripping a photo
through a processor to adjust its diary
risks the picture being re-encoded along
the way. ExifTool's writes never
re-encode — the image data crosses
byte-for-byte (chapter 3) — which is the
whole reason a dedicated metadata tool
exists.

## Operating systems and photo managers

Every desktop shows a friendly subset:
Windows Explorer's Properties, macOS
Finder's Get Info and the Photos app,
GNOME and KDE image viewers. These read
the headline facts (date, camera,
sometimes a map) and are the right tool
for a glance. Their writes, where they
exist at all, are the trap from the
stripped parade frame: more than one
editor's "save" quietly drops the tags it
doesn't understand.

Beyond glances sit the **managers** —
digiKam, Adobe Lightroom and Bridge,
Apple Photos, and their kin — which
catalogue libraries, keep databases of
keywords and ratings, and display the
diary beautifully. They are ExifTool's
customers, not its rivals: several use
the same standards, some embed exiv2, and
all of them eventually meet a file whose
diary needs surgery below the level their
interfaces expose. That surgery is this
guide. The boundary to keep: ExifTool has
no library, no thumbnails, no search
albums. It makes the facts right; what
you keep them in is another tool's job —
and a wrong fact, beautifully catalogued,
is still wrong.
