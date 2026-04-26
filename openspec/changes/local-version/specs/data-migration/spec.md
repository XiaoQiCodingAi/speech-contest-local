# Spec: data-migration

## ADDED Requirements

### Requirement: 从云端版本导入数据
The system SHALL provide an import mechanism for data created in the original cloud-based speech-contest system. The import process SHALL handle:
- Grades with their configuration (recognition, discrimination, expression counts)
- Student lists per grade
- Image URLs that reference server paths (e.g., `/uploads/xxx.png`) — these URLs MUST be converted to Base64 data URLs by fetching and re-encoding

### Requirement: 检测图片来源类型
During import, the system SHALL detect whether an image URL is:
- A Base64 data URL (starts with `data:`) — use directly
- A server-relative path (starts with `/`) — fetch from original server and convert to base64
- A full HTTP(S) URL — fetch from original server and convert to base64

### Requirement: 服务器图片自动下载转换
When importing images from server-relative or HTTP URLs, the system SHALL:
1. Fetch the image data from the appropriate endpoint
2. Convert the binary image data to Base64
3. Store the Base64 string as the image's `url` field in IndexedDB

### Requirement: 导出数据包含所有图片
When exporting data, the resulting JSON file SHALL contain all images as Base64 strings, making the export file completely self-contained and importable on any computer without network access.

### Requirement: 导出文件格式规范
The exported JSON file SHALL follow this schema:
```json
{
  "version": "1.0",
  "exportDate": "2026-04-26T12:00:00.000Z",
  "grades": [
    {
      "id": "1",
      "name": "中低年段",
      "subtitle": "好吃的水果",
      "students": [...],
      "recognition": { "images": [...], "count": 4 },
      "discrimination": { "bigImages": [...], "smallImages": [...], "count": 3 },
      "expression": { "images": [...], "count": 3 }
    }
  ]
}
```

---

#### Scenario: User exports data on computer A
- **WHEN** user clicks "Export Data" on computer A
- **THEN** a JSON file is downloaded containing all grades, students, and images as base64 strings

#### Scenario: User imports exported data on computer B
- **WHEN** user selects the exported JSON file on computer B (no network required)
- **THEN** all data including base64 images are restored to IndexedDB

#### Scenario: User imports cloud-version data with server URLs
- **WHEN** imported data contains image URLs like `/uploads/xxx.png`
- **THEN** system fetches these images and converts them to base64 before storing

#### Scenario: Import handles mixed URL types
- **WHEN** imported data contains a mix of base64 strings, relative paths, and full URLs
- **THEN** system processes each image according to its type (uses directly, fetches and converts, or fetches and converts respectively)