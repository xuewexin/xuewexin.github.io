# 等我个人博客 — 开发文档

## 项目简介

基于 [Hugo](https://gohugo.io/) 静态网站生成器 + [hugo-theme-stack](https://github.com/CaiJimmy/hugo-theme-stack) 主题构建的个人博客。默认语言为中文（zh-cn）。

- **线上地址**：<https://xuewexin.github.io>
- **源码仓库**：`xuewexin/dev`
- **部署仓库**：`xuewexin/xuewexin.github.io`（GitHub Pages）

---

## 快速开始

仓库根目录自带 `hugo.exe`（extended 版本），无需全局安装 Hugo。

```bash
# 启动本地开发服务器（包含草稿文章）
./hugo server -D

# 构建网站（包含草稿）
./hugo -D

# 构建生产版本（排除草稿）
./hugo

# 创建新文章
./hugo new post/文章名称/index.zh-cn.md
```

本地开发服务器默认访问 `http://localhost:1313`。

---

## 目录结构

```
.
├── hugo.yaml              # Hugo 主配置文件
├── hugo.exe               # Hugo extended 可执行文件（无需系统安装）
├── CLAUDE.md              # AI 辅助开发指引
├── README-DEV.md          # 本文件，开发文档
│
├── content/               # Markdown 内容
│   ├── _index.md          # 主页（英文）
│   ├── _index.zh-cn.md    # 主页（中文）
│   ├── post/              # 博客文章 → 线上路径 /p/<slug>/
│   │   ├── 文章A/index.zh-cn.md
│   │   └── ...
│   ├── page/              # 独立页面 → 线上路径 /<slug>/
│   │   ├── about/
│   │   ├── search/
│   │   ├── archives/
│   │   └── links/
│   └── categories/        # 分类索引页
│       ├── 文学创作/
│       ├── 算法题目/
│       └── 编程/
│
├── layouts/               # 布局覆写（覆盖主题默认模板）
│   └── partials/
│       ├── article/
│       │   ├── article.html              # 覆写：文章主体 + 悬浮目录
│       │   └── components/
│       │       ├── details.html          # 覆写：文章元信息（+字数统计）
│       │       ├── footer.html           # 覆写：文章底部（+分享+微信联系）
│       │       ├── share.html            # 新增：社交分享按钮
│       │       ├── wechat-contact.html   # 新增：作者微信联系方式
│       │       └── toc-float.html        # 新增：悬浮目录侧边栏
│       ├── comments/
│       │   └── provider/
│       │       └── giscus.html           # 覆写：giscus 评论（修复主题跟随）
│       └── footer/
│           ├── custom.html               # 覆写：全局底部（背景+字体+粒子+回到顶部）
│           ├── pjax.html                 # 覆写：PJAX 平滑过渡+评论重载
│           ├── aplayer.html              # 新增：APlayer 音乐播放器
│           └── components/
│               └── script.html           # 覆写：JS 脚本加载
│
├── assets/                # 需要 Hugo 处理的资源（Sass/TS/JS/字体/图片）
│   ├── scss/
│   │   ├── custom.scss           # 自定义样式（光标、APlayer 主题）
│   │   ├── aplayer-light.scss    # APlayer 亮色皮肤
│   │   └── aplayer-dark.scss     # APlayer 暗色皮肤
│   ├── ts/
│   │   ├── main.ts               # 覆写：Stack 主题初始化入口
│   │   └── search.tsx            # 覆写：客户端全文搜索
│   ├── js/
│   │   └── topbar.min.js         # 页面加载进度条
│   ├── background/
│   │   ├── particles.min.js      # 粒子动画库
│   │   ├── particlesjs-config.json  # 粒子动画配置
│   │   └── preview.jpg           # 全站背景图
│   └── font/
│       └── 宋.ttf                # 自定义中文字体
│
├── static/                # 静态文件（不经 Hugo 处理，直接输出）
│   ├── favicon.ico
│   ├── music/                    # 音乐文件（每首歌一个文件夹）
│   │   ├── 走马-曲肖冰/
│   │   │   ├── song.mp3
│   │   │   ├── cover.jpg
│   │   │   └── lrc.lrc
│   │   └── ...（共8首）
│   └── mouse/                    # 自定义光标图片
│       ├── 1.png / 2.png / 3.png
│       └── *.ani                 # 动画光标
│
├── themes/
│   └── hugo-theme-stack/         # 基础主题（作为 Hugo Module）
│
├── .github/workflows/
│   └── hugo.yaml                 # GitHub Actions 自动部署
│
└── public/               # Hugo 构建输出（已 gitignore）
```

---

## Hugo 配置要点

配置文件：`hugo.yaml`

| 配置项 | 值 | 说明 |
|--------|-----|------|
| `baseurl` | `https://xuewexin.github.io` | 站点基础 URL |
| `theme` | `hugo-theme-stack` | 使用主题 |
| `DefaultContentLanguage` | `zh-cn` | 默认语言 |
| `hasCJKLanguage` | `true` | 启用 CJK 语言支持（影响 `.WordCount` 和 `.Summary`） |
| `paginate` | `3` | 每页显示 3 篇文章 |

### 数学公式

已启用 Goldmark passthrough 扩展：
- 行内公式：`\(...\)`
- 块级公式：`$$...$$` 或 `\[...\]`

### 代码高亮

- 行号启用（`lineNos: true`）
- 自动语言检测（`guessSyntax: true`）
- 允许 HTML 嵌入 Markdown（`unsafe: true`）

---

## 自定义组件说明

### 1. PJAX 平滑过渡 + giscus 评论（`pjax.html`）

- 使用 PJAX 库实现页面无刷新跳转，提升浏览体验
- 顶部进度条（topbar.js）显示加载状态
- **PJAX 的 innerHTML 替换不会执行 `<script>` 标签**，因此 giscus 评论脚本需要手动重建
- `pjax:complete` 事件中调用 `reloadGiscus()` 重新执行 giscus 脚本

### 2. 回到顶部按钮（`custom.html`）

- 滚动超过 400px 后显示
- 圆形按钮，底部右侧定位
- 使用 `requestAnimationFrame` 优化滚动监听性能
- 自适应移动端位置（避免与 APlayer 播放器重叠）

### 3. 悬浮目录侧边栏（`toc-float.html`）

- 文章页面右侧固定圆形按钮
- 点击滑出目录面板（300px 宽）
- 半透明遮罩层，点击遮罩或 ESC 关闭
- 自动同步主题侧边栏 TOC 的 active-class 高亮状态
- 仅在文章有目录内容时显示（TOC 长度 ≥ 100 字符）

### 4. 社交分享（`share.html`）

- **复制链接**：使用 `navigator.clipboard` API，带"已复制"状态反馈
- **微信分享**：调用 qrserver API 生成当前页面二维码，弹窗展示
- **微博分享**：打开微博分享窗口
- 弹窗支持 ESC 关闭

### 5. 字数统计（`details.html`）

- 在文章阅读时间旁新增字数显示（`{{ .WordCount }} 字`）
- 中文计数依赖 `hasCJKLanguage: true` 配置

### 6. 作者微信联系方式（`wechat-contact.html`）

- 绿色微信风格按钮"加作者微信"
- 点击弹窗展示微信二维码
- 二维码图片路径：`/static/img/wechat-qr.jpg`
- **需要用户自行放置微信二维码图片到该路径**

### 7. APlayer 音乐播放器（`aplayer.html`）

- 底部固定，左侧显示
- 8 首预设歌曲，来自 `static/music/` 目录
- 播放位置通过 localStorage 持久化（页面刷新恢复播放进度）
- 支持亮/暗主题切换（`aplayer-light.scss` / `aplayer-dark.scss`）

### 8. 自定义光标（`custom.scss`）

- 使用 `static/mouse/` 下的 PNG 光标图片
- 区分 default / pointer / text 三种交互状态

### 9. Particles.js 粒子背景（`custom.html`）

- 粉色粒子（`#f3afca`），120 个粒子
- 固定背景层（`z-index: -1`）
- 鼠标悬停抓取、点击排斥

---

## 双仓库说明

| 仓库 | 用途 | 分支 |
|------|------|------|
| `xuewexin/dev` | 源码仓库 — Hugo 源文件、主题、内容 | `main` |
| `xuewexin/xuewexin.github.io` | 部署仓库 — GitHub Pages 静态文件 | `main` |

**工作流程**：
1. 在 `dev` 仓库编辑内容和代码
2. 推送到 `dev` 的 `main` 分支
3. GitHub Actions 自动触发（`.github/workflows/hugo.yaml`）
4. Hugo 构建 → 输出到 `public/`
5. `peaceiris/actions-gh-pages` 将 `public/` 推送到 `xuewexin.github.io` 的 `main` 分支
6. GitHub Pages 自动更新站点

**结论：所有开发提交应提交到 `xuewexin/dev` 仓库，不需要手动操作部署仓库。**

---

## 部署配置

CI/CD 通过 GitHub Actions 实现（`.github/workflows/hugo.yaml`）：

- **触发条件**：推送到 `main` 分支
- **构建命令**：`hugo -D`（包含草稿文章）
- **部署目标**：`xuewexin/xuewexin.github.io`，`main` 分支
- **认证**：使用 Personal Access Token（secret 名：`Ju`）

---

## 注意事项

1. 创建新文章时务必使用 `index.zh-cn.md` 作为文件名（而非 `index.md`），以确保语言正确匹配
2. 文章 frontmatter 中的 `draft: true` 表示草稿，生产构建时需移除或改为 `false`
3. 静态音乐文件较大（约 50MB+），新增歌曲时注意仓库体积
4. 微信二维码图片需自行放置到 `static/img/wechat-qr.jpg`
5. Hugo extended 版本是必须的（用于 SCSS 编译和 TypeScript 构建）
6. 主题覆写遵循 Hugo 的 lookup order：项目根目录的 `layouts/` 文件优先级高于 `themes/hugo-theme-stack/layouts/`
