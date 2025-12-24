# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个基于 Next.js 16 的现代化个人博客系统,采用 GitHub App 作为内容管理后端。用户可以通过前端界面直接编辑内容,所有更改会自动提交到 GitHub 仓库并触发部署。

### 核心特性
- **GitHub App 集成**: 使用 GitHub App 的 Installation Token 进行身份验证和内容管理
- **前端内容管理**: 无需传统 CMS,所有内容编辑都在前端完成
- **Markdown 渲染**: 使用 marked + shiki 实现代码高亮的 Markdown 渲染
- **动画效果**: 使用 motion (framer-motion) 库实现流畅的页面动画
- **状态管理**: 使用 zustand 进行轻量级状态管理
- **Turbopack**: 使用 Next.js 的 Turbopack 进行快速构建

## 开发命令

```bash
# 安装依赖
pnpm i

# 启动开发服务器 (运行在端口 2025)
pnpm dev

# 生成 SVG 索引文件
pnpm svg

# 构建生产版本
pnpm build

# 启动生产服务器
pnpm start

# 代码格式化
pnpm format
```

## 项目架构

### 1. GitHub 认证流程 (src/lib/auth.ts + src/lib/github-client.ts)

完整的 GitHub App 认证流程:
1. 使用私钥(Private Key)和 App ID 签发 JWT
2. 通过 JWT 获取仓库的 Installation ID
3. 使用 Installation ID 创建 Installation Token
4. Token 会被缓存到 sessionStorage 中复用

关键函数:
- `signAppJwt()` - 使用 jsrsasign 库签发 RS256 JWT
- `getInstallationId()` - 获取 GitHub App 在特定仓库的安装 ID
- `createInstallationToken()` - 创建访问令牌
- `getAuthToken()` - 统一的认证入口,自动处理缓存和签发

### 2. 内容提交机制

项目使用 GitHub Git Data API 的批量提交方式:
- `getRef()` - 获取当前分支的最新 commit SHA
- `createTree()` - 创建包含多个文件变更的 Git tree
- `createCommit()` - 创建新的 commit 对象
- `updateRef()` - 更新分支引用到新 commit

这种方式可以在一次操作中修改多个文件,比逐个文件调用 `putFile()` 更高效。

### 3. 内容类型结构

博客系统支持多种内容类型,每种都有对应的 JSON 索引文件:
- **博客文章** (`src/app/blog/`) - 主要内容,支持 Markdown 编辑
- **分享链接** (`src/app/share/list.json`) - 外部链接收藏
- **项目展示** (`src/app/projects/list.json`) - 个人项目列表
- **图片相册** (`src/app/pictures/list.json`) - 图片展示
- **博主列表** (`src/app/bloggers/list.json`) - 友链
- **关于页面** (`src/app/about/list.json`) - 个人简介

所有内容都通过 GitHub API 推送到仓库的对应路径。

### 4. 路由组织

使用 Next.js App Router:
- `(home)` - 路由组,首页相关组件
- `write/[slug]` - 动态路由,博客编辑页面
- `blog/[id]` - 动态路由,博客详情页面

### 5. Markdown 渲染系统 (src/lib/markdown-renderer.ts)

使用 marked 解析 Markdown,shiki 进行语法高亮:
- 自定义 renderer 处理标题、代码块、图片等
- 自动生成文章目录 (TOC)
- 支持代码块语言高亮
- 图片自动添加 lazy loading

## 配置说明

### 环境变量

项目需要配置以下环境变量(或直接修改 `src/consts.ts`):
- `NEXT_PUBLIC_GITHUB_OWNER` - GitHub 用户名
- `NEXT_PUBLIC_GITHUB_REPO` - 仓库名
- `NEXT_PUBLIC_GITHUB_BRANCH` - 分支名(默认 main)
- `NEXT_PUBLIC_GITHUB_APP_ID` - GitHub App ID

### GitHub App 权限要求

必须授予以下权限:
- **Contents**: Read and write (读写仓库内容)

### Next.js 配置要点 (next.config.ts)

- 使用 `@svgr/webpack` 将 SVG 转换为 React 组件
- 启用 React Compiler (实验性特性)
- Turbopack 配置用于开发环境加速
- 配置了语言路径重定向 (/zh, /en → /)

## 开发注意事项

### 1. 编辑流程
当用户点击编辑按钮时,会提示输入 Private Key(仅本地 sessionStorage,不会上传)。认证成功后可以编辑内容并提交。

### 2. 部署触发
内容提交到 GitHub 后,需要等待 Vercel/其他平台的自动部署完成才能看到更新。

### 3. SVG 图标管理
使用 `pnpm svg` 生成 SVG 索引文件 (`src/svgs/index.ts`),确保新增的 SVG 图标能被正确导入。

### 4. TypeScript 路径别名
使用 `@/*` 指向 `src/*`,在导入时使用绝对路径而非相对路径。

### 5. 首页卡片自定义
首页由多个 Card 组件组成 (`src/app/(home)/*-card.tsx`),可以根据需要修改或移除特定卡片。

### 6. 移除特定功能
例如移除 Liquid Grass 动画:
- 删除 `src/layout/index.tsx` 中的 `LiquidGrass` 导入和使用
- 删除 `src/components/liquid-grass` 目录

## 技术栈

- **框架**: Next.js 16 (App Router, React 19)
- **样式**: Tailwind CSS 4
- **动画**: Motion (Framer Motion)
- **状态管理**: Zustand
- **Markdown**: marked + shiki
- **认证**: jsrsasign (JWT 签名)
- **HTTP**: 原生 fetch
- **表单**: React hooks (无额外库)
- **通知**: sonner (toast 提示)
