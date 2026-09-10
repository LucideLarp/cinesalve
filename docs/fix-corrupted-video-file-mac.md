# How to fix a corrupted video file on a Mac

"Corrupted" covers several completely different failures, and the fix for one
does nothing for another. Working out which one you have takes about a minute
and costs nothing, and it decides everything you do next.

## Before anything else

**Stop writing to the card or drive the file came from.** If the file is
damaged, the data behind it may still be intact, and every new file you save
risks landing on top of it. Copy the whole card to a drive first and work from
the copy.

## Find out which failure you have

The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads the structure of the file in your browser and tells you which of these you
are looking at. Nothing is uploaded, it works on any computer, and it takes
seconds.

## The three things "corrupted" usually means

### 1. The recording was never finished

Much the most common, and the most recoverable. A camera writes video to the
card as it records, but the index that says where every frame lives is written
*last*, when you press stop. A flat battery, a pulled card, a crash or a full
card means the footage is all there and the index never got written.

The file is the right size and nothing opens it. Players say `moov atom not
found`, or `Invalid data found when processing input`, or fail without saying
anything at all.

**This one is fixable.** The frames can be found again and a new index written.
See [what that error actually means](moov-atom-not-found.md), or, if you know it
was a flat battery or a pulled card, [what a power cut leaves
behind](video-wont-play-after-power-loss.md).

### 2. The file is there but the data is not

A file of a few kilobytes, or one that reports a length of zero. The camera
created the file and never wrote the recording into it, or the card failed
before anything reached it.

Nothing that works on the file itself can help, because there is nothing inside
it to rebuild. The remaining route is card-level recovery software that scans
the raw card for orphaned data, ignoring the filesystem. Stop using that card
immediately if you go that way.

### 3. The file is complete but your player will not decode it

The file has its index and plays elsewhere, or plays sound with no picture, or
opens in one application and not another. That is a codec problem rather than
damage, and rebuilding anything would not help.

Try [VLC](https://www.videolan.org/vlc/) first: it opens a great deal that
QuickTime refuses, and if it plays there the file is fine and the problem is the
player.

## Checking it yourself in the terminal

If you would rather look directly, the first bytes of the file name its top
level boxes:

```
xxd -l 64 broken.mp4
```

A healthy MP4 or MOV contains `ftyp`, `mdat` and `moov`. Seeing `ftyp` and
`mdat` with no `moov` anywhere in the file is failure 1 exactly. With ffmpeg
installed:

```
ffprobe -v error -show_entries format=duration -of csv broken.mp4
```

A file whose duration cannot be read, but whose size is what you expect for the
length you shot, is the same story.

## What actually rebuilds it

Cinesalve is a Mac app for failure 1. It finds the frames in the media and
writes the index the camera never got to, so the recording plays again. On the
GoPro models measured, H.264 and HEVC alike, it needs nothing but the damaged
file; recordings that do not state their own settings ask for any clip from the
same camera that plays.

Recovery is measured against the original, frame by frame: a frame counts only
when it lands at the same byte offset and the same size the camera wrote.

| Real camera footage | Frames recovered | Exact |
|---|---|---|
| GoPro HERO5 / 6 / 7 / 8, Karma, Fusion, MAX | all | 100% |
| DJI Mavic 3 Pro, 4K | 722 / 722 | 100% |
| iPhone 11, HEVC | 484 / 484 | 100% |
| Canon EOS 5D Mark II | 208 / 208 | 100% |
| QuickTime, Apple's own muxer | 420 / 420 | 100% |

Steps written for a particular camera, where the free things to try first
differ: [GoPro](gopro-video-wont-play.md), [DJI](dji-video-wont-play.md),
[Canon](canon-video-wont-play.md), [iPhone](iphone-video-wont-play.md),
[Sony](sony-video-wont-play.md), [Pixel](pixel-video-wont-play.md),
[OBS](obs-recording-wont-play.md).

## Still stuck

Describe the camera and what happened to
[cinesalve@proton.me](mailto:cinesalve@proton.me) and you will get a straight
answer, including when the answer is that the footage is gone.
