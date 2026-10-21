# Config Files and Shortcuts

**Scope:** the one customisation most
readers will ever want — teaching ExifTool
a word of your own, a **shortcut** that
asks for your usual set of tags at once —
via the config file, named on the command
line or living in its default home.

## Your usual questions, given a name

By chapter 9 a pattern forms: the same
three tags get asked for in every session
— when, where, and with what. A shortcut
names that set once. It is defined in a
config file, which is a small Perl file —
it returns to this guide's refrain that
ExifTool is a Perl program, and its
configuration is written in its own
language:

```
%Image::ExifTool::UserDefined::Shortcuts = (
    WhenWhere => ['DateTimeOriginal', 'GPSPosition', 'Model'],
);
1;  # a config file must end with a true value
```

The shape is a hash: your word on the
left, the list of tag names on the right.
The trailing `1;` is not decoration — a
Perl file loaded as configuration must
end by evaluating to true, and forgetting
it is the config file's one classic
failure (the troubleshooting chapter has
the shape of the complaint). Point
ExifTool at the file once, with `-config`,
and the word works like any tag name:

```
$ exiftool -config exiftool.config -WhenWhere shoebox/birthday-cake.jpg
Date/Time Original              : 2024:02:09 19:05:41
GPS Position                    : 47 deg 36' 22.32", 122 deg 19' 55.56"
Camera Model Name               : iPhone 12
```

Three answers, one word. And the honest
behaviour from chapter 2 survives: ask a
file that has no position, and the answer
simply has a line missing —

```
$ HOME=$PWD/fakehome exiftool -WhenWhere shoebox/harbour-morning.jpg
Date/Time Original              : 2023:06:14 07:42:18
Camera Model Name               : Canon EOS 80D
```

— which second command also demonstrates:
that run used **no `-config` at all**. The
same file had been copied to the config's
default home, `~/.ExifTool_config` (the
capture borrows a scratch home directory
so the build machine's real one stays
untouched), and ExifTool read it on
startup, unprompted. On Windows the same
file is sought as `.ExifTool_config` in the
home directory and beside the executable;
that detail is from the documentation —
the ledger in `criteria.md` keeps it.

That default is the double edge. A config
in the home directory is loaded by
**every** ExifTool run — yours, your
scripts', every future chapter's commands
if you follow this guide there. A shortcut
is a convenience; a home config is a
commitment. Name shortcuts so they cannot
be mistaken for real tags (an initial
capital practice of your own, like
`WhenWhere`, not a name the next standard
might claim), and keep the file short
enough to remember in full.

## What else a config can do

The config file's full powers are larger
than this guide: it can define entirely
**new tags** of your own (a household
cataloguing field, say, written into XMP
where other tools can read it), new
**Composite** tags computed from existing
ones, and new file-format support. Those
are real capabilities with real uses, and
they are also where "a small Perl file"
stops being a figure of speech — they are
programming, with a photo library as the
blast radius. This guide's position is the
series' usual one: the shortcut is the
80% case, it is captured above in full,
and the rest is named here so you know it
exists and documented-only in the ledger,
not walked through half-verified.

**Check:** a working config proves itself
the same way everything in this guide is
proven — run the shortcut against the
birthday photo and count three answers,
then against the harbour photo and watch
the missing position behave exactly as a
missing tag should.
