# MMWave Engine

[中文](./README_CN.md)

Python package that fuses 2-D mmWave observations into room-frame tracks, zone
events and trajectory scores. It does not import Home Assistant.

[mmwave-fusion](https://github.com/zomco/mmwave-fusion) is the Home Assistant
shell. A future desktop or Docker shell can import this same package.

```bash
pip install mmwave-engine
```

## Publishing

Releases go to PyPI from a GitHub Release, via trusted publishing. No API token
is stored in the repo. Once, on [PyPI](https://pypi.org/manage/account/publishing/):

1. Create the project name `mmwave-engine` by uploading the first release, or
   reserve it under Your projects.
2. Add a trusted publisher: owner `zomco`, repository `mmwave-engine`,
   workflow `publish.yml`, environment `pypi`.
3. In this GitHub repo, create an environment named `pypi`.
4. Tag `v0.1.0` matching `mmwave_engine.__version__` and publish a GitHub Release.

Home Assistant then installs it from `mmwave-fusion`'s `manifest.json`
`requirements`. Do not point that pin at a version that is not on PyPI yet.

## What it accepts

A versioned target frame, not a brand protocol and not a presence bit:

```json
{"v":1,"f":42,"ts":1234,"t":[[120.0,340.0,-8]]}
```

`x` and `y` are centimetres in the radar's local frame. `observations_from_frame`
applies the shared yaw/pitch/roll convention. Range-only sensors have nothing
to fuse.

Video is a `VideoSink`: `snapshot` and `record` of a live stream. This package
does not seek an NVR timeline.

## Layout

| Module | Role |
| --- | --- |
| `fusion.py` | Transform, clustering, Hungarian association, alpha-beta tracking |
| `frames.py` / `radar.py` | v1 frame decode and room-frame observations |
| `events.py` | Zone enter / exit / dwell |
| `quality.py` | Score a finished track as `traverse` or `trajectory` |
| `recording.py` | Which events keep a still and a clip |
| `storage.py` | SQLite history |
| `video.py` | Sink protocol only |

Coordinate convention matches mmwave-component and mmwave-card. Change it in
all three together.
