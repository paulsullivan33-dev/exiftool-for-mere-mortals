# Fixing Dates

**Scope:** the six dates a photo seems to
have and the three that are really its own;
the camera-clock repair — shifting every
date by the same amount — and setting a
date outright where none exists.

## Which date is the date?

Ask the clock-wrong photo for its whole
date family (chapter 2's `-time:all`):

```
$ exiftool -time:all shoebox/clock-wrong.jpg
File Modification Date/Time     : 2026:10:21 19:27:20+00:00
File Access Date/Time           : 2026:10:21 19:27:20+00:00
File Inode Change Date/Time     : 2026:10:21 19:27:20+00:00
Modify Date                     : 2024:09:02 03:14:00
Date/Time Original              : 2024:09:02 03:14:00
Create Date                       : 2024:09:02 03:14:00
```

Six lines, two different kinds of fact.
The top three are the **filesystem's**:
when this copy of the file was last
written, read, and re-indexed — here, the
day the lab was built, with a time zone
attached. They describe the file's life on
disk and they change every time the file
is copied or edited (chapter 3). Tools
that sort by "date" and mean these will
happily file a 2024 photograph under the
day you downloaded it.

The bottom three are the **file's own**,
all EXIF: `ModifyDate` (when the camera
last touched the image), `CreateDate`
(when the image was digitised), and
**`DateTimeOriginal`** — when the shutter
fired, the date every photo manager means
by "date taken," and the one this guide
treats as the truth to repair. On an
unedited camera file the three agree, as
they do here. They agree, in this specimen,
on the *wrong* time: the story in its
description is that the camera's clock was
still on home time, an hour and five
minutes behind the moment the ferry was
actually photographed.

## Shifting: the camera-clock repair

The repair is not to retype three dates.
It is to **shift** them — the same error
sits in all three, so all three move by
the same amount, keeping their honest
agreement with each other. The shift
spelling is `+=` (and `-=` for the other
direction), applied here to `AllDates`,
ExifTool's built-in shortcut for exactly
the three EXIF dates:

```
$ exiftool "-AllDates+=1:05:00" shoebox/clock-wrong.jpg
    1 image files updated

$ exiftool -time:all shoebox/clock-wrong.jpg
File Modification Date/Time     : 2026:10:21 19:27:57+00:00
...
Modify Date                     : 2024:09:02 04:19:00
Date/Time Original              : 2024:09:02 04:19:00
Create Date                       : 2024:09:02 04:19:00
```

03:14 became 04:19 on all three, in one
pass. The shift value's grammar, verified
against these captures: a bare value is
**hours:minutes:seconds** — `1:05:00` is
one hour and five minutes. A fuller form,
`years:months:days hours:minutes:seconds`,
shifts calendar fields; a day, added to a
copy of the same photo:

```
$ exiftool "-DateTimeOriginal+=0:0:1 0:00:00" day-shift.jpg
    1 image files updated

$ exiftool -DateTimeOriginal day-shift.jpg
Date/Time Original              : 2024:09:03 04:19:00
```

September 2nd became September 3rd, the
clock time untouched. The two forms cover
the two real repairs: a clock wrong by
minutes or hours, and a clock wrong by a
whole day (or a date never set, ticking up
from the camera's factory epoch — in which
case the shift can be years, and the full
form earns its keep).

## Setting a date outright

The scan has no EXIF date at all; there is
nothing to shift. Where a date is *known*
— the print was developed the week it was
taken, and the family agrees on the day —
it can simply be set, with the ordinary
write from chapter 3:

```
$ exiftool -overwrite_original "-DateTimeOriginal=1994:07:15 12:00:00" shoebox/scan-1994.jpg
    1 image files updated

$ exiftool -DateTimeOriginal shoebox/scan-1994.jpg
Date/Time Original              : 1994:07:15 12:00:00
```

The noon is a declared estimate — scans
rarely know their hour — and the honest
place for that uncertainty is the file's
description, which already says "summer
1994." Setting is for dates you can
defend; shifting is for dates the camera
defended badly. Borrowing a neighbour's
date (chapter 4) for a file from a
different day is neither, and the guide
won't pretend otherwise.

## The trap that isn't ExifTool's

It is tempting to repair dates with the
filesystem instead — `touch` will set a
file's modification time to anything. On a
copy of the harbour photo:

```
$ touch -d "2020-01-01 00:00:00" touched.jpg

$ exiftool -time:all touched.jpg
File Modification Date/Time     : 2020:01:01 00:00:00+00:00
File Access Date/Time         : 2020:01:01 00:00:00+00:00
File Inode Change Date/Time   : 2026:10:21 19:27:58+00:00
Modify Date                     : 2023:06:14 07:42:18
Date/Time Original              : 2023:06:14 07:42:18
Create Date                       : 2023:06:14 07:42:18
```

The filesystem now insists the file is
from New Year's Day 2020; the photograph
inside still says June 2023, and every
photo manager will believe the photograph.
Neither is lying — they are different
facts with similar names, which is the
whole lesson of this chapter in one
capture. (ExifTool can write the
filesystem dates too — `-FileModifyDate`
is a tag like any other — but that is
housekeeping for chapter 11, not a repair
of the photo's own record.)

**Check:** after any date work, re-read
with `-time:all` and look at *both* bands.
The EXIF three should say what the photo
claims; the filesystem three will say when
you did the work, and confusing the second
for the first is the one error this chapter
exists to prevent.
