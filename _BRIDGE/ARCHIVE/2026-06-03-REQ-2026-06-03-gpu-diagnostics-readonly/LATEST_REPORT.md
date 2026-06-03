REQUEST_ID: REQ-2026-06-03-gpu-diagnostics-readonly
STATUS: READY_FOR_AUDIT
TASK_TYPE: general
PROJECT: NONE
COMPLETED: 2026-06-03T06:45:00+03:00
COMPLETED_BY: claude

# GPU Diagnostics Read-Only Report

## Summary

Chronic, game-correlated GPU TDR on an NVIDIA GTX 1060 6 GB driving a 3440×1440 ultrawide.
51 display-driver recoveries (Event 4101) and 757 nvlddmkm engine-hang/reset events across
history, the latest a storm on 2026-06-02 02:00–02:05 that produced a BF2042
`DXGI_ERROR_DEVICE_RESET` crash. Hardware-failure signals are effectively absent (1 WHEA event
all-history, 0 BSOD minidumps, 0 bugcheck reports, 49 °C idle). The correct modern driver 582.28
is installed and all four prior mitigations are intact. No system changes were made.
Diagnosis: GPU TDR from VRAM/load pressure under BF2042 — not failing hardware.

## Evidence

### GPU / Driver
- NVIDIA GeForce GTX 1060, 6144 MiB (Win32 CIM under-reports 4 GB — uint32 cap; nvidia-smi confirms 6144 MiB).
- Driver 582.28 (32.0.15.8228), dated 2026-01-20, InfName oem27.inf, CUDA 13.0. Win32_VideoController, Win32_PnPSignedDriver and nvidia-smi all agree.
- Correct modern driver; the 2018 WU driver 399.24 (24.21.13.9924) is NOT present.
- nvidia-smi idle: 49 °C, P8, 12 W / 78 W, 1561 / 6144 MiB, 15 % util — healthy.
- Active mode: 3440×1440 @ 60 Hz.

### Windows
- Windows 10 Pro, 22H2, build 19045; HAL 10.0.19041.6456.

### Event Logs (all available history unless noted)
- System — 4101 "display driver recovered" (TDR): **51**
- System — nvlddmkm provider (hang 14 / reset 153): **757**
- System — LiveKernelEvent 141 (video TDR): 0
- System — WHEA-Logger (hardware errors): **1**
- System — Kernel-Power 41 (unexpected power loss): 9 (all older than the last 14 days)
- System — WER bugcheck 1001 (BSOD): 0
- Application — WER `RADAR_PRE_LEAK_64`, P1 `BF2042.exe` (2026-05-12) — process memory-leak signal.
- Application — System Restore "Before-NVIDIA-driver-repair-BF2042" (2026-05-18) — the WU-399.24 incident.

### DXGI / TDR / Display Crash Signals
- Latest TDR storm: nvlddmkm IDs 14 + 153 repeated 2026-06-02 02:00:31–02:05:04.
- Prior captured crash dump 2026-06-02 02:00:48 with tokens `DXGI_ERROR_DEVICE_RESET` ×2, `tdr` ×2, `nvwgf2um`; dumps span 2026-04-25 → 2026-06-02 (chronic).
- No nvlddmkm/TDR events after 2026-06-02 02:05 → clean since last storm (game not relaunched).
- 0 LiveKernelReports watchdog dumps and 0 Minidump files currently present.

### Mitigations confirmed present (read-only registry)
- GraphicsDrivers: TdrDelay = 8, TdrDdiDelay = 10
- WindowsUpdate: ExcludeWUDriversInQualityUpdate = 1 (399.24 cannot return via WU)
- Dwm: OverlayTestMode = 5 (MPO disabled)

## Files read
- `_BRIDGE/ACTIVE_REQUEST.md` (this request)
- vault `docs/diagnostics/gpu-bf2042-dxgi-tdr/`: `CURRENT_STATE.md`, `NEXT_ACTIONS.md` (+ directory listing of `reports/` and `evidence/`)
- internal prior-diagnosis note (not in any repo)

## Files changed
- `reports/gpu-diagnostics-readonly-2026-06-03.md` (new local working copy)
- `_BRIDGE/LATEST_REPORT.md` (this report)
- `_BRIDGE/STATUS.json` (state → REPORT_READY)
- No system / driver / registry / game / display / source-repo changes.

## Commands run (all read-only)
- `Get-ComputerInfo` (selected fields)
- `Get-CimInstance Win32_VideoController`; `Win32_PnPSignedDriver` (DISPLAY)
- `nvidia-smi`
- `Get-ItemProperty` (read) on GraphicsDrivers / WindowsUpdate / Dwm keys
- `Get-WinEvent` System & Application (FilterHashtable counts + recent samples)
- `Get-ChildItem` on `C:\Windows\LiveKernelReports`, `C:\Windows\Minidump`, vault diagnostics docs
- `git branch / rev-parse HEAD / status --porcelain` (vault, read-only); `git fetch` + `pull --ff-only` (handoff repo)

## Verification evidence
1. No driver/system/game changes — only read cmdlets, `Get-ItemProperty` (read), and file listings; zero `Set-`/`New-`/`Remove-`/`pnputil`/`winget`/registry-write calls.
2. No forbidden command executed (no DDU/sfc/DISM/bcdedit/powercfg-set/git reset-clean-stash-checkout/Remove-Item -Recurse).
3. Report contains REQUEST_ID = `REQ-2026-06-03-gpu-diagnostics-readonly`.
4. Report contains commands run + evidence (counts, IDs, timestamps).
5. Sanitized — no secrets, no credentials, no absolute user paths, no hostname, no per-process list.

## Git status / branch / HEAD
- Vault repo: branch `diagnostics/gpu-bf2042-dxgi-tdr`, HEAD `acd044d`, 53 pre-existing working-tree entries (only the new local report was added by this diagnostic; no commit/push to the vault).
- Handoff repo `gpt-handoff`: clean and synced to `origin/main` before this report; this report is the only new commit.

## Not touched
GPU driver · registry values (read-only) · BIOS/UEFI · power settings · GPU clocks/voltage/fans · game files/settings · display topology/resolution/refresh · Windows settings · source repositories (no commit/push to vault or TwinCore) · system state.

## Risks / open questions
- BF2042 will keep TDR-crashing under load until the in-game profile is reduced; this is diagnosed, not fixed.
- If `ExcludeWUDriversInQualityUpdate` is ever cleared, Windows Update may re-push the 2018 399.24 driver and regress the system.
- Hardware fault is unlikely on current evidence; re-evaluate ONLY if TDRs/colored artifacts appear at the idle desktop with no game running.
- UNKNOWN: whether the load hang is pure VRAM pressure or also thermal-under-load — resolvable only by a monitored safe-profile test.

## Diagnosis
FACT: GTX 1060 6 GB + driver 582.28 on Win10 22H2 19045; 51 TDR recoveries and 757 nvlddmkm hang/reset events; latest storm 2026-06-02 02:00–02:05 → BF2042 `DXGI_ERROR_DEVICE_RESET`; 1 WHEA all-history, 0 BSOD/minidumps; idle 49 °C, 1.5/6 GB; all 4 mitigations present; clean since last storm.
INFER: Root cause is GPU TDR from VRAM/load pressure under BF2042 (6 GB ceiling at 3440×1440; `BF2042.exe RADAR_PRE_LEAK_64` corroborates), NOT failing hardware. The 9 Kernel-Power 41 are consistent with TDR-induced hard resets earlier in the timeline, not a PSU pattern. The driver is healthy; the earlier 399.24 downgrade was a separate, now-blocked trap.
RISK: Continued black-screen/crash in BF2042 under load; regression if the WU driver-block is removed; low hardware-fault probability on present evidence.
NEXT: Reduce GAME load, not the driver — a verified safe profile is already prepared (Future Frame Rendering Off, Clamp GPU memory On, 1080p or 75–85 % resolution scale, overlays/Reflex Off, 60 fps cap). Keep all mitigations. Then a single monitored BF2042 session; success criterion = 4101 + nvlddmkm counts stay ≈0 during play.

## Next safe step
Gate B: Slava confirms no artifacts/flicker/black screen at the idle desktop. Then Gate C: Slava launches
ONE 15–20 min BF2042 session on the prepared safe profile with `nvidia-smi dmon` running, aborting on the
first artifact/black screen and running the existing crash-evidence collector. Claude does not launch the
game and changes no driver/registry/system setting.
