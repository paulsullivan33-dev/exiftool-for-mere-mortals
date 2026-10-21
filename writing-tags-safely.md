# Writing Tags Safely

**Scope:** the write itself — `-Tag=value`,
what physically happens to the file, the
`_original` backup ExifTool keeps by
default and the flag that skips it — and
the habits that make writes boring.

## The write

Reading used `-Tag` alone. Writing adds an
equals sign:

```
$ exiftool -Artist shoebox/birthday-cake.jpg
Artist                          : A. Rivera

$ exiftool "-Artist=J. Rivera" shoebox/birthday-cake.jpg
    1 image files updated

$ exiftool -Artist shoebox/birthday-cake.jpg
Artist                          : J. Rivera
```

The middle command is the whole grammar:
`"-Artist=J. Rivera"`, quoted because the
value contains a space and the shell would
otherwise split it into two arguments.
ExifTool answers with a summary, not the
value — `1 image files updated` — and the
third command is the read-back that proves
the write. **That read-back is a habit,
not a courtesy.** Every write in this guide
is followed by one, because a summary line
tells you a file changed, not that it
changed the way you meant.

Quote writes as a reflex. An unquoted
`-Artist=J. Rivera` hands ExifTool the file
`Rivera` as a second target, and on a bad
day there is a file by that name.

## What physically happens

A metadata write is not a patch in place.
ExifTool writes the **whole file anew** —
same picture, new metadata — to a
temporary file beside the original, then
renames it over the original. Three
consequences, all of which return later:

- The picture data is copied byte for
  byte; the image you see does not change.
  (In the capture above the file stayed
  1343 bytes, because the new value was
  the same length as the old.)
- A write needs a **writable directory**,
  for the temporary file — a fact the
  troubleshooting chapter demonstrates
  with a real refusal.
- Because the file is replaced, its
  filesystem dates move. The *filesystem's*
  modification time becomes the moment of
  the write, whatever the EXIF dates say.
  Chapter 5 lives inside that distinction.

## The backup: _original

Look at the directory after that first
write:

```
$ ls shoebox | grep birthday
birthday-cake.jpg
birthday-cake.jpg_original
```

By default, before replacing a file,
ExifTool keeps the untouched original
under the name **`_original`** — same
directory, same name plus the suffix. It
is a complete copy, metadata and all. Your
undo is a copy away: replace the file with
its `_original` and the write never
happened. This default is the reason
ExifTool writes are safe to learn on, and
this guide's standing rule follows from
it: **work on copies, keep the backups
until the result is proven, then tidy.**

The flag that skips the backup is
`-overwrite_original`:

```
$ cp shoebox/birthday-cake.jpg work-copy.jpg

$ exiftool -overwrite_original "-Artist=A. Rivera" work-copy.jpg
    1 image files updated

$ ls | grep work
work-copy.jpg
```

No `_original` appeared. The write is now
unaccompanied: if the value was wrong, the
previous value survives only in your notes.
`-overwrite_original` is the right flag for
batch work on expendable copies — most of
chapters 4 to 9 use it inside the lab,
where the shoebox can be rebuilt — and the
wrong flag for the only copy of anything.
Chapter 8 adds the sharpest case: when you
strip metadata for privacy, **the
`_original` keeps everything you stripped**,
GPS included, verified there on a real
stripped file. A stripped photo with its
backup beside it is not stripped at all.

## Deleting a value, adding a tag

An empty assignment deletes:

```
$ exiftool -Artist= shoebox/harbour-morning.jpg
    1 image files updated

$ exiftool -Artist shoebox/harbour-morning.jpg
[no output — the tag is gone]
```

And a tag a file never had can simply be
given. The parade's second frame arrived
with almost no diary (chapter 4 is its
story); a credit line it never carried:

```
$ exiftool -overwrite_original "-Credit=Harbour Festival Committee" shoebox/parade-02.jpg
    1 image files updated

$ exiftool -Credit shoebox/parade-02.jpg
Credit                          : Harbour Festival Committee
```

`Credit` is an IPTC tag — a different group
from everything written so far, and
ExifTool created the IPTC record to hold
it. Writing is not limited to tags that
exist, or to one group at a time: one
command may carry several assignments,
applied by the one pass over the file.

## The safety summary, once

- Writes replace the whole file; the
  picture survives, the filesystem dates
  don't.
- Default writes keep `_original`; treat
  it as part of the operation, not litter
  — and as a copy of the data, with all
  that implies for privacy.
- `-overwrite_original` is for copies you
  can afford to lose the previous state of.
- Read back after every write. The summary
  line is a claim; the read is the proof.
