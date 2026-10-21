# Your dashcam video will not play

There was a collision, the battery was disconnected, or the camera lost power
while it was recording, and now the clip you need will not open. Here is what
to try, in the order that costs you least.

## Before anything else

**Do not drive with that card still recording**, and do not format it, in the
camera or anywhere else.

**Then work on a copy, never the original.** Some repair tools rewrite the file
where it sits, so a failed attempt can leave you worse off than the
interruption did. Keep the untouched original somewhere else and point every
tool at a duplicate.

## 1. Take the card out before the camera records over it

This is the step that matters most on a dashcam. It records in a loop, and
when the card is full it makes room by deleting the oldest recordings.
BlackVue's manual for the DR770X puts it this way: *When the microSD card fills
up, the newest video files will start to replace the oldest video files (called
loop recording)*. VIOFO's manual for the A129 Plus Duo says *loop recording will
automatically overwrite the oldest files (one by one)*. So the clip you need is
on a timer that runs every time the car does, and the first thing to do costs
nothing: turn the camera off and take the card out.

Turn it off first. VIOFO says to *unplug the power cable before inserting or
removing the microSD card*, because *removing the microSD card while recording
may destroy the files*, and BlackVue's DR770X manual has you unplug the power
cord to turn the camera off before opening the card slot. Then copy the whole
card to your Mac before you open anything on it.

**Formatting deletes the protected clips too.** A collision usually locks the
clip it happened in so the loop cannot reach it, and that lock does not survive
a format. BlackVue's manual: *If you format the microSD card, all the video
files stored in the microSD card will be deleted including the locked event
files.* It also warns that changing some settings makes the camera format the
card itself. If the camera or a computer offers to format or repair the card,
say no until you have a copy.

Where to look on the copy: VIOFO cameras keep loop recordings in `DCIM/Movie`
and locked ones in `DCIM/Movie/RO`, with `F` or `R` at the end of the name for
the front or rear camera. A BlackVue marks a collision while driving with `E`
in the file name, one while parked with `I` and a recording you saved by hand
with `M`, and locks up to fifty of them. Other makes have their own folders, so
check the manual for yours, but the clip that matters is usually the last one
written before the camera lost power, the one that will not open.

## 2. Work out whether the footage is still in the file

If the file is a sensible size rather than a few kilobytes, the video is almost
certainly still inside it. A recording needs an index saying where every frame
lives, and a dashcam fills that index in when it closes the file, whether it
puts it at the end of the file or keeps room for it at the front. Lose the
power before the file is closed and you have the footage with no map, which
every player refuses to touch.

On a Mac, this tells you which case you are in:

```
ls -l video.mp4
strings -t d video.mp4 | grep -m 5 -E 'ftyp|mdat|moov'
```

`ftyp` and `mdat` present but no `moov` is the good outcome: the footage is
there and only the index is missing. [What that means in
detail](moov-atom-not-found.md). A dashcam that keeps its index at the front,
as the BlackVue measured here does, can show the name `moov` and still hold no
usable index, so let the checker below have the last word. A file of a few
kilobytes means the footage was never written, and device level recovery
software is the only remaining route.

## 3. Check whether it is recoverable, free

The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads the first and last megabyte of the file for its verdict, then up to
100.7 MB of the footage to tell you how many frames are recoverable, and a
reference clip in full if you add one. Nothing is uploaded, and it runs on any
computer including Windows.
It counts frames and never assembles a playable file. A dashcam recording needs
a clip from the same dashcam that plays, at the same settings, dropped in
beside it.

## 4. Rebuild the index

The index cannot be copied from another clip. It holds the exact byte position
and size of every frame in *your* recording, so it has to be reconstructed by
reading the raw footage and working out where each frame begins, using a
healthy clip from the same dashcam to learn how it writes.

[untrunc](https://github.com/anthwlock/untrunc) is free and open source and does
exactly this, if you are willing to compile a command line tool and hand it a
reference file. Try it before paying anybody. If it will not build on your Mac,
[these are the errors and what they mean](untrunc-on-mac.md).

**The reference clip is the easy part.** The card is full of recordings from
the same camera at the same settings, so any of them that plays will do: the
one just before the damaged clip is the natural choice. Neither dashcam
measured here states in the recording itself how it was encoded, so both the
free checker and the app ask for one and refuse to guess without it.

## Cinesalve

A Mac app that does the same job. Drop in the file that will not open plus any
clip from the same dashcam that does, and it rebuilds the index so the file
plays again. Nothing is uploaded; it runs entirely on your Mac. **$69 once.**

It is measured against two real dashcams, a BlackVue DR770X and a dashcam on a
Novatek chip, camera originals cut mid-take, with every recovered frame
compared against the original by byte position and size and another recording
from the same camera as the reference. The counts are in the table on
[the home page](https://cinesalve.honorboxx.workers.dev/).

The sound comes back with the picture on both. A BlackVue writes a short header
ahead of every sample naming its track and its size, which Cinesalve reads, so
each sample is placed where the camera wrote it. The Novatek camera pads every
sample to a four byte boundary and writes GPS records among the pictures;
Cinesalve steps over both and reads the sound frame by frame with the system's
own decoder as the judge of where each frame ends.

## Send it over

A damaged dashcam clip is genuinely useful: it goes into the test suite and the
fix ships for everyone, and every make of dashcam is worth measuring. Send the
clip, or just describe it, to [cinesalve@proton.me](mailto:cinesalve@proton.me)
with the make and model and what happened when it stopped. You will get a
straight answer about whether it can be recovered, including when the answer is
no.
