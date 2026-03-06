---
title: 部署
order: 10
---

# 部署

[MODES: framework]

## 简介

React Router 可以通过两种方式部署：

- 全栈托管
- 静态托管

官方 [React Router 模板](https://github.com/remix-run/react-router-templates) 可以帮助你快速搭建应用，也可以作为你自己应用的参考。

部署到静态托管时，你可以像部署其他使用 React 的单页应用一样部署 React Router。

## 模板

运行 `create-react-router` 命令后，请务必按照 README 中的说明操作。

### Node.js with Docker

```
npx create-react-router@latest --template remix-run/react-router-templates/default
```

- 服务端渲染
- Tailwind CSS

容器化的应用可以部署到任何支持 Docker 的平台，包括：

- AWS ECS
- Google Cloud Run
- Azure Container Apps
- Digital Ocean App Platform
- Fly.io
- Railway

### Node with Docker（自定义服务器）

```
npx create-react-router@latest --template remix-run/react-router-templates/node-custom-server
```

- 服务端渲染
- Tailwind CSS
- 自定义 express 服务器，提供更多控制权

容器化的应用可以部署到任何支持 Docker 的平台，包括：

- AWS ECS
- Google Cloud Run
- Azure Container Apps
- Digital Ocean App Platform
- Fly.io
- Railway

### Node with Docker 和 Postgres

```
npx create-react-router@latest --template remix-run/react-router-templates/node-postgres
```

- 服务端渲染
- Postgres 数据库（使用 Drizzle）
- Tailwind CSS
- 自定义 express 服务器，提供更多控制权

容器化的应用可以部署到任何支持 Docker 的平台，包括：

- AWS ECS
- Google Cloud Run
- Azure Container Apps
- Digital Ocean App Platform
- Fly.io
- Railway

### Vercel

Vercel 维护着自己的 React Router 模板。更多信息请查看 [Vercel 指南](https://vercel.com/templates/react-router/react-router-boilerplate)。

### Cloudflare Workers

Cloudflare 维护着自己的 React Router 模板。更多信息请查看 [Cloudflare 指南](https://developers.cloudflare.com/workers/framework-guides/web-apps/react-router/)。

### Netlify

Netlify 维护着自己的 React Router 模板。更多信息请查看 [Netlify 指南](https://docs.netlify.com/build/frameworks/framework-setup-guides/react-router/)。

### EdgeOne Pages

EdgeOne Pages 维护着自己的 React Router 模板。更多信息请查看 [EdgeOne Pages 指南](https://pages.edgeone.ai/document/framework-react-router)。
