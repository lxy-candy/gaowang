---
name: git-commit-push-github
description: Commits all changes and pushes to GitHub remote repo. Invoke when user asks to commit, push, or sync the current project to GitHub. Do not use for Gitee.
---

# Git Commit & Push to GitHub

Commit all changes in the current workspace and push them to the GitHub repository.

## Fixed target

- Repository: `https://github.com/lxy-candy/gaowang`
- SSH address: `git@github.com:lxy-candy/gaowang.git`
- Branch: default (usually `main` or `master` — preserve existing branch name, never force-rename)

## Pre-flight checks

Run these checks **before** modifying anything. Report blockers to the user immediately.

1. **Is this a git repo?**
   ```powershell
   git rev-parse --is-inside-work-tree
   ```
   If `true`, continue. If not, ask user whether to `git init` — do **not** auto-init without explicit consent.

2. **User identity configured?**
   ```powershell
   git config user.name
   git config user.email
   ```
   If missing, set locally:
   ```powershell
   git config user.name "lxy-candy"
   git config user.email "lxy-candy@users.noreply.github.com"
   ```
   Use `github-username@users.noreply.github.com` style unless user provides a real email. Never set global config.

3. **SSH public key exists?**
   ```powershell
   Test-Path "$env:USERPROFILE\.ssh\id_ed25519.pub"
   ```
   If missing, generate non-interactively:
   ```powershell
   ssh-keygen -t ed25519 -C "github" -f "$env:USERPROFILE\.ssh\id_ed25519" -N ""
   ```
   **Tell the user explicitly**: "请把 `C:\Users\<你的用户名>\.ssh\id_ed25519.pub` 公钥全文粘贴到 GitHub → Settings → SSH and GPG keys → New SSH key（https://github.com/settings/keys）。添加完成后我才能继续 push。" 这一步需要用户在浏览器里完成，Agent 无法代替。

4. **Remote `github` configured?**
   ```powershell
   git remote -v
   ```
   - 如果当前只有 Gitee origin（常见情况），**不要覆盖 origin**。新增一个 remote 叫 `github`：
     ```powershell
     git remote add github git@github.com:lxy-candy/gaowang.git
     ```
   - 如果已存在 origin 指向 GitHub 目标地址，直接用 origin。
   - 如果存在但地址不匹配，提示用户确认后再 `git remote set-url github ...`。
   - **严禁**使用 `--force` 改写已有 origin，除非用户明确要求替换。

## Commit & push flow

1. **Check status**:
   ```powershell
   git status --short
   ```
   把改动概要列出来给用户，避免把不该提交的文件（临时日志、密钥、`.env` 等）一起推上去。

2. **Stage everything**:
   ```powershell
   git add -A
   ```

3. **Commit with a meaningful message**（优先结合用户最近一次提示词内容，不要用 "update" 这种空泛描述）:
   ```powershell
   git commit -m "feat: 数7规则演示页面 + 收录 index"
   ```
   如果 working tree 为空（没有任何改动），告知用户"无需要提交的内容"并停止，不要产生空 commit。

4. **Push to GitHub remote**：
   - 首次推送需要建立 upstream 关联：
     ```powershell
     git push -u github HEAD
     ```
   - 后续推送：
     ```powershell
     git push github
     ```

5. **Test SSH connectivity first**（可选但推荐，避免 push 卡住后才发现公钥没配对）:
   ```powershell
   ssh -T git@github.com -o StrictHostKeyChecking=accept-new
   ```
   看到 `Hi lxy-candy! You've successfully authenticated...` 就说明公钥已生效。如果提示 Permission denied (publickey)，立即停止并提醒用户先去 GitHub 页面添加公钥。

## Verification

Push 成功后，在回复中同时给出：
- commit hash（`git rev-parse --short HEAD`）
- 推送到的 remote 名和地址
- 分支名
- GitHub 网页链接：`https://github.com/lxy-candy/gaowang`

## Guardrails

- **绝不 force push**（不使用 `--force`、`-f`），除非用户明确说"覆盖远端"且理解风险
- **绝不修改 `.gitignore`** 或仓库结构，除非用户明确要求
- **绝不 rebase**、**绝不 amend** 已有 commit，保持线性历史
- **绝不覆盖已有 origin**，Gitee 和 GitHub 可以并存（用 `origin` 留 Gitee、`github` 指向 GitHub）
- **不要**在 `git push` 上用 `&` 或管道，让它前台执行以便看到认证错误
- Push 到 GitHub 成功后不要自动再 push 到 Gitee，保持两个 remote 独立
