# Spec: local-game

## ADDED Requirements

### Requirement: 游戏页面完全本地化
index.html SHALL be a self-contained HTML file with no external dependencies. All JavaScript, CSS, and the game logic SHALL be embedded directly in the file.

### Requirement: 三环节完整比赛流程
The game mode SHALL implement the full three-phase contest:
1. **认一认 (Recognition)**: Display a random image from the selected grade's recognition pool. Teacher scores 3/2/1/0.
2. **辨一辨 (Discrimination)**: Display one big image as the question. Show 3 shuffled small images as options. Student selects one. Correct = 3 points, wrong = 0.
3. **说一说 (Expression)**: Display a random image from the expression pool. Teacher scores 3/2/1/0.

### Requirement: 每环节按配置数量循环
The number of questions per round SHALL be controlled by the grade configuration (`recognition.count`, `discrimination.count`, `expression.count`). The system SHALL iterate through all questions in a round before advancing.

### Requirement: 当前题目进度显示
The game interface SHALL display the current progress as "环节 X/3 第 Y 题" so the teacher can see which question they are on.

### Requirement: 成绩实时计算与最终展示
The system SHALL calculate running scores as the student progresses through questions. At the end of all three rounds, the system SHALL display a results screen showing:
- 认一认得分 (sum of recognition scores)
- 辨一辨得分 (sum of discrimination scores)
- 说一说得分 (sum of expression scores)
- 总分 (grand total)

### Requirement: 图片预加载提升流畅度
When displaying a question, the system SHALL silently preload the remaining images for the current round to improve perceived performance when the teacher advances to the next question.

### Requirement: 选择答案后防止连续点击
After the student selects an answer or teacher scores, the system SHALL enter a transition state lasting 1-2 seconds during which further input is ignored to prevent accidental double-clicks.

### Requirement: 反馈动画提示
When a discrimination answer is selected:
- Correct: Show green highlight on the selected option with "正确！+3分" message
- Wrong: Show red highlight on the selected option with "错误！" message
The feedback SHALL automatically clear after 2 seconds before advancing.

### Requirement: 使用 IndexedDB 读取图片
The game mode SHALL read all images from IndexedDB. Image URLs stored in grade configuration SHALL be the base64 strings directly (not file paths).

### Requirement: 随机学生点名
The system SHALL provide a "随机点名" button that randomly selects a student from the current grade's student list. The selected student name SHALL be displayed prominently.

---

#### Scenario: Student completes full contest
- **WHEN** student finishes all three rounds (认一认 → 辨一辨 → 说一说)
- **THEN** results screen shows itemized scores for each round and grand total

#### Scenario: Teacher sees progress during contest
- **WHEN** student is on question 2 of round 1 with 4 questions configured
- **THEN** interface displays "环节 1/3 第 2 题"

#### Scenario: Discrimination shows feedback on wrong answer
- **WHEN** student selects a wrong option in 辨一辨
- **THEN** selected option highlights red and "错误！" appears; correct answer is NOT revealed

#### Scenario: Teacher cannot double-click score button
- **WHEN** teacher clicks a score button (e.g., 3分)
- **THEN** all score buttons become unresponsive for 1 second while the transition to next question occurs

#### Scenario: Preloaded images improve next question display
- **WHEN** question 1 is displayed and user is about to advance to question 2
- **THEN** question 2's image has already been loaded into browser cache via preload

#### Scenario: Random student selection
- **WHEN** teacher clicks "随机点名" button
- **THEN** a random student name from the grade's student list is displayed as the current contestant