# ExifTool for Mere Mortals

Every photograph carries a diary inside
itself: when it was taken, what took it, how
it was exposed, and — from a phone — exactly
where its owner was standing. That diary,
the **metadata**, is not the filesystem's
business; it belongs to the file, travels
with every copy, and outlives every rename.
The sibling guide *Finding Files for Mere
Mortals* works on the filesystem's facts.
This guide works on the diary — reading it,
repairing it when a camera clock lied,
copying it back onto files an editor
stripped, filing photos under the dates the
diary claims, giving a trail photo the
position its track log remembers, and
removing the parts of it that should never
be published. The tool for all of it is
**ExifTool**, Phil Harvey's reader and
writer of file metadata, the same program
on Linux, macOS, and Windows.

ExifTool's reputation mirrors its power: it
knows thousands of tags across hundreds of
formats, and its grammar — dashes,
wildcards, group prefixes, values copied
from other tags — is small but exact. Its
writes are the serious part. A write
replaces the whole file and, by default,
keeps the old one beside it as `_original`;
used carelessly against originals it can
also manufacture a tidy, false diary at
great speed. This guide takes the grammar
one specimen at a time, states the risks
where they live, and rehearses everything
on files built to be expendable.

The lab is one shoebox of seven invented
photographs, built by a script in
`examples/` (`build-shoebox.sh`): minimal
synthesised JPEGs — flat colours, no real
scene — because the metadata is the
specimen. A camera photo with no GPS, a
phone photo with all of it, a photo whose
clock was wrong, a trail photo waiting for
its track, a scan with almost no diary at
all, and a parade in two frames, one full
and one stripped. Every name, person, and
date in it is invented, and the coordinates
point at public places.

A note on how this guide was verified.
ExifTool 13.55, the Perl edition, was run
on the build machine against the shoebox,
and every quoted command was captured: the
reads, the writes and their read-backs, the
renames, the geotag interpolation, the
strips, and the failures. Where a platform
behaves differently — the Windows
standalone build above all — the chapters
say so in place and `criteria.md` keeps the
ledger of what is documented-only.

## What this guide covers

- [**What ExifTool is**](what-exiftool-is.md) —
  the diary inside the file, tags and groups,
  the Perl original and the Windows build,
  your version, and the lab
- [**Reading tags**](reading-tags.md) —
  asking by name, the `-s` ladder, duplicates
  and `-a`, groups and `Group:Tag`
- [**Writing tags safely**](writing-tags-safely.md) —
  `-Tag=value`, the whole-file rewrite, the
  `_original` backup, `-overwrite_original`,
  and the read-back habit
- [**Copying tags**](copying-tags.md) —
  `-tagsFromFile`, wholesale and selective,
  and restoring a stripped file from its
  sibling
- [**Fixing dates**](fixing-dates.md) —
  the six dates and the three that are the
  photo's own, shifting a wrong clock, setting
  a date the scan never had
- [**Renaming from metadata**](renaming-from-metadata.md) —
  `-filename` from tags, `%` codes,
  `-testname` rehearsals, collisions, and
  filing by directory
- [**Geotagging**](geotagging.md) — a GPX
  track, interpolation between trackpoints,
  and the UTC trap
- [**Privacy and stripping**](privacy-and-stripping.md) —
  what a shared photo publishes, the surgical
  GPS strip, the full `-all=` strip, and the
  backup that keeps the secret
- [**Batches and output**](batches-and-output.md) —
  directories and `-r`, `-ext` and `-if`
  filters, tables and JSON
- [**Config and shortcuts**](config-and-shortcuts.md) —
  the config file, a shortcut of your own,
  and the default home it can live in
- [**Recipes**](recipes.md) — fixing a
  folder's clock, dating scans, filing by
  month, the dateless audit, restoring and
  share-safe passes, and an index of the shelf
- [**The neighbours**](the-neighbours.md) —
  ffprobe and mediainfo reading containers,
  exiv2, ImageMagick, and the managers:
  what each is actually for
- [**Troubleshooting**](troubleshooting.md) —
  undefined tags, unwritable formats, the
  temporary-file refusal, rename confusion,
  and edits that "didn't take"
- [**Glossary**](glossary.md) — the terms,
  collected in one place

## What it doesn't cover

Developing or editing photographs — the
pixels are never this guide's subject.
Library management and cataloguing: ExifTool
has no database and wants none; the
neighbours chapter places the managers.
MakerNotes in depth — the maker's private
record is named and placed, not decoded
tag by tag. Config-file programming beyond
shortcuts: defining new tags is a real
capability, named in chapter 10 and left to
the tool's own documentation. Video and
audio metadata get their honest mention in
the neighbours chapter; the EXIF heartland
of still photographs is where this guide
lives.

## How to use this guide

Read chapter 1, build the shoebox as it
directs, and keep a terminal open from
chapter 2 on. Work in a shoebox you can
rebuild — chapters rename, strip, and
overwrite on purpose, in the lab only, and
the specimens should survive your
experiments so you can start a chapter
over. Keep your real library out of reach
of every command until the read-back habit
is a reflex; then keep it out of reach of
the first run of any new one anyway.

## Fixture rebuild safety

The fixture builder creates a new target directory. It replaces an existing
target only when a completed-build marker sits beside it
(`shoebox.mere-mortals-fixture` for a shoebox named `shoebox`). Existing unmarked directories, symlink targets,
protected home/current/script directories and overlapping builds are refused.
Older fixture trees have no marker: use a fresh directory name and inspect the
old tree manually. Do not add a marker to an unrelated directory. A failed or
interrupted build may leave an unmarked tree or stale `.build-lock` directory;
inspect it before cleaning up.
