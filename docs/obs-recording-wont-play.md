# OBS crashed and the recording will not play

Hours of stream, interview or gameplay, and the MP4 will not open in anything.

**The recording is almost certainly still there.** Here is what to try, in
order, and the one setting that stops this ever happening again.

## Why an MP4 dies when OBS does

An MP4 keeps its picture and sound in one large block, and the index describing
where every frame sits in a much smaller structure **written at the end**, when
recording stops cleanly. OBS cannot write that index if it is killed first, so
you are left with a complete recording and no map. Every player refuses it,
which looks identical to the file being destroyed. It is not.

This is also why the file is a sensible size. If it is two gigabytes, there are
two gigabytes of video in it.

## 1. Try OBS's own Remux Recordings first

OBS ships a repair path: **File → Remux Recordings**. Point it at the broken
file and let it write a new one. It costs nothing and is the first thing the OBS
forums tell people to do, so try it before anything else.

If you were recording to `.mkv` or `.ts`, this will very likely just work: those
formats do not depend on a final index, which is exactly why they survive a
crash. If you were recording straight to `.mp4` and OBS died, remuxing usually
cannot help, because the information it would need was never written.

## 2. Check that the video is still in the file

On a Mac:

```sh
ls -l "recording.mp4"
strings -t d "recording.mp4" | grep -m 5 -E 'ftyp|mdat|moov'
```

`ftyp` and `mdat` present with no `moov` is the good outcome: the recording is
intact and only the index is missing.
([The detail of what that means](moov-atom-not-found.md).) A file of a few
kilobytes means nothing was ever written to it.

There is also a free checker that reads the file in your browser and counts how
many frames are recoverable. Nothing is uploaded, and it works on any computer
including Windows: **https://cinesalve.honorboxx.workers.dev/check**

## 3. Rebuild the index

The index has to be reconstructed, not copied, because it holds the exact byte
position and size of every frame in *your* recording. Doing that needs a healthy
file made by the same encoder at the same settings, to know how the frames were
written.

**You are in the easiest possible position to supply one.** Unlike somebody
whose camera is at the bottom of a lake, you still have OBS. Open it, keep every
setting exactly as it was, record five seconds of anything, and stop cleanly.
That new file is a perfect reference.

- [untrunc](https://github.com/anthwlock/untrunc) is free and open source and
  does this, if you are happy compiling a command line tool. **Try it before
  paying anyone.** If it will not build on your Mac,
  [these are the errors and what they mean](https://cinesalve.honorboxx.workers.dev/untrunc-on-mac).

### Cinesalve

A Mac app that does the same job. Drop in the broken recording and the short
clip you just made, and it rebuilds the index so the file plays again. Nothing
is uploaded and the recovery runs offline. $69 once, every Mac you own.

OBS writes its MP4s through ffmpeg, which is precisely what this is tested
hardest against. Every frame is compared against the original by byte position
and size:

| Recording cut off at | Frames recovered | Exact |
|---|---|---|
| 30% through | 90 / 90 | 100% |
| 50% through | 149 / 149 | 100% |
| 68% through | 203 / 203 | 100% |
| 90% through | 269 / 269 | 100% |
| 1080p 24fps, 55% through | 107 / 107 | 100% |
| 480p 60fps, 45% through | 217 / 217 | 100% |

Sound comes back with the picture on recordings of this kind, though about one
audio frame in ten can land imperfectly and leave a click. The checker above
counts what survived before you decide anything.

## Make it impossible next time

This is worth two minutes even if you never lose a recording again.

**Record to MKV, not MP4.** In OBS, Settings → Output → Recording, set the
format to `mkv`. MKV does not need a closing index, so a crash costs you nothing
but the last second.

Then turn on **Automatically remux to mp4** in the same settings, and OBS
converts each finished recording for you. You get MP4 files for editing and
crash immunity at the same time, which is the setup the OBS community recommends
and the reason most long time streamers never see this problem.

## If it still will not open

Send it, or just describe it, to cinesalve@proton.me with the OBS version and
the recording settings. You will get a straight answer about whether it can be
recovered, including when the answer is no.

---

*From the [Cinesalve](https://cinesalve.honorboxx.workers.dev) field notes.
Full version: https://cinesalve.honorboxx.workers.dev/obs-recording-wont-play*
