---
title: Hexo + GitHub Action + GitHub Page博客搭建
date: 2025-03-15 23:04:41
tags:
---
# Hexo + GitHub Action + GitHub Page博客搭建

1. 环境要求：Node.js版本20.19.0+、Git版本2.x+
2. 全局安装Hexo CLI

```bash
npm install -g hexo-cli
hexo -v  # 验证安装
```

1. 创建博客项目

```bash
hexo init myBlog
cd myBlog
npm install
```

执行完之后即可 `hexo server`  或 `npm run server` 本地查看博客页面

# Hexo主题更换

以Cactus为例，在根目录下

```bash
git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
```

然后在_config.yml文件中配置

```yaml
# theme: landscape
theme: cactus
```

更多配置参考原项目：https://github.com/probberechts/hexo-theme-cactus

更多主题：[https://hexo.io/themes](https://hexo.io/themes/)

推荐主题：[https://adminlte.io/blog/hexo-themes](https://adminlte.io/blog/hexo-themes/)

# GitHub仓库创建

- 仓库名必须为 ：`你的GitHub用户名.github.io` ，例如yourname.github.io
- 仓库设置为公开

# 上传GitHub仓库

在本地博客项目中：

```bash
# 初始化git
git init
# 设置远程仓库地址
git remote add origin git@github.com:yourname/youname.github.io.git
# 提交代码
git add .
git commit -m 'init myBlog'
git push -u origin main  # 注意分支名
```

# **配置 GitHub Pages 使用 Actions**

- 登录 GitHub，进入你刚创建的仓库 `你的用户名.github.io`。
- 点击 `Settings` → 左侧菜单的 `Pages`。
- 在 `Build and deployment` 下的 `Source` 选项中，选择 `GitHub Actions`

# **创建 GitHub Actions 工作流文件**

在博客项目中按照目录创建.github/workflows/pages.yml文件

```yaml
name: Pages
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "22.20.0"  # 设置你对应的node版本
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

将此文件提交GitHub

```bash
git add .
git commit -m "Add GitHub Actions workflow"
git push
```

推送完成后，在GitHub仓库的 `Actions` 标签页可以看到工作流的运行状态。等待几分钟，待工作流成功运行后，访问 `https://你的用户名.github.io` 即可看到你的博客
