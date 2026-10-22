---
name: git-commit-push-gitee
description: 提交并推送当前项目到 Gitee 仓库。Use when the user asks to commit and push the current project to Gitee (https://gitee.com/wei-peng0328/weipeng).
---

# Git Commit & Push to Gitee

Commit all changes and push to the Gitee remote repository.

## Workflow

1. Check status and diff:
   ```powershell
   git status
   git diff
   git log --oneline -5
   ```

2. Stage all intended files:
   ```powershell
   git add -A
   ```

3. Commit with a concise message describing the changes:
   ```powershell
   git commit -m "描述本次变更内容"
   ```
   Rules:
   - Do NOT commit secrets or keys.
   - Do NOT skip hooks or use `--no-verify` unless explicitly requested.
   - Do NOT use interactive `-i` or force-push unless explicitly requested.

4. Push to the Gitee remote:
   ```powershell
   git push origin master
   ```
   If the branch is not `master`, use the actual branch name (check with `git branch`).
   If push is rejected due to remote changes, pull first (`git pull --rebase origin master`), resolve any conflicts, then push again.

5. Report the result (commit hash, push status) to the user.

## Notes

- Remote: `origin` → `https://gitee.com/wei-peng0328/weipeng.git`
- If remote is missing, add it: `git remote add origin https://gitee.com/wei-peng0328/weipeng.git`
- If auth fails, ask the user to configure Gitee credentials (personal access token or SSH key).
- Verify push succeeded by checking output contains "master -> master" or similar confirmation.
