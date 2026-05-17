# blog-server

一个面向内部部署的闭源博客服务端产品. 本项目以二进制分发为主, 运行时围绕发布包中的 `blog-server`, `boot`, `config/`, `templates/` 与 `docs/` 组织, 不面向外部开源协作.

## 实际项目

效果展示：[https://jiaopengzi.com](https://jiaopengzi.com)

### [🚀点击我！体验在线编辑器](https://jiaopengzi.com/md)

## 快速开始

请参考 [blog-tool](https://github.com/jiaopengzi/blog-tool) 部署服务.

## 产品定位

`blog-server` 为博客系统提供服务端能力, 覆盖文章内容, 用户账号, 评论互动, 上传处理, 支付与订单, 定时任务, 管理后台接口等模块. API 默认前缀为 `/api/v1`, 可与 `blog-client` 等前端项目配套使用.

项目支持以二进制包或容器镜像方式部署. 推荐在生产环境中通过发布包内的 `boot` 启动器拉起主程序, 以获得更稳定的重启与运行控制能力.

## 主要能力

- 用户, 权限, 角色与管理员体系.
- 文章, 分类, 标签, 评论, 通知等博客核心能力.
- 文件上传, 分片上传, 本地存储, 阿里云 OSS, 腾讯云 COS.
- 视频处理能力, 包括 ffmpeg 转码, 缩略图与 HLS 生成.
- Redis Stream 驱动的异步任务处理.
- PostgreSQL 数据持久化.
- Elasticsearch 搜索与索引能力.
- 邮件通知, 社交登录, 支付与账单中心集成.
- Swagger API 文档输出与后台接口支持.

## 发布包结构建议

典型发布包建议包含以下内容:

```text
blog-server/
├─ blog-server              # 主程序
├─ boot                     # 启动包装器, 推荐用于生产启动
├─ config/                  # 运行配置目录
├─ templates/               # 模板资源
├─ docs/                    # 接口文档, 包含 swagger.json
├─ VERSION                  # 版本信息
└─ LICENSE                  # 授权文件
```

其中:

- `blog-server` 是主二进制程序.
- `boot` 用于加载配置并拉起主程序, 同时支持基于配置状态的重启控制.
- `config/` 为运行时核心目录, 部署前必须按环境修改.

## 运行依赖

### 必需依赖

- PostgreSQL
- Redis
- Elasticsearch

### 可选依赖

- SMTP 邮件服务, 用于验证码与通知邮件.
- 阿里云 OSS 或腾讯云 COS, 用于对象存储.
- 外部账单中心服务, 用于支付与计费相关能力.

### 系统级依赖

- `ffmpeg`
- `ffprobe`

如果启用了视频处理能力, 运行环境必须可以在 PATH 中找到 `ffmpeg` 与 `ffprobe`. 仓库中的容器镜像方案也是围绕 ffmpeg 运行时构建的.

## 关键配置文件

运行时配置集中在 `config/` 目录. 建议以 `config/sample/` 中的样例为模板生成正式配置.

至少需要关注以下文件:

- `config/app.yaml`, 应用主配置, 包含主机地址, 端口, API 路径, 是否初始化, 定时任务, 账单中心地址等.
- `config/pgsql.yaml`, PostgreSQL 连接配置.
- `config/redis.yaml`, Redis 节点, 连接池, Stream 与限流等运行参数.
- `config/es.yaml`, Elasticsearch 配置.
- `config/jwt.yaml`, JWT 密钥与令牌生命周期配置.
- `config/log_zap.yaml`, 日志配置.

按功能启用时, 还需要:

- `config/upload.yaml`, 上传存储与 ffmpeg 处理配置.
- `config/email.yaml`, 邮件服务配置.
- `config/social_login.yaml`, 社交登录配置.
- `config/pay.yaml`, 支付配置.
- `config/cors.yaml`, 跨域配置.

### 配置注意事项

- 默认服务端口为 `5426`.
- 默认 API 前缀为 `/api/v1`.
- `config/jwt.yaml` 中的 `secret_key` 必须替换为生产环境密钥.
- `config/upload.yaml` 默认本地上传目录为 `uploads/`.
- 若启用对象存储, 请同步配置对应密钥, bucket 与访问域名.
- 若启用视频处理, 请根据机器规格调整 `ffmpeg` 线程数与优先级.

## 初始化与首次部署

### 1. 准备配置目录

将 `config/sample/` 中的样例文件复制到正式运行目录, 并根据目标环境修改实际值.

建议最少完成以下配置:

1. `config/app.yaml`
2. `config/pgsql.yaml`
3. `config/redis.yaml`
4. `config/es.yaml`
5. `config/jwt.yaml`
6. `config/log_zap.yaml`

### 2. 配置基础服务

确保以下服务已可连接:

- PostgreSQL
- Redis
- Elasticsearch

按需准备:

- SMTP
- OSS 或 COS
- 账单中心服务

### 3. 首次初始化

`config/app.yaml` 中的 `is_setup` 字段用于控制初始化流程. 首次部署时, 请根据你的初始化策略进行设置:

- 首次初始化前, 可将 `is_setup` 设为 `true`, 让程序在启动阶段执行数据库相关初始化逻辑.
- 初始化完成后, 应将 `is_setup` 调整回稳定运行所需的值, 避免重复初始化带来副作用.

如果你需要导入演示数据, 可使用发布包中的主程序执行:

```bash
./blog-server insert-demo-data
```

该命令会读取 `config/demo.sql`.

## 启动方式

### 推荐: 通过 boot 启动

生产环境推荐使用 `boot` 启动器:

```bash
./boot -config ./config/app.yaml -app ./blog-server
```

这种方式更符合当前项目的运行设计, 便于处理配置驱动的重启场景.

### 直接启动主程序

如果你明确知道自己在做什么, 也可以直接运行主程序:

```bash
./blog-server
```

但在正式环境中, 更建议统一使用 `boot` 包装启动.

## Docker 部署

仓库内置了基于发布包思路的容器构建方式. 运行容器时, 建议把配置, 上传目录与日志目录挂载到宿主机.

参考示例:

```bash
docker run -d \
  --name blog-server \
  -p 5426:5426 \
  -v /opt/blog-server/config:/home/blog-server/config \
  -v /opt/blog-server/uploads:/home/blog-server/uploads \
  -v /opt/blog-server/logs:/home/blog-server/logs \
  your-image:tag
```

容器内部约定:

- 配置目录: `/home/blog-server/config`
- 上传目录: `/home/blog-server/uploads`
- 日志目录: `/home/blog-server/logs`
- 默认启动命令: `/home/blog-server/boot -config /home/blog-server/config/app.yaml -app /home/blog-server/blog-server`

## 日志, 重启与运维说明

- 日志配置由 `config/log_zap.yaml` 控制.
- 服务端口由 `config/app.yaml` 中的 `port` 控制, 默认值为 `5426`.
- `boot` 会基于配置状态配合主程序处理重启流程, 因此不建议在生产环境绕开它.
- 服务在收到 `SIGINT` 或 `SIGTERM` 时会尝试优雅关闭.
- PostgreSQL 与 Redis 连接会在关闭流程中被回收.

## ES 索引重建

当 Elasticsearch mapping 发生变更, 或需要按当前数据库数据回填索引时, 可以直接使用主程序内置的重建命令.

单索引重建:

```bash
./blog-server rebuild-es post
```

全量重建:

```bash
./blog-server rebuild-es all
```

`rebuild-es all` 会串行删除并重建全部受支持的 ES 索引, 然后按批次回填历史数据. 这是破坏性操作, 执行前应确认当前环境允许重建索引.

完整支持的表名可通过 `./blog-server rebuild-es --help` 查看.

## API 文档

接口文档位于 `docs/swagger.json`.

如需面向前端, 网关或第三方系统对接, 建议直接基于该文件生成客户端或导入 API 工具查看.

如果你的运行配置中启用了 API 文档能力, 也可以结合应用暴露的接口文档入口使用.

## 与前端联动

- 默认 API 路径组为 `/api/v1`.
- `blog-client` 等前端项目可通过反向代理或开发代理将 `/api` 请求转发到本服务.
- 若启用了支付, 上传, 社交登录, 视频处理等高级能力, 还需要确保对应外部服务配置完整可用.

## 安全与授权

- 本项目为闭源软件, 以二进制方式授权分发.
- 未经书面授权, 不得传播源码, 逆向工程, 反编译, 或以其他方式规避授权控制.
- 生产环境请务必替换所有样例配置中的默认密钥, 密码, 主机地址与证书内容.

## 技术支持

如需部署支持, 商业授权或定制合作, 请联系项目作者.
