# Spec: offline-support

## ADDED Requirements

### Requirement: Service Worker 缓存所有资源
The system SHALL register a Service Worker that caches admin.html, index.html, and all embedded JavaScript/CSS. Once loaded once, the application SHALL work fully offline.

### Requirement: 缓存策略
The Service Worker caching strategy SHALL be:
- **Cache First**: admin.html, index.html, and any local static assets
- **Network First** (fallback to cache): For any future external resources if added

### Requirement: 离线状态提示
When the browser is offline and user attempts to use the system, the system SHALL display a message indicating the application works offline and all previously used data is available locally.

### Requirement: 缓存更新机制
When a new version of the application is served (detected via cache version), the system SHALL:
1. Continue serving the cached version for current session
2. Update the cache in the background
3. Notify the user on next visit that a new version is available

---

#### Scenario: User opens app after initial load
- **WHEN** user opens admin.html or index.html after the first visit
- **THEN** the Service Worker serves the cached version and the app loads instantly without network

#### Scenario: User uses app with no network connection
- **WHEN** user opens the app while completely offline
- **THEN** the Service Worker intercepts the request and serves cached content; images and all functionality remain available

#### Scenario: New version becomes available
- **WHEN** developer updates admin.html or index.html on the server
- **THEN** Service Worker detects this, updates cache in background, and on next session user gets the new version

#### Scenario: Offline user sees status
- **WHEN** offline user loads the page
- **THEN** a small indicator shows "离线模式 - 所有功能可用"