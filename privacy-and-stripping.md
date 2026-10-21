# Privacy and Stripping

**Scope:** what a shared photo tells
strangers, the two grades of removing it —
the GPS family alone, or everything — the
verification read that must follow, and
the backup that quietly keeps the secret.

## What the birthday photo says

The lab's phone photo is ordinary: a
birthday, a cake, candles. Read it whole,
through the magnifying glass:

```
$ exiftool -a -G -s shared.jpg | wc -l
52
```

Fifty-two lines of facts ride along when
this file is posted, messaged, or emailed.
Some are harmless (the exposure). Some are
a diary (the exact evening, the phone
model, a comment naming the occasion). And
one is an address written in numbers:

```
$ exiftool -GPSPosition -Artist -UserComment shared.jpg
GPS Position                    : 47 deg 36' 22.32", 122 deg 19' 55.56"
Artist                          : A. Rivera
User Comment                    : Blowing out the candles, February 2024
```

(The lab's coordinates point at a public
waterfront. Real phone photos point at
houses — the photographer's, and the
homes of everyone whose parties they
photograph.) Any recipient can read these
with the same chapter 2 commands; most
platforms strip metadata on upload, some
don't, and "most" is doing a lot of work
in that sentence. The safe assumption is
the simple one: **whatever is in the file
when it leaves your hands is published.**

## The surgical strip: position only

Usually the photograph's diary is not the
problem — its location is. Removing one
family of tags is a write with the value
left off, with a wildcard for the family:

```
$ exiftool "-GPS*=" shared.jpg
    1 image files updated

$ exiftool -GPSPosition shared.jpg
[no output — the position is gone]

$ exiftool -Artist -UserComment shared.jpg
Artist                          : A. Rivera
User Comment                    : Blowing out the candles, February 2024
```

The position is gone; the birthday
remains. `"-GPS*="` says: every tag whose
name begins `GPS`, in every group, set to
nothing. This is the strip to use when
sending a photo to someone who should
have the picture and its story but not
the coordinates of the kitchen.

## The full strip

For a file going somewhere untrusted, the
whole diary can go:

```
$ cp shoebox/birthday-cake.jpg stripped.jpg

$ exiftool -all= stripped.jpg
    1 image files updated

$ exiftool stripped.jpg
ExifTool Version Number         : 13.55
File Name                       : stripped.jpg
Directory                       : .
File Size                       : 755 bytes
File Modification Date/Time     : 2026:10:21 19:28:57+00:00
File Access Date/Time           : 2026:10:21 19:28:57+00:00
File Inode Change Date/Time     : 2026:10:21 19:28:57+00:00
File Permissions                : -rw-rw----
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Image Width                     : 96
Image Height                    : 96
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                   : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 96x96
Megapixels                      : 0.009

$ exiftool -s -s -s -ImageSize stripped.jpg
96x96
```

What remains is only what cannot be
removed because it is the file itself:
its name, its size on disk, and the JPEG
structure any decoder needs. No dates
from the camera, no camera, no artist, no
position. The file shrank from 1343 bytes
to 755 — the diary was nearly half the
file at specimen scale — and the picture
is untouched, still 96×96, still openable
by everything. `-all=` deletes tag
*values*; it is worth knowing it leaves a
few structural facts (sizes, encoding)
because those are not metadata, they are
the photograph's body.

## The two traps

**The backup keeps the secret.** Chapter
3's `_original` is a complete copy, and
stripping is a write:

```
$ ls
shared.jpg
shared.jpg_original
shoebox
stripped.jpg
stripped.jpg_original
```

`stripped.jpg_original` contains the GPS
coordinates, the date, everything — that
is its job. Stripping for privacy is
therefore a three-step act: strip a
**copy**, verify the copy, and make sure
no `_original` travels with it. Either
strip with `-overwrite_original` when the
source is itself a throwaway copy, or
delete the backups before the folder goes
anywhere. A "stripped" photo emailed from
a folder whose `_original` is attached by
the same drag-selection has published the
very thing it removed.

**The strip doesn't reach outside the
file.** `-all=` cannot remove: the
*filename* (rename `IMG_…_at_home.jpg`
before it confesses for you); the
*filesystem dates* of the copy, which now
say when you stripped it, not when you
took it — harmless, but not nothing; and
above all **the picture itself** — the
street sign in the background, the house
number on the door, the faces. Metadata
stripping is one layer of sharing hygiene,
not the whole of it. This guide's honest
scope stops at the file's diary; the
pixels are your editorial judgement.

**Check:** the verification ritual for any
file about to leave: read it bare, read it
with `-a -G -s`, read `-GPSPosition`
alone, and look at the directory for a
`_original`. Four reads, thirty seconds,
and you know what you are publishing —
which was the point of the tool all along.
