# Spec: offline-support

## ADDED Requirements

### Requirement: 数据持久化通过 IndexedDB 实现
The system SHALL use IndexedDB as the primary mechanism for offline data persistence. All grades, students, images, and results SHALL be stored in IndexedDB and SHALL survive browser restarts without network access.

### Requirement: 首次加载后数据保留
After the initial page load, all user data (grades, uploaded images, student lists, game results) SHALL be retrievable from IndexedDB even when the browser is offline.

### Requirement: 无 Service Worker
The system SHALL NOT implement Service Worker caching. Instead, offline functionality is achieved through local data persistence in IndexedDB. Users need network only for the first page load.

### Requirement: config.json 迁移能力
When moving to a new computer, the user SHALL copy admin.html, index.html, and config.json to the same directory. Opening admin.html SHALL prompt to load the config.json, restoring all data without network dependency.

---

#### Scenario: User opens app after first load on same computer
- **WHEN** user opens index.html or admin.html after the first visit on the same computer
- **THEN** all previously uploaded data (grades, images, results) is available from IndexedDB without network

#### Scenario: User moves to new computer with config.json
- **WHEN** user copies HTML files and config.json to a new computer and opens admin.html
- **THEN** the app prompts to load config.json and restores all data from the local JSON file

#### Scenario: Browser is closed and reopened
- **WHEN** user closes and reopens the browser (or restarts computer)
- **THEN** all game results, grade configurations, and uploaded images remain in IndexedDB and are available on next visit

---

*Note: Service Worker was evaluated but not implemented. IndexedDB persistence combined with config.json export/import provides sufficient offline capability for the target use case (school campus network environment).*
