# 协作说明 — MMWave Engine

[English](AGENTS.md)

这个包是 TraceCue 和 mmwave-fusion 权重最高的视频标签。它把二维雷达帧融合成轨迹、区域事件和轨迹评分。不要导入 Home Assistant、mmwave-fusion 或 `tracecue-engine`。

## 不要做

- 不要在这里解析海康或其他 NVR 的 XML。
- 不要把 NVR 的区域入侵或越界侦测送进 `FusionEngine.step()`。它们没有 x/y，会造出假轨迹。
- 不要把原始 NVR 标签当训练集。它们有大量误报，必须先在别处清洗。
- 先不要在这个包里训练模型。调参标签是人工结论：`person`、`pet`、`false_positive`、`uncertain`。用这些结论调整确定性阈值。
- 不要导入 `tracecue-engine`。那个库以后可以导出离线 `timeline.v1` 参考，不是运行时助手。

## 以后做对照时的排序

外壳先把 NVR 标签对照成雷达 `zone_id`，再调用本包。结果可以给雷达事件排序，不得改写它。

- 雷达事件和清洗后的 NVR 标签落在同一区域、同一时间窗：排最前。
- 只有雷达：仍然显示。
- 只有 NVR：低权重提示，不是已确认的人。
- 时间对上、区域对不上：留给人看。

## 契约

雷达适配器契约是 v1 目标帧。只测距或只有有人/无人的传感器不进入融合轨迹。视频是直播流上的 `VideoSink`，不是 NVR seek。
