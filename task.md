# 识图选一 label 改造任务

## 目标
改造「识图选一」环节：随机选一个 label 作为题目，从该 label 图片库中选1张正确答案 + 其他 label 中选1张干扰项，纯图片展示，让学生靠眼力辨别。

---

## 一、数据结构改造

### 1.1 config.json / 图片数据结构
每个图片增加 `label` 字段：

```json
{
  "id": "img_001",
  "url": "data:image/...",
  "label": "筷子"
}
```

### 1.2 admin.html 改造
- **图片上传区**：增加 `label` 输入框，上传时必须填写
- **图片列表**：显示每张图的 label，方便教师管理
- **编辑图片**：支持修改 label

---

## 二、index.html 逻辑改造

### 2.1 抽题逻辑（`prepareRound` → `imageSelect` 分支）

**现状：** 随机抽2张图，随机定正确答案

**改动后：**

1. 从所有图片中提取去重的 label 列表（过滤掉没有 label 的图）
2. 随机选一个 label 作为题目（记作 `targetLabel`）
3. 从 `targetLabel` 对应的图片中随机选1张 → 正确答案
4. 从「不是 `targetLabel`」的图片中随机选1张 → 干扰项
5. 随机决定哪张放左边/右边
6. 存储 `imageSelectCorrect`（正确答案索引）、`imageSelectTargetLabel`（题目文本）

### 2.2 展示层改造

**题目文本：** `哪个是【{targetLabel}】`

**选项区：** 两张纯图片，无文字、无 label 标注

```jsx
<div className="options-container">
    <div className="option-item" onClick={() => handleImageSelect(0)}>
        <img src={imageSelectPair[0].url} />
    </div>
    <div className="option-item" onClick={() => handleImageSelect(1)}>
        <img src={imageSelectPair[1].url} />
    </div>
</div>
```

### 2.3 判分逻辑
不变，`imageSelectCorrect` 记录正确答案索引，点击 index 对比即可。

### 2.4 去重逻辑
新增 `usedImageSelectIds` 机制：记录**已出过的 label**，避免同一 label 连续出现（当前已实现，用的是图片 ID，后续改为 label 维度去重）。

---

## 三、admin.html 改造任务清单

- [ ] 图片上传增加 `label` 必填字段
- [ ] 图片列表显示 label 列
- [ ] 编辑图片支持修改 label
- [ ] 删除图片时同时清理 label 数据

---

## 四、测试验证

- [ ] 上传2个 label 各1张图，验证抽题正确
- [ ] 验证 label 过滤逻辑（无 label 的图不参与抽题）
- [ ] 验证 label 去重（连续不出现相同 label）
- [ ] 验证判分正确
- [ ] 验证无 label 图片不影响正常流程

---

## 五、文件清单

| 文件 | 改动内容 |
|------|----------|
| `admin.html` | 图片 label 配置功能 |
| `index.html` | 抽题逻辑 + 展示改造 |
| `config.json` | 数据结构兼容（新增 label 字段）|