---
title: 使用 Hexo 和 Butterfly 搭建与维护个人博客
date: 2026-11-23 00:00:00
description: 从本地搭建、主题配置、内容组织到 GitHub Pages 自动部署的完整操作流程，附日常发布步骤。
cover: /images/hexo-guide-cover.jpg
top_img: /images/hexo-guide-cover.jpg
categories:
  - 技术学习
tags:
  - Hexo
  - Butterfly
  - GitHub Pages
  - Git
  - 博客搭建
---

## 一、项目概况

本文记录从本地搭建、主题配置、内容组织，到 GitHub Pages 自动部署的完整操作流程，并整理日后发布新文章的日常步骤。

### 1. 技术选型

| 项目 | 当前采用方案 |
|---|---|
| 静态博客框架 | Hexo |
| 主题 | Butterfly |
| 运行环境 | Node.js |
| 包管理工具 | npm |
| 版本管理 | Git |
| 远程仓库 | GitHub |
| 网站托管 | GitHub Pages |
| 自动部署 | GitHub Actions |
| 本地开发环境 | Windows |
| 编辑内容 | Markdown |

### 2. 当前项目环境

本次搭建过程中确认的版本：

| 组件 | 版本 |
|---|---:|
| Node.js | 26.13.1 |
| npm | 11.20.0 |
| Git | 2.56.1.windows.1 |
| Hexo CLI | 4.3.2 |
| Hexo | 8.1.2 |
| Butterfly | 5.7.2 |
| hexo-renderer-pug | 3.0.0 |
| hexo-renderer-stylus | 3.0.1 |

> 版本会随着后续维护而变化。排查问题时，先运行 `npx hexo version` 查看当前实际版本，不要仅凭本文中的版本号判断。

### 3. 项目路径与网站地址

本机项目根目录：

```text
E:\Isolde Veil's Blog\myblog
```

GitHub 用户名：`IsoldeVeil`

GitHub 仓库：

```text
https://github.com/IsoldeVeil/isoldeveil.github.io
```

正式网站：

```text
https://isoldeveil.github.io/
```

所有本地命令都应在项目根目录执行。由于路径中包含空格，命令中使用路径时必须保留双引号。

---

## 二、项目目录结构

与日常维护最相关的目录和文件如下：

```text
myblog/
├── .github/
│   ├── dependabot.yml
│   └── workflows/
│       └── pages.yml
├── source/
│   ├── about/
│   │   └── index.md
│   ├── _posts/
│   │   ├── 文章一.md
│   │   └── 文章二.md
│   ├── css/
│   │   └── custom.css
│   ├── images/
│   │   └── index-background.jpeg
│   └── favicon.ico
├── themes/
│   └── .gitkeep
├── _config.yml
├── _config.butterfly.yml
├── _config.landscape.yml
├── .gitignore
├── package.json
└── package-lock.json
```

常用文件说明：

| 文件或目录 | 用途 |
|---|---|
| `_config.yml` | Hexo 根配置，管理站点标题、网址、语言、主题等 |
| `_config.butterfly.yml` | Butterfly 主题配置 |
| `source/_posts/` | 存放博客文章 |
| `source/about/` | 关于页面 |
| `source/images/` | 文章图片、横幅等图片资源 |
| `source/css/custom.css` | 自定义 CSS 样式 |
| `source/favicon.ico` | 网站图标 |
| `.github/workflows/pages.yml` | GitHub Pages 自动部署工作流 |
| `.gitignore` | 指定 Git 不应提交的文件 |
| `package.json` | 项目依赖清单 |
| `package-lock.json` | 锁定依赖版本，供自动部署使用 |

Butterfly 通过 npm 依赖安装，因此主题文件位于 `node_modules` 中；项目的 `themes/` 目录目前只有 `.gitkeep`，这是当前项目采用的方式，不需要为了部署而手动复制整个主题目录。

---

## 三、站点基础配置

### 1. 修改 Hexo 根配置

打开项目根目录下的 `_config.yml`，确认站点信息类似如下：

```yaml
title: "Isolde Veil"
subtitle: '技术 · 创作 · 游戏开发'
description: '记录技术学习、个人创作、游戏开发和生活'
keywords:
author: Isolde Veil
language: zh-CN
timezone: Asia/Tokyo

url: https://isoldeveil.github.io
root: /

theme: butterfly
```

说明：

- `title`：网站标题。
- `subtitle`：网站副标题。
- `description`：网站描述。
- `author`：文章作者信息。
- `language`：网站语言。
- `timezone`：时区。
- `url`：正式网站域名，不要保留 `localhost:4000`。
- `root`：当前 GitHub 用户站点使用 `/`。
- `theme`：使用 Butterfly 主题。

修改 YAML 配置时，注意缩进和冒号后的空格；不要在同一层级重复添加相同配置项。

### 2. Butterfly 主题配置

主题配置文件位于项目根目录：

```text
_config.butterfly.yml
```

本项目采用将主题默认配置复制到根目录的方式进行管理。这样可以保留主题默认配置，并在需要时只修改相关项目。

创建配置文件时使用：

```cmd
cd /d "E:\Isolde Veil's Blog\myblog"
if not exist _config.butterfly.yml copy "node_modules\hexo-theme-butterfly\_config.yml" "_config.butterfly.yml"
```

> 只有在配置文件不存在时才执行复制。已经存在的 `_config.butterfly.yml` 不要直接覆盖，否则会丢失已经完成的个性化设置。

目前导航菜单配置为：

```yaml
menu:
  首页: / || fas fa-home
  技术学习: /categories/技术学习/ || fas fa-code
  游戏开发: /categories/游戏开发/ || fas fa-gamepad
  个人创作: /categories/个人创作/ || fas fa-palette
  生活随笔: /categories/生活随笔/ || fas fa-coffee
  知识库: /categories/知识库/ || fas fa-book
  归档: /archives/ || fas fa-archive
  关于: /about/ || fas fa-user
```

修改主题配置时，应在现有配置中定位对应项目并修改，不要重复创建同名 YAML 键，也不要为了精简而删除不相关的默认配置。

### 3. 首页横幅图片

当前首页横幅图片放在：

```text
source/images/index-background.jpeg
```

主题配置中引用：

```yaml
index_img: /images/index-background.jpeg
```

为了让横幅完整显示，项目使用自定义 CSS：

文件位置：

```text
source/css/custom.css
```

内容：

```css
/* 首页横幅：完整显示背景图片 */
body[data-type="index"] #page-header {
    background-size: contain !important;
    background-position: center center !important;
    background-repeat: no-repeat !important;
    background-color: #07130c;
}
```

并在 `_config.butterfly.yml` 的 `inject.head` 中引入：

```yaml
inject:
  head:
    - <link rel="stylesheet" href="/css/custom.css">
```

如果 `inject.head` 已经存在，应把这一项合并进去，不要再创建第二个 `inject` 配置块。

### 4. 网站图标 Favicon

图标文件：

```text
source/favicon.ico
```

主题配置：

```yaml
favicon: /favicon.ico
```

修改图片或图标后，可以通过本地预览检查路径是否正确。

---

## 四、页面与文章管理

### 1. 创建关于页面

关于页面使用 Hexo Page，而不是普通文章。

创建命令：

```cmd
npx hexo new page about
```

生成位置：

```text
source/about/index.md
```

编辑该 Markdown 文件即可修改关于页面内容。

### 2. 创建新文章

使用以下命令：

```cmd
npx hexo new post "文章标题"
```

例如：

```cmd
npx hexo new post "Unity 6 项目初始化记录"
```

新文章会生成在：

```text
source/_posts/
```

文件名通常由 Hexo 根据标题生成。创建后打开对应的 `.md` 文件编辑即可。

### 3. 文章 Front-matter

文章开头的 Front-matter 用于设置标题、日期、分类、标签等信息。示例：

```yaml
---
title: Unity 6 项目初始化记录
date: 2026-11-23 10:00:00
categories:
  - 游戏开发
tags:
  - Unity
  - 游戏开发
  - 开发日志
---
```

本博客现有内容分类：

| 分类 | 内容方向 |
|---|---|
| 技术学习 | 编程、软件开发、工具使用和技术实践 |
| 游戏开发 | 游戏设计、开发日志、技术实现和项目进度 |
| 个人创作 | 世界观构建、创意设计和原创作品 |
| 生活随笔 | 日常记录、个人思考和灵感 |
| 知识库 | 教程、资料整理和长期有价值的知识 |

分类名称应与导航菜单中的分类路径保持一致。标签可以根据文章主题灵活增加。

### 4. 编辑、删除文章

- 修改旧文章：直接编辑 `source/_posts/` 中对应的 Markdown 文件。
- 删除文章：删除对应的 Markdown 文件，再提交并推送。
- 修改分类或标签：编辑文章 Front-matter。
- 修改文章标题：修改 Front-matter 中的 `title`；如需调整文件名，也要留意 Hexo 的文章链接变化。

删除或修改文章后，重新生成网站并部署，线上内容才会同步更新。

### 5. 在文章中引用图片

建议将图片统一放在：

```text
source/images/
```

例如图片文件：

```text
source/images/unity-project.jpg
```

在 Markdown 正文中引用：

```markdown
![Unity 项目截图](/images/unity-project.jpg)
```

注意：

- 文件名大小写应与引用路径一致。
- 图片必须实际放在 `source/images/` 下。
- 不要把本机绝对路径写进文章，例如 `E:\...`。
- 图片文件也需要通过 Git 提交并推送，线上才能显示。

---

## 五、本地预览与检查

每次发布前，建议先在本地检查。

### 1. 进入项目目录

```cmd
cd /d "E:\Isolde Veil's Blog\myblog"
```

### 2. 清理并启动本地服务器

```cmd
npx hexo clean
npx hexo server
```

终端出现本地服务地址后，在浏览器打开：

```text
http://localhost:4000/
```

### 3. 检查项目

建议逐项检查：

- 首页能否正常打开。
- 新文章是否显示在首页。
- 文章标题、正文、图片和排版是否正确。
- 分类和标签是否正确。
- 顶部导航菜单能否跳转。
- 关于页面能否正常打开。
- 首页横幅和 Favicon 是否正常。

本地服务器运行期间，在 CMD 按 `Ctrl + C` 可以停止服务。

---

## 六、Git 与 GitHub 仓库管理

### 1. GitHub 仓库

当前远程仓库：

```text
https://github.com/IsoldeVeil/isoldeveil.github.io.git
```

本地主要分支：

```text
main
```

检查远程仓库地址：

```cmd
git remote -v
```

检查当前分支和文件状态：

```cmd
git status
```

### 2. `.gitignore` 的作用

项目通过 `.gitignore` 排除依赖目录和 Hexo 生成文件。当前规则包括：

```gitignore
# Dependencies
node_modules/

# Hexo generated files
public/
.deploy_git/
db.json

# Cache and temporary files
*.log
.cache/
.tmp/

# Operating system files
.DS_Store
Thumbs.db
desktop.ini

# IDE settings
.idea/
.vscode/
```

这些目录或文件不需要作为博客源代码提交：

- `node_modules/`：可通过 npm 依赖清单重新安装。
- `public/`：Hexo 生成的网站文件，由 GitHub Actions 构建。
- `.deploy_git/`：部署过程中可能生成的临时 Git 目录。
- `db.json`：Hexo 本地缓存数据。

不要删除 `package.json` 或 `package-lock.json`，自动部署需要它们安装项目依赖。

### 3. GitHub 分支说明

当前仓库可能看到多个分支：

- `main`：博客源代码主分支，日常文章和配置修改都推送到这里。
- `dependabot/...`：Dependabot 检查 npm 依赖更新时创建的分支。

Dependabot 分支用于依赖更新审查，不是重复的网站分支。当前自动部署工作流只监听 `main`，因此普通的 Dependabot 分支不会直接发布到正式网站。

---

## 七、GitHub Pages 自动部署

### 1. GitHub Pages 设置

在 GitHub 仓库中进入：

```text
Settings → Pages → Build and deployment
```

将 `Source` 设置为：

```text
GitHub Actions
```

### 2. 自动部署工作流文件

工作流文件位置：

```text
.github/workflows/pages.yml
```

当前配置：

```yaml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v7

      - name: Setup Node.js
        uses: actions/setup-node@v7
        with:
          node-version: "26"
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Build Hexo site
        run: |
          npx hexo clean
          npx hexo generate

      - name: Configure GitHub Pages
        uses: actions/configure-pages@v5

      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v4
        with:
          path: ./public

  deploy:
    needs: build
    runs-on: ubuntu-latest

    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}

    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

工作流执行逻辑：

1. 当 `main` 分支收到新推送时触发。
2. GitHub Actions 检出仓库源代码。
3. 安装 Node.js 26。
4. 执行 `npm ci` 安装锁定的项目依赖。
5. 执行 `npx hexo clean` 和 `npx hexo generate`。
6. 将 `public/` 上传为 Pages artifact。
7. 将 artifact 发布到 GitHub Pages。

也可以在 GitHub Actions 页面手动触发 `workflow_dispatch` 工作流。

> Actions 的版本可能随时间更新。修改工作流前，建议核对相应 GitHub Actions 的官方版本和兼容性，不要随意把不同教程中的版本号拼在一起。

### 3. 查看部署结果

工作流记录：

```text
https://github.com/IsoldeVeil/isoldeveil.github.io/actions
```

正式网站：

```text
https://isoldeveil.github.io/
```

在 Actions 页面打开最新一次 `Deploy Hexo to GitHub Pages` 运行记录：

- 绿色勾号：工作流成功。
- 红色叉号：工作流失败，需要打开失败步骤查看日志。
- 正在运行：等待构建和部署结束。

部署成功后访问正式网站检查更新。如果浏览器仍显示旧内容，可以稍等片刻并使用 `Ctrl + F5` 强制刷新。

---

## 八、以后发布新文章的标准流程

这是日常最常用的部分。完成首次部署后，今后不需要重新配置 GitHub Pages，也不需要手动上传 `public/`。

### 第一步：进入项目目录

```cmd
cd /d "E:\Isolde Veil's Blog\myblog"
```

### 第二步：创建文章

```cmd
npx hexo new post "文章标题"
```

### 第三步：编辑文章

在 `source/_posts/` 中找到新生成的 Markdown 文件，填写 Front-matter 和正文。

### 第四步：本地预览

```cmd
npx hexo clean
npx hexo server
```

打开：

```text
http://localhost:4000/
```

确认文章、图片、分类和排版正常后，按 `Ctrl + C` 停止服务器。

### 第五步：提交并推送

```cmd
git add .
git status
git commit -m "Add new blog post"
git push
```

建议将提交说明写得具体一些，例如：

```cmd
git commit -m "Add Unity 6 project setup post"
```

如果本次修改包含多篇文章或主题配置，也可以使用能够概括本次工作的提交说明。

### 第六步：等待自动部署

打开：

```text
https://github.com/IsoldeVeil/isoldeveil.github.io/actions
```

确认最新工作流成功后，访问：

```text
https://isoldeveil.github.io/
```

检查正式网站。

### 日常发布流程速查

```text
创建文章
   ↓
编辑 Markdown
   ↓
本地预览
   ↓
检查文章与图片
   ↓
git add .
   ↓
git commit -m "更新说明"
   ↓
git push
   ↓
GitHub Actions 自动构建和部署
   ↓
检查正式网站
```

---

## 九、可选：创建一键推送脚本

如果希望减少每次输入命令的操作，可以在项目根目录创建：

```text
deploy.bat
```

文件内容：

```bat
@echo off
chcp 65001 >nul
cd /d "E:\Isolde Veil's Blog\myblog"

echo ===== 检查当前修改 =====
git status

echo.
set /p msg=请输入本次更新说明：

if "%msg%"=="" (
    echo 更新说明不能为空。
    pause
    exit /b 1
)

echo.
echo ===== 添加修改 =====
git add .

echo.
echo ===== 创建提交 =====
git commit -m "%msg%"

if errorlevel 1 (
    echo.
    echo 提交未成功，请检查上方提示。
    pause
    exit /b 1
)

echo.
echo ===== 推送到 GitHub =====
git push

if errorlevel 1 (
    echo.
    echo 推送失败，请检查网络或 Git 提示。
    pause
    exit /b 1
)

echo.
echo ===== 推送完成 =====
echo 请前往 GitHub Actions 查看自动部署结果。
pause
```

使用方法：

1. 先完成文章编辑和本地预览。
2. 双击 `deploy.bat`。
3. 输入本次更新说明。
4. 等待 Git 提交和推送结束。
5. 到 GitHub Actions 查看部署结果。

> 一键脚本只负责 Git 添加、提交和推送，不会替代文章检查，也不会保证远程部署一定成功。每次仍要检查 Actions 结果。

---

## 十、常见问题与处理

### 1. 本地网站正常，GitHub Pages 没有更新

检查顺序：

1. 是否执行了 `git push`。
2. GitHub 仓库的 `main` 分支是否出现最新提交。
3. Actions 页面最新工作流是否成功。
4. 如果失败，打开失败步骤查看错误日志。
5. 如果部署成功但浏览器仍显示旧内容，等待片刻并强制刷新。

### 2. `git push` 提示没有可推送内容

先执行：

```cmd
git status
```

如果显示：

```text
nothing to commit, working tree clean
```

说明当前没有新的本地修改需要提交。

### 3. `git commit` 提示没有内容可提交

通常表示没有新的已暂存修改。先检查：

```cmd
git status
```

如果确实没有修改，不需要强行创建提交。

### 4. 文章图片本地能显示，线上不显示

检查：

- 图片是否位于 `source/images/`。
- Markdown 是否使用 `/images/文件名.jpg` 这样的站点路径。
- 图片是否已经执行 `git add` 并推送。
- 文件名大小写是否一致。

### 5. 修改主题配置后网站样式异常

先检查 `_config.butterfly.yml` 是否存在重复键、缩进错误，以及 `inject` 等配置是否被重复定义。

可以在本地执行：

```cmd
npx hexo clean
npx hexo server
```

确认问题后再提交推送。

### 6. Dependabot 创建了新分支

这是依赖更新机制的一部分。先查看对应 Pull Request 的更新内容和检查结果，再决定是否合并。不要把 Dependabot 分支当作正式博客发布分支；正式内容仍以 `main` 为准。

---

## 十一、维护原则与注意事项

1. **始终在项目根目录执行命令。** 本项目路径为 `E:\Isolde Veil's Blog\myblog`。
2. **文章和页面使用 Markdown 管理。** 不需要直接编辑 Hexo 生成的 `public/` 文件。
3. **不要提交 `node_modules/` 和 `public/`。** 它们已在 `.gitignore` 中排除。
4. **保留 `package.json` 和 `package-lock.json`。** 自动部署需要依赖清单。
5. **修改 YAML 时注意缩进和重复键。** 尽量只修改目标配置项，保留其他默认设置。
6. **重要修改先本地预览。** 尤其是主题配置、导航、CSS 和图片路径。
7. **推送后检查 GitHub Actions。** `git push` 成功不等于网站构建一定成功。
8. **不要把 GitHub 登录凭据、Personal Access Token 或其他密钥写入文章、配置文件或仓库。**
9. **定期检查 Dependabot 的依赖更新 PR。** 合并前确认兼容性，避免未经检查就升级核心依赖。

---

## 十二、常用命令备忘

| 目的 | 命令 |
|---|---|
| 进入项目目录 | `cd /d "E:\Isolde Veil's Blog\myblog"` |
| 查看环境版本 | `npx hexo version` |
| 创建文章 | `npx hexo new post "文章标题"` |
| 创建页面 | `npx hexo new page 页面名称` |
| 清理生成文件 | `npx hexo clean` |
| 生成网站 | `npx hexo generate` |
| 启动本地预览 | `npx hexo server` |
| 查看 Git 状态 | `git status` |
| 添加修改 | `git add .` |
| 创建提交 | `git commit -m "更新说明"` |
| 推送到 GitHub | `git push` |
| 查看远程仓库 | `git remote -v` |

## 结语

博客的首次搭建和 GitHub Pages 自动部署已经完成。后续维护的核心工作就是持续写作、在本地检查，并通过 Git 将修改推送到 `main` 分支。

只要 GitHub Actions 的构建与部署流程正常运行，正式网站就会随着每次成功推送自动更新。

博客不仅是文章发布页面，也可以逐渐成为技术学习、独立游戏开发、个人创作和知识整理的长期记录空间。
