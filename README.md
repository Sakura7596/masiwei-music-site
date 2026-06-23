# MASIWEI Music Site v0.4 — Fast Load

这个版本针对进入网页慢做了性能优化。测试

## 主要变化

- 封面从 `data/library.json` 的 base64 里拆出来
- 封面转成 `covers/*.webp`
- `library.json` 体积大幅降低
- `index.html` 加载 `library.json` 时取消 `cache: "no-store"`
- 音频继续使用 Cloudflare R2 地址
- 部署包里不包含 MP3

## 结构

```txt
index.html
data/
  library.json
covers/
  *.webp
docs/
  v0.4-fast-load-notes.md
README.md
```

## 部署

重新上传整个文件夹内容到 Cloudflare Workers & Pages：

```txt
index.html
data/
covers/
docs/
README.md
```

不要漏掉 `covers/`，否则专辑封面会显示不出来。

## 测试地址

部署后检查：

```txt
你的网站地址
你的网站地址/data/library.json
你的网站地址/covers/01-xxx.webp
```

## 注意

首次打开仍然需要加载封面文件，但 JSON 不再一次性塞 2MB+ base64，整体启动会更快；二次打开也会受益于浏览器/CDN缓存。


## v0.4.1 更新

- 加入首屏封面预加载，减少进入网页时图片空白。
- 加入 loader 过渡。
- 保留 v0.4 的 WebP 封面拆分和 R2 音频。
