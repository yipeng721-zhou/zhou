---
name: skill-tester
description: "Use this skill whenever the user wants to test whether a newly installed skill works correctly, validate a skill's functionality, check if a skill runs end-to-end, or verify a skill meets its description. Triggers include: 'test this skill', 'check if this skill works', 'validate my skill', 'run a test on [skill name]', 'does [skill name] work', 'help me verify this skill', or any mention of testing/debugging an installed skill. Always use this skill when the user provides a skill name and asks whether it functions as described."
---

# Skill Tester

用于测试用户新安装的 Skill 是否功能完善、能够跑通的验证工具。

---

## 执行流程

严格按照以下5步顺序执行，不可跳步。

### Step 1：定位 Skill

根据用户提供的 Skill 名称，在以下路径中查找：

```
/mnt/skills/public/<skill-name>/SKILL.md
/mnt/skills/user/<skill-name>/SKILL.md
/mnt/skills/private/<skill-name>/SKILL.md
/mnt/skills/examples/<skill-name>/SKILL.md
```

用 `view` 工具读取找到的 `SKILL.md`，提取：
- **name**：Skill 标识符
- **description**：触发场景和功能描述
- **核心功能点**：Skill 声称能做到的事情列表

如果找不到 Skill，告知用户路径不存在，请求确认 Skill 名称或路径。

---

### Step 2：生成测试用例

根据 Skill 的 description 和功能描述，**自动设计 3 个测试用例**：

- **用例1（基础功能）**：覆盖 Skill 最核心、最典型的使用场景
- **用例2（边界场景）**：覆盖 description 中提到的次要功能或特殊输入
- **用例3（触发准确性）**：验证 Skill 的 description 能否准确描述触发时机

在执行前，以列表形式告知用户将要运行的测试用例，格式如下：

```
即将运行以下测试：
① [用例1描述]
② [用例2描述]  
③ [用例3描述]
```

---

### Step 3：逐一执行测试

按照 Skill 的指令，依次完成每个测试用例的任务。

执行时：
- 完整遵循 SKILL.md 中的步骤
- 记录每一步的执行结果
- 注意是否有步骤报错、工具调用失败、输出格式不符

每个用例执行完后，给出即时判断：
- ✅ **通过**：输出符合 Skill 描述的预期
- ⚠️ **部分通过**：核心功能跑通，但有细节偏差
- ❌ **未通过**：执行失败或输出与描述不符

---

### Step 4：判断整体结果

**全部通过（3/3）**：
直接输出测试通过报告，无需进入 Step 5。

**有任意用例未通过或部分通过**：
重新读取 SKILL.md 全文，进入 Step 5 分析问题。

---

### Step 5：问题分析与修改意见

仅在 Step 4 判定有问题时执行。

逐一分析失败用例，给出以下格式的修改意见：

```
## 问题 #N

**失败用例**：[用例描述]
**失败原因**：[具体哪一步出了问题，是指令不清、步骤缺失、工具调用错误还是输出格式问题]
**修改建议**：
  - 建议在 SKILL.md 的 [具体位置] 补充/修改以下内容：
  - [具体改动内容]
```

---

## 输出格式

### 通过时

```
# Skill 测试报告：[skill-name]

## 测试结论
✅ 全部通过，Skill 功能完善，可正常使用。

## 测试明细
| 用例 | 描述 | 结果 |
|------|------|------|
| 用例1 | ... | ✅ 通过 |
| 用例2 | ... | ✅ 通过 |
| 用例3 | ... | ✅ 通过 |
```

### 未通过时

```
# Skill 测试报告：[skill-name]

## 测试结论
⚠️/❌ [N]/3 个用例存在问题，需要修改。

## 测试明细
| 用例 | 描述 | 结果 |
|------|------|------|
| 用例1 | ... | ✅/⚠️/❌ |
...

## 修改意见
[逐条列出修改建议]
```

---

## 注意事项

- 测试用例必须基于 Skill 自身的 description 设计，不能凭空发明 Skill 没有描述的功能
- 执行测试时使用真实工具完成任务，不能模拟或假设结果
- 修改意见必须精确到 SKILL.md 的具体位置，不能只说「需要改进」
- 如果 Skill 依赖外部脚本或资源文件（scripts/、references/），需检查这些文件是否存在
