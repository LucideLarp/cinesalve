# Repairing an MP4 without a reference file

Every tool for repairing a truncated MP4 asks for a healthy clip from the same
camera, and the advice everywhere is that you cannot proceed without one.

**For most recordings that is not true.**

## Why a reference is asked for

Rebuilding a lost index means finding the frames again *and* writing a header
that says how to decode them. That header needs the codec configuration: picture
size, profile and level, and the parameter sets a decoder initialises from.

In an MP4 those live in a box called `avcC`, inside the index that was never
written. Hence the reasoning: the configuration is gone, so copy it from a
recording made the same way.

## What that misses

H.264 encoders also write the parameter sets **into the video stream**, beside the
pictures. They are there so a decoder joining a broadcast partway through can
start without any container at all, and cameras write them because that is what
their encoders do.

So in many recordings the configuration is sitting inside the damaged file the
whole time. A sequence parameter set gives the picture size directly, and its
optional timing section gives the frame rate — everything the missing header
needed.

Measured on nine GoPro recordings (HERO5, HERO6, HERO7, HERO8, Karma, Fusion,
MAX, HERO6+BLE): the parameter sets were present in **every one**, and a header
rebuilt from them came out byte-identical to the one the camera wrote.

## Finding one is not the same as trusting one

A parameter set is a short run of bytes with nothing announcing it, and a large
recording contains millions of positions where telemetry or picture data happens
to look like one. A search that accepts the first plausible match will confidently
report a configuration for files that carry none.

Three things must agree:

1. the unit header must be one a parameter set is permitted to use
2. the syntax must decode cleanly and end exactly on its stop bit
3. **the length written in front of it must equal the length decoding implied**

The third carries most of the weight, and it must be a four-byte length. A
one-byte length agreeing is a 1-in-256 coincidence, which a search across tens of
millions of positions manufactures thousands of times over — in testing it
reported a 656×16 picture in a QuickTime file carrying no parameter sets at all.
Four bytes agreeing is roughly 1 in 4 billion.

## Which files genuinely need a reference

Some muxers strip the parameter sets out of the stream because the container is
supposed to hold them. Files from Apple's QuickTime muxer and some drones are
like this, and no search will find what was never written. For those a reference
clip is genuinely required, and any tool claiming otherwise is guessing at your
footage.

Any clip from the same camera in the same mode will do. A few seconds recorded
right now is enough.

## Checking your own file

The free browser checker reports whether yours carries its configuration and how
many frames are recoverable — nothing uploaded, works on any computer:
**https://cinesalve.honorboxx.workers.dev/check**

---

*From the [Cinesalve](https://cinesalve.honorboxx.workers.dev) field notes.
Full version: https://cinesalve.honorboxx.workers.dev/repair-mp4-without-reference-file*
