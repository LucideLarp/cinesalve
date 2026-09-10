# Your Pixel video will not play

The phone went flat, the camera app quit, or it overheated and stopped while it
was still recording. Now there is a take that will not open, or a take that is
not in the gallery at all. Here is what to try, in the order that costs you
least.

## Before anything else

**Do not record anything else on that phone yet**, and do not delete anything
to "clear space".

**Then work on a copy, never the original.** Some repair tools rewrite the file
where it sits, so a failed attempt can leave you worse off than the interruption
did. Keep the untouched original somewhere else and point every tool at a
duplicate.

## 1. If the take is nowhere in the gallery, look for a file starting with `.pending-`

This step has no equivalent on any other camera. When an app records through
Android's media store, the file is marked *pending* until the app finishes it,
and Android's own reference for that flag is plain about what pending means:
*While this flag is set, only the owner of the item can open the underlying
file; requests from other apps will be rejected.* On disk the file sits in the
same folder as the finished recordings, under a name that begins with
`.pending-`, then a number, then the original name. The leading dot hides it,
and the media store leaves it out of what it shows other apps, so a take the
phone never finished can be on the phone and appear nowhere.

**The number in the name is a deadline.** It is the moment, in seconds since
1970, when Android will delete the file. The reference says the default pending
expiration is typically 7 days, and that expired items are automatically
deleted once their expiration time has passed, typically during the next device
idle period. So a recording the phone never finished lives about a week from
when it began, and then it is gone. On a Mac, `date -r` followed by the number
prints the date.

The gallery does not show it and the camera app offers no way to it: Android
rejects another app's request to open a pending file, and hands the real folder
listing only to an app granted all files access, the permission Android's
settings describe as *Allow access to manage all files* and grant per app. A
file manager with that permission sees pending files. Set it to show hidden files, copy the file to
your Mac, and drop the `.pending-` and the number from the copy's name. Then
carry on below with that file. Not every recording app goes through the media
store this way, so this is a place to look rather than a promise, and it costs
a minute to find out.

## 2. Work out whether the footage is still in the file

If the file is a sensible size rather than a few kilobytes, the video is almost
certainly still inside it. What a phone writes *last*, when a recording stops
cleanly, is the index saying where every frame lives. A Pixel does exactly
that: the recording measured here is a camera original from a Pixel 7 Pro that
names the phone inside its index (`com.android.manufacturer` Google,
`com.android.model` Pixel 7 Pro), and the index is the last thing in the file.
Stop before it is written and you have a complete recording with no map, which
every player refuses to touch.

On a Mac, this tells you which case you are in:

```
ls -l video.mp4
strings -t d video.mp4 | grep -m 5 -E 'ftyp|mdat|moov'
```

`ftyp` and `mdat` present but no `moov` is the good outcome: the footage is
there and only the index is missing. [What that means in
detail](moov-atom-not-found.md). A file of a few kilobytes means the footage
was never written, and device level recovery software is the only remaining
route.

## 3. Check whether it is recoverable, free

The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads the first and last megabyte of the file and tells you how many frames are
recoverable. Nothing is uploaded, and it runs on any computer including Windows.
It counts frames and never assembles a playable file. A Pixel recording needs a
clip from the same phone that plays, at the same settings, dropped in beside it.

## 4. Rebuild the index

The index cannot be copied from another clip. It holds the exact byte position
and size of every frame in *your* recording, so it has to be reconstructed by
reading the raw footage and working out where each frame begins, using a
healthy clip from the same phone to learn how it writes.

[untrunc](https://github.com/anthwlock/untrunc) is free and open source and does
exactly this, if you are willing to compile a command line tool and hand it a
reference file. Try it before paying anybody. If it will not build on your Mac,
[these are the errors and what they mean](untrunc-on-mac.md).

**No other clip from that phone?** Record one now, in the same mode and the
same resolution. A few seconds is enough. A Pixel recording carries no codec
settings in the stream itself and no index part way through the file, so
nothing in the damaged take states how it was encoded; both the free checker
and the app ask for a second clip from the same phone at the same settings, and
refuse to guess without one.

## Cinesalve

A Mac app that does the same job. Drop in the file that will not open plus any
clip from the same phone that does, and it rebuilds the index so the file plays
again. Nothing is uploaded; it runs entirely on your Mac. **$69 once.**

It is measured against a real Google Pixel 7 Pro recording, 1080p, camera
original and cut mid-take at two points, with every recovered frame compared
against the original by byte position and size. The counts are in the table on
[the home page](https://cinesalve.honorboxx.workers.dev/), on the rows marked
with the phone's own full recording as the reference, because it is the one
camera original Pixel clip found that names its phone in its own index.

The sound comes back with the picture. A Pixel writes its sound around a
metadata record for each picture, and Cinesalve reads that sound frame by frame
with the system's own decoder as the judge of where each frame ends, stepping
over the records, each of which names its own length. A Pixel also records at a
variable frame rate and states each picture's duration in the record ahead of
it, which Cinesalve reads, so the recovered take plays at the phone's own speed.

## Send it over

A damaged Pixel clip is genuinely useful: it goes into the test suite and the
fix ships for everyone. One from any other Android phone is just as wanted,
since the Pixel 7 Pro is the one Android phone that has been through the suite.
Send the clip, or just describe it, to
[cinesalve@proton.me](mailto:cinesalve@proton.me) with the phone and the
recording mode. You will get a straight answer about whether it can be
recovered, including when the answer is no.
