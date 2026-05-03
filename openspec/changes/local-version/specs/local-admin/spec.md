# Spec: local-admin

## ADDED Requirements

### Requirement: 后台页面完全本地化
admin.html SHALL be a self-contained HTML file with no external dependencies (no CDN for React/Babel). All JavaScript and CSS SHALL be embedded directly in the file.

### Requirement: 图片以 Base64 存储
When a user uploads an image in admin.html, the system SHALL convert the image to a Base64-encoded string and store it in IndexedDB alongside the image metadata (id, name, url field will store the base64 string directly).

### Requirement: 数据导出为 JSON 文件（config.json）
The system SHALL allow users to export all configuration data (grades, students, images as base64) as a single JSON file download via saveToConfig. The file SHALL be named `config.json` for portability across computers.

### Requirement: 数据从 JSON 文件导入
The system SHALL allow users to import a previously exported JSON file to restore all configuration data, including restoring images from base64 strings back to IndexedDB.

### Requirement: 自动加载 config.json
Upon page load, admin.html SHALL automatically present a file picker dialog to prompt the user to load a config.json file if one exists in the same directory.

### Requirement: 辨一辨标记正确答案
In admin.html's discrimination configuration, the user SHALL be able to select one small image as the correct answer for each big image. The system SHALL store this mapping as `correctSmallId` on the big image object.

### Requirement: 识图选一图片 label 管理
In admin.html's 识图选一 configuration:
- Each uploaded image SHALL have an editable `label` field (initially empty string)
- The image grid SHALL display each image's current label below the image
- An edit button on each image SHALL allow the teacher to set/modify the label via a prompt dialog
- Images with empty label SHALL be excluded from the game抽题
- The UI SHALL show a count of "已设置 label 的图片 X / Y"

### Requirement: 最小小图数量限制
The discrimination section SHALL require at least 3 small images before allowing game mode to start. If fewer than 3 small images exist, the system SHALL display a warning message.

### Requirement: 删除已上传图片
The admin SHALL be able to delete any uploaded image. Deleting a big image that has a correctSmallId mapping SHALL also clear that mapping.

---

#### Scenario: User uploads a recognition image
- **WHEN** user drags or clicks to upload an image file for recognition section
- **THEN** system converts image to Base64 and stores in IndexedDB with id, name, url (base64 string), and label (empty string)

#### Scenario: User exports all data
- **WHEN** user clicks "保存配置到 config.json" button
- **THEN** system downloads a JSON file containing all grades, students, and images (base64 encoded)

#### Scenario: Admin auto-loads config.json on startup
- **WHEN** admin.html is opened in a browser
- **THEN** a file picker dialog appears automatically prompting to load config.json

#### Scenario: User imports data from JSON file
- **WHEN** user selects a previously exported JSON file via file input
- **THEN** system parses the JSON and restores all data to IndexedDB, including converting base64 images

#### Scenario: User edits label for 识图选一 image
- **WHEN** user clicks the edit (✎) button on an 识图选一 image
- **THEN** a prompt dialog appears with the current label; user can type a new label and confirm
- **AND** the label display below the image updates to show the new label

#### Scenario: User marks correct answer for big image
- **WHEN** user clicks on a small image in the discrimination section to mark it as correct for the selected big image
- **THEN** system sets that small image's id as the big image's `correctSmallId` field

#### Scenario: User attempts to play with fewer than 3 small images
- **WHEN** user has configured discrimination but only 2 small images exist
- **THEN** system displays red warning text indicating minimum 3 small images required
