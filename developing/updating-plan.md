# Updating Plans Prompt Templates

这些模板用于 Codex / AI Agent 在已有 implementation plan 的情况下，根据最新 repo 状态、需求变化、执行失败、测试结果或新的约束来**更新计划**，但**不直接修改源码**。

推荐搭配工作流：

```text
define-goal
→ writing-plans
→ executing-plans
→ updating-plans
→ project-handoff
```

`updating-plans` 的核心定位：

```text
计划过期 / 需求变了 / 执行失败 / repo 状态不一致
→ 先更新计划
→ 再继续执行
```

---

## 1. 通用更新计划模板

适合大多数“计划需要改一下”的场景。

```text
Use the updating-plans skill.

Task:
Update the existing implementation plan based on the current repo state and latest requirements.

Plan:
[Paste the current plan here, or reference the plan file path.]

New context / changes:
[Describe what changed, what failed, or what new requirement was added.]

Rules:
- Do not modify source code.
- Do not implement anything.
- Inspect only the files needed to validate the plan.
- Keep completed checklist items marked as done.
- Preserve useful history.
- Update remaining tasks into the safest execution order.
- Add revision notes explaining what changed and why.
- Make assumptions explicit.
- Ask clarification questions only if blocked.

Expected output:
1. Current plan status
2. What is still valid
3. What is outdated
4. What needs to change
5. Updated step-by-step plan
6. Updated verification plan
7. Risks / blockers
8. Recommended next execution prompt
```

---

## 2. 从计划文件更新

适合计划保存在项目里，例如 `docs/superpowers/plans/*.md`。

```text
Use the updating-plans skill.

Update this plan file:

docs/superpowers/plans/[plan-name].md

Reason for update:
[Explain why the plan needs to be updated.]

Rules:
- Read the existing plan first.
- Inspect current repo state only as needed.
- Do not modify source code.
- Keep completed `[x]` items unchanged unless clearly wrong.
- Keep incomplete `[ ]` items but revise, split, remove, or reorder as needed.
- Add a "Revision notes" section.
- Include date/time if useful.
- Explain why each major plan change was made.
- End with the next recommended unchecked task.

Expected output:
1. Summary of changes to the plan
2. Updated plan content
3. Next recommended execution step
4. Whether executing-plans can continue safely
```

---

## 3. 执行失败后更新计划

适合 `executing-plans` 执行中遇到测试失败、类型错误、构建失败，不能继续硬改的情况。

```text
Use the updating-plans skill.

The previous execution attempt failed. Update the implementation plan before continuing.

Plan:
[Paste or reference the current plan.]

Failure details:
- Failed command: [command]
- Error summary: [error]
- Files changed before failure: [files]
- Suspected cause: [if known]

Rules:
- Do not modify source code.
- Do not try another fix yet.
- Determine whether the failure was caused by the current step.
- Determine whether the plan is stale or incomplete.
- Update the plan to address the failure safely.
- If the failure is unrelated to the current task, document it separately.
- Add a verification step that would catch this failure earlier next time.

Expected output:
1. Failure analysis
2. Whether the current plan caused the failure
3. Plan changes needed
4. Updated remaining checklist
5. Updated verification commands
6. Recommended next prompt for executing-plans
```

---

## 4. Next.js / Supabase 项目更新计划模板

适合你的官网、投研 App、全资产净值管理 App 等 Next.js + Supabase 项目。

```text
Use the updating-plans skill.

Update the existing implementation plan for this Next.js + Supabase project.

Plan:
[Paste or reference the plan.]

New information:
[Describe new schema info, route change, UI constraint, Supabase issue, or product requirement.]

Project constraints:
- Keep the existing UI and route structure stable.
- Prefer minimal and incremental changes.
- Avoid unnecessary schema changes.
- Do not add auth/payment unless explicitly in scope.
- Do not hardcode secrets.
- Preserve current TypeScript patterns where possible.

Update requirements:
- Re-check relevant routes, components, data fetching logic, and Supabase usage.
- Validate table/column assumptions.
- Update the plan if the schema or data flow assumptions were wrong.
- Add loading/empty/error state requirements if missing.
- Update typecheck/lint/build verification commands.
- Identify any RLS or permission risks.

Rules:
- Do not modify files.
- Do not create migrations.
- Do not implement code.
- Ask only if blocked.

Expected output:
1. Current plan validity
2. Updated Supabase assumptions
3. Updated file change list
4. Updated implementation steps
5. Updated verification plan
6. Risks and blockers
7. Next execution prompt
```

---

## 5. 数据库 / Migration 计划更新模板

适合 schema、RLS、migration、数据修复计划发生变化时使用。

```text
Use the updating-plans skill.

Update the database-related implementation plan.

Plan:
[Paste or reference the current plan.]

New database context:
[Paste schema, migration error, RLS issue, table/column discovery, or dashboard findings.]

Safety rules:
- Do not run database commands.
- Do not create or edit migration files.
- Do not modify source code.
- Do not propose destructive changes unless clearly justified.
- Prefer additive migrations.
- Separate local, staging, and production steps.
- Include rollback strategy.

Update requirements:
- Validate table names, column names, types, indexes, and relationships.
- Re-check RLS/security assumptions.
- Identify whether schema changes are still required.
- Update migration sequence if needed.
- Add verification queries or checks.
- Add manual dashboard steps only when necessary.

Expected output:
1. Schema gap analysis
2. What changed from the old plan
3. Updated migration plan
4. Updated code-impact plan
5. Verification and rollback plan
6. Stop conditions before execution
```

---

## 6. 多服务联动计划更新模板

适合 Next.js + Supabase + Python service + Cloud Run / GCP 等多服务架构。

```text
Use the updating-plans skill.

Update the multi-service implementation plan.

Plan:
[Paste or reference the current plan.]

New information:
[Describe updated API contract, env var issue, deployment constraint, service failure, or architecture decision.]

System context:
- Frontend/business layer: Next.js + TypeScript
- Database/auth/storage: Supabase
- AI/data processing service: Python
- Deployment: Google Cloud / Cloud Run
- Users are mainly in China, so network/proxy constraints may matter.

Rules:
- Do not modify source code.
- Do not deploy.
- Do not change cloud resources.
- Keep service boundaries explicit.
- Prefer MVP path over over-engineering.
- Separate frontend, backend, database, and deployment steps.

Update requirements:
- Revalidate API boundaries.
- Revalidate env vars and secrets.
- Revalidate data flow.
- Update local verification plan.
- Update deployment verification plan.
- Add fallback behavior if an external service fails.
- Identify billing/quota/network risks.

Expected output:
1. What part of the old plan is stale
2. Updated responsibility split
3. Updated API/data flow
4. Updated step-by-step plan
5. Updated env/config requirements
6. Updated verification plan
7. Recommended next execution step
```

---

## 7. 重构计划更新模板

适合 refactor 执行中发现范围过大、测试不够、依赖关系复杂，需要重新拆分。

```text
Use the updating-plans skill.

Update the refactoring plan.

Plan:
[Paste or reference the current refactor plan.]

New findings:
[Describe unexpected coupling, failing tests, circular dependencies, unclear ownership, or scope changes.]

Rules:
- Do not modify source code.
- Preserve existing behavior.
- Do not expand scope without explicitly marking it.
- Separate mechanical refactor steps from behavior changes.
- Keep each step small and reviewable.
- If test coverage is weak, add manual verification steps.

Update requirements:
- Identify which refactor steps are still safe.
- Split large steps into smaller steps.
- Reorder high-risk steps later.
- Add verification after each phase.
- Add rollback strategy for each risky phase.
- Mark any behavior-changing step clearly.

Expected output:
1. Refactor risk reassessment
2. Updated non-goals
3. Updated phased refactor plan
4. Updated verification plan
5. Steps requiring explicit approval
6. Next safe execution step
```

---

## 8. 需求变更后更新计划

适合产品需求、MVP 范围、优先级发生变化。

```text
Use the updating-plans skill.

Update the existing plan because the product requirements changed.

Current plan:
[Paste or reference the plan.]

Requirement changes:
- Added: [new requirements]
- Removed: [requirements removed from scope]
- Changed: [modified requirements]
- Priority changes: [if any]

Rules:
- Do not modify source code.
- Keep completed work acknowledged.
- Separate MVP from future improvements.
- Do not expand implementation scope unless required.
- Update non-goals if needed.
- Update acceptance criteria.
- Update test plan.

Expected output:
1. Requirement diff
2. Impact on current plan
3. Updated MVP scope
4. Updated non-goals
5. Updated implementation checklist
6. Updated acceptance criteria
7. Next recommended execution prompt
```

---

## 9. Repo 状态变化后更新计划

适合换了分支、pull 了新代码、别人改过相关文件、计划和现实不一致。

```text
Use the updating-plans skill.

Update the plan based on the current repo state.

Plan:
[Paste or reference the plan.]

Repo change context:
[Describe what changed, e.g. pulled latest main, merged branch, resolved conflicts, schema changed, files moved.]

Rules:
- Do not modify source code.
- Inspect current git status and relevant files.
- Determine whether planned file paths still exist.
- Determine whether planned changes are still necessary.
- Update the plan to match the current repo.
- Preserve completed tasks and useful notes.
- Identify conflicts with existing changes.

Expected output:
1. Current repo status summary
2. Plan vs repo mismatch
3. Updated file/module list
4. Updated checklist
5. Conflicts or blockers
6. Whether execution can safely continue
```

---

## 10. 计划太大时重新收缩

适合 Codex 计划发散，或者你觉得实现范围太大。

```text
Use the updating-plans skill.

The current plan is too large. Update it into a smaller MVP plan.

Current plan:
[Paste or reference the plan.]

Scope constraint:
- Time budget: [e.g. 1 day / 2 days / 1 week]
- Must-have outcome: [core deliverable]
- Nice-to-have items should be deferred.

Rules:
- Do not modify source code.
- Be strict about scope.
- Separate MVP from later phases.
- Prefer the smallest shippable change.
- Preserve only the tasks required for the MVP.
- Move everything else to "Later".

Expected output:
1. Reduced MVP goal
2. Removed/deferred items
3. Updated MVP checklist
4. Updated verification plan
5. Later roadmap
6. Next execution prompt
```

---

## 11. 收尾前更新计划

适合代码已经基本完成，但计划文件还没同步、需要整理最终状态。

```text
Use the updating-plans skill.

Update the plan to reflect the actual completed work before handoff.

Plan:
[Paste or reference the plan.]

Implementation summary:
[Paste recent execution summary, changed files, test results, known issues.]

Rules:
- Do not modify source code.
- Mark truly completed items as `[x]`.
- Keep unfinished items as `[ ]`.
- Add notes for skipped or deferred items.
- Add verification results.
- Add known issues.
- Add recommended next steps.
- Prepare the plan for project-handoff.

Expected output:
1. Updated completion status
2. Items completed
3. Items deferred
4. Verification summary
5. Known issues
6. Recommended project-handoff prompt
```

---

## 12. 最短日常口令

日常发现计划不准时，可以直接用这个。

```text
Use the updating-plans skill.

Update the current implementation plan based on the latest repo state and new information.

Rules:
- Do not modify source code.
- Keep completed tasks marked as done.
- Revise only the remaining plan.
- Add revision notes.
- End with the next recommended executing-plans prompt.
```

---

## 13. 和 executing-plans 配合的失败恢复流程

执行失败时，不要让 Codex 继续乱修，按这个流程来。

第一步：暂停执行。

```text
Stop executing for now.

Summarize:
- Last completed step
- Files changed
- Failed command
- Error summary
- Whether changes were committed
```

第二步：更新计划。

```text
Use the updating-plans skill.

The previous executing-plans run failed. Update the plan before continuing.

Failure summary:
[Paste the failure summary.]

Rules:
- Do not modify source code.
- Analyze whether the plan is stale or incomplete.
- Update the remaining checklist.
- Add a verification step to prevent this failure from recurring.
- Recommend the next safe execution step.
```

第三步：重新执行。

```text
Use the executing-plans skill.

Continue from the updated plan.

Rules:
- Start from the next unchecked task.
- Make one logical change at a time.
- Run the updated verification commands.
- Stop on blockers.
```

---

## 14. 推荐保存位置

可以把本文档保存到项目里：

```text
docs/ai-handoff/PROMPTS_UPDATING_PLANS.md
```

计划文件建议保存到：

```text
docs/superpowers/plans/<task-name>.md
```

执行进度建议同步到：

```text
docs/ai-handoff/CURRENT_TASKS.md
```

这样在 Codex 新会话里可以快速恢复上下文。
