# Reading Tags

**Scope:** asking for the tags you want —
names, short forms, duplicates, groups, and
the date family — and reading exactly what
came back, no more and no less.

## Ask for tags by name

A bare `exiftool FILE` prints everything.
Most of the time you want a few named facts.
Name them, each with a dash:

```
$ exiftool -DateTimeOriginal -Model -ISO shoebox/harbour-morning.jpg
Date/Time Original              : 2023:06:14 07:42:18
Camera Model Name               : Canon EOS 80D
ISO                             : 200
```

Three things to notice. The printed labels
are the friendly forms (`Date/Time
Original`); the names you type are the
**tag names** (`DateTimeOriginal`). Tag
names are forgiving: case doesn't matter,
and most punctuation is optional, so
`-datetimeoriginal` asks the same question.
And a tag that a file doesn't have is
simply not printed — ask the scan for its
`DateTimeOriginal` and the answer is
silence, not an error. Absence is an answer
you will use as a tool in chapter 9.

## The -s ladder

`-s` shortens the output, one rung at a
time, and the three rungs are the difference
between reading as a human and reading as a
script:

```
$ exiftool -s -DateTimeOriginal shoebox/harbour-morning.jpg
DateTimeOriginal                : 2023:06:14 07:42:18

$ exiftool -s -s -DateTimeOriginal shoebox/harbour-morning.jpg
DateTimeOriginal: 2023:06:14 07:42:18

$ exiftool -s -s -s -DateTimeOriginal shoebox/harbour-morning.jpg
2023:06:14 07:42:18
```

One `-s`: tag names instead of friendly
labels. Two: the machine-shaped
`Name: value` line. Three: **the value
alone**, which is what a script captures
into a variable. Nothing else changes —
the same tag, the same value — only the
dressing. Most captures in the later
chapters use the third rung when the value
is the whole point.

## Duplicates and -a

The same fact can live in two groups at
once. The birthday photo carries an
`XResolution` in its JFIF segment and
another in its EXIF segment. Asked plainly,
ExifTool prints one of them:

```
$ exiftool -XResolution shoebox/birthday-cake.jpg
X Resolution                    : 1

$ exiftool -a -XResolution shoebox/birthday-cake.jpg
X Resolution                    : 1
X Resolution                    : 1
```

`-a` allows duplicates: every copy, in
group order. Without it you are seeing the
first copy and being told, by silence, that
it is the only one — the same family of
quiet surprise as find's silent actions in
the sibling guide. Which copy you got is
unknowable until you ask where it lives:

```
$ exiftool -a -G -XResolution shoebox/birthday-cake.jpg
[JFIF]          X Resolution                    : 1
[EXIF]          X Resolution                    : 1
```

`-G` prefixes each line with its **group**.
With `-a -G -s` together you get the full
address of every fact: group, tag name,
value. That is this guide's standard
magnifying glass, and the specimen listings
in the copying and privacy chapters are
shown through it.

## Asking a group, naming a group

A group can be asked for whole. The dates
of the clock-wrong photo, as a family:

```
$ exiftool -time:all shoebox/clock-wrong.jpg
File Modification Date/Time     : 2026:10:21 19:27:20+00:00
File Access Date/Time           : 2026:10:21 19:27:20+00:00
File Inode Change Date/Time     : 2026:10:21 19:27:20+00:00
Modify Date                     : 2024:09:02 03:14:00
Date/Time Original              : 2024:09:02 03:14:00
Create Date                       : 2024:09:02 03:14:00
```

`-time:all` means "every tag in every group
whose name concerns time" — the filesystem's
three (top, showing the build day and a
time zone) and EXIF's three (bottom, the
camera's claim). Other family names work
the same way: `-GPS:all`, `-EXIF:all`.

And one tag can be pinned to one group,
because `DateTimeOriginal` in EXIF and a
date of the same words in XMP are different
facts that usually agree and occasionally
don't:

```
$ exiftool "-EXIF:DateTimeOriginal" shoebox/harbour-morning.jpg
Date/Time Original              : 2023:06:14 07:42:18
```

The spelling is `Group:Tag`. When a read
surprises you — a date that won't move in
chapter 5, two values for one fact — the
first diagnostic is always to re-read with
`-a -G -s` and see how many copies of the
fact there are and where each one lives.

**Check:** on your own shoebox, read the
birthday photo three ways — bare, then
`-a -G -s`, then ask for `-GPSPosition`
alone. The first shows you the diary, the
second shows you its filing system, and
the third shows you the single fact the
privacy chapter exists to manage.
