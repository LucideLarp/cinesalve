# Getting untrunc working on a Mac

[untrunc](https://github.com/anthwlock/untrunc) genuinely repairs truncated MP4
files and costs nothing. Getting it to run on macOS is where most people give
up. Here is what each error actually means.

### `fatal error: 'libavcodec/avcodec.h' file not found`

The most common one. untrunc is built against FFmpeg's libraries, and this says
the compiler cannot find their **headers**. Having the `ffmpeg` command
installed is not the same thing: the command is a binary, and building against
it needs the development headers and a way to locate them.

On macOS that usually means installing FFmpeg and `pkg-config` through Homebrew
so the build can discover the include paths. On Apple silicon Homebrew installs
under `/opt/homebrew` rather than `/usr/local`, which is why instructions
written for Intel Macs frequently fail here with exactly this error.

### `zsh: permission denied: ./untrunc`

This one is not a build failure at all, which is why it is so confusing. The
binary exists but is not marked executable:

```sh
chmod +x untrunc
./untrunc reference.mp4 broken.mp4
```

If it still refuses, macOS may have quarantined the file because it arrived from
the internet. `xattr -d com.apple.quarantine untrunc` clears that.

### Docker: `No such file or directory` even though the file is there

The Docker route avoids compiling entirely, and this error catches nearly
everyone who tries it. The container cannot see your Mac's filesystem. When you
mount a folder, you must then refer to your files by their path **inside the
container**, not on your Mac:

```sh
# mount the folder that holds BOTH files, then use /mnt paths
docker run --rm -v ~/Downloads:/mnt untrunc /mnt/good.mp4 /mnt/broken.mp4
```

Passing `/Users/you/Downloads/broken.mp4` to the container fails, because inside
the container that path does not exist.

### It builds and runs, but the output is pixelated or short

Usually the reference file is not close enough. It has to come from the same
camera and the same recording mode: change resolution or frame rate and the
encoder configuration changes with it. Camera specific metadata sometimes also
needs the `-s` flag so it skips atoms it does not recognise instead of stopping.

## What untrunc cannot tell you

You get an output file and find out whether the recovery succeeded by opening
it. If it produced nothing useful, you are left without a next step and without
knowing whether the footage was ever recoverable.

## Find out now, without building anything

Before you spend another evening on a compiler, it is worth knowing whether the
footage is there at all. The free checker tells you. It runs in your browser:
nothing is uploaded, only the first and last megabyte are read, and it works on
any computer, Windows included.

**https://cinesalve.honorboxx.workers.dev/check**

Give it the second clip and it counts real frames, using the same engine the app
uses. That number is the answer to the question untrunc leaves you with.

### Cinesalve

The same rebuild, with no compiler and no Docker. Every recovered frame lands at
the byte offset and size the camera originally wrote. GoPro recordings are
recovered from the damaged file alone, with no reference clip needed at all.

Measured against footage from real cameras, every frame compared to the original
by byte position and size:

| Real footage | Frames recovered | Exact |
|---|---|---|
| DJI Mavic 3 Pro, 4K 59.94fps | 722 / 722 | 100% |
| GoPro HERO6, separate take as reference | 425 / 425 | 100% |
| GoPro HERO5 / 7 / 8 / Karma / Fusion / MAX | all | 100% |
| QuickTime, Apple's own muxer | 420 / 420 | 100% |

It has the same structural requirement untrunc has, and for the same reason: a
healthy clip from the same camera, except on GoPro. A damaged file usually no
longer states its own codec settings, so they must be read from a working
recording. No other clip? Record one now, a few seconds is enough.

## Prefer the terminal?

If you got here from fighting a build, there is a Homebrew tap. It handles
upgrades, which the zip does not:

```sh
brew install --cask lucidelarp/cinesalve/cinesalve
xattr -dr com.apple.quarantine /Applications/Cinesalve.app
```

The second line is needed because the app is not signed yet and Homebrew
quarantines what it installs, so macOS kills the first launch. Current Homebrew
has no `--no-quarantine` option, whatever older guides say.

## Stuck either way

Describe the file and the error to cinesalve@proton.me. You will get a straight
answer, including when the answer is that untrunc is the right tool and here is
how to get it running.

---

*From the [Cinesalve](https://cinesalve.honorboxx.workers.dev) field notes.
Full version: https://cinesalve.honorboxx.workers.dev/untrunc-on-mac*
