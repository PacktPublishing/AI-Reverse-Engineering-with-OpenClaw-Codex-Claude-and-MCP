# radare2 + r2pipe Reverse Engineering Skill for Codex

## Purpose

This skill tells Codex how to use the existing radare2 + r2pipe environment on Windows 11 for reverse engineering Windows PE binaries.

This is **not** an installation guide. Assume radare2, Python, and r2pipe have already been installed and verified.

Use this skill in fresh Codex sessions so Codex knows:

- where the lab folder is located
- where binaries should be placed
- where reports should be saved
- where logs should be saved
- where reusable scripts should be saved
- when to use radare2 CLI directly
- when to use Python + r2pipe
- how to document findings
- how to avoid overwriting previous work

---

## Lab Root

Use this lab root:

```text
C:\radare2_ai
```

All radare2/r2pipe work should be organized under this folder unless the user explicitly says otherwise.

Use underscores in project folder names, for example `radare2_ai`, rather than dashes. This avoids possible confusion when Python scripts, module names, import paths, or generated helper files are later created.

---

## Folder Structure

Expected structure:

```text
C:\radare2_ai\
│
├── binaries\      # Input EXE/DLL files for analysis
├── scripts\       # Reusable PowerShell and Python scripts
├── reports\       # Final Markdown analysis reports
├── logs\          # Raw command output and execution logs
├── notes\         # Setup notes, troubleshooting notes, observations
├── skills\        # Reusable Codex skill files
└── README.md
```

If any of these folders are missing, create them before continuing:

```powershell
New-Item -ItemType Directory -Force -Path .\binaries, .\scripts, .\reports, .\logs, .\notes, .\skills
```

---

## Fresh Session Instruction

At the start of a fresh Codex session, the user may say:

```text
Read C:\radare2_ai\skills\radare2_r2pipe_skill.md and use it as your operating procedure for radare2/r2pipe reverse engineering tasks.
```

After reading this file, Codex should follow this workflow exactly unless the user gives different instructions.

---

## Environment Assumptions

Assume the environment is Windows 11 using PowerShell.

Before doing analysis, verify the working directory and tools:

```powershell
cd C:\radare2_ai
r2 -v
rabin2 -v
python --version
pip show r2pipe
```

If using Python + r2pipe, activate the virtual environment first:

```powershell
cd C:\radare2_ai
.\.venv\Scripts\Activate.ps1
```

Then verify r2pipe:

```powershell
python -c "import r2pipe; print('r2pipe ready')"
```

If the virtual environment does not exist, do not guess. Tell the user that the installation/setup step may not have been completed.

---

## General Reverse Engineering Workflow

When asked to analyze a binary:

1. Confirm or infer the binary path.
2. Check whether the file exists.
3. Identify whether the file is an EXE, DLL, or another file type.
4. Create timestamped output names for reports and logs.
5. Use radare2 CLI for quick inspection.
6. Use Python + r2pipe for structured extraction, automation, and report generation.
7. Save raw command outputs in `logs`.
8. Save final Markdown reports in `reports`.
9. Save reusable scripts in `scripts`.
10. Save working notes and troubleshooting information in `notes`.
11. Do not overwrite previous reports or logs.
12. Explain which tool was chosen and why.

---

## Tool Selection Rules

### Use direct radare2 CLI when:

Use radare2 CLI directly when the task is quick, exploratory, or command-oriented.

Examples:

- quick file inspection
- list imports
- list exports
- list strings
- list sections
- list functions
- disassemble entry point
- manually inspect a function
- demonstrate radare2 commands for learning

Useful radare2/rabin2 commands:

```text
rabin2 -I target.exe       # PE/binary information
rabin2 -i target.exe       # imports
rabin2 -E target.exe       # exports
rabin2 -z target.exe       # strings
rabin2 -S target.exe       # sections
r2 -A target.exe           # open and analyze binary
```

Useful commands inside radare2:

```text
aaa             # analyze all
iI              # binary information
ii              # imports
iE              # exports
iS              # sections
iz              # strings
afl             # list functions
s entry0        # seek to entry point
pdf @ entry0    # disassemble entry point function
pdf @ main      # disassemble main if available
axt @ <addr>    # find cross-references to address
izz             # list more strings
q               # quit
```

Use command-line batch mode where practical:

```powershell
r2 -A -q -c "iI" -c "ii" -c "iz" -c "afl" -c "pdf @ entry0" -c "q" .\binaries\target.exe
```

---

### Use Python + r2pipe when:

Use r2pipe when the task needs automation, structured extraction, JSON parsing, repeatability, or report generation.

Examples:

- generate a full Markdown report
- analyze multiple binaries
- compare two binaries
- extract imports/strings/functions into structured data
- automate repeated radare2 commands
- create reusable analysis tools
- produce cleaner output than raw radare2 text

Prefer JSON commands where possible:

```text
ij      # general binary information as JSON
iIj     # binary info as JSON
iij     # imports as JSON
iEj     # exports as JSON
izj     # strings as JSON
iSj     # sections as JSON
afij    # current function info as JSON
aflj    # functions as JSON
```

Use normal text commands only when no useful JSON command is available.

---

## Standard Output Naming

Use timestamps to avoid overwriting old work.

Timestamp format:

```text
YYYYMMDD_HHMMSS
```

Example:

```text
20260611_153000
```

---

## Report Naming

Save final analysis reports in:

```text
C:\radare2_ai\reports
```

Use this format:

```text
reports\<binary_name>_analysis_<YYYYMMDD_HHMMSS>.md
```

Example:

```text
reports\crackme1_analysis_20260611_153000.md
```

---

## Log Naming

Save raw logs in:

```text
C:\radare2_ai\logs
```

Use this format:

```text
logs\<binary_name>_<task>_<YYYYMMDD_HHMMSS>.log
```

Examples:

```text
logs\crackme1_info_20260611_153000.log
logs\crackme1_imports_20260611_153000.log
logs\crackme1_strings_20260611_153000.log
logs\crackme1_functions_20260611_153000.log
logs\crackme1_entrypoint_disasm_20260611_153000.log
```

---

## Notes Naming

Save troubleshooting notes and investigation notes in:

```text
C:\radare2_ai\notes
```

Use descriptive names:

```text
notes\setup_report.md
notes\analysis_observations_<YYYYMMDD_HHMMSS>.md
notes\troubleshooting_<YYYYMMDD_HHMMSS>.md
```

---

## Script Storage Rules

Save reusable scripts in:

```text
C:\radare2_ai\scripts
```

Before creating a new script:

1. Check whether a suitable script already exists.
2. Reuse or improve the existing script if appropriate.
3. Do not create duplicate scripts with nearly identical purposes.
4. If creating a new script, give it a clear descriptive name.

Expected script examples:

```text
scripts\basic_r2_analysis.ps1
scripts\analyze_with_r2pipe.py
scripts\extract_strings.py
scripts\extract_imports.py
scripts\function_report.py
scripts\batch_analyze.py
scripts\compare_binaries.py
```

---

## Standard PowerShell Usage

Move to the lab root:

```powershell
cd C:\radare2_ai
```

Activate Python virtual environment:

```powershell
.\.venv\Scripts\Activate.ps1
```

Run direct radare2 helper script:

```powershell
.\scripts\basic_r2_analysis.ps1 .\binaries\target.exe
```

Run r2pipe Python analysis script:

```powershell
python .\scripts\analyze_with_r2pipe.py .\binaries\target.exe
```

---

## Standard Report Format

Each final Markdown report should use this structure:

```markdown
# Binary Analysis Report

## Target

- File:
- Full path:
- SHA256:
- File type:
- Architecture:
- Entry point:
- Analysis date:
- Tool used:

## Executive Summary

Briefly explain what the binary appears to be or what was discovered.

## Tool Choice

Explain whether radare2 CLI, r2pipe, or both were used, and why.

## File Information

Include PE metadata and general binary information.

## Hashes

Include at least SHA256 if available.

## Sections

List PE sections and note anything unusual, such as suspicious permissions or unexpected section names.

## Imports

List important imported DLLs and functions.

Highlight APIs related to:

- file operations
- registry operations
- process creation
- memory allocation
- networking
- cryptography
- anti-debugging
- user interface

## Exports

List exported functions if any.

If the binary is an EXE and has no exports, say so.

## Strings

List interesting strings only unless the user asks for all strings.

Prioritize strings related to:

- URLs
- IP addresses
- file paths
- registry keys
- commands
- error messages
- passwords or keys
- suspicious keywords
- UI messages

## Functions

List discovered functions, especially:

- entry point
- main or WinMain if found
- imported API wrappers
- suspicious functions
- user-defined functions
- functions with many cross-references

## Disassembly Notes

Explain important code paths in plain English.

Include radare2 addresses where useful.

## Cross-References

Document important xrefs, especially references to interesting strings, imported APIs, or suspicious functions.

## Suspicious or Interesting Findings

Highlight anything worth deeper investigation.

## Limitations

Mention anything that could not be determined from the current analysis.

## Recommended Next Steps

Suggest further static or dynamic analysis steps.
```

---

## Hashing

When possible, compute SHA256 using PowerShell:

```powershell
Get-FileHash .\binaries\target.exe -Algorithm SHA256
```

Include the hash in the report.

---

## Basic Static Analysis Checklist

For each binary, try to collect:

- SHA256 hash
- file type
- architecture
- compiler clues if available
- entry point
- sections
- imports
- exports
- strings
- functions
- entry point disassembly
- suspicious or interesting APIs
- suspicious or interesting strings
- possible main or WinMain function
- recommended next steps

---

## Interesting API Categories

When reviewing imports or disassembly, pay attention to these categories.

### File APIs

```text
CreateFileA
CreateFileW
ReadFile
WriteFile
DeleteFileA
DeleteFileW
CopyFileA
CopyFileW
MoveFileA
MoveFileW
```

### Process and Execution APIs

```text
CreateProcessA
CreateProcessW
ShellExecuteA
ShellExecuteW
WinExec
ExitProcess
TerminateProcess
```

### Memory APIs

```text
VirtualAlloc
VirtualAllocEx
VirtualProtect
VirtualProtectEx
WriteProcessMemory
ReadProcessMemory
CreateRemoteThread
HeapAlloc
HeapFree
```

### Registry APIs

```text
RegOpenKeyExA
RegOpenKeyExW
RegCreateKeyExA
RegCreateKeyExW
RegSetValueExA
RegSetValueExW
RegQueryValueExA
RegQueryValueExW
RegDeleteKeyA
RegDeleteKeyW
```

### Networking APIs

```text
InternetOpenA
InternetOpenW
InternetConnectA
InternetConnectW
HttpOpenRequestA
HttpOpenRequestW
HttpSendRequestA
HttpSendRequestW
URLDownloadToFileA
URLDownloadToFileW
socket
connect
send
recv
WSAStartup
```

### Anti-Debugging or System APIs

```text
IsDebuggerPresent
CheckRemoteDebuggerPresent
NtQueryInformationProcess
GetTickCount
QueryPerformanceCounter
OutputDebugStringA
OutputDebugStringW
```

### UI APIs

```text
MessageBoxA
MessageBoxW
DialogBoxParamA
DialogBoxParamW
GetDlgItemTextA
GetDlgItemTextW
SetDlgItemTextA
SetDlgItemTextW
```

---

## radare2 Command Logging

When running radare2 commands, save important output to logs.

Example:

```powershell
rabin2 -I .\binaries\target.exe > .\logs\target_info_20260611_153000.log
rabin2 -i .\binaries\target.exe > .\logs\target_imports_20260611_153000.log
rabin2 -z .\binaries\target.exe > .\logs\target_strings_20260611_153000.log
r2 -A -q -c "afl" -c "q" .\binaries\target.exe > .\logs\target_functions_20260611_153000.log
```

---

## r2pipe Script Guidelines

When writing Python r2pipe scripts:

1. Use `argparse` for input arguments.
2. Validate that the binary path exists.
3. Run `aaa` before extracting functions.
4. Prefer JSON commands such as `aflj`, `izj`, `iij`, `iSj`.
5. Handle errors gracefully.
6. Save reports with timestamps.
7. Close the radare2 session properly.
8. Do not hard-code a single binary name.

Basic r2pipe pattern:

```python
import argparse
import datetime
import pathlib
import r2pipe


def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("binary", help="Path to EXE or DLL to analyze")
    args = parser.parse_args()

    binary_path = pathlib.Path(args.binary).resolve()
    if not binary_path.exists():
        raise FileNotFoundError(f"Binary not found: {binary_path}")

    r2 = r2pipe.open(str(binary_path))
    try:
        r2.cmd("aaa")
        info = r2.cmdj("iIj")
        imports = r2.cmdj("iij")
        strings = r2.cmdj("izj")
        functions = r2.cmdj("aflj")
        sections = r2.cmdj("iSj")

        print(info)
        print(imports)
        print(strings)
        print(functions)
        print(sections)
    finally:
        r2.quit()


if __name__ == "__main__":
    main()
```

---

## When Creating New Scripts

If the user asks for a new analysis ability, create or update a script in `scripts`.

Examples:

- extract all strings and classify them
- extract imports and group them by DLL
- find xrefs to interesting strings
- find functions calling a specific API
- compare imports between two binaries
- batch analyze all EXE files in a folder
- create a summary report for many binaries

For every new script, include:

- comments
- command-line usage
- error handling
- timestamped output
- clear saved output path

Also update `README.md` if the new script becomes part of the normal workflow.

---

## Analysis Discipline

For every analysis task:

1. Do not overwrite previous reports.
2. Use timestamps.
3. Keep raw logs separate from final reports.
4. Explain commands used.
5. Explain why radare2 CLI or r2pipe was chosen.
6. Summarize findings clearly.
7. Suggest next steps.
8. Save reusable work.
9. Avoid unnecessary duplication.
10. Keep the lab folder organized.

---

## Handling Errors

If a command fails:

1. Show the exact error.
2. Explain the likely cause.
3. Try a safe fix.
4. Save troubleshooting notes in `notes`.
5. Do not silently skip failed steps.

Common issues to check:

- radare2 not in PATH
- Python virtual environment not activated
- r2pipe not installed in the active environment
- binary path is wrong
- PowerShell execution policy prevents running scripts
- file is locked by another process
- insufficient permissions

---

## PowerShell Execution Policy Note

If PowerShell blocks local scripts, suggest this safer per-user command:

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

Do not change system-wide execution policy unless the user explicitly asks.

---

## Do Not Guess Rule

If required information is missing, do not invent it.

Examples:

- If the binary path is missing, ask for the path or inspect the `binaries` folder.
- If a script is missing, say it is missing and offer to create it.
- If radare2 is unavailable, report that the installation may not be complete.
- If a command output is ambiguous, say so and recommend the next check.

---

## Default Behavior for Future Codex Sessions

When this skill file is loaded, Codex should behave as follows:

1. Treat `C:\radare2_ai` as the lab root.
2. Use `binaries` for input files.
3. Use `scripts` for reusable tools.
4. Use `reports` for final Markdown reports.
5. Use `logs` for raw command output.
6. Use `notes` for setup, troubleshooting, and working notes.
7. Use radare2 CLI for quick inspection.
8. Use r2pipe for structured automation and reporting.
9. Save outputs with timestamps.
10. Explain tool choices clearly.
11. Never overwrite earlier work unless the user explicitly asks.

---

## Example User Instruction

The user may say:

```text
Use the radare2/r2pipe skill to analyze C:\radare2_ai\binaries\crackme1.exe.
```

Codex should then:

1. Load this skill.
2. Verify the file exists.
3. Decide whether to use CLI, r2pipe, or both.
4. Run the analysis.
5. Save logs.
6. Save a final Markdown report.
7. Summarize the findings to the user.

---

## End of Skill

Follow this skill file for radare2/r2pipe reverse engineering tasks on Windows 11.
