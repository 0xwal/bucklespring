Nostalgia bucklespring keyboard sound
====================================

Copyright 2016 Ico Doornekamp

Fork with additional features: custom sound pack support (MechVibes-compatible), press-only mode, OGG segment extraction, and sound pack loading.

This project emulates the sound of my old faithful IBM Model-M space saver
bucklespring keyboard while typing on my notebook, mainly for the purpose of
annoying the hell out of my coworkers.

![Model M](img/model-m.jpg)
![Buckle](img/buckle.gif)

Bucklespring runs as a background process and plays back the sound of each key
pressed and released on your keyboard, just as if you were using an IBM
Model-M. The sound of each key has carefully been sampled, and is played back
while simulating the proper distance and direction for a realistic 3D sound
palette of pure nostalgic bliss.

To temporarily silence bucklespring, for example to enter secrets, press
ScrollLock twice (but be aware that those ScrollLock events _are_ delivered to
the application); same to unmute. The keycode for muting can be changed with
the `-m` option. Use keycode 0 to disable the mute function.

Installation
------------

[![Packaging status](https://repology.org/badge/tiny-repos/bucklespring.svg)](https://repology.org/project/bucklespring/versions)

### Debian

Bucklespring is available in the latest Debian and Ubuntu dev-releases, so you can
install with

```
$ sudo apt-get install bucklespring
```

### VoidLinux

Bucklespring is available in the VoidLinux repositories, so you can install with

```
$ sudo xbps-install -S bucklespring
```

### FreeBSD

Bucklespring can be installed via package:

```
$ pkg install bucklespring
```

or built via port:

```
$ cd /usr/ports/games/bucklespring
$ make install clean
```

### Linux, building from source

To compile on debian-based linux distributions, first make sure the require
libraries and header files are installed, then simply run `make`:

#### Dependencies on Debian
```
$ sudo apt-get install libopenal-dev libalure-dev libxtst-dev pkg-config
```

#### Dependencies on Arch Linux
```
$ sudo pacman -S openal alure libxtst
```

#### Dependencies on Fedora Linux
```
$ sudo dnf install gcc openal-soft-devel alure-devel libX11-devel libXtst-devel
```

#### Building
```
$ make
$ ./buckle
```

The default Linux build requires X11 for grabbing events. If you want to use
Bucklespring on the linux console or Wayland display server, you can configure
buckle to read events from the raw input devices in /dev/input. This will
require special permissions for buckle to open the devices, though. Build with

```
$ make libinput=1
```

#### Using snap on Ubuntu (since 16.04) and other distros

```
$ sudo snap install bucklespring
$ bucklespring.buckle
```

The snap includes the OpenAL configuration tweaks mentioned in this README.
See http://snapcraft.io/ for more info about Snap packages


### MacOS

I've heard rumours that bucklespring also runs on MacOS. I've been told that
the following should do:

```
$ brew install alure pkg-config
$ git clone https://github.com/zevv/bucklespring.git && cd bucklespring
$ sed -i '' 's/-Wall -Werror/-Wall/' Makefile
$ make
$ ./buckle
```

Note that you need superuser privileges to create the event tap on Mac OS X.
Also give your terminal Accessibility rights: system preferences -> security -> privacy -> accessibility

If you want to use buckle while doing normal work, add an & behind the command.
```
$ sudo ./buckle &
```

### Windows

[The program has been compiled](https://github.com/Matin6725/bucklespring-Windows/releases/tag/bucklespring-Windows), but it has not yet received Microsoft's security certificate. Therefore, it may be detected as a virus by some antivirus software. To view reports from some antivirus programs, you can visit [link to reports](https://www.virustotal.com/gui/file/fe4a813c39793515d726311da50b9ac5e64e6d87ab21c8a16b8980b756a4e07b?nocache=1).

For better performance and to resolve some issues, it is recommended to run the program in **Administrator** mode.


Usage
-----

````
usage: ./buckle [options]

options:

  -d, --device=DEVICE       use OpenAL audio device DEVICE
  -f, --fallback-sound      use a fallback sound for unknown keys
  -g, --gain=GAIN           set playback gain [0..100]
  -m, --mute-keycode=CODE   use CODE as mute key (default 0x46 for scroll lock)
  -M, --mute                start the program muted
  -c, --no-click            don't play a sound on mouse click
  -p, --pack=NAME           load sound pack NAME from wav directory
  -u, --press-only          don't play sound on key release (press-only mode)
  -h, --help                show help
  -l, --list-devices        list available OpenAL audio devices
  -s, --stereo-width=WIDTH  set stereo width [0..100]
  -v, --verbose             increase verbosity / debugging
  -w, --wav-dir=DIR         set base wav directory (default ./wav)
````

## Custom Sound Packs and MechVibes Compatibility

This fork extends bucklespring with comprehensive **MechVibes-compatible sound pack support**. You can now use sound packs designed for MechVibes by placing a `config.json` file in your audio directory.

### Features Added in This Fork

- **MechVibes config.json support** - Load packs with JSON configuration
- **Two audio modes** - Multi-file (individual WAVs) and Single-file (OGG with segments)
- **OGG segment extraction** - Extract key sounds from a single OGG file using `[start_ms, duration_ms]` timing
- **Sound pack loading** - Use `-p/--pack` to load packs from subdirectories
- **Random fallback segments** - Undefined keys randomly pick from defined segments
- **Fallback WAV support** - `fallback.wav` for missing keys with `-f` flag

### Audio Modes

#### Multi-File Mode (Individual WAV Files)

In this mode, each key has its own audio file. Use `key_define_type: "multi"` in config.json:

```json
{
    "key_define_type": "multi",
    "defines": {
        "1": "key-1.wav",
        "2": "key-2.wav",
        "30": "key-a.wav"
    }
}
```

#### Single-File Mode (OGG with Segments)

In this mode, all key sounds come from a single OGG file using time segments. Use `key_define_type: "single"`:

```json
{
    "key_define_type": "single",
    "sound": "keyboard.ogg",
    "defines": {
        "1": [971, 142],
        "2": [1276, 115],
        "30": [11171, 109]
    }
}
```

The segment format `[start_ms, duration_ms]` specifies where in the OGG file to extract each key's sound.

### Config.json Format

Create a `config.json` file in your audio directory:

```json
{
    "id": "sound-pack-123",
    "name": "My Sound Pack",
    "key_define_type": "single",
    "sound": "main.ogg",
    "defines": {
        "1": [971, 142],
        "2": [1276, 115],
        "30": [11171, 109],
        "31": [11462, 120]
    }
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `key_define_type` | Yes | `"single"` for OGG segments, `"multi"` for individual files |
| `sound` | Yes (single mode) | Main audio filename (OGG format) |
| `defines` | Yes | Keycode mappings to filenames or `[start_ms, duration_ms]` arrays |

### Loading Sound Packs

Organize sound packs in subdirectories under your audio directory:

```
wav/
├── oreo/
│   ├── config.json
│   └── oreo.ogg
├── banana1/
│   ├── config.json
│   ├── banana-l-1.wav
│   └── ...
└── my-pack/
    └── ...
```

Load a pack using the `-p` option:

```bash
# Load "oreo" pack from wav/oreo/
./buckle -p oreo

# Load pack with custom audio directory
./buckle -w /path/to/packs -p my-pack

# Verbose mode to see config loading
./buckle -v -p oreo
```

### MechVibes Compatibility

This feature is designed to work with **MechVibes sound packs**. Many community-created packs include a `config.json` file that can be used directly with this fork.

Example MechVibes pack structure:
```
my-pack/
├── config.json
├── main.ogg
└── ...audio files
```

### Fallback Sound

If a key has no defined sound, bucklespring will:

1. **Single-file mode**: Randomly pick a segment from another defined key
2. **Multi-file mode / with -f flag**: Use `fallback.wav` if it exists
3. **Otherwise**: Play nothing

Use the `-f` flag to enable fallback.wav for missing keys:

```bash
./buckle -f
```

### Directory Structure

```
bucklespring/
├── buckle              # Compiled binary
├── wav/                # Audio files directory
│   ├── config.json     # Root config (optional)
│   ├── fallback.wav    # Fallback for missing keys
│   ├── *.wav           # Individual key sounds
│   ├── pack1/          # Sound pack 1
│   │   ├── config.json
│   │   └── ...
│   └── pack2/          # Sound pack 2
│       ├── config.json
│       └── ...
└── deps/
    ├── minivorbis.c    # OGG decoder
    └── json.h          # JSON parser
```

### Keycodes

Keycodes use the Linux input subsystem standard. Common codes:

| Code | Key |
|------|-----|
| 1 | ESC |
| 2-13 | F1-F12 |
| 14 | Backspace |
| 15 | Tab |
| 16-28 | Q-P |
| 29-42 | A-L |
| 43-53 | Z-M |
| 57 | Space |
| 58 | Caps Lock |
| 59-68 | F1-F10 (alternate) |

### Press-Only Mode

The `--press-only` (`-u`) option disables audio playback for key release events, playing sounds only when keys are pressed down:

```bash
./buckle --press-only
```

OpenAL notes
------------


Bucklespring uses the OpenAL library for mixing samples and providing a
realistic 3D audio playback. This section contains some tips and tricks for
properly tuning OpenAL for bucklespring.

* The default OpenAL settings can cause a slight delay in playback. Edit or create
  the OpenAL configuration file `~/.alsoftrc` and add the following options:

 ````
 period_size = 32
 periods = 4
 ````

* If you are using headphones, enabling the head-related-transfer functions in OpenAL
  for a better 3D sound:

 ````
 hrtf = true
 ````

* When starting an OpenAL application, the internal sound card is selected for output,
  and you might not be able to change the device using pavucontrol. The option to select
  an alternate device is present, but choosing the device has no effect. To solve this,
  add the following option to the OpenAL configuration file:

 ````
 allow-moves = true
 ````
