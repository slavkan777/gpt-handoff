REQUEST_ID: REQ-2026-06-03-gpu-diagnostics-readonly
STATE: READY_FOR_CLAUDE
TASK_TYPE: general
PROJECT: NONE
TARGET_REPORT_PATH: _BRIDGE/LATEST_REPORT.md
AIKB_UPDATE_REQUIRED: no
CREATED: 2026-06-03T00:00:00+03:00
CREATED_BY: architect-gpt
GATE: audit

# GPU DIAGNOSTICS — READ-ONLY TEST REQUEST

## CURRENT STATE
We are testing the Slava → Architect GPT → GitHub bridge → Claude → GitHub report → Architect GPT audit loop.

Known prior context:
- Local Claude/work area may be: C:\Users\DEVELOPER\Claude
- Previous diagnostic branch name may exist: diagnostics/gpu-bf2042-dxgi-tdr
- The user wants a fast, short, evidence-based diagnostic report.
- This is a read-only diagnostic test, not a repair task.

## CURRENT GATE
audit / read-only diagnostics

## WHY THIS TASK EXISTS
We need to test the prompt/report workflow on a small practical task: diagnose possible GPU / driver / DXGI / TDR instability without making system changes.

## GOAL
Collect enough local evidence to answer:
1. Which GPU is installed?
2. Which display driver version/date is installed?
3. Which Windows build is running?
4. Are there recent GPU/display/DXGI/TDR-related errors?
5. Is there evidence pointing more toward driver crash, overheating, game-specific crash, DirectX issue, power/PSU instability, or insufficient evidence?
6. What is the next safest manual step?

## DONE STATE
The task is done when Claude publishes a sanitized report to `slavkan777/gpt-handoff/_BRIDGE/LATEST_REPORT.md` containing:
- REQUEST_ID exactly matching this request.
- GPU model and driver evidence.
- Windows version/build evidence.
- Recent relevant System/Application event log evidence.
- Any available DXGI/TDR/display driver crash evidence.
- Commands run.
- Clear diagnosis category: FACT / INFER / RISK / NEXT.
- No fixes performed.
- No secrets or private data included.

## RISK PROFILE
LOW if read-only.
YELLOW/STOP if any action would change drivers, registry, BIOS, power settings, game files, system files, or Windows settings.

## READ FIRST
If accessible, inspect:
- current local folder: `C:\Users\DEVELOPER\Claude`
- current git state if it is a repo
- any existing diagnostic notes/reports about GPU, BF2042, DXGI, TDR

## READ-ONLY SCOPE
Allowed:
- Inspect local files in the diagnostic workspace.
- Run read-only Windows/PowerShell diagnostic commands.
- Read Windows event logs.
- Generate a local markdown report.
- Upload sanitized report to gpt-handoff if GitHub access is already configured.

## WRITABLE SCOPE
Allowed only:
- A local temporary diagnostic report file, e.g.:
  `C:\Users\DEVELOPER\Claude\reports\gpu-diagnostics-readonly-2026-06-03.md`
- GitHub handoff report:
  `slavkan777/gpt-handoff/_BRIDGE/LATEST_REPORT.md`

## FORBIDDEN SCOPE
Do NOT:
- install/reinstall/update/rollback GPU drivers;
- run DDU;
- edit registry;
- change BIOS/UEFI;
- change GPU clocks/voltage/fan curves;
- run stress tests or benchmarks;
- delete, clean, reset, stash, move, archive files;
- change game settings/files;
- change Windows power settings;
- expose secrets/tokens/credentials;
- commit/push source repo changes.

## PRESERVE
- System state.
- Driver state.
- Game files.
- Git working tree.
- Existing reports unless writing only the declared handoff report.

## NON-GOALS
- No repair.
- No optimization.
- No driver recommendation beyond next safe step.
- No hardware replacement recommendation unless evidence is strong.
- No long theory.

## ALLOWED COMMANDS
Use only read-only commands such as:

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber, OsHardwareAbstractionLayer

Get-CimInstance Win32_VideoController |
  Select-Object Name, AdapterCompatibility, DriverVersion, DriverDate, VideoProcessor, AdapterRAM, CurrentHorizontalResolution, CurrentVerticalResolution

Get-CimInstance Win32_PnPSignedDriver |
  Where-Object { $_.DeviceClass -eq "DISPLAY" } |
  Select-Object DeviceName, Manufacturer, DriverVersion, DriverDate, InfName

Get-Command nvidia-smi -ErrorAction SilentlyContinue
nvidia-smi

Get-Command dxdiag -ErrorAction SilentlyContinue
dxdiag /t "$env:TEMP\dxdiag_gpu_diag.txt"
Get-Content "$env:TEMP\dxdiag_gpu_diag.txt" -TotalCount 120

Get-WinEvent -LogName System -MaxEvents 300 |
  Where-Object {
    $_.ProviderName -match "Display|nvlddmkm|amdwddmg|igfx|WHEA-Logger" -or
    $_.Message -match "Display driver|TDR|DXGI|LiveKernelEvent|video|graphics"
  } |
  Select-Object TimeCreated, ProviderName, Id, LevelDisplayName, Message |
  Format-List

Get-WinEvent -LogName Application -MaxEvents 300 |
  Where-Object {
    $_.Message -match "DXGI|DirectX|nvlddmkm|amdwddmg|LiveKernelEvent|Battlefield|BF2042|Display"
  } |
  Select-Object TimeCreated, ProviderName, Id, LevelDisplayName, Message |
  Format-List

git status
git branch --show-current
git rev-parse --short HEAD
```

If a command is unavailable or fails, record the error and continue.

## FORBIDDEN COMMANDS
Do not run:
```powershell
pnputil /delete-driver
pnputil /add-driver
winget install
winget upgrade
choco install
sfc /scannow
DISM /Online /Cleanup-Image /RestoreHealth
reg add
reg delete
bcdedit
powercfg /set*
git reset
git clean
git stash
git checkout
rm -r
Remove-Item -Recurse
```

## STOP CONDITIONS
Stop and report if:
- task requires admin elevation for anything beyond read-only logs;
- a command would modify system state;
- secrets/tokens appear in any output;
- GitHub upload would require exposing credentials;
- evidence is insufficient.

## VERIFICATION STEPS
Before final report:
1. Confirm no driver/system/game changes were made.
2. Confirm no forbidden command was run.
3. Confirm report contains REQUEST_ID.
4. Confirm report contains commands run and evidence.
5. Confirm report is sanitized.

## GITHUB HANDOFF
Publish sanitized report to:
`slavkan777/gpt-handoff/_BRIDGE/LATEST_REPORT.md`

If upload fails:
- save local report;
- state the local path;
- do not perform extra actions.

## REPORT BACK FORMAT
Use this exact structure:

```markdown
REQUEST_ID: REQ-2026-06-03-gpu-diagnostics-readonly
STATUS: DONE | PARTIAL | BLOCKED | FAILED | READY_FOR_AUDIT
TASK_TYPE: general
PROJECT: NONE
COMPLETED: <timestamp>
COMPLETED_BY: claude

# GPU Diagnostics Read-Only Report

## Summary
## Evidence
### GPU / Driver
### Windows
### Event Logs
### DXGI / TDR / Display Crash Signals
## Files read
## Files changed
## Commands run
## Verification evidence
## Git status / branch / HEAD
## Not touched
## Risks / open questions
## Diagnosis
FACT:
INFER:
RISK:
NEXT:
## Next safe step
```

## NEXT GATE
After Claude publishes the report, Slava writes `отчёт` to Architect GPT.
Architect GPT audits the report and returns:
- VERDICT
- EVIDENCE
- ISSUES
- NEXT
