---
title: Astro Cloudflare 部署完整流程
published: 2026-03-13
description: 5分钟快速上手！本文详细介绍如何将 Astro 静态站点部署到 Cloudflare Workers & Pages，包括配置构建设置和常见问题解答。
tags: ["Astro", "Cloudflare", "部署", "教程"]
category: 技术教程
draft: false
language: zh-CN
---

> 参考：https://eastondev.com/blog/zh/posts/dev/20251203-astro-cloudflare-deploy/

# Astro Cloudflare 部署完整流程

静态站点部署：5分钟快速上手

如果你的 Astro 项目是纯静态的（博客、文档站、作品集这类），部署真的超级简单。我按步骤带你走一遍。

## 前提条件

- 项目已经推送到 GitHub
- 有 Cloudflare 账号（没有的话注册一个，免费 的）

## 具体步骤

### 1. 登录 Cloudflare Dashboard

打开 dash.cloudflare.com，进入左侧菜单的 Workers & Pages。

### 2. 创建新项目

点击右上角 Create application → 选择 Pages → Connect to Git。

### 3. 连接 GitHub 仓库

选择你的 Astro 项目仓库。第一次可能需要授权 Cloudflare 访问 GitHub，按提示操作就行。

### 4. 配置构建设置

这一步很关键，填错了就部署失败。设置如下：

- **Framework preset**：选择 Astro（会自动填充命令）
- **Build command**：`npm run build`
- **Build output directory**：`dist`
- **Root directory**：如果项目在仓库根目录就留空，在子目录就填路径

### 5. 点击部署

点 Save and Deploy，等几分钟。Cloudflare 会自动拉代码、安装依赖、构建、部署。

构建成功后，你会得到一个 `your-project.pages.dev` 的域名，直接访问就能看到网站了。每次你推送代码到 GitHub，Cloudflare 会自动触发构建，超方便。

## 常见问题

### 构建失败，提示 Node.js 版本不对

在项目根目录加个 `.nvmrc` 文件或者 `.node-version`，写上 18 或 20。Cloudflare 会自动识别。

### 页面显示 404

检查 Build output directory 是不是 dist。有些配置可能是 public 或 build，根据你的 astro.config.mjs 调整。

### 构建超时

可能是依赖太多或网络问题，重新部署试试，一般第二次就好了。

## 绑定自定义域名（可选）

部署成功后，在项目设置里找到 Custom domains，点 Set up a custom domain，输入你的域名（比如 blog.yourdomain.com）。Cloudflare 会给你一个 CNAME 记录，去你的 DNS 服务商那里添加就行。如果域名本身就在 Cloudflare DNS，会自动配置，更简单。
