# Troubleshooting

**Scope:** the failures this guide's
commands can actually produce — symptom,
cause, fix — with every quoted message
captured from a real run unless it says
otherwise.

## "Tag 'Nonsense' is not defined" — and "Nothing to do."

```
$ exiftool "-Nonsense=5" shoebox/harbour-morning.jpg
Warning: Tag 'Nonsense' is not defined
Nothing to do.
```

A tag name ExifTool doesn't know.
**Cause:** a misspelling, a name from
another tool's vocabulary, or a tag that
exists only in a group you haven't
qualified (chapter 2's `Group:Tag`
spelling is worth trying). Nothing was
written — `Nothing to do.` is the write
declining itself. **Fix:** read the file
with `-a -G -s` and find the tag's true
name in its own diary; ExifTool's names
are the canonical EXIF/IPTC/XMP ones.
A user-defined tag of your own needs the
config file of chapter 10, not just a
confident spelling.

## "Writing of TXT files is not yet supported"

```
$ exiftool "-Artist=Someone" note.txt
Error: Writing of TXT files is not yet supported - note.txt
    0 image files updated
    1 files weren't updated due to errors
```

**Cause:** ExifTool *reads* far more
formats than it *writes*. Text files,
among others, are read-only to it — the
read in chapter 9 that listed `notes.txt`
was genuine, and this refusal is its
mirror. **Fix:** there isn't one within
the tool, and the error is the guide's
honesty about scope made audible: check a
format's writability before building a
workflow on it. The formats at this
guide's heart — JPEG, TIFF, PNG, HEIC,
the RAW family, PDF — are writable.

## "Error creating file: …_exiftool_tmp"

**Symptom:** a write fails with an error
naming a temporary file, and
`0 image files updated`.

**Cause:** chapter 3's mechanism, caught
in the act — a write builds the new file
beside the old one and renames it over.
That needs a **writable directory**. It
does not need a writable *file*: in a
writable directory the rename succeeds
even over a read-only file, which is why
casual tests often "prove" permissions
don't matter. On the build machine the
honest capture needed a genuinely
unprivileged user:

```
$ su -s /bin/sh nobody -c 'exiftool "-Artist=Someone" locked/clock-wrong.jpg'
Error: Error creating file: locked/clock-wrong.jpg_exiftool_tmp - locked/clock-wrong.jpg
    0 image files updated
    1 files weren't updated due to errors
```

**Fix:** give the operation a directory
the writer can create files in — copy the
photo somewhere writable, edit, copy back
— rather than loosening permissions on a
whole tree to please one write.

## The rename that answered with a tag error

```
$ exiftool "-filename<one-name.jpg" first.jpg second.jpg
Warning: No writable tags set from first.jpg
Warning: Invalid tag name 'one-name.jpg'. Use '=' not '<' to assign a tag value - first.jpg
Warning: No writable tags set from second.jpg
    0 image files updated
    2 image files unchanged
```

**Cause:** `<` means *copy from* — the
text after it is read as a source tag or
format string, and `one-name.jpg` is
neither. **Fix:** a fixed literal name is
an assignment: `-filename=one-name.jpg`.
Names *derived* from tags keep `<` and
the `${…}` spelling of chapter 6.

## "Tag 'DateTimeOriginal' not defined" during a rename

From chapter 6's batch: the file simply
has no such tag — a scan, a stripped
frame. The file is skipped, its name
unchanged, the rest of the batch proceeds.
**Fix:** it isn't an error to repair at
the rename step; it is the audit finding
its files. Date them first (recipes), or
except them knowingly.

## "Time is too far before track in File:Geotime"

The geotag found no trackpoint near the
photo's time (chapter 7's capture, from a
photo a year away from the track).
**Cause:** either the photo is genuinely
outside the recorded walk, or the UTC
trap — the camera's local stamp read as
UTC. **Fix:** check which by comparing
the photo's stamp against the track's
first and last times and your time zone;
an offset-shaped gap (a round number of
hours) is the trap, and the chapter's
`-geotime` offset or a prior date shift
is the cure. `0 image files updated`
means no position was invented in the
meantime.

## "My edit didn't take"

Three causes, in the order they actually
occur. **You read the backup:** the
`_original` beside the file holds the
pre-write state by design (chapter 3) —
make sure the reader opened the file, not
its suffix twin. **You read the wrong
copy of the tag:** with duplicates in
play, a plain read shows one group's
value; re-read with `-a -G -s` (chapter
2) before concluding anything. **You
changed the other date:** filesystem
tools move the filesystem's dates;
`-time:all` (chapter 5) shows both bands
and settles which one your edit touched.

## "The summary says updated, but I can't see a change"

The summary counts *files written*, and a
write whose new value equals the old
still rewrites the file. Read back the
specific tag (chapter 3's habit) rather
than the whole output — or diff the
before and after reads, which, for a
guide's worth of discipline, is why the
shoebox transcripts keep both.

## The config file that does nothing

A home config (chapter 10) that ExifTool
silently doesn't apply. **Causes, in
order:** the file isn't where ExifTool
looks (`~/.ExifTool_config`, beside the
executable on Windows — the documented
locations); the trailing `1;` is missing,
so Perl rejects the file as false; or a
syntax slip makes the whole file fail to
load, shortcuts with it. **Fix:** test
with `-config` pointed at the file
explicitly — if it works there, the file
is fine and the location was the fault.
(Systematic diagnosis of a config that
*errors aloud* follows the message; this
entry covers the silent case, which is
the one that wastes evenings.)
