# Changelog

本文件将记录本项目的所有重要变更。

该格式基于 [Keep a Changelog](https://keepachangelog.com),
本项目遵循 [语义化版本控制](https://semver.org/spec/v2.0.0.html)。

## [v1.1.1] - 2026-09-18

### 📦 Build

- 依赖升级

### Fix

- 文章浏览量日期维度统计修正
- docker logs blog-server 中出现预期外的日志
- 单设备机制与同浏览器多个多标签都满足

## [v1.1.0] - 2026-09-04

### ✅ Feat

- 增加了 PV UV 文章浏览量的统计

### Fix

- 信任代理网段列表使用 blog-tool 中自定义的 docker 网段

### 💥 boom

- sitemap 配合前端 ssr 改造
- config 中 app.yaml 和 redis.yaml 配置项有更新，从 `v1.0.1` 不向下兼容

## [v1.0.1] - 2026-08-12

### ✅ Test

- 测试用例去掉多余噪声和ffmpeg集成测试

### ✨ Feat

- ffmpeg 集成测试保证 ffmpeg 升级后的一致性

### 📦 Build

- 依赖升级
- ffmpeg 依赖升级到 9.0

### 🔧 Chore

- 增加 chglog 配置

## [v1.0.0] - 2026-07-31

### Initial release

- 生产环境首次发布。
