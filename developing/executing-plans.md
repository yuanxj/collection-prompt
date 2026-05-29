# Codex `writing-plans` 常用 Prompt 模板

> 用途：让 Codex 在动代码前，先读项目、拆方案、列文件、列风险、列验证命令。  
> 核心原则：**Plan first, no code changes.**

---

## 0. 推荐工作流

```text
define-goal
→ writing-plans
→ executing-plans
→ updating-plans
→ project-handoff
```

对应含义：

| Skill | 用途 |
|---|---|
| `define-goal` | 把模糊目标变成可衡量、可验收的工程目标 |
| `writing-plans` | 在改代码前，生成实施计划 |
| `executing-plans` | 按已批准计划逐步执行 |
| `updating-plans` | 当 repo 状态或需求变化时更新计划 |
| `project-handoff` | 收尾总结、交接、沉淀上下文 |

---

## 1. 通用工程任务模板

适合大多数开发任务：新功能、bugfix、轻量重构、数据接入等。

```text
Use the writing-plans skill.

Task:
[Describe the feature / bugfix / refactor you want.]

Context:
- Project: [project name]
- Tech stack: [Next.js / Supabase / Python / Cloud Run / etc.]
- Current problem: [what is wrong or missing]
- Desired behavior: [what should happen after this task]

Requirements:
- Inspect the current repo structure first.
- Identify relevant files and modules.
- Propose the smallest safe implementation.
- Include database/API/env changes if needed.
- Include test and verification commands.
- Include risks and rollback plan.

Rules:
- Do not modify files.
- Do not run destructive commands.
- Do not implement anything yet.
- Ask clarification questions only if blocked.
- Make assumptions explicit.

Expected output:
1. Repo understanding
2. Relevant files
3. Proposed approach
4. Step-by-step implementation plan
5. Test plan
6. Risks / blockers
7. Recommended next prompt for execution
```

---

## 2. 官网 / Next.js / Supabase 功能开发模板

适合 `money-talk-site`、官网 2.0、页面改造、Supabase 数据接入。

```text
Use the writing-plans skill.

Task:
Create an implementation plan for [feature name].

Project context:
- This is a Next.js project.
- Frontend and business logic are mainly TypeScript.
- Data is stored in Supabase.
- The goal is to keep the existing UI and route structure stable.

Feature goal:
[Describe the feature.]

Current issue:
[Describe what currently happens.]

Expected result:
[Describe what should happen after implementation.]

Planning requirements:
- Inspect the app routes, components, data fetching logic, and Supabase client usage.
- Find where demo/mock data is currently used.
- Identify the minimal set of files to change.
- Define expected Supabase tables/fields.
- Include loading, empty, and error states.
- Include typecheck/lint/build verification steps.

Rules:
- Do not modify files.
- Do not change database schema unless clearly necessary.
- Do not redesign UI unless required.
- Do not add authentication/payment unless explicitly in scope.
- Prefer incremental, low-risk changes.

Expected output:
1. Current implementation summary
2. Files likely involved
3. Data flow plan
4. Step-by-step implementation plan
5. Supabase query/schema assumptions
6. Verification commands
7. Risks and open questions
```

---

## 3. Bug 修复模板

适合修 bug 前先让 Codex 分析，不要一上来乱改。

```text
Use the writing-plans skill.

Task:
Create a fix plan for the following bug.

Bug description:
[Paste the error message / screenshot description / failing behavior.]

Reproduction steps:
1. [step]
2. [step]
3. [step]

Expected behavior:
[What should happen.]

Actual behavior:
[What happens now.]

Planning requirements:
- Inspect relevant files before proposing changes.
- Identify the most likely root cause.
- List alternative hypotheses if uncertain.
- Propose the smallest safe fix.
- Include verification commands.
- Include rollback plan.

Rules:
- Do not modify files.
- Do not apply fixes yet.
- Do not hide uncertainty.
- If the error depends on env vars or external services, say what needs to be checked.

Expected output:
1. Root cause hypothesis
2. Evidence from repo
3. Files to inspect/change
4. Fix plan
5. Test plan
6. Risks / unknowns
```

---

## 4. Supabase / 数据库改造模板

适合 schema、RLS、数据表、API 查询、迁移计划相关任务。

```text
Use the writing-plans skill.

Task:
Create a database-aware implementation plan for [task].

Context:
- The app uses Supabase.
- Existing schema may already support part of this feature.
- We want to avoid unnecessary migrations.

Requirements:
- Inspect existing Supabase usage in the codebase.
- Identify relevant tables, columns, types, and relationships.
- Determine whether schema changes are required.
- If schema changes are required, propose migration steps.
- Consider RLS policies if applicable.
- Include frontend/backend changes needed to consume the data.
- Include test and verification steps.

Rules:
- Do not modify files.
- Do not create migrations yet.
- Do not assume table names or columns without evidence.
- If schema is missing, list required fields explicitly.
- Avoid destructive database changes.

Expected output:
1. Current data model understanding
2. Required data model
3. Schema gap analysis
4. Implementation plan
5. Migration plan, if needed
6. RLS/security considerations
7. Verification plan
```

---

## 5. 多服务联动模板

适合架构：Next.js + Supabase + Python service + Cloud Run。

```text
Use the writing-plans skill.

Task:
Create an implementation plan for a multi-service feature.

System context:
- Frontend/business layer: Next.js + TypeScript
- Database/auth/storage: Supabase
- AI/data processing service: Python
- Deployment: Google Cloud / Cloud Run
- Users are mainly in China, with possible proxy/region constraints.

Feature:
[Describe the feature.]

Requirements:
- Identify which layer should own each responsibility.
- Define API boundaries between services.
- Define required database reads/writes.
- Define env vars and secrets needed.
- Include local development workflow.
- Include deployment considerations.
- Include observability/logging requirements.
- Include failure modes and fallback behavior.

Rules:
- Do not modify files.
- Do not over-engineer.
- Prefer a simple MVP path first.
- Separate MVP from future improvements.
- Make all assumptions explicit.

Expected output:
1. Layer responsibility split
2. API/data flow design
3. Files/services likely involved
4. Step-by-step implementation plan
5. Env/config requirements
6. Local verification plan
7. Deployment verification plan
8. Risks and fallback plan
```

---

## 6. 重构模板

适合整理项目、降低技术债，但不希望 Codex 一次性大改。

```text
Use the writing-plans skill.

Task:
Create a safe refactoring plan for [area/module].

Goal:
[Describe why this refactor is needed.]

Constraints:
- Preserve existing behavior.
- Avoid large rewrites.
- Keep public APIs stable unless explicitly approved.
- Prefer small, reviewable commits.

Planning requirements:
- Inspect current implementation.
- Identify coupling, duplication, dead code, and risky areas.
- Separate mechanical changes from behavior changes.
- Propose an incremental refactor sequence.
- Include test commands after each phase.
- Include rollback strategy.

Rules:
- Do not modify files.
- Do not change behavior in the plan unless clearly marked.
- Do not combine unrelated refactors.
- Stop if test coverage is insufficient and say what manual checks are needed.

Expected output:
1. Current architecture summary
2. Problems found
3. Refactor goals
4. Non-goals
5. Step-by-step refactor plan
6. Verification plan
7. Risk assessment
```

---

## 7. 从 PRD / Spec 到开发计划

如果 PRD 在 Notion，可以优先用 `notion-spec-to-implementation`。如果只是把一段 PRD 粘给 Codex，可以用这个模板。

```text
Use the writing-plans skill.

Task:
Turn the following product spec into an engineering implementation plan.

Product spec:
[paste PRD/spec here]

Project context:
- [tech stack]
- [existing modules]
- [known constraints]

Planning requirements:
- Extract functional requirements.
- Extract non-functional requirements.
- Identify MVP scope.
- Separate must-have from nice-to-have.
- Map requirements to files/modules.
- Identify data model/API changes.
- Include test and acceptance criteria.

Rules:
- Do not modify files.
- Do not implement yet.
- If the spec is too broad, propose a smaller MVP.
- Make assumptions explicit.

Expected output:
1. Requirement summary
2. MVP scope
3. Non-goals
4. Implementation plan
5. Data/API changes
6. Acceptance criteria
7. Execution prompt
```

---

## 8. 最短默认模板

日常最常用，可以直接复制。

```text
Use the writing-plans skill.

Create a detailed implementation plan for this repo.

Task:
[你的任务]

Rules:
- Plan first, no code changes.
- Inspect the repo before planning.
- Prefer the smallest safe change.
- List exact files likely to change.
- Include test commands.
- Include risks and blockers.
- End with a ready-to-use execution prompt.
```

---

## 9. 后续执行模板

计划确认后，再用这个执行。

```text
Use the executing-plans skill to implement the approved plan step by step.

Rules:
- Make one logical change at a time.
- Update the checklist after each completed step.
- Summarize changed files after each step.
- Run relevant verification commands.
- Stop and ask if blocked by schema, env vars, secrets, migrations, or failing tests.
```

---

## 10. 更新计划模板

如果执行中发现计划和代码现状不一致，用这个。

```text
Use the updating-plans skill.

Revise the existing implementation plan based on the current repo state.

Rules:
- Do not modify source code.
- Keep completed items marked as done.
- Preserve useful history.
- Update only the remaining plan.
- Explain what changed and why.
- End with the next recommended execution step.
```

---

## 11. 收尾交接模板

任务完成后，用这个把上下文沉淀下来。

```text
Use the project-handoff skill.

Summarize the completed work.

Include:
1. Goal
2. What changed
3. Files changed
4. Verification commands and results
5. Known issues
6. Deployment notes
7. Next recommended tasks
8. Suggested commit message
```

---

## 12. 建议保存位置

可以把本文保存到项目里：

```text
docs/ai-handoff/WRITING_PLANS_PROMPTS.md
```

或者：

```text
docs/superpowers/prompts/writing-plans.md
```
