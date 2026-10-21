# Recipes

**Scope:** the earlier chapters combined
into the jobs people actually sit down to.
Every ingredient was verified on its own;
the recipes add only the assembly and the
order of operations.

## Fix a whole folder's clock

Chapter 5's repair, at chapter 9's scale.
One camera, one wrong clock, forty files:

```
$ exiftool "-AllDates+=1:05:00" holiday-folder
    1 directories scanned
    2 image files updated
```

(Shown against two lab files; the count
line scales, the grammar doesn't.) The
shift is identical per file, which is the
point: whatever each photo's moment was,
it moves by the same error. Rehearse by
reading the folder's dates first
(`-time:all` on the folder, or the `-T`
table), and remember the backups: forty
files, forty `_original`s, kept until the
read-back table looks right.

## Date the scans

The scan's repair from chapter 5, as a
standing job for a box of prints being
digitised. Each scan gets the date the
family can defend, typed once per print:

```
$ exiftool -overwrite_original "-DateTimeOriginal=1994:07:15 12:00:00" scan-1994.jpg
```

Scans destined for renaming or filing
need the date *first* — the next recipes
are why. And where even the day is a
guess, say so in the file's `Description`
when you set the date; the diary should
not claim a certainty its keeper didn't
have.

## File the year by month

Repair the dates, then let the dates file
the photos (chapter 6's move, at scale):

```
$ exiftool "-directory<by-date/\${DateTimeOriginal}" -d %Y-%m holiday-folder
```

Add `-testname`'s sibling rehearsal
(`-testdir`, documented) or run it first
on a copied subset; watch the
`directories created` count — one per
month present is right, one per photo
means the `-d` format didn't do what you
thought.

## Audit for the dateless

The condition from chapter 9 as a
standing report — which files in this
tree have no date taken:

```
$ exiftool -if 'not $DateTimeOriginal' -s -s -s -FileName -r archive
```

Run it after every import. The output is
a work list: each name is a file the
timeline tools will misfile, and the
repairs are the two above — shift if the
camera lied, set if the date is known,
copy (chapter 4) if an untouched sibling
shares the moment.

## Restore the stripped file

An editor dropped a photo's diary; the
unedited original of the same shot still
has it:

```
$ exiftool -tagsFromFile original.jpg edited.jpg
```

And the selective form when the position
mustn't travel with the rest:

```
$ exiftool -tagsFromFile original.jpg "-EXIF:DateTimeOriginal" "-ISO" edited.jpg
```

Both captured in chapter 4 on the parade
pair. The judgement call from that
chapter stands: donor and patient must
truly share a moment, or the copy
manufactures a past.

## Share-safe in one pass

Chapter 8's ritual, assembled. Work on a
copy destined for sending; strip
everything; leave no backup beside it;
verify before it leaves:

```
$ cp birthday-cake.jpg send.jpg
$ exiftool -overwrite_original -all= send.jpg
$ exiftool -a -G -s send.jpg
$ exiftool -GPSPosition send.jpg
```

The last two reads are the recipe, not a
garnish: the first proves the diary is
gone, the second proves the specific
secret is. For position-only removal —
the diary may stay, the address may not —
the second line becomes
`exiftool -overwrite_original "-GPS*=" send.jpg`,
and the same verification follows.

## An index of the whole shelf

The batch shapes of chapter 9 as a
cataloguing habit — a table for a
spreadsheet:

```
$ exiftool -T -FileName -DateTimeOriginal -Model -GPSPosition -r archive > index.txt
```

or JSON for anything programmatic:

```
$ exiftool -json -r archive > index.json
```

The dash rows in the table are the audit
recipe wearing its other dress: an index
regenerated after each repair round shows
the dateless and the position-less counts
shrinking — or not, which is also
information.

## Set the copies' filesystem dates from the diary

Housekeeping promised in chapter 5: some
old tools sort by the filesystem's date.
Make the copies agree with the diary:

```
$ exiftool "-FileModifyDate<DateTimeOriginal" -r archive
```

(documented form; the mechanics are
chapter 4's copy, with a `File` tag as the
destination). Do this to *copies* made for
such a tool, not to the archive itself —
the filesystem date of the archive's files
is the record of when they were last
handled, and flattening it into the diary
destroys one fact to flatter another.
