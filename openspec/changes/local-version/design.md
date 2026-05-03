# Design: 口语交际比赛系统本地化版本

## Context

口语交际比赛系统现有版本依赖：
- Node.js 后端（`speech-contest-backend`）存储图片
- Nginx 服务器提供图片访问
- CDN（unpkg.com）加载 React 17 和 Babel

目标：改造为完全本地化的双 HTML 文件应用，去除所有外部依赖。

## Goals / Non-Goals

**Goals:**
- 双击 `admin.html` 或 `index.html` 即可运行，无需服务器
- 所有图片存储在浏览器 IndexedDB 中（Base64 格式）
- 无网络依赖，首次加载后完全离线可用（通过 localStorage/IndexedDB 持久化）
- 保留现有比赛流程和后台管理功能
- 新增「识图选一」环节支持无语言学生
- 数据可导入/导出 JSON 文件（config.json）
- 换电脑时 config.json 和 HTML 文件同目录即可自动加载

**Non-Goals:**
- 不需要 Electron/Capacitor 桌面包装
- 不需要真正的原生 App
- 不需要微信支付等外部集成
- 不需要多用户协作或云端同步
- 不需要 Service Worker（数据持久化通过 IndexedDB 实现，够用）

## Decisions

### Decision 1: React 内嵌 vs CDN

**选择：内嵌 React + Babel 到 HTML**

当前：`https://unpkg.com/react@17/umd/react.development.js` 等 CDN 引入

改造后：将 React 和 Babel 的 production-ready 版本下载到本地，通过相对路径引用（如 `<script src="./react.min.js">`）

**替代方案考虑：**
- **Preact +htm**：体积更小（<5KB），但需要重写现有 React 代码
- **纯 vanilla JS**：去掉 React 彻底重写，工作量大，易出错

**结论**：继续用 React，仅改变加载方式为本地内嵌。

### Decision 2: 图片存储 - IndexedDB Blob vs Base64

**选择：Base64 字符串存储在 IndexedDB**

现有云端版本：图片存储在服务器文件系统，URL 为 `/uploads/xxx.png`

本地版本：图片转为 Base64 字符串，直接存储在 IndexedDB 的 JSON 对象中

**优点**：
- 无需额外服务器存储
- IndexedDB 有 50MB+ 容量（大部分浏览器）
- 迁移时只需导出/导入 JSON

**缺点**：
- Base64 字符串比原始二进制大约 33%
- 大图片（如 2MB）转为 Base64 后约 2.7MB，单个 IndexedDB 条目可能接近限制

**缓解方案**：
- 在 admin.html 上传时添加图片压缩（Max width 1920px）
- 用户提示单张图片建议不超过 2MB

### Decision 3: 数据导入/导出

**选择：单 JSON 文件包含所有数据（含 Base64 图片）**

```json
{
  "version": "1.0",
  "exportDate": "ISO8601 timestamp",
  "grades": [...]
}
```

**优点**：一个文件包含一切，导入时无需额外资源
**缺点**：导出文件较大（包含图片），但便于完全迁移

**固定配置文件**：系统使用固定文件名 `config.json`，与 HTML 同目录时页面加载时自动弹出文件选择框提示加载。

### Decision 4: 识图选一 label 方案

**选择：每个图片独立设置 label，抽题时以 label 为单位**

- 每个图片对象包含 `label` 字段（如 `label: "筷子"`）
- 抽题时从所有 label 中随机选一个作为题目
- 从该 label 的图片中随机选1张作为正确答案
- 从其他 label 中随机选1张作为干扰项
- 两张图片纯展示，不在图片上叠加任何文字

**优点**：
- 教师配置灵活，每个 label 可以有多张不同角度/风格的图片
- 学生无法通过图片上的文字作弊
- 方便教师批量管理某类物品的图片

### Decision 5: 无语言年段专属环节

**选择：识图选一只在年段 id=3（「无语言」）中显示**

其他年段显示原有三环节（认一认、辨一辨、说一说）。

**优点**：不需要修改现有年段的配置逻辑，零影响。

## Risks / Trade-offs

[Risk] IndexedDB 存储限制
→ **Mitigation**：单张图片压缩后建议 < 2MB；提示用户避免上传过大图片

[Risk] Base64 字符串导致 localStorage/IndexedDB 配额超限
→ **Mitigation**：分级存储 - 小数据（配置）存 IndexedDB；图片按需压缩

[Risk] 用户误删浏览器数据导致配置丢失
→ **Mitigation**：提供数据导出功能（saveToConfig），用户定期备份

[Risk] 识图选一 label 配置容易遗漏
→ **Mitigation**：在 admin.html 的识图选一标签页显示「已设置 label 的图片数量」提示，未设置 label 的图片不参与抽题

## Migration Plan

1. **Phase 1**: 创建 `speech-contest-local/` 项目骨架 ✅
   - 复制 `admin.html` 和 `index.html` 到新目录
   - 下载 React.min.js、Babel.min.js、ReactDOM.min.js 到本地

2. **Phase 2**: 改造存储层 ✅
   - 修改 `SpeechContestDB` 对象，图片存储改为 Base64
   - 修改 `handleImageUpload` 函数，FileReader 读取后转为 Base64

3. **Phase 3**: 移除服务器相关代码 ✅
   - 删除 `/speech-contest-api/upload` 调用
   - 删除 DELETE `/speech-contest-api/files/:filename` 调用
   - 图片 URL 直接使用存储的 Base64 字符串

4. **Phase 4**: 添加导出/导入功能 ✅
   - 实现 saveToConfig 导出为 JSON 文件（固定文件名 config.json）
   - 实现从 JSON 文件导入恢复数据（importData）
   - 页面加载时自动检测并提示加载 config.json

5. **Phase 5**: 添加识图选一环节 ✅
   - 新增 imageSelect 数据结构和配置 UI
   - 实现基于 label 的抽题逻辑
   - 仅在无语言年段（id=3）中显示

6. **Phase 6**: 测试验证 ✅
   - 在无网络环境下测试所有功能
   - 验证导入/导出功能正常
   - 验证识图选一 label 抽题正确

## Open Questions

1. ~~**图片压缩阈值**：是否需要添加自动压缩，超过 1920px 的图片强制缩小？~~ → 暂不实现，用户手动压缩
2. ~~**浏览器兼容性**：是否需要支持 IE11（可能性低）或只面向现代浏览器？** → 现代浏览器即可
3. ~~**Service Worker**：是否需要实现离线缓存？** → 不需要，IndexedDB 数据持久化已足够
