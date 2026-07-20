# Codex Task: Install radare2 and r2pipe on Windows 11 and Prepare AI-Assisted Reverse Engineering Workflow

## Objective

Set up a Windows 11 reverse engineering environment using:

* radare2 CLI
* r2pipe for Python
* Python virtual environment
* helper scripts for analyzing Windows PE binaries

The goal is to allow Codex to use either:

1. **radare2 directly** when simple CLI inspection is enough, or
2. **Python + r2pipe** when structured automation, JSON parsing, or report generation is better.

## Important Rules

* Do not assume radare2 is already installed.
* Do not assume Python is already configured correctly.
* Prefer official radare2 Windows releases when possible.
* Verify every installation step before continuing.
* Use Windows 11 PowerShell commands.
* Use underscores in project folder names, for example `radare2_ai`, rather than dashes, because underscores are safer for Python package/module-style naming and scripting.
* Create clear notes in Markdown explaining what was installed and how to use it.

## Step 1: Check Existing Environment

Check whether the following are installed and available in PATH:

```powershell
python --version
pip --version
r2 -v
radare2 -v
```

If Python is missing, tell me clearly and stop.

If radare2 is missing, install it using a suitable Windows method.

Preferred options:

1. Official radare2 Windows release from GitHub
2. Scoop package manager, if already installed
3. Manual ZIP extraction and PATH update

Do not use random third-party downloads.

Official references:

* https://github.com/radareorg/radare2
* https://github.com/radareorg/radare2/releases
* https://book.rada.re/install/windows.html

## Step 2: Install radare2

Install radare2 for Windows 11.

After installation, verify:

```powershell
r2 -v
radare2 -v
rabin2 -v
rasm2 -v
rahash2 -v
```

If `r2` works but `radare2` does not, note that `r2` is the normal short command and continue.

## Step 3: Create Python Virtual Environment

Create a project folder:

```text
C:\radare2_ai
```

Inside it, create a Python virtual environment:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
```

Install r2pipe:

```powershell
pip install r2pipe
```

Verify:

```powershell
python -c "import r2pipe; print('r2pipe OK')"
```

Official references:

* https://book.rada.re/scripting/r2pipe.html
* https://pypi.org/project/r2pipe/
* https://github.com/radareorg/radare2-r2pipe

## Step 4: Create Test Binary Folder Structure

Create this folder structure:

```text
C:\radare2_ai\
│
├── binaries\
├── scripts\
├── reports\
├── notes\
└── README.md
```

The `binaries` folder is where I will place Windows `.exe` or `.dll` files for analysis.

## Step 5: Create Basic radare2 CLI Helper Script

Create:

```text
scripts\basic_r2_analysis.ps1
```

The script should accept a binary path and run basic radare2/rabin2 analysis.

It should collect:

* PE file information
* imports
* exports
* strings
* sections
* functions
* entry point disassembly

Use commands such as:

```powershell
rabin2 -I target.exe
rabin2 -i target.exe
rabin2 -E target.exe
rabin2 -z target.exe
r2 -A -q -c "iI" -c "afl" -c "pdf @ entry0" -c "q" target.exe
```

Save output into:

```text
reports\
```

## Step 6: Create r2pipe Python Analysis Script

Create:

```text
scripts\analyze_with_r2pipe.py
```

This script should:

1. Accept a binary path as an argument.
2. Open the binary with r2pipe.
3. Run analysis using `aaa`.
4. Use JSON commands where possible.
5. Extract useful information such as:

   * binary info
   * imports
   * exports
   * strings
   * functions
   * sections
   * entry point
6. Save a structured Markdown report into the `reports` folder.

Prefer JSON commands such as:

```text
ij
iIj
iij
iEj
izj
aflj
iSj
```

Use normal text output only when JSON output is unavailable.

## Step 7: Tool Selection Rules

When analyzing a binary, decide which tool to use:

### Use direct radare2 CLI when:

* I only need a quick inspection.
* I ask for strings, imports, exports, sections, or file info.
* The analysis can be done with a few radare2 commands.
* Human-readable output is enough.

### Use r2pipe when:

* I ask for an automated report.
* I ask to compare multiple binaries.
* I ask for JSON or structured output.
* I ask to extract functions, imports, strings, and sections into a reusable format.
* I ask for repeated analysis across many files.
* I ask for a script that can be reused later.

## Step 8: Create README.md

Create a README.md explaining:

* what was installed
* how to activate the Python virtual environment
* how to check radare2
* how to run the PowerShell CLI helper
* how to run the r2pipe Python script
* when to use radare2 directly
* when to use r2pipe

Example usage:

```powershell
cd C:\radare2_ai

.\.venv\Scripts\Activate.ps1

.\scripts\basic_r2_analysis.ps1 .\binaries\target.exe

python .\scripts\analyze_with_r2pipe.py .\binaries\target.exe
```

## Step 9: Verification

After setup, perform these checks:

```powershell
r2 -v
rabin2 -v
python --version
pip show r2pipe
python -c "import r2pipe; print('r2pipe ready')"
```

Then create a final setup report:

```text
notes\setup_report.md
```

The report should include:

* radare2 version
* Python version
* r2pipe version
* installation method used
* project folder path
* any errors encountered
* how the errors were fixed
* next recommended steps

## Step 10: Future Reverse Engineering Workflow

For future reverse engineering tasks:

* Determine whether direct radare2 CLI or r2pipe is the most appropriate tool.
* Prefer direct radare2 commands for quick analysis.
* Prefer r2pipe for automation, JSON extraction, reporting, and repeatable workflows.
* Explain which approach was chosen and why.
* Create reusable scripts whenever automation would be beneficial.
* Save generated reports in the `reports` folder.
* Save reusable scripts in the `scripts` folder.
* Save setup and operational notes in the `notes` folder.

## Step 11: Do Not Guess

If an installation step fails:

1. Show the exact error.
2. Explain the likely cause.
3. Try a safe fix.
4. Document the fix in `notes\setup_report.md`.

Do not silently skip failed steps.
