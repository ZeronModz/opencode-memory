# Project: tplay (Terminal ASCII Media Player) on Termux

## What
tplay v0.9.3 — Rust ASCII media player (maxcurzi/tplay, MIT). Converts images/gifs/videos/YouTube/webcam/streams to ASCII art in terminal, with sound (mpv backend).

## Install Status (2026-08-08)
- INSTALLED & WORKING on Termux (Android, aarch64, Termux+glibc mix repo).
- Binary: `/data/data/com.termux/files/home/.cargo/bin/tplay`
- PATH added to `~/.bashrc` + `~/.zshrc`.

## Prereqs Installed
- rust 1.97.1 (`apt install rust`)
- mpv 0.41.0-2 (gives libmpv.so + include/mpv headers)
- python-yt-dlp
- ffmpeg (already had, with dev .pc + headers libavcodec/avformat/avfilter/avdevice/swscale)
- pkg-config, clang (already present)

## Build Command
```
CARGO_BUILD_JOBS=4 cargo install tplay --locked
```
Compile time ~18-19 min on phone. Output: ~/.cargo/bin/tplay.

## Usage
```
tplay image.png
tplay video.mp4
tplay https://www.youtube.com/watch?v=xxx   # needs yt-dlp
tplay /dev/video0                            # webcam
tplay srt://... rtsp://... udp://... rtmp://...
```

### Flags
- `-f <fps>` force fps; `-c "<chars>"` custom charmap; `-g` grayscale; `-w 2` width-mod for emoji charmaps; `-a` frame skip (slow phones); `-n` newlines; `-l` loop; `-x` auto-exit when done; `-s` stretch full terminal; `-smooth` CatmullRom downscale; `-b <browser>` YT cookies (firefox default).

### Playback Keys
- `0-9` switch charmap; `space` pause; `g` gray/color; `m` mute
- `←/→` ±5s, `j/l` ±10s; `[/]` speed ±0.25x, `,/.` ±0.1x; `\` reset 1.0x
- `c` subtitle cycle; `Shift+C` subs on/off; `q` quit

## Performance Tips
- Larger font, smaller terminal = faster. `-a` frame skip if laggy.
- Real rendering `[?1049h` alt-screen escape — needs a real interactive terminal (Termux session), not non-tty.

## Reinstall / Rebuild
```
cargo install tplay --force
```

## Troubleshooting
- If `tplay` not found → run `source ~/.bashrc` or log out/in.
- If YouTube error → `yt-dlp` must be installed (python-yt-dlp ok, verify `which ytl-dlp`).