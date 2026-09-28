# MMWave Engine

[English](./README.md)

不依赖 Home Assistant 的多雷达融合核心：户型轨迹、区域事件、轨迹评分、SQLite。
这些事件是回查安防视频时权重最高的标签。不要导入 `tracecue-engine`。

NVR 的区域入侵和越界侦测不是观测，不要送进 `FusionEngine.step()`。原始 NVR
标签不是训练集；先在别处清洗，再用人工结论（`person`、`pet`、`false_positive`、
`uncertain`）调整确定性阈值。见 [AGENTS_CN.md](AGENTS_CN.md)。

[mmwave-fusion](https://github.com/zomco/mmwave-fusion) 是 HA 外壳。以后的桌面或
Docker 外壳导入同一个包。

```bash
pip install mmwave-engine
```

发版：在 GitHub 打与 `mmwave_engine.__version__` 一致的 tag 并发布 Release，
`publish.yml` 用 PyPI 可信发布上传。仓库里不存 token。首次需要在 PyPI 登记
项目名，并把可信发布方设为 `zomco/mmwave-engine`、workflow `publish.yml`、
environment `pypi`。

入口是 v1 目标帧，不是品牌协议，也不是「有人/无人」：

```json
{"v":1,"f":42,"ts":1234,"t":[[120.0,340.0,-8]]}
```

`x`、`y` 为雷达本地厘米。一维存在雷达没有可融合的坐标。视频只通过 `VideoSink`
抓直播流的抓拍和短片，不检索 NVR 时间轴。

坐标约定与固件、卡片相同，三处必须一起改。
