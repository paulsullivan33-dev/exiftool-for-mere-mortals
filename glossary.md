# Glossary

**Scope:** the words this guide uses in
one exact sense, gathered in one place.

**AllDates.** ExifTool's built-in shortcut
for the three EXIF date tags —
`DateTimeOriginal`, `CreateDate`,
`ModifyDate` — as a group, for reads and
for shifts (chapter 5).

**Composite tag.** A value ExifTool
computes on read from other tags —
`GPSPosition`, `ImageSize`, `Aperture`,
`Light Value` — rather than a fact stored
in the file. Answers, not contents
(chapters 1, 2).

**DateTimeOriginal.** The EXIF tag for the
moment the photograph was taken — "date
taken" in every manager's language, and
the date this guide treats as the photo's
own (chapter 5).

**EXIF.** The camera metadata standard:
exposure, camera, lens, dates, GPS, kept
in records inside the image file. One
group among several (chapters 1, 2).

**Geosync.** The tolerance a `-geotag`
lookup allows around a track's points and
ends before declaring a photo outside the
track (chapter 7).

**Geotime.** The tag whose value a geotag
matches against the track — by default
`DateTimeOriginal` — read as UTC unless an
offset is supplied (chapter 7).

**GPX.** The XML track format GPS loggers
export: trackpoints with latitude,
longitude, elevation, and a UTC time
(chapter 7).

**Group.** The family a tag belongs to —
`EXIF`, `IPTC`, `XMP`, `GPS`, `File`,
`Composite` — written as the prefix in
`Group:Tag`, and shown by `-G`
(chapter 2).

**IPTC.** The press metadata standard:
captions, credits, headlines. The group
the `Credit` write in chapter 3 created.

**MakerNotes.** A camera maker's private
record block inside EXIF, decoded by
ExifTool model by model; deep water,
named in chapter 12, not swum in.

**Metadata.** The facts a file carries
inside itself about its own making and
contents — the diary, as opposed to the
filesystem's facts about the file.

**-overwrite_original.** The flag that
suppresses the `_original` backup on a
write (chapter 3) — and, when stripping,
the flag that stops the secret being kept
(chapter 8).

**_original.** The suffix of the backup
copy ExifTool keeps by default beside any
file it rewrites: the complete pre-write
state, picture and diary alike
(chapter 3).

**Shortcut.** A name of your own for a set
of tags, defined in the config file and
usable anywhere a tag name is (chapter
10).

**Tag.** A single named fact in the diary
— `ISO`, `Artist`, `GPSPosition` — read
with `-Tag`, written with `-Tag=value`,
deleted with `-Tag=` (chapters 2, 3).

**-tagsFromFile.** The copy operation:
take tag values from a named donor file
and write them into the file being
processed, wholesale or by list
(chapter 4).

**-testname.** The rehearsal form of a
`-filename` rename: computes and displays
the proposed names, changes nothing
(chapter 6).

**Trackpoint.** One fix in a GPS track:
latitude, longitude, elevation, and a UTC
timestamp (chapter 7).

**XMP.** Adobe's extensible metadata
standard, living in its own record inside
the file (or in a `.xmp` sidecar file
beside it). The scan's `Description`
lives here (chapter 1).
