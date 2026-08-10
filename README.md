# mpv-winbuild-slim

Kazumi Lite 专用 **mpv 精简构建**（GitHub Actions 云端按需编译）。

## 目标

标准 mpv-winbuild 是"全功能"构建（含 NVENC 编码器、蓝光、直播流、VapourSynth、Whisper 等我们用不到的东西，ffmpeg 层 ~117MB）。
本项目通过 GitHub Actions **裁剪 ffmpeg**，只保留播放必需能力，把最终 mpv 从 ~117MB 压到 ~50-60MB。

## 保留 vs 砍掉

**保留**（播放必需）：
- 解码：h264 / hevc / av1(dav1d) / vp9 / aac / opus / mp3 / flac / ac3 / eac3
- 硬解：d3d11va / dxva2
- 渲染：opengl / vulkan（Anime4K 超分着色器依赖）
- 字幕：libass / libaribcaption / harfbuzz / freetype
- 网络：http / https / tls（防盗链流媒体必需）

**砍掉**：全部编码器（--disable-encoders）、封装写入（--disable-muxers）、NVENC/CUDA、蓝光/DVD、直播流(SRT/SSH)、VapourSynth、Whisper、frei0r、libaom(av1 用 dav1d) 等。

## 使用

1. 仓库 → Actions → **Build Slim MPV** → Run workflow
2. 选 CPU 基线：`64-v3`（Skylake+/Zen，兼容性最好，推荐）或 `64-v4`（Icelake+/Zen4，更快）
3. 构建完成后（约 40-60 分钟）从 Artifact 下载 `mpv-slim-*.7z`

## 产物

- `mpv-slim-64-v3/*.7z`：完整播放器包（mpv.exe + 必需 dll）
- 解压后替换 Kazumi Lite 的 `mpv/` 目录即可

## 说明

- 裁剪逻辑在 `.github/workflows/build.yml` 的 **Slim down ffmpeg** 步骤（sed 处理 `mpv-winbuild-cmake/packages/ffmpeg.cmake`）
- 上游：https://github.com/Andarwinux/mpv-winbuild （构建系统）
