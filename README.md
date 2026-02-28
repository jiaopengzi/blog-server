# blog-server

一个用 Go 开发的闭源博客/服务端模板项目，供内部部署与二进制分发使用。

概要

- 目的：提供一个轻量、可定制的博客服务端骨架，包含管理、文章、用户与支付等模块。
- 代码位置：项目根目录包含服务实现、配置与脚本。

主要特性（示例）

- 用户、权限与角色管理
- 帖子、分类、标签管理
- 支付与订单集成（可选）
- 管理后台接口与定时任务支持

先决条件

- Go 语言环境（建议 Go 1.20+）
- 可选服务：PostgreSQL、Redis、Elasticsearch（视配置而定）

快速开始

1. 克隆仓库并进入目录

```bash
git clone <repository> && cd blog-server-dev
```

1. 编辑配置文件（位于 `config/` 目录）

- 复制并修改 `config/*.yaml` 中的示例配置，确保数据库、缓存与其他服务配置正确。

1. 本地运行（开发）

```bash
go run main.go
# 或使用 go build 构建二进制
go build -o bin/blog-server main.go
./bin/blog-server
```

1. 使用 Docker（示例）

```bash
docker build -t blog-server .
docker run -p 8080:8080 --env-file .env blog-server
```

配置与数据

- 配置文件：请参考 `config/` 下的 YAML 文件（例如 `pgsql.yaml`, `redis.yaml`）。
- 示例数据：`config/demo.sql` 可用于初始化示例数据。

许可与使用

- 本项目采用专有许可，详见 [LICENSE](LICENSE)。未经许可方书面授权，不得分发、反向工程或以其他方式取得源代码。

贡献与联系

- 本项目为闭源项目，不接受未授权的外部贡献。如需合作或授权，请联系：焦棚子。
