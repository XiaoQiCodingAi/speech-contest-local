# Spec: local-game

## ADDED Requirements

### Requirement: 游戏页面完全本地化
index.html SHALL be a self-contained HTML file with no external dependencies. All JavaScript, CSS, and the game logic SHALL be embedded directly in the file.

### Requirement: 三环节完整比赛流程
The game mode SHALL implement the full three-phase contest:
1. **认一认 (Recognition)**: Display a random image from the selected grade's recognition pool. Teacher scores based on configured score options.
2. **辨一辨 (Discrimination)**: Display one big image as the question. Show 3 shuffled small images as options. Student selects one. Correct = configured score, wrong = 0.
3. **说一说 (Expression)**: Display a random image from the expression pool. Teacher scores based on configured score options.

### Requirement: 识图选一环节（无语言年段专属）
The game mode SHALL implement an additional fourth phase for grade id=3 ("无语言"):
- Each image in imageSelect has a `label` field (e.g., `label: "筷子"`)
- The system randomly selects one label as the question topic (e.g., "哪个是【筷子】")
- One image from that label's pool is randomly selected as the correct answer
- One image from a different label is selected as the distractor
- Both images are displayed without any text overlay — students identify by visual recognition only
- Used labels are tracked to avoid repeating the same label consecutively

### Requirement: 每环节按配置数量循环
The number of questions per round SHALL be controlled by the grade configuration (`recognition.count`, `discrimination.count`, `expression.count`, `imageSelect.count`). The system SHALL iterate through all questions in a round before advancing.

### Requirement: 当前题目进度显示
The game interface SHALL display the current progress as "环节 X/3 第 Y 题" so the teacher can see which question they are on.

### Requirement: 成绩实时计算与最终展示
The system SHALL calculate running scores as the student progresses through questions. At the end of all rounds, the system SHALL display a results screen showing per-round scores and grand total.

### Requirement: 选择答案后防止连续点击
After the student selects an answer or teacher scores, the system SHALL enter a transition state lasting 1-2 seconds during which further input is ignored to prevent accidental double-clicks.

### Requirement: 反馈动画提示
When a discrimination or 识图选一 answer is selected:
- Correct: Show green highlight on the selected option with "正确！+{score}分" message
- Wrong: Show red highlight on the selected option with "错误！" message
The feedback SHALL automatically clear after 2 seconds before advancing.

### Requirement: 使用 IndexedDB 读取图片
The game mode SHALL read all images from IndexedDB. Image URLs stored in grade configuration SHALL be the base64 strings directly (not file paths).

### Requirement: 随机学生点名
The system SHALL provide a "随机点名" button that randomly selects a student from the current grade's student list. The selected student name SHALL be displayed prominently.

### Requirement: 识图选一 label 维度过滤
The system SHALL track which labels have been used in the current session. When selecting the next question, the system SHALL exclude labels that were just used, ensuring no label appears in consecutive questions. When all labels are exhausted, the used set SHALL be reset.

---

#### Scenario: Student completes full contest (standard grade)
- **WHEN** student finishes all three rounds (认一认 → 辨一辨 → 说一说)
- **THEN** results screen shows itemized scores for each round and grand total

#### Scenario: Student in non-verbal grade completes contest
- **WHEN** student in grade id=3 finishes all rounds (辨一辨 → 识图选一)
- **THEN** results screen shows 辨一辨 and 识图选一 scores

#### Scenario: 识图选一 question display
- **WHEN** a 识图选一 question is selected with target label "筷子"
- **THEN** the display shows "哪个是【筷子】" and two images with no text overlay

#### Scenario: Teacher sees progress during contest
- **WHEN** student is on question 2 of round 1 with 4 questions configured
- **THEN** interface displays "环节 1/3 第 2 题"

#### Scenario: Discrimination shows feedback on wrong answer
- **WHEN** student selects a wrong option in 辨一辨
- **THEN** selected option highlights red and "错误！" appears; correct answer is NOT revealed

#### Scenario: 识图选一 no consecutive same labels
- **WHEN** a question with label "筷子" just appeared
- **THEN** the next 识图选一 question will NOT have label "筷子" (it will be filtered out until reset)
