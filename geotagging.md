# Geotagging from a Track

**Scope:** giving a position to a photo
that has none — a GPS track log, the
`-geotag` write, interpolation between
trackpoints, and the UTC trap that waits
inside every timestamp.

## Two halves of one moment

A camera without GPS still records *when*.
A GPS logger — a watch, a phone app, a
handheld unit — records *where*, as a
**track**: a list of **trackpoints**, each
a latitude, a longitude, and a time. If
both were running, the photo's time falls
somewhere along the track, and the
position can be looked up. The lab's
track is a hand-written **GPX** file,
`trail.gpx`, four trackpoints of an
invented ridge walk:

```
<trkpt lat="46.852300" lon="-121.760300">
  <ele>1320.4</ele><time>2024-05-18T11:05:00Z</time>
</trkpt>
<trkpt lat="46.854100" lon="-121.757200">
  <ele>1408.9</ele><time>2024-05-18T11:18:00Z</time>
</trkpt>
<trkpt lat="46.855860" lon="-121.754060">
  <ele>1481.2</ele><time>2024-05-18T11:30:00Z</time>
</trkpt>
<trkpt lat="46.857400" lon="-121.751500">
  <ele>1530.0</ele><time>2024-05-18T11:41:00Z</time>
</trkpt>
```

And the trail photo was "taken" — by the
builder's tags — at 11:23:40 on that date,
with no position at all, as the empty
answer from chapter 2's grammar shows:

```
$ exiftool -GPSPosition shoebox/trail-summit.jpg
[no output — no GPS tags]
```

## The geotag

```
$ exiftool -geotag shoebox/trail.gpx shoebox/trail-summit.jpg
    1 image files updated

$ exiftool -a -G -s -GPS:all shoebox/trail-summit.jpg
[EXIF]          GPSVersionID                    : 2.3.0.0
[EXIF]          GPSLatitudeRef                  : North
[EXIF]          GPSLatitude                     : 46 deg 51' 17.75"
[EXIF]          GPSLongitudeRef                 : West
[EXIF]          GPSLongitude                      : 121 deg 45' 20.58"
[EXIF]          GPSAltitudeRef                  : Above Sea Level
[EXIF]          GPSAltitude                     : 1443.041667 m
[EXIF]          GPSTimeStamp                    : 11:23:40
[EXIF]          GPSDateStamp                    : 2024:05:18

$ exiftool -GPSPosition shoebox/trail-summit.jpg
GPS Position                    : 46 deg 51' 17.75" N, 121 deg 45' 20.58" W
```

The photo acquired a full GPS record it
never had — coordinates, altitude, and the
GPS time and date stamps, stamped from the
photo's own moment. The written position
is **interpolated**: 11:23:40 lies between
the 11:18 and 11:30 trackpoints, about
47% of the way, and the position sits
about 47% of the way between them. The
altitude gives the arithmetic away — the
trackpoints say 1408.9 m and 1481.2 m, and
the file now says 1443.04 m, which is
1408.9 plus 47% of the climb. The machine
did not find this position anywhere; it
computed it, on the assumption that the
walker moved steadily between fixes. On a
steady walk that assumption is good. On a
walk with a long lunch stop between two
trackpoints, interpolation will place the
photo politely in the middle of nowhere
the walker never stood — the answer is as
honest as the track is dense, and a sparse
track is a reason to read results with a
map open.

## The UTC trap

Look again at the GPX times: each ends in
**`Z`**, which declares them UTC. GPS
time, everywhere on earth, is UTC. But the
photo's `DateTimeOriginal` is a bare local
time — EXIF carries no time zone for it.
So whose 11:23:40 did ExifTool just match
against the track?

By default, the geotag treats the photo's
stamp **as if it were UTC** — it takes the
**Geotime** (the tag it matches on, by
default `DateTimeOriginal`) and reads it
in UTC. The lab was built so this is true:
the specimen's stamped time and the
track's UTC time are the same clock. Real
walks are where the trap springs: a camera
set to local time in a zone five hours
behind UTC stamps 11:23 for a moment the
track calls 16:23, the lookup lands five
hours along the track — usually off its
end entirely — and the photo gets either a
wildly wrong position or none.

The defences, in order of preference:
set the camera to UTC on walking days (one
clock, no conversion); or tell ExifTool
the offset when geotagging, with a
`-geotime` expression carrying the zone
(`-geotime<${DateTimeOriginal}-05:00` for
a camera on UTC−5) — that form is from the
tool's documentation and is named, not
captured, in this guide's ledger; or
shift the photo's dates first (chapter 5)
so the diary itself tells one consistent
story. What you cannot do is nothing, at
any distance from your own time zone.

Outside the window deserves its own
capture. The harbour photo is from a
different year than the track; asking is
harmless and instructive:

```
$ exiftool -geotag shoebox/trail.gpx shoebox/harbour-morning.jpg
Warning: Time is too far before track in File:Geotime (ValueConvInv) - shoebox/harbour-morning.jpg
Warning: No writable tags set from shoebox/harbour-morning.jpg
    0 image files updated
    1 image files unchanged
```

No position was written, and the warning
says precisely why: the photo's time is
too far from any trackpoint. ExifTool does
allow a small reach beyond the track's
ends and across gaps (the **Geosync**
tolerance), because loggers start late and
stop early; a different *year* is beyond
anyone's tolerance. **Check:** a geotag
that reports `0 image files updated` is a
geotag that did not happen, whatever the
command's intention — the summary line is,
as ever, the claim, and `-GPSPosition`
afterwards is the audit.
