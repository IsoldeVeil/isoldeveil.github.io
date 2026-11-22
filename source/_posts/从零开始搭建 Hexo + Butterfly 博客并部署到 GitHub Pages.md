---
title: 从零开始搭建 Hexo + Butterfly 博客并部署到 GitHub Pages
date: 2026-11-23 00:00:00
description: 面向 Windows 用户的完整搭建流程：环境准备、Hexo 项目、Butterfly 主题配置、GitHub Pages 自动部署与日常发文。
cover: /images/post-hexo-cover.jpg
top_img: /images/post-hexo-cover.jpg
categories:
  - 技术学习
tags:
  - Hexo
  - Butterfly
  - GitHub Pages
  - Git
  - 网站搭建
---

> 本文面向第一次搭建个人博客的 Windows 用户。按照章节顺序操作，可以从准备环境、创建 Hexo 项目、安装 Butterfly 主题，一直完成 GitHub Pages 自动部署，并掌握以后如何发布新文章。
>
> 本文中的命令以 Windows CMD 为例。PowerShell 的部分命令写法可能不同。

---

## 一、最终效果与工作原理

完成后，你会拥有：

- 一个在自己电脑上编辑和预览的 Hexo 博客；
- Butterfly 主题和可自定义的导航菜单；
- 一个用于保存博客源代码的 GitHub 仓库；
- 一个由 GitHub Pages 托管的公开网站；
- 一套 GitHub Actions 自动部署流程。

### 网站更新是怎样完成的？

```text
在电脑上编辑 Markdown 文章
          ↓
本地启动 Hexo，检查文章效果
          ↓
Git 将修改提交到本地仓库
          ↓
git push 推送到 GitHub
          ↓
GitHub Actions 自动安装依赖并生成网页
          ↓
GitHub Pages 发布生成的网站
          ↓
读者访问你的博客
```

日常更新时，通常只需要编辑文章并执行 Git 提交、推送，不需要手动上传生成后的 `public` 文件夹。

## 二、开始前需要准备什么

### 1. 注册或准备 GitHub 账号

打开 GitHub：

https://github.com/

注册并登录账号。记下自己的用户名，后面创建仓库和配置网站地址时会用到。

### 2. 准备软件

本教程需要：

| 软件 | 用途 | 官方网站 |
|---|---|---|
| Node.js | 运行 Hexo | https://nodejs.org/ |
| Git | 管理版本并推送代码 | https://git-scm.com/ |
| 代码编辑器 | 编辑配置和 Markdown | https://code.visualstudio.com/ |

也可以使用你熟悉的 Markdown 编辑器，例如 Obsidian 或 Typora 来写文章。

建议安装 Node.js 的 LTS 版本。安装程序通常可以使用默认选项。Git 安装时也可以先使用默认选项。

### 3. 规划项目目录

选择一个方便长期保存的位置。例如：

```text
D:\MyBlog
```

本教程后续统一用这个路径举例。如果你使用其他路径，请将命令中的 `D:\MyBlog` 替换成自己的实际路径。

> 路径尽量简单，避免特殊字符。若路径包含空格，CMD 命令中的路径要用英文双引号括起来。

## 三、安装并检查 Node.js 和 Git

安装完成后，打开 Windows CMD：

- 按 `Win + R`；
- 输入 `cmd`；
- 按回车。

分别执行：

```cmd
node -v
npm -v
git --version
```

如果成功安装，会显示各自的版本号。版本号不必与本文示例完全相同。

如果提示“不是内部或外部命令”，通常是软件未安装成功，或安装目录没有加入系统 PATH。先重新打开 CMD；仍然不行，再检查安装情况。

### 配置 Git 提交身份

将下面的名称和邮箱替换成你自己的信息：

```cmd
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的邮箱"
```

检查配置：

```cmd
git config --global --list
```

邮箱建议使用你希望关联 Git 提交的邮箱。不要把密码、Personal Access Token 或其他密钥写进配置文件或文章。

## 四、创建 Hexo 博客项目

### 1. 安装 Hexo CLI

在 CMD 执行：

```cmd
npm install -g hexo-cli
```

安装完成后检查：

```cmd
hexo -v
```

### 2. 创建项目

先进入准备存放博客的目录。例如：

```cmd
cd /d D:\
```

创建名为 `MyBlog` 的 Hexo 项目：

```cmd
hexo init MyBlog
```

进入项目目录：

```cmd
cd /d D:\MyBlog
```

安装项目依赖：

```cmd
npm install
```

> 如果你选择的目录或项目名不同，请将上面的 `D:\MyBlog` 替换为自己的实际路径。之后所有 Hexo 和 Git 命令都应在项目根目录执行。

### 3. 启动初始博客

```cmd
npx hexo server
```

打开浏览器访问：

http://localhost:4000/

如果出现 Hexo 初始页面，说明项目创建成功。

回到 CMD，按 `Ctrl + C` 停止本地服务器。

## 五、安装 Butterfly 主题

### 1. 安装主题包

在项目根目录执行：

```cmd
npm install hexo-theme-butterfly
```

### 2. 安装主题需要的渲染器

```cmd
npm install hexo-renderer-pug hexo-renderer-stylus
```

### 3. 启用主题

打开项目根目录的 `_config.yml`，找到：

```yaml
theme: landscape
```

改成：

```yaml
theme: butterfly
```

如果原来没有 `theme` 配置项，则在根配置文件合适位置添加一次即可，不要重复添加。

### 4. 复制主题配置到项目根目录

为了方便管理主题设置，将主题默认配置复制到项目根目录，并命名为 `_config.butterfly.yml`。

CMD 执行：

```cmd
if not exist _config.butterfly.yml copy "node_modules\hexo-theme-butterfly\_config.yml" "_config.butterfly.yml"
```

如果文件已经存在，不要再次覆盖。覆盖可能会丢失你已经做过的自定义配置。

### 5. 检查主题是否生效

```cmd
npx hexo clean
npx hexo server
```

访问：

http://localhost:4000/

如果页面已经使用 Butterfly 主题，说明安装成功。按 `Ctrl + C` 停止服务器。

## 六、配置博客基本信息

打开项目根目录的 `_config.yml`。找到对应配置项并修改为自己的信息：

```yaml
title: "你的博客名称"
subtitle: '你的博客副标题'
description: '用一句话介绍这个博客'
keywords:
author: 你的作者名
language: zh-CN
timezone: Asia/Shanghai

url: http://localhost:4000
root: /

theme: butterfly
```

字段说明：

| 配置项 | 作用 |
|---|---|
| `title` | 网站标题 |
| `subtitle` | 网站副标题 |
| `description` | 网站描述 |
| `author` | 作者名称 |
| `language` | 网站语言 |
| `timezone` | 时区 |
| `url` | 网站地址；部署前需要改成正式网址 |
| `root` | 网站根路径 |
| `theme` | 当前启用的主题 |

在正式部署前，`url` 暂时可以保留本地地址。完成 GitHub Pages 设置时，再替换成自己的正式网站地址。

> YAML 对缩进和格式比较敏感。请修改已有字段，不要复制粘贴后留下两个同名的 `title`、`url` 或 `theme`。

## 七、配置 Butterfly 导航和外观

打开项目根目录的：

```text
_config.butterfly.yml
```

### 1. 设置导航菜单

找到 `menu` 配置项。可以按自己的栏目修改。以下为示例：

```yaml
menu:
  首页: / || fas fa-home
  技术文章: /categories/技术文章/ || fas fa-code
  游戏开发: /categories/游戏开发/ || fas fa-gamepad
  生活随笔: /categories/生活随笔/ || fas fa-coffee
  归档: /archives/ || fas fa-archive
  关于: /about/ || fas fa-user
```

菜单中的分类名称需要与文章 Front-matter 中使用的分类一致。若你修改了栏目名称，也要同步检查文章分类。

### 2. 保留主题默认配置

Butterfly 默认配置包含很多功能选项。初次配置时，不建议把整个文件删减成少数几项。

推荐做法：

1. 保留默认配置文件中的其他内容；
2. 搜索需要修改的配置名称；
3. 只修改对应值；
4. 避免重复定义同名 YAML 键；
5. 修改后启动本地服务器检查效果。

### 3. 放置首页横幅图片

将图片放入：

```text
source/images/
```

例如：

```text
source/images/banner.jpg
```

在 `_config.butterfly.yml` 中设置：

```yaml
index_img: /images/banner.jpg
```

如果不想使用横幅图片，可以根据主题配置关闭或清空相应选项。

### 4. 设置 Favicon 网站图标

将图标放入：

```text
source/favicon.ico
```

主题配置中设置：

```yaml
favicon: /favicon.ico
```

### 5. 自定义 CSS（可选）

如果需要覆盖主题样式，可以创建：

```text
source/css/custom.css
```

然后在主题配置的 `inject.head` 中引入：

```yaml
inject:
  head:
    - <link rel="stylesheet" href="/css/custom.css">
```

如果 `inject` 或 `inject.head` 已经存在，应合并配置，不要重复创建同名键。

例如，想让首页横幅完整显示，可以在 `custom.css` 中写：

```css
body[data-type="index"] #page-header {
    background-size: contain !important;
    background-position: center center !important;
    background-repeat: no-repeat !important;
}
```

CSS 属于可选的进阶设置。第一次搭建时可以先跳过，等基础网站运行正常后再调整。

## 八、创建关于页面和第一篇文章

### 1. 创建关于页面

在项目根目录执行：

```cmd
npx hexo new page about
```

生成文件：

```text
source/about/index.md
```

打开并编辑它，例如：

```markdown
---
title: 关于
date: 2026-01-01 12:00:00
---

# 关于我

这里填写作者介绍、博客用途和联系方式等内容。
```

### 2. 创建第一篇文章

执行：

```cmd
npx hexo new post "我的第一篇文章"
```

Hexo 会在 `source/_posts/` 中创建 Markdown 文件。

### 3. 编辑文章 Front-matter

文章开头通常包含 YAML 格式的元信息：

```yaml
---
title: 我的第一篇文章
date: 2026-01-01 12:00:00
categories:
  - 技术文章
tags:
  - Hexo
  - 博客
---
```

在下面编写 Markdown 正文：

```markdown
# 我的第一篇文章

这是正文内容。

## 二级标题

这里可以继续写内容。
```

分类决定文章归属，标签用于描述文章主题。分类名称应与导航菜单中的分类归档路径对应。

### 4. 添加文章图片

将图片放入：

```text
source/images/
```

文章中使用网站相对路径引用：

```markdown
![图片说明](/images/example.jpg)
```

不要使用电脑本地绝对路径，例如 `D:\MyBlog\...`，因为读者访问不到你的本地文件。

## 九、本地预览与检查

每次发布前，建议先在本地预览。

### 1. 清理旧生成文件

```cmd
npx hexo clean
```

### 2. 启动本地服务器

```cmd
npx hexo server
```

### 3. 打开本地网站

浏览器访问：

http://localhost:4000/

检查：

- 首页是否正常显示；
- 文章是否出现；
- 标题、正文、代码块和图片是否正常；
- 分类、标签和导航是否能正确跳转；
- 关于页面是否正常；
- 手机端布局是否可用。

检查结束后，回到 CMD 按 `Ctrl + C` 停止服务器。

如果本地页面异常，先修复后再发布，不要急着推送到正式网站。

## 十、创建 GitHub 仓库并上传源代码

### 1. 创建 GitHub 仓库

登录 GitHub，点击右上角 `+`，选择 `New repository`。

如果希望网站地址采用：

```text
https://你的用户名.github.io/
```

仓库名称必须填写：

```text
你的用户名.github.io
```

例如 GitHub 用户名是 `example-user`，仓库名就应为：

```text
example-user.github.io
```

建议仓库设置为 Public，便于按 GitHub Pages 的公开站点方式使用。创建仓库时可以先不要勾选自动生成 README、License 或 `.gitignore`，避免本地已有项目与远程初始化文件产生冲突。

点击 `Create repository` 创建仓库。

### 2. 创建 `.gitignore`

在 Hexo 项目根目录创建文件：

```text
.gitignore
```

写入：

```gitignore
node_modules/
public/
.deploy_git/
db.json
*.log
.cache/
.tmp/
.DS_Store
Thumbs.db
desktop.ini
.idea/
.vscode/
```

作用：不把依赖目录、生成的网站文件、缓存和本机编辑器配置提交到源代码仓库。

### 3. 初始化 Git

在项目根目录执行：

```cmd
git init
git branch -M main
```

### 4. 配置 Git 身份（如果第三节尚未配置）

```cmd
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的邮箱"
```

### 5. 添加远程仓库

将下面地址替换成你刚创建的仓库地址：

```cmd
git remote add origin https://github.com/你的用户名/你的用户名.github.io.git
```

检查：

```cmd
git remote -v
```

### 6. 首次提交并推送

```cmd
git add .
git status
git commit -m "Initial commit: Hexo blog"
git push -u origin main
```

首次推送时，Git 可能会提示在浏览器中完成 GitHub 身份验证。按照浏览器提示登录并授权即可。不要在聊天或仓库中发送密码或令牌。

推送成功后，刷新 GitHub 仓库页面，应能看到项目文件。

## 十一、配置 GitHub Pages 自动部署

### 1. 在 GitHub 设置 Pages 来源

进入自己的 GitHub 仓库：

```text
Settings → Pages → Build and deployment
```

在 `Source` 中选择：

```text
GitHub Actions
```

### 2. 修改正式网站地址

回到本地项目，打开根目录 `_config.yml`。

假设仓库名为 `你的用户名.github.io`，正式网址就是：

```text
https://你的用户名.github.io
```

修改为：

```yaml
url: https://你的用户名.github.io
root: /
```

注意：这是 GitHub 用户站点仓库的情况。如果你使用普通项目仓库（不是 `用户名.github.io`），网站通常会部署在项目子路径下，`url` 和 `root` 需要按实际 Pages 地址另行设置，不能直接照抄这里的 `/` 配置。

### 3. 创建 Actions 工作流文件

在项目中创建目录和文件：

```text
.github/workflows/pages.yml
```

Windows CMD 创建目录：

```cmd
mkdir .github\workflows
```

用文本编辑器创建 `pages.yml`，写入以下工作流：

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

> 说明：上面是一份可参考的工作流示例，采用本项目部署时使用的 Action 版本与 Node.js 版本。Action 版本可能更新；如果新建项目时运行失败，应先查看 Actions 日志和相应官方仓库的兼容说明，不要盲目替换版本号。`npm ci` 需要项目中存在并提交 `package-lock.json`。

### 4. 提交部署配置

在项目根目录执行：

```cmd
git add _config.yml .github/workflows/pages.yml
git status
git commit -m "Configure GitHub Pages deployment"
git push
```

### 5. 查看自动部署结果

打开仓库的 `Actions` 页面，点击最新的 `Deploy Hexo to GitHub Pages` 工作流。

如果构建和部署均成功，会显示绿色勾号。

然后访问：

```text
https://你的用户名.github.io/
```

首次部署可能需要等待一段时间。如果工作流失败，点开失败的任务和步骤，查看具体错误信息。

## 十二、发布新文章和更新网站

首次部署成功后，日常更新不需要重新配置 Pages，也不需要手动上传 `public`。

### 1. 创建文章

进入项目根目录：

```cmd
cd /d D:\MyBlog
```

创建文章：

```cmd
npx hexo new post "新文章标题"
```

### 2. 编辑 Markdown

打开 `source/_posts/` 中新生成的 `.md` 文件，编辑标题、日期、分类、标签和正文。

图片放入 `source/images/`，使用 `/images/图片文件名.jpg` 的形式引用。

### 3. 本地检查

```cmd
npx hexo clean
npx hexo server
```

访问 `http://localhost:4000/` 检查效果，完成后按 `Ctrl + C` 关闭服务器。

### 4. 提交并推送

```cmd
git status
git add .
git status
git commit -m "Add new blog post"
git push
```

把提交说明换成能概括本次修改的内容，例如：

```cmd
git commit -m "Add Unity development log"
```

如果 `git commit` 提示没有内容可提交，先运行 `git status` 检查是否确实有文件修改。

### 5. 等待 GitHub 自动发布

推送成功后，GitHub Actions 会自动执行构建和部署。

检查：

```text
https://github.com/你的用户名/你的用户名.github.io/actions
```

工作流成功后，打开正式网站：

```text
https://你的用户名.github.io/
```

如果页面暂时没有变化，稍等片刻后按 `Ctrl + F5` 强制刷新。

### 6. 修改旧文章、删除文章或调整配置

- 修改旧文章：直接编辑 `source/_posts/` 中原有的 Markdown 文件。
- 删除文章：删除对应 `.md` 文件。
- 修改分类或标签：编辑文章顶部的 Front-matter。
- 修改主题：编辑 `_config.butterfly.yml`。
- 修改站点信息：编辑 `_config.yml`。
- 增加图片：放入 `source/images/` 并在文章中引用。

完成修改后，都执行本节的本地检查、Git 提交和推送步骤。

## 十三、常见问题排查

### 1. `node`、`npm` 或 `git` 不是内部或外部命令

检查软件是否安装；安装后重新打开 CMD。如果仍然无法识别，检查系统 PATH 环境变量。

### 2. `npx hexo` 找不到命令或依赖

先确认当前目录是 Hexo 项目根目录，并检查 `package.json` 是否存在。然后执行：

```cmd
npm install
```

再运行：

```cmd
npx hexo version
```

### 3. 本地预览正常，但线上没有更新

按顺序检查：

1. 是否执行了 `git push`；
2. GitHub 仓库的 `main` 分支是否出现最新提交；
3. Actions 页面最新工作流是否成功；
4. 失败时查看具体任务日志；
5. 部署成功但浏览器仍显示旧页面时，稍等并强制刷新。

### 4. 图片本地显示，线上不显示

检查图片是否在 `source/images/` 中、引用路径是否以 `/images/` 开头、文件名大小写是否一致，以及图片是否已经提交并推送。

### 5. Git 提示没有内容可提交

执行：

```cmd
git status
```

如果显示 `nothing to commit, working tree clean`，说明没有未提交的修改，不需要强行提交。

### 6. Dependabot 创建了其他分支

Dependabot 可能为 npm 依赖更新创建分支和 Pull Request。这些不是重复的网站分支。日常文章和配置仍然提交到 `main`。查看依赖更新 PR 后，再决定是否合并。

### 7. 工作流提示找不到 `package-lock.json`

确认项目根目录中是否存在 `package-lock.json`，并且它已经提交到 GitHub。若项目尚未生成锁文件，在项目根目录运行：

```cmd
npm install
```

然后提交 `package.json` 和 `package-lock.json`。

## 十四、日常维护命令速查

以下命令都应在 Hexo 项目根目录执行：

| 操作 | 命令 |
|---|---|
| 查看环境版本 | `node -v`、`npm -v`、`git --version` |
| 查看 Hexo 版本 | `npx hexo version` |
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

### 每次更新博客只需记住这条流程

```text
创建或修改文章
      ↓
本地预览检查
      ↓
git add .
      ↓
git commit -m "本次更新说明"
      ↓
git push
      ↓
检查 GitHub Actions
      ↓
打开正式网站确认更新
```

## 结语

完成以上步骤后，你就拥有了一套可持续维护的 Hexo + Butterfly 博客。

第一次搭建需要完成环境安装、项目创建、主题配置、GitHub 仓库和自动部署设置；之后的日常工作会简单很多：写文章、预览、提交、推送，网站就会自动更新。

建议保留本教程，并根据自己实际使用的 Node.js、Hexo、主题和 GitHub Actions 版本适时更新。遇到问题时，优先检查本地命令输出和 GitHub Actions 日志，再针对具体错误处理。

