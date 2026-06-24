# Masiwei Music Site

一个已上线的静态音乐播放器网站，以马思唯相关专辑/单曲为内容主体，重点是“可听歌”的视觉音乐体验，而不是单纯的专辑展示页。

当前线上地址：

```txt
https://music.minamir.cn
```

---

## 当前版本

```txt
v0.7 final batch import
```

当前状态：

- GitHub 管理代码
- Cloudflare 自动部署
- `music.minamir.cn` 自定义域名访问
- Cloudflare R2 存放音频
- `data/library.json` 管理专辑、歌曲、音频 URL、歌词 URL
- `lyrics/` 存放 LRC 同步歌词
- `covers/` 存放 WebP 专辑封面
- 左侧同步滚动歌词已接入
- P.E.I Vol.2 已按当前可用资源精简为 4 首

---

## 项目结构

```txt
project/
├─ index.html
├─ data/
│  └─ library.json
├─ covers/
│  ├─ *.webp
│  └─ ...
├─ lyrics/
│  ├─ rnb-all-night-masiwei-remix.lrc
│  ├─ dog-bite-dog/
│  │  ├─ 01-dbd-intro.lrc
│  │  ├─ 12-babe-feat-罗言.lrc
│  │  └─ ...
│  └─ ...
├─ docs/
│  ├─ v0.7-final-batch-import-report.md
│  ├─ v0.7-final-batch-import-manifest.csv
│  └─ ...
└─ README.md
```

---

## 核心文件说明

### `index.html`

网站主程序，负责：

- 页面结构
- 视觉样式
- 专辑封面墙
- 红白流动文字
- 歌单抽屉
- 播放器 UI
- 播放 / 暂停 / 上一首 / 下一首
- 音频进度条
- 读取 `data/library.json`
- 读取 `lyricsUrl`
- 左侧 LRC 同步滚动歌词

当前前端逻辑基于：

```txt
v0.6.6 disable legacy lyric fragment
```

重要：旧的 `LYRIC FRAGMENT` 展示逻辑已停用，现在左侧只在歌曲播放时显示同步歌词；未播放时左侧保持空白。

---

### `data/library.json`

网站音乐数据中心，负责管理：

- 网站基础信息
- 专辑列表
- 曲目列表
- 封面路径
- R2 音频 URL
- LRC 歌词路径
- 歌词偏移量
- 歌词预览
- 曲目时长

单首歌曲数据格式示例：

```json
{
  "title": "Babe",
  "artist": "马思唯 / 艾志恒Asen",
  "audio": "https://pub-bf6c625d61634054bc2ce5d65fd24d5e.r2.dev/audio/狗咬狗/Babe.flac",
  "duration": 191.33,
  "lyricPreview": "每当你觉得孤单 just talk to me babe",
  "lyricsUrl": "./lyrics/04-狗咬狗/12-babe-feat-罗言.lrc",
  "lyricsFormat": "lrc",
  "lyricsOffset": 0,
  "lyrics": ""
}
```

字段说明：

| 字段 | 作用 |
|---|---|
| `title` | 歌曲名 |
| `artist` | 歌手 |
| `audio` | Cloudflare R2 音频 URL |
| `duration` | 歌曲时长，单位秒 |
| `lyricPreview` | 歌词预览句，可用于备用展示 |
| `lyricsUrl` | LRC 歌词文件路径 |
| `lyricsFormat` | 当前使用 `lrc` |
| `lyricsOffset` | 歌词偏移，正数提前，负数延后 |
| `lyrics` | 预留字段，当前通常留空 |

---

### `lyrics/`

存放 LRC 同步歌词文件。

示例：

```txt
lyrics/
├─ dog-bite-dog/
│  ├─ 01-dbd-intro.lrc
│  ├─ 02-训练日.lrc
│  └─ ...
└─ rnb-all-night-masiwei-remix.lrc
```

LRC 格式示例：

```lrc
[00:09.90]让我来给大家介绍 she’s my bae
[00:12.30]我带她去逛街 他们都说气质般配
[00:14.28]她是我自信心的来源同时也是软肋
```

---

### `covers/`

存放专辑封面，建议使用 WebP 格式。

`library.json` 中通过相对路径引用：

```json
"cover": "./covers/dog-bite-dog.webp"
```

---

### Cloudflare R2

音频文件不放 GitHub，不放项目目录，统一放 Cloudflare R2。

当前 R2 公开基础地址：

```txt
https://pub-bf6c625d61634054bc2ce5d65fd24d5e.r2.dev
```

推荐音频路径结构：

```txt
masiwei-audio/
└─ audio/
   ├─ rnb all night.flac
   ├─ 狗咬狗/
   │  ├─ Babe.flac
   │  └─ ...
   └─ ...
```

`library.json` 中只保存音频 URL：

```json
"audio": "https://pub-bf6c625d61634054bc2ce5d65fd24d5e.r2.dev/audio/狗咬狗/Babe.flac"
```

---

## 当前部署逻辑

```txt
GitHub 仓库
↓
Commit 到 main
↓
Cloudflare 自动检测
↓
Cloudflare Workers & Pages 自动部署
↓
music.minamir.cn 自动更新
```

现在不需要手动上传 ZIP 到 Cloudflare。

---

## 更新网站的标准流程

### 1. 新增或修改音频

1. 将音频上传到 Cloudflare R2。
2. 获取公开 URL。
3. 修改 `data/library.json` 中对应歌曲的 `audio` 字段。
4. Commit 到 GitHub。
5. 等 Cloudflare 自动部署。

---

### 2. 新增或修改歌词

1. 将 LRC 文件放入 `lyrics/` 对应专辑文件夹。
2. 修改 `data/library.json` 中对应歌曲的：
   - `lyricsUrl`
   - `lyricsFormat`
   - `lyricsOffset`
   - `lyricPreview`
3. Commit 到 GitHub。
4. 测试播放时歌词是否同步。

---

### 3. 歌词偏移调整

如果某首歌歌词整体慢，增大 `lyricsOffset`：

```json
"lyricsOffset": 0.45
```

如果歌词整体快，改成负数：

```json
"lyricsOffset": -0.25
```

判断标准：

```txt
歌词慢 → lyricsOffset 用正数
歌词快 → lyricsOffset 用负数
```

---

### 4. 批量导入歌曲和歌词

推荐先整理成批量模板：

```txt
专辑文件夹/
├─ album-info.md
└─ tracks/
   └─ 01-song-name/
      ├─ audio-url.txt
      ├─ lyrics.lrc
      └─ track-info.md
```

然后由脚本或 ChatGPT 批量生成：

- `data/library.json`
- `lyrics/`
- 导入报告

---

## 当前 v0.7 导入结果

```txt
处理专辑：8 张
导入曲目：85 首
缺失音频 URL：0
缺失歌词：0
LRC 时间倒序问题：0
P.E.I Vol.2：移除缺失资源的 5 首
```

P.E.I Vol.2 当前只保留有资源的 4 首，不再保留找不到资源的旧曲目。

---

## GitHub 提交建议

每次修改后用清晰 commit message：

```txt
v0.7 final batch import audio urls and lyrics
```

或：

```txt
fix Babe lyrics offset
add new album lyrics
update Dog Bite Dog audio urls
```

不要在仓库里保存多个版本文件，例如：

```txt
index-v0.6.1.html
index-v0.6.2.html
library-v0.7.json
```

正确做法：

```txt
index.html 始终只有一个
data/library.json 始终只有一个
历史版本交给 Git commit 保存
```

---

## 测试清单

每次部署后测试：

```txt
https://music.minamir.cn
https://music.minamir.cn/data/library.json
```

重点抽测：

- 页面是否打开
- 专辑封面是否显示
- 歌曲是否能播放
- 左侧同步歌词是否出现
- 播放中切换专辑，歌词是否继续显示当前播放歌曲
- 未播放时左侧是否保持空白
- R2 音频 URL 是否可访问

---

## 重要注意事项

不要做：

```txt
不要把音频文件提交到 GitHub
不要把 MP3 / FLAC 放进项目目录
不要把封面 base64 写进 JSON
不要恢复旧的 LYRIC FRAGMENT 逻辑
不要在仓库里保留多个旧版本 HTML / JSON 文件
不要上传 ZIP 到 GitHub 作为网站代码
```

可以做：

```txt
可以新增专辑
可以新增歌曲
可以新增歌词
可以调整 lyricsOffset
可以更新 covers/
可以更新 data/library.json
可以继续优化同步歌词视觉效果
```

---

## 当前一句话总结

这是一个基于 GitHub + Cloudflare + R2 的静态音乐播放器网站：

```txt
GitHub 管理代码
Cloudflare 自动部署网页
music.minamir.cn 作为访问域名
data/library.json 管理音乐数据
lyrics/ 管理 LRC 同步歌词
covers/ 管理专辑封面
Cloudflare R2 存放音频
```
