# Agent instructions — MMWave Engine

[中文](AGENTS_CN.md)

This package is the highest-weight video tag for TraceCue and mmwave-fusion.
It fuses 2-D radar frames into tracks, zone events and trajectory scores.
It does not import Home Assistant, mmwave-fusion, or `tracecue-engine`.

## Do not

- Do not parse Hikvision or other NVR XML here.
- Do not pass NVR region-intrusion or line-crossing events into `FusionEngine.step()`. They have no x/y and would create false tracks.
- Do not treat raw NVR labels as a training set. They contain dense false positives and must be cleaned elsewhere first.
- Do not train a model in this package yet. Tuning labels are human verdicts: `person`, `pet`, `false_positive`, `uncertain`. Adjust deterministic thresholds against those labels.
- Do not import `tracecue-engine`. That library may later export an offline `timeline.v1` reference. It is not a runtime helper.

## Ranking, when corroboration is added

A shell maps an NVR label onto a radar `zone_id` before calling this package.
The result may rank a radar event. It must not rewrite it.

- Radar event plus a cleaned NVR label in the same zone and window: show first.
- Radar only: keep it visible.
- NVR only: low-weight hint, not a confirmed person.
- Time matches but the zone does not: leave it for a person.

## Contract

The radar adapter contract is a v1 target frame. Range-only or presence-only sensors do not enter fusion tracks. Video is a live-stream `VideoSink`, not an NVR seek.
