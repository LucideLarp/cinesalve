# My test suite was lying to me

Eight test cases, every recovered frame byte-exact against the original, green
for hours. Then I pointed it at footage from an actual camera and it recovered a
quarter of the file.

## The thing being tested

When a camera loses power mid-recording, the video is all on the card but the
index describing where each frame lives is never written. Rebuilding that index
means walking a few gigabytes of raw bytes with nothing marking the boundaries
and working out where each frame starts.

It is a satisfying problem because it is checkable. You take a known good file,
truncate it, rebuild the index, and compare the result against the original's
own tables. A frame only counts if it lands at the *same byte offset and the
same size* the camera wrote. There is no partial credit and no judgement call.

By that measure the engine was perfect:

```
case                                  video exact      audio exact  decode errs
h264 1280x720 30fps  cut 30%         90/90 100.0%    129/131 98.5%            5
h264 1280x720 30fps  cut 50%       149/149 100.0%    212/214 99.1%            5
h264 1280x720 30fps  cut 68%       203/203 100.0%    291/293 99.3%            5
h264 1280x720 30fps  cut 90%       269/269 100.0%    386/388 99.5%            5
h264 1920x1080 24fps cut 55%       107/107 100.0%    190/192 99.0%            5
h264 640x480 60fps   cut 45%       217/217 100.0%   157/157 100.0%            0
h264 no audio        cut 60%       144/144 100.0%              n/a            0
hevc 1280x720 30fps  cut 60%       146/146 100.0%    205/208 98.6%            7
```

Different codecs, resolutions, frame rates and cut points. A frame decoded from
a repaired file came out byte-identical to the same frame in the original. I was
fairly pleased with myself.

## Then a real file

GoPro publishes genuine camera recordings in their
[gpmf-parser](https://github.com/gopro/gpmf-parser) repository. Two minutes to
download. The result:

```
Recovered 107 video frames (3.57s)      <- 425 frames were recoverable
[h264] non-existing PPS 1 referenced
[h264] no frame!
```

Every claim resting on that green matrix was, for this entire class of file,
worthless.

## Why the fixtures lied

I had generated every fixture with `ffmpeg`, which writes the tidy case: one
video track, one audio track, clean interleave. A real camera writes **five**
tracks. Video, audio, timecode, telemetry, and a camera-specific one, all
interleaved into the same block of bytes.

None of that existed in anything I had made, so none of it was tested. Two
defects followed directly, and neither was visible without a real file.

### The first frame was found 334 bytes too early

Real video started at offset 740. My scanner confidently reported a frame at
406. The bytes in between are telemetry, and a chance pattern inside them parsed
as a valid chain of length-prefixed units. Everything after inherited the
misalignment.

### Frame starts kept landing a few bytes early

At offset 80970 sat something reading as NAL **type 14**, a prefix unit, length
32. The real frame began at 81000 with NAL **type 9**, an access unit delimiter.
The spurious unit chained directly into the real frame, so it looked entirely
valid on inspection.

## The fix, which is the interesting part

The format permits several kinds of unit to open a frame. I was accepting all of
them, because that is what the specification allows. But *a given camera is
consistent*: GoPro opens every single frame with an access unit delimiter,
ffmpeg opens with an SEI or the slice itself.

So instead of allowing everything legal, read the first unit type of 128 frames
in the reference file and accept only what that camera habitually uses. For
GoPro the answer is the single value `[9]`. That plus one more rule, that a
recording always opens on a keyframe so nothing before the first one counts,
took it from 107 frames to all 425, exactly, with zero decoder errors.

> **Learn the producer's habits from a sample rather than allowing everything
> the spec permits.** The same move solved two other problems in the same
> codebase: which byte prefix an encoder opens its audio frames with, and which
> terminator it closes them with. Three unrelated-looking problems, one
> technique.

## Two more lessons, both cheap and both painful

### Compare by identity, never by position

Audio recovery on an Apple-muxed QuickTime file scored **0%** and I was about to
report it as broken. It was actually 92% correct. My comparison walked the two
lists by index, and the repair had merged two 11-byte priming frames at the
start, so every later frame was two positions out of step while sitting at
exactly the right byte offset.

An off-by-two at the head makes a working feature look totally broken, and the
failure is silent and confident. Compare recovered artifacts by *what they are*,
offset and size, not by where they appear in a list.

### When a byte level count looks implausible, suspect the walk

A later measurement returned NAL type counts of one and three, with impossible
type numbers. The conclusion would have been interesting. The cause was that I
was walking the media block from its start, which also contains audio and
telemetry. Walking only the ranges the file's own index declares gave clean
numbers immediately.

## Where it ended up

```
case                      video recovered    exact       audio  decode
HERO6  paired take                425/425     100%  98% placed  30
HERO6  paired, 85%                603/603     100%  98% placed  43
HERO5  self                       456/456     100%  98% placed  42
HERO7  self                       210/210     100%  99% placed  5
HERO8  self                       210/210     100%  99% placed  7
Karma  self                       200/200     100%  99% placed  9
Fusion self                       248/248     100%  100% placed  3
MAX    self                       175/175     100%  100% placed  5
QuickTime paired                  420/420     100%  92% placed  130
```

Every GoPro row there used to say *omitted*, and this note used to explain why.
Telemetry bytes could not be told apart from AAC frames confidently enough to
place them, so the audio search returned plausible looking nonsense: frames
recovered, none of them at a position the camera had written. A coverage gate
dropped the track rather than write noise into someone's footage, and that part
still stands. A silent recovery is worth more than a loud wrong one.

The *conclusion* was another measurement artifact, and it is the one that took
longest to spot, because it was phrased as a property of the format rather than
of my method. Telemetry is not indistinguishable from sound. It brackets every
audio frame at a fixed stride, so the region between two pictures reads *filler,
audio, filler*, and the boundaries follow from arithmetic instead of a search.
Measured from the camera's own sample offsets rather than its track
declarations, which on these files do not even claim the filler, that lead-in is
16 bytes on a GoPro and absent everywhere else. Stepping over it places the
sound the search had spent months guessing at.

Same lesson one level up: I had measured my search, and written down the result
as a fact about the file.

## The rule I actually took away

> **Fixtures you generate test the generator, not the domain.** My suite was
> thorough, byte-exact, and measuring the wrong universe. Thoroughness inside
> the wrong universe reads exactly like rigour.
>
> Ask of any suite: *did I make these inputs, or did the world?* The real
> artifacts were one search away the whole time.

---

This is from building Cinesalve, a Mac app that rebuilds the index of a
truncated MP4 or MOV so it plays again. The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
counts your surviving frames free; the app is what rebuilds the file. If you
have a damaged clip from a camera not in that table, I would genuinely like it:
[cinesalve@proton.me](mailto:cinesalve@proton.me).
