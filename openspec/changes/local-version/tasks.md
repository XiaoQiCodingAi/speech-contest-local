# Tasks: 口语交际比赛系统本地化版本

## 1. 项目初始化

- [ ] 1.1 创建项目目录结构
- [ ] 1.2 下载 React 17 minified 版本保存为 `react.min.js`
- [ ] 1.3 下载 ReactDOM 17 minified 版本保存为 `react-dom.min.js`
- [ ] 1.4 下载 Babel Standalone minified 版本保存为 `babel.min.js`
- [ ] 1.5 更新 HTML 引用从 CDN 改为本地相对路径

## 2. 改造图片存储（IndexedDB Base64）

- [ ] 2.1 修改 `SpeechContestDB.uploadImage` 函数：使用 FileReader 读取图片并转为 Base64
- [ ] 2.2 修改图片读取逻辑：不再依赖服务器 URL，直接使用存储的 Base64 字符串作为 img src
- [ ] 2.3 删除所有 `/speech-contest-api/` API 调用（upload, delete, list）
- [ ] 2.4 简化 `handleImageUpload` 函数：移除 FormData 和 fetch，上传后直接存储到 IndexedDB
- [ ] 2.5 删除 `removeImage` 函数中的服务器 DELETE 调用

## 3. 实现数据导出/导入功能

- [ ] 3.1 在 admin.html 添加"导出数据"按钮，点击生成并下载 JSON 文件
- [ ] 3.2 导出 JSON 包含所有年级、学生、图片（Base64）
- [ ] 3.3 在 admin.html 添加"导入数据"文件选择器
- [ ] 3.4 导入时检测图片 URL 类型并转换（服务器路径 → Base64）
- [ ] 3.5 添加导入进度提示（处理大图片时）

## 4. 修复游戏流程逻辑

- [ ] 4.1 确保 useEffect 正确监听 currentRound 和 currentQuestionIndex 变化
- [ ] 4.2 确认 score 按钮点击后正确切换到下一题
- [ ] 4.3 确认辨一辨选择后正确进入下一组
- [ ] 4.4 添加 isTransitioning 防止连续点击（已完成）

## 5. 添加离线支持（Service Worker）

- [ ] 5.1 创建 `sw.js` Service Worker 文件
- [ ] 5.2 实现 Cache First 缓存策略
- [ ] 5.3 在 admin.html 和 index.html 中注册 Service Worker
- [ ] 5.4 添加离线状态提示 UI

## 6. 代码清理与测试

- [ ] 6.1 移除所有 console.log 调试语句
- [ ] 6.2 删除 speech-contest-backend 目录（不再需要）
- [ ] 6.3 清理服务器上的 uploads 目录
- [ ] 6.4 在完全断网环境下测试所有功能
- [ ] 6.5 验证数据导入/导出循环完整性

## 7. 文档更新

- [ ] 7.1 更新 README.md 说明本地版使用方法
- [ ] 7.2 更新项目规划文档 SPEC.md
- [ ] 7.3 删除不再需要的文档引用

---

*OpenSpec Change: local-version*
*All specs must pass before marking tasks complete*