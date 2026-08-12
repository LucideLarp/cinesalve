# Your GoPro file will not play

The battery died, the housing flooded, or you pulled the card while it was still
writing. Now the clip is on the card and nothing will open it.

Here is what to try, in the order that costs you least, starting with the things
that are free.

**Before anything else: stop recording on that card.** Take it out of the camera
and copy everything off it. Each new clip risks landing on top of the data you
are trying to get back, and no software recovers what has been overwritten.

## 1. Let the camera try. It has a repair function built in.

GoPro cameras carry a recovery feature usually called **SOS**. Put the original
card back into the camera that shot it and power on: if the camera finds a
recording it never finished closing, it offers to repair it.

It costs nothing and takes a minute, so it is always worth trying first. It also
fails often, particularly when the card was removed and put back, or when the
file was copied to a computer before the camera saw it again. If SOS does not
appear or does not fix it, nothing is lost.

## 2. Look for the LRV file. Your footage may already be watchable.

Next to every clip, a GoPro writes two companion files: a `.THM` thumbnail and
an `.LRV`, which is a low resolution proxy of the same recording. The proxy is
written separately from the main clip, so it is frequently intact when the big
file is not.

Find the `.LRV` with the matching number, copy it, and rename the copy to
`.MP4`. If it plays, you have your footage. It is low resolution and it is not
what you shot, but for a moment you thought was gone entirely it is often
enough, and it tells you immediately that the recording really happened.

Note that GoPro chapters long recordings across several files (`GH011234.MP4`,
`GH021234.MP4` and so on). Only the chapter that was being written when power
was lost is usually damaged. Check the others: they may open perfectly.

## 3. Work out whether the footage is still in the file

If the clip is a normal size, say two gigabytes rather than zero, the video is
almost certainly still inside it. What a camera writes **last**, when you press
stop, is the index that tells a player where each frame lives. Lose power before
that and you have a complete recording with no map, which every player refuses.

On a Mac, this tells you which case you are in:

```sh
ls -l GH011234.MP4
strings -t d GH011234.MP4 | grep -m 5 -E 'ftyp|mdat|moov'
```

Seeing `ftyp` and `mdat` but no `moov` is the good outcome: the footage is there
and only the index is missing. ([What that means in detail](moov-atom-not-found.md).)

A file of a few kilobytes means the camera never wrote the footage at all, and
your only remaining route is card level recovery software.

There is also a free checker that reads the file in your browser and tells you
how many frames are recoverable. Nothing is uploaded, only the first and last
megabyte are read, and it works on any computer including Windows:
**https://cinesalve.honorboxx.workers.dev/check**

## 4. Rebuild the index

This is where the free options run out. The index cannot be copied from another
clip, because it holds the exact byte position and size of every single frame in
*your* recording. It has to be reconstructed by reading the raw recording and
working out where each frame begins.

- [untrunc](https://github.com/anthwlock/untrunc) is free, open source and does
  this well if you are comfortable compiling a command line tool and passing it
  a reference file. GoPro's extra metadata sometimes needs its `-s` flag. **Try
  it before paying anyone.** If it will not build on your Mac,
  [these are the errors and what they mean](https://cinesalve.honorboxx.workers.dev/untrunc-on-mac).

**No other clip from that camera? Record one now.** A few seconds at the same
settings is enough. A damaged file usually no longer states its own codec
settings, so they have to be read from a working recording, which is why every
tool that can do this asks for one.

### Cinesalve

A Mac app that does the same job. GoPro recordings carry their own codec
configuration, in the H.264 and the HEVC modes alike, so you give it the clip
that will not open and nothing else.

Every recovered frame lands at the byte offset and size the camera originally
wrote, measured that way against footage straight off real GoPro cameras:

| Camera | Frames recovered | Exact |
|---|---|---|
| HERO6, separate take as the reference | 425 / 425 | 100% |
| HERO5 | 456 / 456 | 100% |
| HERO7 | 170 / 170 | 100% |
| HERO8 | 323 / 323 | 100% |
| Karma | 200 / 200 | 100% |
| Fusion | 160 / 160 | 100% |
| MAX | 175 / 175 | 100% |

Nothing is uploaded and the recovery runs offline. $69 once.

On GoPro footage the sound is not recovered. These cameras write timecode and
telemetry into the same block as the picture, and those bytes cannot be told
apart from audio confidently enough to place it. Rather than write noise into
your recording, Cinesalve returns the video and says so. If the audio matters
more than the picture, that is worth knowing before you spend anything.

## If none of it works

Send the clip, or just describe it, to cinesalve@proton.me with the camera model
and the recording mode. You will get a straight answer about whether it can be
recovered, including when the answer is no. That costs nothing and there is no
pitch attached.

---

*From the [Cinesalve](https://cinesalve.honorboxx.workers.dev) field notes.
Full version: https://cinesalve.honorboxx.workers.dev/gopro-video-wont-play*
