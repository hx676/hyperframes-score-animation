# HyperFrames Score Animation


一个用于制作真实曲谱驱动的 HyperFrames 3D 音乐动画 Skill。


This repository contains a reusable Codex Skill for building deterministic HyperFrames 3D score animations from real MusicXML/MXL, MIDI, and audio.


## Output Example / 输出示例


15 秒示例来自真实曲谱动画成片，包含同步音频：


![15-second preview](examples/hyperframes-score-animation-15s.gif)


- [Download the 15-second MP4 example](examples/hyperframes-score-animation-15s.mp4)
- [Download the GIF preview](examples/hyperframes-score-animation-15s.gif)


示例内容包括真实五线谱、谱面上的多球分裂与汇聚、连续镜头、发光球、短拖尾和暗调纸面舞台。


### Additional Case / 新案例


来自 `小球+workbuddy.mp4` 的 15 秒高分辨率片段：


- [Download the Workbuddy 15-second MP4 case](examples/hyperframes-score-animation-workbuddy-15s.mp4)
- [Preview the Workbuddy 15-second GIF](examples/hyperframes-score-animation-workbuddy-15s.gif)
- Source / 源视频: 2560x1440, 30 fps, H.264 + AAC
- Clip length / 片段时长: 15 seconds
- GIF preview / GIF 预览: 480x270, 12 fps


This case is included as a visual reference for the score-following camera, luminous balls, multi-ball behavior, trails, and dark paper-stage treatment.


这个案例用于展示曲谱跟随镜头、发光小球、多球表现、拖尾光效和暗调纸面舞台。


## What This Skill Does / Skill 作用


### 中文


这个 Skill 适用于：


- 用真实 `.mxl`、`.musicxml` 或 `.xml` 渲染曲谱；
- 用 MIDI 控制音符时间、音高、时值、和弦和声部；
- 将 `MIDI Note -> MusicXML Note -> SVG Notehead` 建立成可审计映射；
- 在 Three.js WebGPU/WebGL 或 TypeGPU 中制作确定性的 3D 演奏动画；
- 处理单音、多音、和弦、重复音、延音和 Tie；
- 让镜头沿曲谱连续滑行，而不是按系统换行跳转；
- 在导出前检查瞬移、回跳、错音、漏音、黑帧和音画同步。
