# lazy-netlog-viewer

[懒猫微服](https://lazycat.cloud) 上的 Chromium [Net Log Viewer](https://netlog-viewer.appspot.com/) 一键部署 ——
本地可视化分析 Chrome `chrome://net-export/` 导出的 NetLog JSON 文件。

> 数据完全在浏览器里解析，**不会上传到任何服务器**。本应用只负责把那一坨静态前端文件用 nginx serve 出来。

## 安装

下载最新 lpk：[Releases](https://github.com/microlazy-apps/lazy-netlog-viewer/releases/latest)

```sh
lpk-manager install ./cloud.lazycat.app.netlog-viewer-*.lpk
```

或在懒猫应用商店搜索「Net Log Viewer」直接安装。

## 使用

1. 打开 Chrome / Chromium 系浏览器，访问 `chrome://net-export/`，点击 **Start Logging to Disk**，操作复现网络问题，再点击 **Stop Logging**，得到 `.json` 文件。
2. 安装完成后访问 `https://netlog-viewer.{你的微服域名}` 打开 Web UI。
3. 点击 **Choose File** 选择刚才导出的 NetLog JSON。
4. 在左侧标签页里看 Events / Timeline / DNS / HTTP cache / Sockets / QUIC / SPDY 等。

## 与官方部署的关系

[netlog-viewer.appspot.com](https://netlog-viewer.appspot.com/) 是 Chromium 团队部署到 Google App Engine 的官方版本。本仓库做的事：

- 上游 Chromium [catapult](https://chromium.googlesource.com/catapult/+/refs/heads/main/netlog_viewer/) 仓库的 `netlog_viewer/` 子目录在 docker build 时按 sparse-checkout 拉下来
- 跑上游自带的 `netlog_viewer_build/build_for_appengine.py` 把 HTML imports / Polymer 1 组件 vulcanize 成单个 `vulcanized.html`
- nginx alpine serve 该静态文件

## 开发

详见 [CLAUDE.md](CLAUDE.md)。

## License

本仓库的 lazycat 包装层为 BSD 3-Clause（与上游一致）。上游 Chromium catapult 项目同样为 BSD —— 见
[catapult/LICENSE](https://chromium.googlesource.com/catapult/+/refs/heads/main/LICENSE)。
