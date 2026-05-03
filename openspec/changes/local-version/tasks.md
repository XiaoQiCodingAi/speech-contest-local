# Tasks: 口语交际比赛系统本地化版本

## 1. 项目初始化 ✅

- [x] 1.1 创建项目目录结构
- [x] 1.2 下载 React 17 minified 版本保存为 `react.min.js`
- [x] 1.3 下载 ReactDOM 17 minified 版本保存为 `react-dom.min.js`
- [x] 1.4 下载 Babel Standalone minified 版本保存为 `babel.min.js`
- [x] 1.5 更新 HTML 引用从 CDN 改为本地相对路径

## 2. 改造图片存储（IndexedDB Base64）✅

- [x] 2.1 修改 `SpeechContestDB.uploadImage` 函数：使用 FileReader 读取图片并转为 Base64
- [x] 2.2 修改图片读取逻辑：不再依赖服务器 URL，直接使用存储的 Base64 字符串作为 img src
- [x] 2.3 删除所有 `/speech-contest-api/` API 调用（upload, delete, list）
- [x] 2.4 简化 `handleImageUpload` 函数：移除 FormData 和 fetch，上传后直接存储到 IndexedDB
- [x] 2.5 删除 `removeImage` 函数中的服务器 DELETE 调用

## 3. 实现数据导出/导入功能 ✅

- [x] 3.1 在 admin.html 添加"导出数据"按钮，点击生成并下载 JSON 文件（saveToConfig）
- [x] 3.2 导出 JSON 包含所有年级、学生、图片（Base64）
- [x] 3.3 在 admin.html 添加"导入数据"文件选择器（importData）
- [x] 3.4 页面加载时自动弹出文件选择框检测 config.json 并加载
- [x] 3.5 添加 config.json 固定文件名支持，换电脑时直接读取

## 4. 修复游戏流程逻辑 ✅

- [x] 4.1 确保 useEffect 正确监听 currentRound 和 currentQuestionIndex 变化
- [x] 4.2 确认 score 按钮点击后正确切换到下一题
- [x] 4.3 确认辨一辨选择后正确进入下一组
- [x] 4.4 添加 isTransitioning 防止连续点击（已完成）

## 5. 添加识图选一环节 ✅

- [x] 5.1 数据结构：为 imageSelect.images 每项添加 label 字段
- [x] 5.2 admin.html：识图选一图片列表显示 label，支持编辑 label
- [x] 5.3 admin.html：上传图片时自动初始化 label 为空字符串
- [x] 5.4 index.html：抽题逻辑改为先随机选 label，再从该 label 图片中选正确答案，从其他 label 中选干扰项
- [x] 5.5 index.html：展示「哪个是【{label}】」，图片纯展示不加文字
- [x] 5.6 index.html：按 label 去重，已出过的 label 不连续出现
- [x] 5.7 仅在无语言年段（id=3）显示识图选一环节

## 6. 成绩统计与复盘功能 ✅

- [x] 6.1 成绩统计页面按年级分组显示
- [x] 6.2 点击成绩查看答题详情复盘
- [x] 6.3 复盘页面正确映射各环节分数（r/d/e/s → 认一认/辨一辨/说一说/识图选一）

## 7. 代码清理与测试 ✅

- [x] 7.1 清理调试用 console.log（保留必要的错误日志）
- [x] 7.2 删除 speech-contest-backend 目录（不再需要）
- [x] 7.3 测试数据导入/导出循环完整性
- [x] 7.4 测试识图选一 label 抽题正确性
- [x] 7.5 测试无语言年段正确显示识图选一，其他年段不显示

## 8. 文档与同步 ✅

- [x] 8.1 更新 openspec/proposal.md（架构变更说明）
- [x] 8.2 更新 openspec/design.md（决策记录与实现状态）
- [x] 8.3 同步文件到 Nginx 静态目录 /usr/share/nginx/html/speech-contest-local/

---

*OpenSpec Change: local-version*
*All specs must pass before marking tasks complete*
