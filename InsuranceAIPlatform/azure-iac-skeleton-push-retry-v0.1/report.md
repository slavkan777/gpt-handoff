# Gate Report — AZURE_IAC_SKELETON_PUSH_RETRY_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** push-only retry of existing local commit + verification + handoff
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**PUSH_BLOCKED (attempt #2)** — the push of the existing, already-QA-cleared commit `a70071d` was **rejected again** with the identical reason: the GitHub PAT git uses lacks the **`workflow`** scope, and the commit adds `.github/workflows/azure-deploy-demo.yml`. **No commit change, no force, no main, no credential change.** `origin/dev` still `2e9443a`, `origin/main` untouched.

## Remote rejection (verbatim, unchanged)

```
! [remote rejected] dev -> dev (refusing to allow a Personal Access Token to create or
   update workflow `.github/workflows/azure-deploy-demo.yml` without `workflow` scope)
error: failed to push some refs to 'https://github.com/slavkan777/InsuranceAIPlatform.git'
```

## State (no change this gate)

```
branch: dev
local HEAD:   a70071d8e676126c4129e8fed00c3424082001e4   (1 ahead, intact)
origin/dev:   2e9443a6726f34f20bd26233e840ae8cc982d91a   (UNCHANGED — push rejected)
origin/main:  69e67312a10cc9bcf28c4e387a126b48c91fb9c5   (untouched)
working tree: clean except test-results/ (untracked)
force used:   NO   |   new commit: NO   |   amend/reset/rebase: NO
```

## Read-only credential diagnosis (no token printed)

| Check | Finding |
|---|---|
| `remote.origin.url` | `https://slavkan777@github.com/slavkan777/InsuranceAIPlatform.git` (HTTPS + PAT) |
| `credential.helper` | `manager-core` → Git Credential Manager → **Windows Credential Manager** |
| `gh auth status` | **gh not installed / not logged in** → so `gh auth refresh -s workflow` is NOT applicable |

**Conclusion:** the PAT git authenticates with lives in **Windows Credential Manager** (key `git:https://github.com`), not in `gh`. The fix must target that token's scope, not `gh`.

## Fix (operator action — executor will not change credentials)

Pick the case that matches the token:

**Case 1 — it's a classic PAT (most common):**
GitHub → Settings → Developer settings → Personal access tokens → **Tokens (classic)** → open the token in use → tick **`workflow`** → **Update token**. Same token string gains the scope **immediately** server-side — no re-cache needed. Then re-run this push-retry gate.

**Case 2 — you create/regenerate a NEW token (or it's fine-grained):**
1. Grant scope: classic → tick `workflow`; fine-grained → Repository permissions → **Workflows: Read and write** (+ Contents: Read and write).
2. Clear the stale cached credential so Credential Manager re-prompts for the new token:
   - Control Panel → Credential Manager → Windows Credentials → remove `git:https://github.com`; **or** `cmdkey /delete:git:https://github.com`.
3. Next `git push origin dev` will prompt for the new token → paste it (never into this chat).
Then re-run this push-retry gate.

> Verify quickly (optional): the token only needs **`repo` + `workflow`** for this push.

## Boundary confirmations

| Boundary | Status |
|---|---|
| File edits / staging / new commit / amend / reset / rebase | NO |
| Force-push / push to main | NO |
| Credential or token-scope changed by executor | NO (operator-only) |
| Workflow file removed/modified | NO |
| `az login` / resources / deploy | NO |
| Secrets / token printed | NO |
| AIKB updated | NO |

## Next recommended gate

Re-run **`AZURE_IAC_SKELETON_PUSH_RETRY_V0.1`** after the token gains `workflow` scope — the same commit `a70071d` will push cleanly (no rework). *Alternative if the scope can't be granted:* a re-scoped commit gate that drops the workflow file and defers it to `AZURE_MINIMAL_DEPLOY_V0.1` (requires explicit authorization).

## Limitations

1. Local commit `a70071d` is correct and complete; only the push is blocked. No rework needed once scope is fixed.
2. This gate made zero changes to source, remotes, Azure, or credentials.
