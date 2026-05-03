# Proposal: 口语交际比赛系统 - 本地化版本

## Why

当前口语交际比赛系统（speech-contest）依赖云端服务器存储图片，需要网络连接才能使用。校园内网环境复杂，教师设备可能无法稳定访问外部网络。此外，系统架构存在 CDN 依赖问题（React/Babel 通过 unpkg.com CDN 加载），网络不稳定时会卡顿。

将系统改造为完全本地化运行，消除所有外部依赖，实现：双击即可使用、无需网络、数据完全存储在本地。

## What Changes

**BREAKING** - 移除服务器端部署模式：
- 删除 `speech-contest-backend` Node.js 后端服务
- 删除服务器端图片存储 `/uploads/` 目录
- 前端不再依赖任何外部 CDN（React、Babel 内嵌到 HTML）
- 图片数据从「服务器文件存储」改为「IndexedDB Blob 存储（Base64）」

**新增功能**：
- 完全离线运行的双 HTML 文件应用（admin.html + index.html）
- 图片以 Base64 编码存储在 IndexedDB 中
- 数据导出为 JSON 文件功能（saveToConfig）
- 页面加载时自动弹出文件选择框，加载 config.json
- config.json 固定文件名，与 HTML 同目录即可自动读取

**保留功能**（从 speech-contest 项目迁移）：
- 三环节比赛流程：认一认 → 辨一辨 → 说一说
- 年级/班级/学生管理
- 教师评分系统（可配置分数选项，如 3/2/1/0 或 3/0）
- 成绩统计与历史记录
- 随机点名机制

**新增环节 - 识图选一（无语言年段专项）：**
- 仅对「无语言」年段（id=3）显示
- 每个图片配置 label（物品名称）
- 随机选一个 label 作为题目（如「哪个是【筷子】」）
- 从该 label 图片库中随机选1张作正确答案
- 从其他 label 图片中随机选1张作干扰项
- 两张图片纯展示，无文字标注，学生靠眼力辨别

## Capabilities

### New Capabilities

- `local-admin`: 本地化后台管理系统，支持图片 Base64 存储、数据导入导出、自动加载 config.json
- `local-game`: 本地化比赛模式，无网络依赖，含四环节（新增识图选一）
- `data-migration`: 数据迁移工具，支持从云端版本导入配置
- `offline-support`: 无 Service Worker，通过 localStorage/IndexedDB 实现数据持久化

### Modified Capabilities

- （无 - 本地版本是全新起点，不存在需要修改的旧需求）

## Impact

**删除**：
- `speech-contest-backend/` 目录（Node.js 后端）
- 服务器 Nginx 配置中的 `/speech-contest-api/` 反向代理规则
- 服务器图片存储 `/usr/share/nginx/html/speech-contest/uploads/`

**新增**：
- `speech-contest-local/admin.html` - 本地版后台管理（内嵌 React/Babel）
- `speech-contest-local/index.html` - 本地版比赛入口（内嵌 React/Babel）
- `speech-contest-local/react.min.js` - React 17 内嵌
- `speech-contest-local/react-dom.min.js` - ReactDOM 17 内嵌
- `speech-contest-local/babel.min.js` - Babel Standalone 内嵌
- `speech-contest-local/SPEC.md` - 项目规划文档
- `speech-contest-local/openspec/` - OpenSpec 变更管理目录

**依赖变化**：
- 移除：Node.js 后端、npm 依赖、服务器环境、CDN
- 新增：无外部依赖，纯前端静态文件

---

*OpenSpec Change ID: local-version*
*Created: 2026-04-26*
*Last Updated: 2026-05-03*
