# Batches and Output Shapes

**Scope:** many files at once — directories
as arguments, recursion, extension
filters, conditions — and getting answers
out in shapes other tools can eat: plain
values, tables, and JSON.

## A directory is an argument

Every command so far named one file. Name
a directory instead and the command runs
over its contents, with a per-file header
and a summary at the end. The shoebox's
dates, at the third rung of the `-s`
ladder, with a decoy slipped in:

```
$ echo "a note, not a photo" > shoebox/notes.txt

$ exiftool -r -s -s -s -DateTimeOriginal shoebox
======== shoebox/harbour-morning.jpg
2023:06:14 07:42:18
======== shoebox/birthday-cake.jpg
2024:02:09 19:05:41
======== shoebox/clock-wrong.jpg
2024:09:02 03:14:00
======== shoebox/trail-summit.jpg
2024:05:18 11:23:40
======== shoebox/scan-1994.jpg
======== shoebox/parade-01.jpg
2024:07:20 10:15:03
======== shoebox/parade-02.jpg
======== shoebox/notes.txt
    1 directories scanned
    8 image files read
```

Things worth seeing:

- `-r` recurses into subdirectories. This
  shoebox has none, but on real trees `-r`
  is the difference between a folder and
  the whole archive beneath it.
- Files lacking the tag print their header
  and then nothing — the scan and the
  stripped frame, dateless, are *visible
  by their silence*, in context.
- **`notes.txt` was read too.** ExifTool
  reads hundreds of formats, text files
  among them; "image files read" in the
  summary is the tool's generic phrase for
  files processed. When a batch touches
  something unexpected, this is usually
  why — and the cure is a filter.

## Filters: -ext and -if

By extension:

```
$ exiftool -r -ext jpg -s -s -s -DateTimeOriginal shoebox
======== shoebox/harbour-morning.jpg
2023:06:14 07:42:18
...
    1 directories scanned
    7 image files read
```

Seven files, not eight: the note is
excluded. `-ext jpg` (repeatable,
case-insensitive) is the blunt filter and
the right first one for a photo tree.

By condition — "files where this is true"
— is `-if`, taking an expression in
ExifTool's own small language, where `$`
names a tag:

```
$ exiftool -if 'not $DateTimeOriginal' -s -s -s -FileName -r shoebox
======== shoebox/scan-1994.jpg
scan-1994.jpg
======== shoebox/parade-02.jpg
parade-02.jpg
    1 directories scanned
    5 files failed condition
    2 image files read
```

Exactly the two dateless specimens, and
the summary changes its wording to keep
the accounts honest: five files **failed
the condition** (they were examined and
didn't match — that is not an error), two
were read. A condition is a question the
batch answers file by file; the recipes
chapter turns this one into a standing
audit for undated photos.

## Shapes: table and JSON

Single values are for scripts that want
one fact. For an overview, `-T` prints a
tab-separated table, one row per file,
with a dash where a tag is missing:

```
$ exiftool -T -FileName -DateTimeOriginal -Model shoebox
harbour-morning.jpg     2023:06:14 07:42:18     Canon EOS 80D
birthday-cake.jpg       2024:02:09 19:05:41     iPhone 12
clock-wrong.jpg         2024:09:02 03:14:00     NIKON D3500
trail-summit.jpg        2024:05:18 11:23:40     ILCE-6400
scan-1994.jpg           -                       -
parade-01.jpg           2024:07:20 10:15:03     Canon EOS 80D
parade-02.jpg           -                       -
notes.txt               -                       -
```

(Tabs render as space here; the capture
file holds the real thing.) A table pastes
straight into a spreadsheet, and the `-`
rows are the audit from the previous
section wearing a different dress.

For programs, `-json`:

```
$ exiftool -json -FileName -DateTimeOriginal shoebox/parade-01.jpg shoebox/trail-summit.jpg
[{
  "SourceFile": "shoebox/parade-01.jpg",
  "FileName": "parade-01.jpg",
  "DateTimeOriginal": "2024:07:20 10:15:03"
},
{
  "SourceFile": "shoebox/trail-summit.jpg",
  "FileName": "trail-summit.jpg",
  "DateTimeOriginal": "2024:05:18 11:23:40"
}]
```

An array of objects, one per file, tag
names as keys. (The `2 image files read`
summary goes to the error stream, so a
pipe receives clean JSON — the summary is
for the terminal, not the data.) The
sibling guide *jq for Mere Mortals* takes
it from there; the point for this chapter
is that metadata, which began as a diary
inside pictures, ends as ordinary data
that ordinary tools can filter, join, and
count. The recipes end there too.

**Batch discipline, once:** everything
chapter 3 said about writes applies with
a multiplier. A batch write's `_original`
backups accumulate per file (one per
photo, same rule, larger pile), the
summary's counts are the audit — updated,
unchanged, failed, *weren't updated due
to errors* are four different outcomes —
and a rehearsal (a read with the same
arguments, or `-testname` for renames)
costs seconds against a tree that took
years to grow.
