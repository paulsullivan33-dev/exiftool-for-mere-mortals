# Renaming from Metadata

**Scope:** letting the diary name the files
— `-filename` fed from tags, the `%` date
codes, rehearsing with `-testname`, and
what happens on collisions and on files
with no date to give.

## The problem with camera names

Cameras name files like a counter with
amnesia: `IMG_4021.jpg` says nothing, and
the counter restarts. A folder of those is
a folder you search by opening pictures.
The diary knows a better name — the moment
each photo was taken — and ExifTool will
rename from it, because a file's name is,
to ExifTool, just another writable tag:
`FileName`.

**Rehearse first.** The rehearsal flag is
`-testname`: same machinery, no rename,
and it shows its working:

```
$ exiftool -d %Y-%m-%d_%H-%M-%S "-testname<harbour-\${DateTimeOriginal}-%f.%e" shoebox/harbour-morning.jpg
'shoebox/harbour-morning.jpg' --> 'shoebox/harbour-2023-06-14_07-42-18-harbour-morning.jpg'
    0 image files updated
    1 image files unchanged
```

Read the proposed name's anatomy, because
it is the whole chapter in one string:

- `-d %Y-%m-%d_%H-%M-%S` is a **date
  format**: how the tag's timestamp is
  rendered. `%Y` four-digit year, `%m`
  month, `%d` day, `%H` `%M` `%S` the
  clock, all zero-padded — which is what
  makes the results sort correctly as
  plain text.
- `${DateTimeOriginal}` inside the pattern
  means "the value of this tag, rendered
  by the `-d` format." The backslash in
  the capture is the shell's doing: the
  `$` must reach ExifTool unexpanded, so
  in these commands it is escaped (or the
  whole argument single-quoted).
- `%f` is the file's existing name without
  its extension, `%e` the extension
  itself. Keeping `%f` in the pattern
  preserves a thread back to the camera's
  naming; dropping it gives pure date
  names.
- The `<` in `-testname<...` is the same
  "take the value from" operator as
  chapter 4's `-tagsFromFile`: the name is
  *copied from* a computed string.
  (Assigning a fixed literal name uses
  `-filename=Name.jpg` with `=` — the
  troubleshooting chapter shows the real
  warning that mixing the two up earns.)

`0 image files updated` beside the arrow
is the rehearsal keeping its promise.

## The rename, on a folder

Applied to a directory of copies, with
the pattern kept to date-plus-old-name:

```
$ exiftool -d %Y-%m-%d_%H-%M-%S "-filename<\${DateTimeOriginal}-%f.%e" renamed
Warning: [minor] Tag 'DateTimeOriginal' not defined - renamed/parade-02.jpg
Warning: No writable tags set from renamed/parade-02.jpg
Warning: [minor] Tag 'DateTimeOriginal' not defined - renamed/scan-1994.jpg
Warning: No writable tags set from renamed/scan-1994.jpg
    1 directories scanned
    5 image files updated
    2 image files unchanged

$ ls renamed
2023-06-14_07-42-18-harbour-morning.jpg
2024-02-09_19-05-41-birthday-cake.jpg
2024-05-18_11-23-40-trail-summit.jpg
2024-07-20_10-15-03-parade-01.jpg
2024-09-02_03-14-00-clock-wrong.jpg
parade-02.jpg
scan-1994.jpg
```

Five files renamed into chronological
order — a plain `ls` now reads as a
timeline. And the two the date repair
hadn't reached (in this fresh shoebox the
scan and the stripped frame have no
`DateTimeOriginal`) were **warned about
and left exactly as they were**: a missing
tag is a skipped file, not a guessed name.
The warnings are the honest part of batch
work; read them every time, because they
are the list of what your pattern quietly
didn't cover.

(In this run the scan kept its name
because the shoebox was fresh — chapter
5's repair, setting its date outright, is
what a real folder would get first. Order
of operations is a theme of the recipes
chapter: repair the diary, then let the
diary name the files.)

## Collisions

Date-derived names collide the moment two
photos share the rendered moment — burst
frames within one second, or a pattern too
coarse for its folder. Two copies of the
same photo, renamed by day alone:

```
$ exiftool -d %Y-%m-%d "-filename<\${DateTimeOriginal}.%e" first.jpg second.jpg
Error: '2024-09-02.jpg' already exists - second.jpg
    1 image files updated
    1 files weren't updated due to errors
```

The first took the name; the second was
**refused and left untouched** — ExifTool
never overwrites a file to satisfy a
rename. If collisions are expected and
acceptable, ask for a copy number in the
pattern — `%+c` inserts one, only when
needed:

```
$ exiftool -d %Y-%m-%d "-filename<\${DateTimeOriginal}%+c.%e" second.jpg
    1 image files updated

$ ls
2024-09-02.jpg
2024-09-02_1.jpg
```

The `_1` appears only on the file that
needed it. Choosing between the error and
the suffix is choosing what your folder
means: the error says every name is a
claim worth checking; the suffix says the
sequence is what matters. Bursts belong to
the second; evidence belongs to the first.

## Moving as well as naming

The same machinery moves files: give
`Directory` a pattern and the file is
filed as well as named. Month buckets,
from the one verified pass:

```
$ exiftool -overwrite_original "-directory<by-date/\${DateTimeOriginal}" -d %Y-%m shoebox/trail-summit.jpg
    1 directories created
    1 image files updated

$ ls by-date
2024-05
```

The directory was created for the move,
and the summary counts it separately —
directories created is a line worth
reading before a large filing run, in case
a pattern typo proposes to invent two
hundred folders. `-testname` has a
sibling, `-testdir`, that rehearses moves
the same way; and filing by month is the
shape of the archiving recipe in
chapter 11.
