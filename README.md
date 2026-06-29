# New-API 轮询版 部署指南

## 📦 包含内容

| 文件 | 大小 | 说明 |
|------|------|------|
| `new-api-source.zip` | 36.4 MB | 源码包（含轮询功能修改，共 14 个文件 +509/-52 行） |
| `new-api-polling.tar` | 108.9 MB | 预构建 Docker 镜像（可直接 docker load） |
| `docker-compose.yml` | - | Docker Compose 配置 |
| `.env.example` | - | 环境变量模板 |
| `README.md` | - | 本文档 |

## 🚀 快速部署（使用预构建镜像）

```bash
# 1. 加载镜像
docker load -i new-api-polling.tar
# 验证: docker images | grep new-api-polling

# 2. 复制环境变量模板
cp .env.example .env
# 编辑 .env 文件，设置数据库密码和 SESSION_SECRET

# 3. 启动服务
docker compose up -d

# 4. 查看日志确认启动成功
docker compose logs -f new-api

# 5. 访问 Web 界面
# http://localhost:3000
# 默认账户: root / 123456（首次登录后请修改密码）
```

## 🔧 源码构建

```bash
# 1. 解压源码
unzip new-api-source.zip
cd new-api

# 2. 安装依赖并确认构建环境
# Go 1.26+、Bun 1.3+

# 3. 构建 Docker 镜像（约 30-60 分钟）
docker build -t new-api-polling:local -f Dockerfile.polling .

# 4. 复制环境变量模板
cp .env.example .env
# 编辑 .env 文件

# 5. 启动服务
docker compose up -d
```

## 📊 镜像信息

| 属性 | 值 |
|------|------|
| 镜像名称 | `new-api-polling:local` |
| 镜像大小 | 108.9 MB |
| 基础镜像 | `new-api:local` (runtime) + `golang:1.26.1-alpine` (builder) |
| 端口 | 3000 |
| 修改范围 | 14 个文件，+509/-52 行 |

## ⚙️ 环境变量说明

| 变量 | 必填 | 说明 |
|------|------|------|
| `SQL_DSN` | 是 | 数据库连接字符串 |
| `REDIS_CONN_STRING` | 否 | Redis 连接字符串 |
| `SESSION_SECRET` | 是 | 会话密钥（随机字符串） |
| `CRYPTO_SECRET` | 否 | 加密密钥（多机部署时必填） |
| `TZ` | 否 | 时区，默认 Asia/Shanghai |

## 🔄 轮询功能说明

本版本包含渠道轮询功能，支持：
- 多 Key 模式下的轮询调度
- 渠道健康检查与自动禁用
- 轮询状态持久化

## 🛠️ 常用命令

```bash
# 查看容器状态
docker compose ps

# 查看日志
docker compose logs -f new-api

# 停止服务
docker compose down

# 重启服务
docker compose restart
```

## ⚠️ 安全提醒

1. **首次登录后立即修改 root 密码**
2. **不要将 .env 文件提交到 Git**
3. **生产环境建议配置 HTTPS 反向代理**
4. **数据库和 Redis 不要暴露到公网**

## 📋 版本信息

- 基于: `QuantumNous/new-api` v1.0.0-rc.14
- 修改: 新增渠道轮询功能
- 构建时间: 2026-06-29
