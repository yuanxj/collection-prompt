# Executing Plans Prompt Templates

这些模板用于 Codex / AI Agent 在已经有 implementation plan 之后，按步骤安全执行、更新进度、运行验证，并在遇到风险时暂停。

推荐搭配工作流：

```text
define-goal
→ writing-plans
→ executing-plans
→ updating-plans
→ project-handoff
```

---

## 1. 通用执行模板

适合大多数已经有计划的工程任务。

```text
Use the executing-plans skill.

Plan:
[Paste the approved plan here, or reference the plan file path.]

Execution rules:
- Follow the approved plan step by step.
- Make one logical change at a time.
- Update the plan checklist after each completed step.
- Run relevant verification commands after each meaningful change.
- Summarize changed files and verification results after each step.
- Stop and ask before making destructive changes.
- Stop if the plan conflicts with the current repo state.
- Stop if required env vars, secrets, schema fields, or external services are missing.

Output after each step:
1. Completed step
2. Files changed
3. Commands run
4. Verification result
5. Next step
6. Blockers, if any
```

---

## 2. 从计划文件执行

适合你把 `writing-plans` 生成的计划保存到了项目里，例如 `docs/superpowers/plans/*.md`。

```text
Use the executing-plans skill.

Execute this approved plan:

docs/superpowers/plans/[plan-name].md

Rules:
- Read the plan first.
- Confirm the next unchecked `[ ]` task before starting.
- Execute only one checklist item at a time.
- Mark completed items as `[x]`.
- Add short progress notes under each completed item:
  - files changed
  - commands run
  - verification result
  - known issues
- Do not skip checklist items silently.
- Do not modify unrelated files.
- Stop if the plan is stale or no longer matches the repo.

After each step, report:
- Progress
- Changes
- Verification
- Next recommended step
```

---

## 3. Next.js / Supabase 功能执行模板

适合你的官网、投研 App、全资产净值管理 App 这类 Next.js + Supabase 项目。

```text
Use the executing-plans skill.

Execute the approved implementation plan for this Next.js + Supabase task.

Plan:
[Paste plan or reference plan path.]

Project constraints:
- Keep the existing UI and route structure stable.
- Prefer minimal and incremental changes.
- Do not redesign components unless the plan explicitly says so.
- Do not change database schema unless the plan explicitly says so.
- Do not add auth/payment unless explicitly in scope.
- Do not hardcode production secrets.

Execution rules:
- First inspect the files listed in the plan.
- Make one logical change at a time.
- Preserve existing TypeScript types where possible.
- Add or update types only when needed.
- Handle loading, empty, and error states if relevant.
- Run typecheck/lint/build commands if available.
- Update the plan checklist after each step.

Verification:
- Check package.json for available commands.
- Prefer:
  - npm run typecheck
  - npm run lint
  - npm run build
  - npm test
- If a command is unavailable, say so clearly.

Stop conditions:
- Missing Supabase env vars
- Unknown table or column names
- RLS/security uncertainty
- Production-impacting migration needed
- Failing tests unrelated to current change
```

---

## 4. Bug Fix 执行模板

适合已经有 bug fix plan，现在让 Codex 一步步修。

```text
Use the executing-plans skill.

Execute the approved bug fix plan.

Bug:
[Brief bug description.]

Plan:
[Paste plan or reference plan path.]

Rules:
- Start with the smallest safe fix.
- Do not refactor unrelated code.
- Confirm the root-cause hypothesis before editing.
- Make one change at a time.
- Run the narrowest relevant verification first.
- Then run broader checks if needed.
- If the first fix does not work, stop and update the plan before trying a different approach.

After each step, report:
1. What changed
2. Why it should fix the bug
3. Verification command
4. Result
5. Remaining risk
```

---

## 5. 数据库 / Supabase Migration 执行模板

适合 schema、migration、RLS、数据修复等高风险任务。

```text
Use the executing-plans skill.

Execute the approved database-related plan.

Plan:
[Paste plan or reference plan path.]

Safety rules:
- Do not run destructive database commands without explicit confirmation.
- Do not drop columns, tables, indexes, or policies unless explicitly approved.
- Do not modify production data unless explicitly approved.
- Prefer additive migrations.
- Keep rollback steps clear.
- Confirm target environment before running migrations.

Execution steps:
- Inspect existing migration files.
- Inspect current Supabase client/query usage.
- Apply only the planned schema changes.
- Update generated types if the project uses them.
- Update code that depends on changed schema.
- Run local verification commands.
- Document any manual Supabase dashboard steps if needed.

Stop conditions:
- Ambiguous table/column names
- Missing RLS policy requirements
- Production vs local environment unclear
- Migration may cause data loss
- Required Supabase credentials missing
```

---

## 6. 多服务联动执行模板

适合 Next.js + Supabase + Python service + Cloud Run / GCP 的多服务开发任务。

```text
Use the executing-plans skill.

Execute the approved multi-service implementation plan.

Plan:
[Paste plan or reference plan path.]

System context:
- Frontend/business layer: Next.js + TypeScript
- Database/auth/storage: Supabase
- AI/data processing service: Python
- Deployment: Google Cloud / Cloud Run
- Users are mainly in China, so network/proxy constraints may matter.

Rules:
- Execute one service boundary at a time.
- Do not mix frontend, backend, and infra changes in one large diff.
- Keep API contracts explicit.
- Update env var documentation when adding config.
- Add logging around external service calls if relevant.
- Prefer local verification before deployment changes.
- Do not deploy unless explicitly asked.

Execution order preference:
1. Types/contracts
2. Database/API layer
3. Backend service logic
4. Frontend integration
5. Tests and verification
6. Deployment/docs

Stop conditions:
- API contract is unclear
- Env vars/secrets are missing
- Cloud project/region is unclear
- Deployment may affect production
- External service quota or billing impact is unclear
```

---

## 7. 重构执行模板

适合已经写好 refactor plan，需要小步执行、避免大改崩掉。

```text
Use the executing-plans skill.

Execute the approved refactoring plan.

Plan:
[Paste plan or reference plan path.]

Rules:
- Preserve existing behavior.
- Make mechanical changes separately from behavior changes.
- Avoid unrelated cleanup.
- Keep each diff reviewable.
- Run checks after each refactor phase.
- If tests are weak or missing, identify manual verification steps.
- Update imports/types carefully.
- Stop before changing public APIs unless explicitly approved.

Execution process:
1. Execute the next small refactor step.
2. Run narrow verification.
3. Run broader verification if available.
4. Update the plan checklist.
5. Report changed files and risk.
6. Continue only to the next planned step.

Stop conditions:
- Public API change required
- Unexpected circular dependency
- Tests fail unexpectedly
- Refactor scope grows beyond the approved plan
```

---

## 8. 执行并维护进度日志

适合你希望 Codex 在计划文件里持续记录执行状态，方便换会话和 handoff。

```text
Use the executing-plans skill.

Execute the approved plan and maintain a progress log.

Plan file:
docs/superpowers/plans/[plan-name].md

Progress log:
docs/ai-handoff/CURRENT_TASKS.md

Rules:
- Before editing code, read both files.
- Execute the next unchecked task in the plan.
- After completing a step, update:
  1. The plan checklist
  2. CURRENT_TASKS.md
- Include:
  - completed step
  - changed files
  - verification commands
  - test results
  - known blockers
  - next recommended task
- Do not overwrite unrelated handoff content.
- Keep updates concise and useful for the next session.
```

---

## 9. 失败后暂停并更新计划

适合执行中遇到失败，不要让 Codex 硬改。

```text
Use the executing-plans skill.

Continue executing the approved plan, but follow this failure policy:

Failure policy:
- If a verification command fails, stop.
- Summarize the failing command and error.
- Identify whether the failure is caused by the current change.
- If the failure is unrelated, do not fix it unless asked.
- If the plan is outdated, stop and recommend using updating-plans.
- Do not try multiple unrelated fixes in one run.

Output on failure:
1. Failed command
2. Error summary
3. Likely cause
4. Whether current change caused it
5. Recommended next action
6. Whether the plan should be updated
```

---

## 10. 最短日常口令

日常执行计划时可以直接用这个。

```text
Use the executing-plans skill to execute the approved plan step by step.

Rules:
- Make one logical change at a time.
- Update the checklist after each completed step.
- Run relevant checks.
- Summarize changed files and verification results.
- Stop on blockers or risky/destructive operations.
```

---

## 11. 和 writing-plans 配合的完整流程

第一步：写计划，不动代码。

```text
Use the writing-plans skill.

Create a detailed implementation plan for this repo.

Task:
[Your task]

Rules:
- Plan first, no code changes.
- Inspect the repo before planning.
- Prefer the smallest safe change.
- List exact files likely to change.
- Include test commands.
- Include risks and blockers.
- End with a ready-to-use execution prompt.
```

第二步：确认计划后执行。

```text
Use the executing-plans skill.

Execute the approved plan step by step.

Rules:
- Follow the plan exactly unless the repo state proves it is stale.
- Update the plan checklist after each step.
- Run verification commands.
- Stop and ask if blocked.
```

第三步：执行中计划过期时更新。

```text
Use the updating-plans skill.

Revise the existing plan based on the current repo state and latest verification results.

Rules:
- Do not modify source code.
- Keep completed items marked as done.
- Reorder remaining tasks if needed.
- Add revision notes explaining what changed and why.
```

第四步：收尾交接。

```text
Use the project-handoff skill.

Summarize:
- What was completed
- Files changed
- Verification results
- Known issues
- Next recommended tasks
- Any deployment or environment notes
```

---

## 推荐保存位置

可以把本文档保存到项目里：

```text
docs/ai-handoff/PROMPTS_EXECUTING_PLANS.md
```

也可以把每次生成的计划保存到：

```text
docs/superpowers/plans/<task-name>.md
```

这样 Codex 新会话可以直接接着执行。
